# React 实践优化

> 细节决定成败，最近在React项目复盘中，发现很多很简单的内容，却往往处理的不是太好，所以，这篇文章主要针对项目中的一些点，记录下优化或使用的方法。

## 一、引用优化

**反例：所有引用没有顺序，看起来比较混乱**

```
import React, { useState, useEffect } from "react";
import { Tabs, Card } from "antd";
import Base from "../../components/FormDemo/base";
import moment from "moment";
import UseFormDemo from "../../components/FormDemo/useForm";
import { getList } from "../api"
import lodash from 'lodash'
```

**优化：依赖引入，第三方库放在前面，本地文件的引用，放在下面**

```
import React, { useState, useEffect } from "react";
import lodash from 'lodash'
import moment from "moment";
import { Tabs, Card } from "antd";

import Base from "../../components/FormDemo/base";
import UseFormDemo from "../../components/FormDemo/useForm";

import { getList } from "../api"
```



## 二、关于对象的属性是否存在

1、Javascript获取对象的属性值，可能属性值不存在，善用`||` `&&`

```js
const object = {
	a: 1,
	b: 2
}
// es6+, 判断对象属性值是否存在
const c = object.c  
// 优化写法
const c = object?.c 
```

2、非必要的三元选择符直接使用 ||

```js
const size = sise ? size : 1 
// 优化写法
const size = size || 1 
```

3、判断数组长度，利用1==true，减少判断

```js
const arr = []
if(Array.isArray(arr)&&arr.length > 0){
	return true
}else{
	return false
} 
// 优化写法
Array.isArray(arr)&&arr.length ? true : false
```

4、if else => 替换成三元选择符

```js
if(Array.isArray(arr)){
  getList(arr)
}else{
  getDetail()
} 
// 优化写法
Array.isArray(arr) ? getList(arr) : getDetail()
```



## 三、FC中的常量和方法写在组件外

函数式组件每次变量更新都会导致函数的重新执行和渲染，如果将变量和普通函数定义在组件内，会导致无效的更新，浪费性能

- 对应常量的定义和普通方法应该放在函数组件外部
- 组件内的函数，可以使用useCallback，依赖变量更新

```js
// 只有在id变化的情况下，才会触发函数的更新
const getList = useCallback((id) => {
	Api.getList({id}).then((Data) => {
		setData(Data)
	})
}, [id])
```

- 使用useCallback处理ESlint检测告警处理

![ESlint告警](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639715719515useEffect1.png)

当依赖的函数中有可变的参数，那么在useEffect使用，就可能导致函数更新但是调用没有更新

```jsx
const [Data, setData] = useState([])
const [params, setParams] = useState("")

const getList = useCallback((params) => {
  let data = {
  	query: params
  }
  Api.GetList(data).then(res => {
  	setData(Data)
  })
}, [])

useEffect(() => {
	getList()
}, [getList])
```

这样就成功解决了告警，也解决了参数更新，函数不更新的问题。



## 四、对组件使用prop-types参数校验

> 如果使用js开发，那么组件的参数会比较混乱，最好使用prop-types进行限制，这样开开发阶段就避免了错误的出现

子组件：使用

```react
import PropTypes from "prop-types"

function TableWrap(){
  return <div>一个表格组件</div>
}

TableWrap.defaultProps = {
    usePagination: false
};

TableWrap.propTypes = {
    columns: PropTypes.array.isRequired, // 标题数据
    useReloadButton: PropTypes.bool, // 刷新表格按钮
    leftAction: PropTypes.any, // 表格头部左侧按钮操作
}

export default TableWrap
```

父组件

```react
import TableWrap from './TableWrap'

function FatherComponent(){
	return <TableWrap  useReloadButton={false} />
}
export default FatherComponent
```

父组件没有赋值cloumns参数，控制台报错

![控制台报错](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639650251735prop-types.png)



##  五、子组件和父组件交互

### 1、父组件给子组件传值

```jsx
// 父组件
function Father(){
  const data = "我是来自父组件的数据"
	return <ChildComponent data={data} />
}
export default Father

// 子组件
function ChildComponent({data}){
	return <div>{data}</div>
}
export default ChildComponent
```



### 2、子组件给父组件传值

> 一般子组件给父组件传值很简单，直接使用组件方法就可以了

```jsx
// 父组件
function Father(){
  const [message, setMessage] = useState("")
	return <ChildComponent handleClick={setMessage} />
}
export default Father

// 子组件
function ChildComponent({handleClick}){
  const [data, setData] = useState("")
	return <div onClick={()=>handleClick(data)}>我是子组件</div>
}
export default ChildComponent
```



### 3、父组件调用子组件方法/或获取子组件数据

> 使用useImperativeHandle，来暴露子组件方法，或子组件的数据

```jsx
// 父组件通过childRef.current调用子组件数据或方法
import React, { useRef } from 'react'
function Father(){
  const childRef = useRef()
  useEffect(() => {
    childRef.current.fetchData()
  }, [])
  
  const getData = () => {
    const result = childRef.current && childRef.current.data
    console.log("子组件的数据是：", result)
  }
  
	return <div>
    <Button onClick={getData}>获取子组件数据</Button>
    <ChildComponent ref={childRef} />
  </div>
}
export default Father

// 子组件暴露方法或数据给父组件，通过useImperativeHandle第二个参数，刷新暴露的数据或方法
import React, { useState, forwardRef, useImperativeHandle } from 'react'
function ChildComponent(props, ref){
  
  const [Data, setData] = useState([{a: 1}])
  
  useImperativeHandle(ref, () => ({
     fetchData: () => fetchData(),
     data: Data
  }), [Data]);
  
  const fetchData = () => {
    console.log("获取数据")
  }
  
	return <div>我是子组件</div>
}
export default forwardRef(ChildComponent)
```



### 4、多个组件间的消息共享

- 对于多个组件简单数据传递或共享，我们可以采用Context，维护一个底层的数据绑定。
- 这这种做法也有弊端，组件依赖于Context，或者在子组件更新值，会导致代码无法解耦，不利于复用。
- 如果对于需要全局管理更新的数据可以使用状态管理`Redux`或者`Mobx`

**`createContext`、`useContext`、`Provider`、`consumer`的使用**

> Context的使用也很简单，具体实例：

**Context.js**： 用来创建一个Context

```react
import React, { createContext } from 'react'
export default MessageContent = createContext(null)
```

**Index.js**： 用来包裹父组件，让数据可以共享

```react
import React from 'react'
import MessageContent from './Context'
import Child from './Child'
const initState = {
  username: "zhangsan"，
  age: 10
}
function Father(){
	return <MessageContent.Provider value={initState}>
  	<Child1 />
    <Child2 />
  </MessageContent.Provider>
}
```

**Child1.js**：子组件利用`useContext`获取Context共享的数据

```react
import React, { useContext } from 'react'
import MessageContext from './Context'
function Child1(){
  
  const { username, age } = useContext(MessageContext)
  
  return <div>
    <p>this is child1 component</p>,
    <div>username: {username}</div>
    <div>username: {age}</div>
  </div>
}
```

**Child2.js**：子组件利用`Consumer`读取共享数据

```react
import React from 'react'
import MessageContext from './Context'
function Child2(){
  return <MessageContext.Consumer>
            {
                ({username, age}) => {
                    return <div>
                        <p>this is child2 component</p>
                        <div>username: {username}</div>
                        <div>age: {age}</div>
                    </div>
                }
            }
        </MessageContext.Consumer>
}
```

插件加载的组件，就可以看到这两种方式都可以获取共享的数据。

![show](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639987762971react-hooks1.png)



## 六、防抖和节流

### 1、防抖

> 防抖：当连续输入内容时，不希望每次都执行查询调用，在用户停止输入一段时间后执行一次

利用setTimeout，延迟执行的特性，在连续输入时候，

```js
export function debounce(cb, wait = 2000) {
  let timer = null
  return (...args) => {
      if (timer) clearTimeout(timer)
      timer = setTimeout(() => {
          timer = null
          cb(args)
      }, wait)
  }
}
```

> 使用防抖

```js
import { debounce } from './debounce'
const getList = (data) => {
	console.log("远程查询产品类型列表")
}
const debounceList = debounce(getList, 1000) //连续事件停止1秒后执行
// Input onChange事件
const onChange = (val) => {
  debouncList({query: val})
}
```

> 在React中防抖函数处理

我们知道函数式组件，在每次刷新后都会重新初始化，那么就会导致如上所示的调用失效。所以我们可以利用useRef将debounce的函数绑定，这样就可以防止重复更新。

```jsx
import React, { useState, useRef } from 'react'
import { Input, Button, Card } from "antd"
import { debounce } from "../../utils/common"

function Debounce(){
    const [value, setValue] = useState(undefined)
    const getQueryList = (params) => {
        console.log("获取查询列表", params)
    }
    const debounceRef = useRef(debounce(getQueryList))
    const onChange = (e) => {
      	console.log("连续输入")
        setValue(e.target.value)
        debounceRef.current({a: e.target.value})
    }
    return <Card>
        <div>
            <p>防抖：多次触发同一事件，经过一段时间后最少执行一次</p>
        </div>
        <div style={{marginBottom: 20}}>
            输入内容：<Input value={value} onChange={onChange}/>
        </div>
    </Card>
}
export default Debounce
```



效果：![防抖](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639985308246debounce1.png)

可以看到防抖，连续触发，只要事件不停，最后只会执行一次

**应用场景（一般只需要最终触发一次的事件）：Input输入、页面resize**

### 2、节流

> 当一段时间内，大量触发同样的事件，只生效一次，类似游戏中的技能冷却。

```react
export function throttle(func, delay=1000){
  // 初始化没有调用setTimeout
  let isWorking = false;
  return (params) => {
    if(isWorking){
      // 如果正在执行setTimeout,则return
      return false
    }
    // 开始执行setTimeout
    isWorking = true;
    setTimeout(() => {
      // 调用函数
      func(params)
      // 调用完毕后，告诉函数执行完了，可以再次延迟定时
      isWorking = false
    }, delay)
  }
}
```

执行效果

```react
import React, { useState, useRef } from 'react'
import { Input, Button, Card } from "antd"
import { throttle } from "../../utils/common"

function Throttle(){
    const [value, setValue] = useState(undefined)
    const getQueryList = (params) => {
        console.log("获取查询列表", params)
    }
    const throttleRef = useRef(throttle(getQueryList, 1000))
    const onChange = (e) => {
      	console.log("连续输入")
        setValue(e.target.value)
        throttleRef.current({a: e.target.value})
    }
    return <Card>
        <div>
            <p>节流：多次触发同一事件，经过一段时间最多只会执行最后一次</p>
        </div>
        <div style={{marginBottom: 20}}>
            输入内容：<Input value={value} onChange={onChange}/>
        </div>
    </Card>
}
export default Throttle
```

节流的展示效果：

![节流](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639985180888thorttle1.png)

可以看到连续触发事件，但会按固定频率触发，最后也会执行一次。

**应用场景（强制函数以固定的频率触发）：input、keyup、resize、touchmove、 mousemove、scroll，这些不需要频繁触发但是却需要以特定频率触发的场景（这样说来，防抖的场景，其实都可以使用节流来实现）**

## 七、Hooks的使用

### 1、memo、useMemo和useCallback的区别

> memo用来优化React组件的渲染次数

一个简单的父组件

```react
function Context(){
    
    const [childData, setChildData] = useState(1)
    const [child2Data, setChild2Data] = useState(2)

    return <Card>
      <div style={{marginBottom: 100}}>
      	<Child data={childData}/>
      </div>
      <div>
      	<Child2 data={child2Data}/>
      </div>
      <Button onClick={() => setChildData(childData+1)}>更新组件一的数据</Button>
      <Button onClick={() => setChild2Data(child2Data+1)}>更新组件二的数据</Button>
    </Card>
}

export default Context
```

包含两个子组件

```react
function Child1({data}){
    console.log("组件一的数据", data)
    return <div>
        <p>this is child1 component</p>
    </div>
}
function Child2({data}){
 		console.log("组件二的数据", data)
    return <div>
        <p>this is child2 component</p>
    </div>
}
```

当更新组件一的数据时，打印发现每次组件二也会更新

![打印数据](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639988953567react-child1.png)

```react
import React, { memo } from 'react'
function Child2({data}){
 		console.log("组件二的数据", data)
    return <div>
        <p>this is child2 component</p>
    </div>
}
export default memo(Child2)
```

给组件2使用memo，再次更新组件一的数据发现

![打印](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639988884479react-hooks2.png)

发现，只更新了一次，这是为什么呢？

`React.memo`会对组件的props做个浅比较，发现组件参数变化菜会更新，接着我们看下memo的第二个参数

```react
import React, { memo } from 'react'
function diff(prev, next){
	console.log("prev", prev, next)
	return true
}
function Child2({data}){
 		console.log("组件二的数据", data)
    return <div>
        <p>this is child2 component</p>
    </div>
}
export default memo(Child2, diff)
```

第二个参数是一个函数，他有两个参数，更新之前和更新之后的props，在这里我们可以人工对比参数，返回true则不渲染组件、返回false渲染组件

> useMemo 用来优化React组件内部返回值的渲染次数

```react
import Child from './Child'
function Father(){
	return <Child
           data={useMemo(() => {
      				name,
              childName: `名字：${name}`
    				}),[name]}
          >组件
    </Child>
}
```

根据依赖，返回更新的值

> useCallback用来优化React组件内部函数的渲染次数

```react
function Child(){
  const [query, setQuery] = useState(1)
  
  const getList = useCallback(()=>{
    const data = {
			query
    }
    fetchList(data).then(res => {
       console.log("接口请求", res)
    })
  }, [query])
  
  useEffect(() => {
    getList()
  }, [getList])
	
	return <div></div>
}
```

getList函数依赖于query参数更新，本来useEffect不能依赖函数，使用useCallback以后，就可以作为依赖更新

### 2、useEffect使用注意事项

**组件告警，提示如下**

```js
Warning: Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in a useEffect cleanup function.in Notification
```

如下，常见的数据请求方式

```react
function List(){
	return <div>
  		<router1>路由一</router1>
    	<router2>路由二</router2>
  </div>
}
function Router1(){
  const [data, setData] = useState([])
  useEffect(() => {
    getList().then(({Data}) => {
      setData(Data)
    })
  }, [])
  return <div>路由组件一</div>
}
function Router2(){
  return <div>路由组件二</div>
}
```

这个告警常见于，请求接口后，请求发出后，相应较慢，但是用户已经切换了组件路由，退出并且销毁，接口返回的数据没法使用setData，报错。

解决这个问题一般情况下，就是要在组件销毁后拒绝执行setState方法，具体的实现如下：

```react
import React, { useRef } from 'react'
function Router1(){
	const mounted = useRef(true)
  useEffect(() => {
    getList().then({Data}=>{
      if(Data&&mounted.current){
        setData(Data)
      }
    })
    return () => {
      mounted.current = false
    }
  }, [])
}
```



## 八、参考文章

- [React Hooks最佳实践](https://github.com/ascoders/weekly/blob/master/%E5%89%8D%E6%B2%BF%E6%8A%80%E6%9C%AF/120.%E7%B2%BE%E8%AF%BB%E3%80%8AReact%20Hooks%20%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5%E3%80%8B.md)
- [防抖和节流](https://zhuanlan.zhihu.com/p/38313717)