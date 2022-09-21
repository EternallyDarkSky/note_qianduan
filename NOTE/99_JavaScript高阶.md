JavaScript高级教程





# 第一章: 文件处理



```html
<body>
	<div id="app">
		<input type="file" id="file"  @change="changeFiles"/>
		<hr >
		<div >
			<button type="button" @click="readAsDataURL">readAsDataURL</button>
		</div>
		<hr >
		<div >
			<button type="button" @click="readAsText">readAsText</button>
		</div>
		<hr >
		<div >
			<button type="button" @click="readAsBinaryString">readAsBinaryString</button>
		</div>
		<hr >
		<div @click="readAsDataURL">
			<button type="button" >图片预览</button>
			<img :src="DataURL" >	
		</div>
	
	</div>
</body>
<script>
	var vm = new Vue({
		el: '#app', //element 代指待控制的元素
		data:{
			files:[],
			DataURL:null,
			DataText:null,
			BinaryString:null
		},
		methods:{
			changeFiles(e) {
				
				length = e.target.files.length
				for (let i=0;i<length;i++){
					this.files.unshift(e.target.files[i])
					console.log(this.files)
				}
			},
			readAsDataURL(){
				if(window.FileReader){
					var reader = new FileReader()
					//base64 编码
					reader.readAsDataURL(this.files[0])
					new Promise((reslove,reject)=>{
						reader.onload=function(){
							reslove(reader.result)
						}
					}).then((data)=>{
						this.DataURL = data
						alert("dataurl changed")
					})
						
					
				}else{
					alert("not supported by this browser")
					throw new Error("update version of browser")
				}
			},
			readAsText(){
				if(window.FileReader){
					var reader = new FileReader()
				
					reader.readAsText(this.files[0])
					reader.onload=function(){
						this.DataText = reader.result
						console.log(this.DataText)
					}
				}else{
					alert("not supported by this browser")
					throw new Error("update version of browser")
				}
			},
			readAsBinaryString(){
				if(window.FileReader){
					var reader = new FileReader()

					reader.readAsBinaryString(this.files[0])
					reader.onload=function(){
						this.BinaryString = reader.result
						console.log(this.BinaryString)
					}
				}else{
					alert("not supported by this browser")
					throw new Error("update version of browser")
				}
			}
		}
	})
</script>
```



# 第二章：

## 2.1 ArrayBuffer

 用法：

​	let arrBuf = new ArrayBuffer(bufferByteSzie)

​	申请一个bufferByteSzie字节大小的连续空间

里面的数据不能被访问，也不能被操作

## 2.2 Uint8Array

 用法：

​	let u8a = new UintArray(arrBuf)

 	等价于

​	let u8a = new Uint8Array(bufferByteSzie)

```js
var a = new ArrayBuffer(6)
var b = new Uint8Array(a)
等价
var c = new Uint8Array(6)
b :Uint8Array(6) [0, 0, 0, 0, 0, 0, buffer: ArrayBuffer(6), byteLength: 6, byteOffset: 0, length: 6]
c :Uint8Array(6) [0, 0, 0, 0, 0, 0, buffer: ArrayBuffer(6), byteLength: 6, byteOffset: 0, length: 6]

c[0] = 255
c[1] = 254
c[2] = 253
c[3] = 252
c[4] = 251
c[5] = 250
c :Uint8Array(6) [255, 254, 253, 252, 251, 250, buffer: ArrayBuffer(6), byteLength: 6, byteOffset: 0, length: 6]
b[0] = 255
b[1] = 254
b[2] = 253
b[3] = 252
b[4] = 251
b[5] = 250
b :Uint8Array(6) [255, 254, 253, 252, 251, 250, buffer: ArrayBuffer(6), byteLength: 6, byteOffset: 0, length: 6]

a			:ArrayBuffer(6)	
地址相同，是同一个对象
b.buffer	:ArrayBuffer(6)

a			:ArrayBuffer(6)	
等价于,类型相同
c.buffer	:ArrayBuffer(6)	
```

## 2.3 Blob

Blob（Binary Large Object）对象代表了一段二进制数据

用法：	

​	var blob = new Blob(['我是Blob'],{type: 'text/html'});

创建一个URL来指向blob

​	href = URL.createObjectURL(blob);

​	<a download="data.jpeg" >下载</a>   

```js
<body>
	<div id="app">
		<input type="file" id="file"  @change="changeFiles"/>
		<hr >
		<div >
			<button type="button" @click="readAsDataURL">readAsDataURL</button>
		</div>
		<button @click="down">设置URL</button>
		<a download="data.jpeg" :href="link" >下载</a>
	</div>
</body>
<script>
	var vm = new Vue({
		el: '#app', //element 代指待控制的元素
		data:{
			files:[],
			DataURL:null,
			blob:null,
			link:null,
		},
		methods:{
            //加载文件
			changeFiles(e) {
				
				length = e.target.files.length
				for (let i=0;i<length;i++){
					this.files.unshift(e.target.files[i])
					console.log(this.files)
				}
			},
            //转base64
			readAsDataURL(){
				if(window.FileReader){
					var reader = new FileReader()
					//base64 编码
					reader.readAsDataURL(this.files[0])
					new Promise((reslove,reject)=>{
						reader.onload=function(){
							reslove(reader.result)
						}
					}).then((data)=>{
						this.DataURL = data
						alert("dataurl changed")
						this.blob = this.dataURItoBlob(this.DataURL)
					})			
				}else{
					alert("not supported by this browser")
					throw new Error("update version of browser")
				}
			},
			//base64 解码 转blob
			dataURItoBlob(dataURI) {
				// base64 解码
				let byteString = window.atob(dataURI.split(',')[1]);
                
				let mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0];
				let T = mimeString.split('/')[1];
				let ab = new ArrayBuffer(byteString.length);
				let ia = new Uint8Array(ab);
				for (let i = 0; i < byteString.length; i++) {
					ia[i] = byteString.charCodeAt(i);//指定位置的Unicode字符编码
				}
				console.log(ab)
				return new Blob([ab], {
					type: mimeString
				});
			},
			//a连接指向blobURL
			down(){
				this.link = URL.createObjectURL(this.blob)
			}
		}
	})
</script>
```









# 第三章：音视频



## 3.1 音视频HTML标签对象

```html
<!-- 音频 -->
	<audio controls >
		<!-- 优先使用第一个：冗余操作 -->
		<source src="./music/芒种.mp3" >
		<source src="./music/芒种1.mp3" >
		<!-- IE8等老版本兼容方式 -->
		<!-- 类型
				audio/mp3
				-->
		<embed src="./music/芒种.mp3" width="100" height="60"  type="audio/mp3"></embed>
		<!-- 您的浏览器不支持 HTML5 video 标签。 -->
	</audio>
<!-- 视频 -->
	<video width="840" height="560" controls  loop="loop">
		<source src="./music/芒种.mp4"  type="video/mp4">
		<!-- IE8等老版本兼容方式 -->
		<!-- 类型
				video/mp4
				-->
		<embed src="./music/芒种.mp4" width="800" height="500"  type="video/mp4"></embed>
		<!-- 您的浏览器不支持 HTML5 video 标签。 -->
	</video>
<!-- 第三方源引用 -->
	<iframe frameborder="0" src="https://v.qq.com/txp/iframe/player.html?vid=m00253deqqo" allowFullScreen="true">
	 </iframe>
```

## 3.2音视频标签对象的属性和方法

video/audio标签属性：

​	controls :向用户显示音视频播放控件（比如播放/暂停按钮）

​	autoplay:音视频在就绪后马上播放。

​	loop：循环播放音视频

​	muted：将视频的音视频输出为静音

​	volume:	1.0 是最高音量（默认）0.5 是一半音量 （50%）0.0 是静音

更加详细的对象属性参考：	

​		🏷[HTML DOM Audio 对象 (w3school.com.cn)](https://www.w3school.com.cn/jsref/dom_obj_audio.asp)

​		🏷[HTML DOM Video 对象 (w3school.com.cn)](https://www.w3school.com.cn/jsref/dom_obj_video.asp)

video/audio标签方法(js中使用)：

​	load()  重新加载音频元素。

​	play() 开始播放音频。

​	pause() 暂停当前播放的音频。

更加详细的对象方法参考：

​		🏷[HTML DOM Audio 对象 (w3school.com.cn)](https://www.w3school.com.cn/jsref/dom_obj_audio.asp)

​		🏷[HTML DOM Video 对象 (w3school.com.cn)](https://www.w3school.com.cn/jsref/dom_obj_video.asp)

js控制操作音视频对象的方法

	<!--设置html-->
	<video width="840" height="560" controls  loop="loop">
			<source src="芒种result.mp4"  type="video/mp4">
	</video>
	<!--暂停与播放按钮-->
	<button id="pause" onclick="pause()">pause</button>
	<button id="pause" onclick="play()">play</button>
	<button id="pause" onclick="mut()">muted</button>
	<button id="pause" onclick="volum()">volum</button>
	<!--控制脚本-->
	<script>
		function pause(){
			var p  = document.getElementsByTagName("video")[0] ;
			p.pause();
		}
		function play(){
			var p  = document.getElementsByTagName("video")[0] ;
			p.play();
		}
		function mut(){
			var p  = document.getElementsByTagName("video")[0] ;
			p.muted=true;
		}
		function volum(){
			var p  = document.getElementsByTagName("video")[0] ;
			p.volume =1.0;
			p.muted = false;
		}
	</script>

## 3.3音视频加载时发生的事件

​	当音频/视频处于加载过程中时，会依次发生以下事件：

​	⭐Event-1. loadstart:当浏览器开始寻找指定的音频/视频时，会发生 loadstart 事件。即当加载过程开始时。

```
html同上

var myVid=document.getElementsByTagName("video")[0] ;
myVid.onloadstart=alert("浏览器开始寻找指定的音频/视频");
```

​	⭐Event-2.durationchange:当指定音频/视频的时长数据发生变化时，发生 durationchange 事件。

​		当音频/视频加载后，时长将由 "NaN" 变为音频/视频的实际时长。

```
myVid.ondurationchange=alert("时长将由 NaN 变为音频/视频的实际时长");
```

​	⭐Event-3.loadedmetadata:当指定的音频/视频的元数据已加载时，会发生 loadedmetadata 事件。

​	音频/视频的元数据包括：时长、尺寸（仅视频）以及文本轨道。

```
myVid.onloadedmetadata=alert("元数据包括：时长、尺寸（仅视频）以及文本轨道");
```

​	⭐Event-4.loadeddata:当当前帧的数据已加载，但没有足够的数据来播放指定音频/视频的下一帧时，会发生 loadeddata 事件。

```
myVid.onloadeddata=alert("期待其他缓存进去，当权数据不够播放");
```

​	⭐Event-5. progress:当浏览器正在下载指定的音频/视频时，会发生 progress 事件。

```
myVid.onprogress=alert("正在下载视频");
```

​	⭐Event-6.canplay:当浏览器能够开始播放指定的音频/视频时，发生 canplay 事件。

```
myVid.oncanplay=alert("视频可以播放");
```

​	⭐Event-7.canplaythrough:当浏览器预计能够在不停下来进行缓冲的情况下持续播放指定的音频/视频时，会发生 canplaythrough 事件。

```js
myVid.oncanplaythrough=alert("预计能一直播放");
```

```js
// var myVid=document.getElementsByTagName("video")[0] ;
// myVid.onloadstart=alert("浏览器开始寻找指定的音频/视频");
// myVid.ondurationchange=alert("时长将由 NaN 变为音频/视频的实际时长");
// myVid.onloadedmetadata=alert("元数据包括：时长、尺寸（仅视频）以及文本轨道");
// myVid.onloadeddata=alert("期待其他缓存进去，当权数据不够播放");
// myVid.onprogress=alert("正在下载视频");
// myVid.oncanplay=alert("视频可以播放");
// myVid.oncanplaythrough=alert("预计能一直播放");
```

------

（以下内容待修改，原因：无法理解）

## 3.4音视频媒体元素

#### 3.4.1介绍：（我也看不懂）

HTMLMediaElement：html 媒体元素 继承自 HTML元素

​		HTMLVodeoElement-----> HTMLMediaElement  -----> HTMLElement

​		HTML媒体元素接口在属性和方法中添加了 [`HTML元素`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement)来支持基础的媒体相关的能力，就像audio和video一样。[`HTML 视频元素`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLVideoElement)和 [`HTML 音频元素`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLAudioElement)元素都继承自此接口

#### 3.4.2相关属性

​	[`HTMLMediaElement`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLMediaElement) 接口的 **`srcObject`** 属性设定或返回一个对象，这个对象提供了一个与[`HTMLMediaElement`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLMediaElement)关联的媒体源，这个对象通常是 [`MediaStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream) 。

## 3.5网页的媒体设备

​	MediaDevices.getUserMedia()会提示用户给予使用媒体输入的许可，媒体输入会产生一个[`MediaStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream)，里面包含了请求的媒体类型的轨道。此流可以包含一个视频轨道（来自硬件或者虚拟视频源，比如相机、视频采集设备和屏幕共享服务等等）、一个音频轨道（同样来自硬件或虚拟音频源，比如麦克风、A/D转换器等等），也可能是其它轨道类型。

​	它返回一个👀 [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 对象，成功后会`resolve`回调一个 [`MediaStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream) 对象。若用户拒绝了使用权限，或者需要的媒体源不可用，`promise`会`reject`回调一个 `PermissionDeniedError` 或者 `NotFoundError` 。	

具体内容:[MediaDevices.getUserMedia() - Web API 接口参考 | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaDevices/getUserMedia)

```js
navigator.mediaDevices.getUserMedia(constraints)
.then(function(stream) {
  /* 使用这个stream stream */
})
.catch(function(err) {
  /* 处理error */
});
```

语法：

​		var promise = navigator.mediaDevices.getUserMedia(constraints);

```js
// 想要获取一个最接近 1280x720 的相机分辨率
var constraints = { audio: true, video: { width: 1280, height: 720 } };

navigator.mediaDevices.getUserMedia(constraints)
.then(function(mediaStream) {
   //定位
   var video = document.querySelector('video');
   //改源
   video.srcObject = mediaStream;
   //加载事件，见2.3
   video.onloadedmetadata = function(e) {
      video.play();
  };
})
.catch(function(err) { console.log(err.name + ": " + err.message); }); // 总是在最后检查错误
```

