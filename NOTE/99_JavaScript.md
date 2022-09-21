

## JavaScript基础：语法

## 第七章：常用对象



### 5.5浏览器前端Cookie

Cookie详解：

```
<table>
<tr>
    <td>用户名：</td>
    	<td><input id="demo_input1" type="text"  name="username" value="999"/></td>
    <td>密码：</td>
    	<td><input id="demo_input2" type="text" name="pwd" value="888" /></td>
</tr>
<tr>
    <td colspan="2" align="center">
    	<input id="demo_button1" type="button" value="设置Cookie" /></td>
    <td colspan="2" align="center">
    	<input id="demo_button2" type="button" value="获取Cookie" /></td>
</tr>
</table>
```

<table>
		    <tr>
		        <td>用户名：</td>
		        <td><input id="demo_input1" type="text"  name="username" value="999"/></td>
		        <td>密码：</td>
		        <td><input id="demo_input2" type="text" name="pwd" value="888" /></td>
		    </tr>
		    <tr>
		        <td colspan="2" align="center">
					<input id="demo_button1" type="button" value="设置Cookie" /></td>
		        <td colspan="2" align="center">
					<input id="demo_button2" type="button" value="获取Cookie" /></td>
		    </tr>
		</table>

```
<script type="text/javascript">	
	    $("#demo_button1").click( function(){
	        var cookie_username=$("#demo_input1").val();
	        var cookie_password=$("#demo_input2").val();
			alert(cookie_username+cookie_password)
			
	        setCookie("username",cookie_username,30);
			setCookie("password",cookie_password,30);
	    })
		$("#demo_button2").click( function(){
		   var name = getCookie("username");
		   var pass = getCookieObj().password ;
		   alert(name+''+pass) ;
		})
</script>
```

工具包：util.js

```
/**
 *  func  getCookie()  获取单个cookie的值
 *  pram  cookieName  cookie的名称
 **/
 // 用法  getCookie("name");
function getCookie(cookieName) {
	var cookieObj = {},
		cookieSplit = [],
		// 以分号（;）分组
		cookieArr = document.cookie.split(";");
	for (var i = 0, len = cookieArr.length; i < len; i++)
		if (cookieArr[i]) {
			// 以等号（=）分组
			cookieSplit = cookieArr[i].split("=");
			// Trim() 是自定义的函数，用来删除字符串两边的空格
			cookieObj[cookieSplit[0].trim()] = cookieSplit[1].trim();
		}
	return cookieObj[cookieName];
}
/**
 *  func  getCookieObj()  获取所有cookie的值并将其保存为对象的属性
 **/
 // 用法
 //  	var cookieObj=getCookieObj();
 // 	cookieObj.name;
function getCookieObj() {
	var cookieObj = {},
		cookieSplit = [],
		// 以分号（;）分组
		cookieArr = document.cookie.split(";");
	for (var i = 0, len = cookieArr.length; i < len; i++)
		if (cookieArr[i]) {
			// 以等号（=）分组
			cookieSplit = cookieArr[i].split("=");
			// Trim() 是自定义的函数，用来删除字符串两边的空格
			cookieObj[cookieSplit[0].trim()] = cookieSplit[1].trim();
		}
	return cookieObj;
}
// 使用方法：setCookie('username','Darren',30)
function setCookie(c_name, value, expiredays){
　　　　var exdate=new Date();
　　　　exdate.setDate(exdate.getDate() + expiredays);
　　　　document.cookie=c_name+ "=" + escape(value) + ((expiredays==null) ? "" : ";expires="+exdate.toGMTString());
　　}


```



## 第八章:DOM

### 6.4元素拖曳

https://www.cnblogs.com/weiqinl/p/7886049.html

每一个可拖动的元素，在拖动过程中，都会经历三个过程，`拖动开始`-->`拖动过程中`--> `拖动结束`。

元素设置为可拖曳

​	draggable=”true"

拖动

| 针对对象     | 事件名称  | 说明                     |
| ------------ | --------- | ------------------------ |
| 被拖动的元素 | dragstart | 在元素开始被拖动时候触发 |
|              | drag      | 在元素被拖动时反复触发   |
|              | dragend   | 在拖动操作完成时触发     |

| 目的地对象 | dragenter | 当被拖动元素进入目的地元素所占据的屏幕空间时触发 |
| ---------- | --------- | ------------------------------------------------ |
|            | dragover  | 当被拖动元素在目的地元素内时触发                 |
|            | dragleave | 当被拖动元素没有放下就离开目的地元素时触发       |

释放

| 针对对象   | 事件名称 | 说明                                                         |
| ---------- | -------- | ------------------------------------------------------------ |
| 目的地对象 | drop     | 当被拖动元素在目的地元素里放下时触发，一般需要取消浏览器的默认行为。 |



```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8">
		<meta http-equiv="X-UA-Compatible" content="IE=edge">
		<meta name="viewport" content="width=device-width, initial-scale=1.0">
		<title>Document</title>
		<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
		<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
	</head>
	<body>
		<style type="text/css">
			#src {
				width: 50px;
				height: 50px;
				background-color: goldenrod;
			}

			#dest {
				width: 50px;
				height: 50px;
				background-color: palegreen;
			}
		</style>
		<div id="src">
			<span draggable="true">12</span>
		</div>
		<hr>
		<div id="dest">
			<span>34</span>
		</div>
	</body>
	<script>
		//功能：
		//1.将src中的span 标签拖到dest中
		//2.删除dest中的span便签及其内容
		//3.删除src 中的span标签及其内容
		//步骤：
		//1. 定位src及dest
		var dragSrc = document.getElementById('src')
		var target = document.getElementById('dest')
		//2.src拖曳过程各个状态进行函数绑定
		//元素被开始被拖动时进行触发的函数
		// dragSrc.ondragstart = handle_start
		//元素在拖曳过程中一直会促发的函数
		// dragSrc.ondrag = handle_drag
		//元素在拖曳操作完成时进行的促发的操作，即松开鼠标就视为拖曳完成
		dragSrc.ondragend = handle_end


		function handle_start(e) {
			console.log('dragstart-在元素开始被拖动时候触发')
		}

		function handle_drag() {
			// console.log('drag-在元素被拖动时候反复触发')
		}

		function handle_end() {
			console.log('dragend-在拖动操作完成时触发')
			src.innerHTML=''
		}

		//3.src在拖曳到dest时促发的函数，这里对其进行绑定
		//当被拖动元素进入目的地元素所占据的屏幕空间时触发，从鼠标指针进入dest空间开始计算
		// target.ondragenter = handle_enter
		//当被拖动元素在目的地元素内时反复触发
		target.ondragover = handle_over
		//当被拖动元素没有放下就离开目的地元素时触发
		// target.ondragleave = handle_leave

		target.ondrop = handle_drop

		function handle_enter(e) {
			console.log('handle_enter-当元素进入目的地时触发')
			// 阻止浏览器默认行为
			e.preventDefault()
		}
		//必须的函数
		function handle_over(e) {
			console.log('handle_over-当元素在目的地时一直触发')
			// 阻止浏览器默认行为
			e.preventDefault() //必须存在不能丢
		}

		function handle_leave(e) {
			console.log('handle_leave-当元素离开目的地时触发')
			// 阻止浏览器默认行为
			// e.preventDefault()
		}

		function handle_drop(e) {
			console.log('handle_drop-当元素在目的地放下时触发')
			target.innerHTML = ''
			target.append(src.innerText)
			e.preventDefault()
		}
	</script>
</html>

```

