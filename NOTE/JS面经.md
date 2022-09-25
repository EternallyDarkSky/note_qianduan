



# 第一部分：

## Promise

### 介绍

```
性质：
	permise  是一种异步编程的新解决方案，使用链式解决回调地狱问题，是ES6新增的规范
	
细节
	三个状态
	数据存储
	同异步回调函数集合
	
三个状态:
	const PENDING = 'pending' 初始状态
    const RESOLVED = 'fulfilled'
    const REJECTED = 'failure'
	状态之间只能切换一次
		执行reslove时，由pedding->fulfilled
		执行reject 时，由pedding->failure
数据存储：
	self.data = undefined
回调函数集合：
	self.callbacks = []  
	// 每个回调函数原生的结构为：
	//	  {onResolved(){},onRejected(){}}
```

### 书写步骤

```
一.整体结构书写
	1. IIFE（立即执行函数）
	2. 自定义Promise 构造函数
	3. Promise绑定到Window
	4. 添加两个原型方法[then/catch]
	5. 添加Promise类方法[all/race/resolve/reject/自定义类方法]
二.resolve/reject函数书写
	1. 改变状态
	2. 接收数据
	3. 回调函数集合中有回调函数：执行回调函数集合
三. then/catch 函数书写
	性质callback是异步的！！！
	1. self变量接受this
	2. 返回新的Promise
	3. 在Promise中使用self获得上一个Promise的数据及状态，构造逻辑闭环
		1. 当上一个Promise为PEDDING时，入栈即可
		2. 当上一个Promise不为PEDDING时，需要执行当前then指定的回调函数，获得结果
		3. 将获得的结果，利用resolve和reject进行修改当前的Promise
	4. 提取handler公共代码
四.类方法书写
	resolve/reject
		1. 返回Promise
		2. 将参数利用resolve\reject进行对当前返回promise进行适当的修改
	all/race
```

### 代码核心

```js
// 共80行代码实现可promise的核心功能
见 W3C JS 高级
```







