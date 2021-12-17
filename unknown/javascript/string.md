## 字符串相关整理

### 一、字符串的定义

字符串定义ES5以前我们指用var=””定义，ES6之后我们使用let const定义变量

let 来定义变量，const通常用来定义常量，通常不可变。

```
let name = "张三"
const PI = "3.14"
```

我们常常会看到下面这几种定义方式

```
let str = "this is a 'string'"
let str1 = `this is 
a string`
let str2 = 'this is a string'
```

双引号，定义字符串内可以包含单引号，不能直接定义换行

反引号，定义字符串可以包含单引号，双引号等等，可以直接换行

单引号，定义字符串不能包含双引号，不能换行

以上三种方式都可以识别\n \t ‘ 等转义字符

### 二、字符串自带的一些方法

- length
- 读取字符串的长度

```
let str = "this is a string"
let len = str.length // 16
```

toString将其他类型的变量转化成字符串，不同的变量适用toString方法如下

| 变量      | 是否有toString方法 | 备注               |                           |
| --------- | ------------------ | ------------------ | ------------------------- |
| number    | 有                 |                    |                           |
| Bool      | 有                 |                    |                           |
| null      | 无                 |                    |                           |
| undefined | 无                 |                    |                           |
| array     | 有                 | 去去掉[], 输出内容 | 一般使用 JSON.stringify() |
| object    | 有                 | “[Object Object]”  | 一般使用JSON.stringify()  |

示例：

```
let num = 122
console.log(num.toString()) // "122"

let isShow = true
console.log(isShow.toString()) // "true"

let und //undefined
console.log(und.toString())
// VM982:2 Uncaught TypeError: Cannot read property 'toString' of undefined

let nu = null
console.log(un.toString())
//VM213:2 Uncaught TypeError: Cannot read property 'toString' of null

let arr = [1,2,45,6]
console.log(arr.toString()) // "1,2,45,6"
console.log(JSON.stringify(arr)) // "[1,2,45,6]"

let obj = {"name": "san.zhang", "age": 18}
console.log(obj.toString(obj)) // [object Object]
consoie.log(JSON.stringify(obj)) //"{"name": "san.zhang", "age": 18}"
```

- 在运算的时候常常会有隐式的转化
- 字符串加上其他类型，会转化成字符串

```
// 数字和字符串相加
let num = 0
console.log(num+"1") // "01"
// 布尔值和字符串相加
let isShow = true
console.log(isShow+"1") //"true1"
// undefined
let und = undefined
console.log(und+"1") // "undefined1"
// null
let nu = null
console.log(nu+"1")// "null1"
// arr (注意对比下面这两个的区别)
let arr = [1,23,4]
console.log(arr+12) // "1,23,412"
let arr1 = [1,23,]
console.log(arr+12) // "1,2312"
// obj
let c = {"age": 18}
console.log(c+"1") // "[object Object]1"
```

注意：通过以上例子，我们知道其实不同类型的数据和字符串相加，其实是先调用了toString(),先将非字符串的变量转化成字符串，在进行拼接。
字符串转化成数字

```
let a = "1"
console.log(+a) // 1
console.log(-a) // -1
console.log(5-a) // 4
```

### 三、字符串的”增删改查”

字符串存在栈区，没法修改，这里的增删改查主要为了便于记忆，将字符串的各种方法分类成“增删改查”

#### 增（字符串拼接）

```
let a = "this ",
b = "is",
c = "string"

1、使用+
console.log(a+b + " a " +c) // "this is a string"

2、使用es6 反引号
let result = `${a}${b} a ${c}`
console.log(result) //"this is a string"

3、concat方法
let result1 = a.concat(b, " a ", c)
console.log(result1) //"this is a string"
```

以上三种方法都是新生成了一个字符串，原字符串不变

#### 删(字符串空格)

```
**1、trim()、trimEnd()、trimLeft()、trimRight() **
trim() 删除字符串前后的空格
trimEnd()、trimRight() 删除字符串右边的空格
trimStart()、trimLeft() 删除字符串左边的空格
trimStart()、trimEnd() es2019新增方法，注意兼容性
let str = " abc defghijklmnopqrstuvwxyz "
console.log(str.trim()) // "abc defghijklmnopqrstuvwxyz"
console.log(str.trimEnd())
console.log(str.trimRight()) // " abc defghijklmnopqrstuvwxyz"
console.log(str.trimLeft()) // "abc defghijklmnopqrstuvwxyz "
```

字符串的删除可以通过变量覆盖，或者改、查的其他方法实现

#### 改(大小写转化、截取、替换)

```
字符串大小写转化 toLowerCase() toUpperCase()
字符串分割slice, substr, substring
字符串替换resplace

1、toLowerCase() toUpperCase()
let str = "Hello Javascript"
console.log(str.toLowerCase()) // "HELLO JAVASCRIPT"
console.log(str.toUpperCase()) // "hello javascript"
`
1、slice()、substr()、substring()
slice和substring的语法相同点
都是取字符串从indexStart到indexEnd的字符，下标包括前面的不包括后面下标的。
str.slice(indexStart, [indexEnd])
str.substring(indexStart, [indexEnd])
1、两者不传参数，返回原字符串
2、传入一个参数(正数或0)，从indexStart截取到str.length
3、传入indexStart参数大于字符串长度,返回空字符串，indexEnd参数大于字符串长度，则返回从indexStart 到indexEnd
4、indexStart==indexEnd 返回空字符串
let str = "abcdefghijklmnopqrstuvwxyz"
// 不传参数
console.log(str.slice())
console.log(str.substring()) // "abcdefghijklmnopqrstuvwxyz"

// 传入一个参数
console.log(str.slice(2))
console.log(str.substring(2)) //"cdefghijklmnopqrstuvwxyz"

// 传入参数indesStart大于字符串长度
console.log(str.slice(30)) //""
console.log(str.substring(30)) //""

// 传入参数indexEnd大于字符串长度
console.log(str.slice(0,30)) //"abcdefghijklmnopqrstuvwxyz"
console.log(str.substring(0,30)) //"abcdefghijklmnopqrstuvwxyz"

// 传入参数indexStart == indexEnd，返回空字符串
console.log(str.slice(2,2)) //""
console.log(str.substring(3,3)) //""
```

不同点

1、参数存在负值，则表现不同

```
slice() 更强大，indexStart和indexEnd 可以为负值，从字符串末尾开始取
substring() 的取值必须是非负整数,如果是负值或NaN 则默认为0
let str = "abcdefghijklmnopqrstuvwxyz"
// indexStart > 0 indexEnd < 0
console.log(str.slice(2, -1)) //实际取值（2，str.length）返回 "cdefigklmnopqrstuvwxy"
console.log(str.slice(2, 0)) //返回 ""
console.log(str.substring(2, -1)) //实际取值，交换参数实际取值（0, 2）返回 "a,b"

// indexStart < 0 
console.log(str.slice(-2, 1)) // slice 前面的值不允许大于后面的值，返回 ""
console.log(str.slice(-2, -1)) // 从字符串末尾开始取值 "y"
console.log(str.substring(-2, 1)) //实际取[0, 1] 返回 "a"
console.log(str.substring(-2, -1)) //实际取[0,0] 返回""
```

2、参数不存在负值，且indexEnd > indexStart 时，取值表现不同

```
slice() indexStart必须要小于indexEnd, 如果等于或者大于，返回值为""
substring() 如果indexStart > indexEnd，前者的值大于后者的值，则会默认从交换两个值
let str = "abcdefghijklmnopqrstuvwxyz"
console.log(str.slice(3, 2)) //返回“”
console.log(str.substring(3, 2)) 实际取值（2,3）返回 "c"

substr 语法
str.substr(indexStart, [strLength])
1、截取字符串从indexStart开始，长度为strLength的字符串
2、str.substr() 复制原字符串
3、如果indexStart>=strlength，则返回空字符串
4、如果indexStart < 0,则从字符串末尾开始取值，如果indexStart+strLength < 0 ,则从0开始取值
5、如果strLength 为0或<0 则返回空，如果省略或大于strLength 则取strLength
let str = "abcdefghijklmnopqrstuvwxyz"
console.log(str.substr(3, 2)) //返回“de”
console.log(str.substr()) // "abcdefghijklmnopqrstuvwxyz"
console.log(str.substr(30)) 返回 ""
console.log(str.substr(-1)) //返回 "z"
console.log(str.substr(-30)) //"abcdefghijklmnopqrstuvwxyz"
console.log(str.substr(-1, 0)) //返回 ""
```

replace 字符串替换

#### 查

```
1、str[sub]、charAt(sub)、charCodeAt(sub) //sub为字符串下标
这一组通过下标获取字符
其中str[sub]、charAt(sub) 获取的时对应的字符 charCodeAt(sub) 获取的是，对应字符的unicode编码
let str = "abcdefghijk"
console.log(str[0]) //"a"
console.log(str.charAt(0)) //"a"
console.log(str.charCodeAt(0)) //"a"的uincode 97

2、indexOf(searchValue, [fromindex]) lastIndexOf(searchValue, [fromindex])
indexOf 查找字符串，匹配第一次出现位置对应的下标，没有找到返回 -1, 大小写敏感
lastIndexOf 查找最后一次出现位置对应的下标，没有找到返回-1,大小写敏感
fromindex 开始查找的位置，一般使用中常常省略，大小为0-str.length
3、search()、match()

4、str.includes(searchStr, [indexfrom])
在str字符串中，从indexfrom开始查找searchStr，找到返回true,否则返回false, es6新增方法
indexfrom 默认值为0 一般使用中常常省略，大小为0-str.length
let str = "abcdefghifk"
console.log(str.includes("cd")) //true
console.log(str.includes("z")) //false
console.log(str.includes("cd", 5)) //false
```

### 四、字符串和数组相互转化

> 注意：要区分数组和字符串相同的方法

```
1、字符串切割成数组str.split()
语法：str.split(separator, [length])
separator参数为分隔符，可以为正则，length 返回的数组长度，如果省略则默认返回最大的长度
let str = "hello word"
console.log(str.split()) // ["hello word"]
console.log(str.split("")) // ["h", "e", "l", "l", "o", " ", "w", "o", "r", "d"]
console.log(str.split(" ")) // ["hello", "word"]
console.log(str.split(" ", 1)) //["hello"]

2、数组转化成字符串arr.join() 默认逗号
let str = ["hello", "word"]
console.log(arr.join(" ")) //"hello word"
console.log(arr.join()) // "hello,word"
console.log(arr.join("")) // "helloword"
```

注意点：

- 1、concat 字符串的拼接，数组的拼接
- 2、indexOf 字符串的查找，数组的查找
- 3、splice、reverse 数组方法 字符串没有
- 4、includes 两者都有
- 5、可以使用正则匹配的方法有 replace search match、split

### 五、字符串的常见操作

- 字符串的循环
- 字符串获取单个字符
- 字符串反转
- 字符串比较
- 字符串大小写转化
- 字符串正则校验