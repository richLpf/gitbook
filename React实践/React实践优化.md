# React 实践优化

> 细节决定成败

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

**useContext的使用**

```
```

**Redux的使用**

```
```



## 六、防抖和节流

> 在实现防抖

```

```

在React中防抖函数处理

```
```



## 七、Hooks的使用

### 1、useEffect使用注意事项

- 第二个参数的使用

- 模拟componentWillUnmount方法
- 组件告警，提示如下

```
Warning: Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in a useEffect cleanup function.in Notification
```

这个告警常见于，请求接口后，请求发出，但是组件已经退出并且销毁，接口返回的数据没法使用setState，报错。

解决这个问题一般情况下，就是要在组件销毁后拒绝执行setState方法，具体的实现如下：

```

```

### 2、useMemo和useCallback的区别







## 八、常用组件

### 1、定时器

### 2、css在js文件中的使用



## 九、参考文章

- [React Hooks最佳实践](https://github.com/ascoders/weekly/blob/master/%E5%89%8D%E6%B2%BF%E6%8A%80%E6%9C%AF/120.%E7%B2%BE%E8%AF%BB%E3%80%8AReact%20Hooks%20%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5%E3%80%8B.md)
- [防抖和节流](https://zhuanlan.zhihu.com/p/38313717)