### vue 入门（一）
***
主要内容
- Vue介绍
- MVVM模式介绍
- Vue常用系统指令
- Vue调试工具

#### Vue介绍
***
1. Vue 学习资源
Vue官网： https://cn.vuejs.org/
Vue GitHub：https://github.com/vuejs/vue
Vue2.0 在线文档：https://cn.vuejs.org/v2/guide/
Vue2下载地址：https://unpkg.com/vue@2.2.1/dist/vue.js
2. Vue框架
element：http://element-cn.eleme.io/#/zh-CN
iview: https://www.iviewui.com/
mint-ui: http://mint-ui.github.io/#!/zh-cn (移动端)

#### MVVM模式介绍
***
##### MVVM模式
- MVVM拆分解释为：
    - Model:负责数据存储
    - View:负责页面展示
    - View Model:负责业务逻辑处理（比如Ajax请求等），对数据进行加工后交给视图展示

- MVVM要解决的问题是将业务逻辑代码与视图代码进行完全分离，使各自的职责更加清晰，后期代码维护更加简单。

#### Vue常用系统指令
***
- 插值表达式{{}}：
数据绑定最常见的形式就是使用 “Mustache” 语法（双大括号）的文本插值        
例如：`<span>Message: {{ msg }}</span>`
Mustache 标签将会被替代为对应数据对象上 msg 属性（msg定义在data对象中）的值。
无论何时，绑定的数据对象上 msg 属性发生了改变，插值处的内容都会更新。
{{}}对JavaScript 表达式支持，例如：
```javscript
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
```
但是有个限制就是，每个绑定都只能包含单个表达式，如下表达式无效：
<!-- 这是语句，不是表达式 -->
`{{ var a = 1 }}`
<!-- 流控制也不会生效，请使用三元表达式 -->
`{{ if (ok) { return message } }}`

- v-text
v-text可以将一个变量的值渲染到指定的元素中,例如：
```html
<div v-text="msg"></div>
```
```javascript
  new Vue({
  	  data:{
   	      msg:'hello ivan'                                            
   	     }
   	});
```
输出结果：
`<div>hello ivan</div> `             

- v-html
双大括号和v-text会将数据解释为纯文本，而非 HTML 。
为了输出真正的 HTML ，你需要使用 v-html 指令：
例如：
```html
<div v-html="rawHtml"></div>
```
```javascript
new Vue({
	data:{
		 rawHtml:'<h1>hello ivan</h1>'
	}
})
```
被插入的内容都会被当做 HTML,但是对于没有HTML标签的数据绑定时作用同v-text和{{}}
注意：使用v-html渲染数据可能会非常危险，因为它很容易导致 XSS（跨站脚本） 攻击，使用的时候请谨慎，能够使用{{}}或者v-text实现的不要使用v-html

- v-cloak
   v-cloak指令保持在元素上直到关联实例结束编译后自动移除，v-cloak和 CSS 规则如 [v-cloak] { display: none } 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕。
   通常用来防止{{}}表达式闪烁问题
   例如：
   ```html
   <style>
   	[v-cloak] { display: none } 
   </style>
   ```
   <!-- 在span上加上 v-cloak和css样式控制以后，浏览器在加载的时候会先把span隐藏起来，知道 Vue实例化完毕以后，才会将v-cloak从span上移除，那么css就会失去作用而将span中的内容呈现给用户 -->
   ​	```html
   ​	<span v-cloak>{{msg}}</span>    
   ​	```
   ​	```javascript
   ​	new Vue({
   ​	    data:{
   ​	        msg:'hello ivan'
   ​	      }
   ​	})

   ```
   
   ```

- v-model以及双向数据绑定
	1. 在表单控件或者组件上创建双向绑定
	2. v-model仅能在如下元素中使用：
		input
		select
		textarea
		components（Vue中的组件）
		
	3. 举例：
	```html
	<input type="text" v-model="uname" />
	```
	```javacript
	new Vue({
		data:{
	//这个属性值和input元素的值相互一一对应，二者任何一个的改变都会联动的改变对方
			uname:'' 
		}
	})
	```
    4. 修饰符（了解）：
	    .lazy - 取代 input 监听 change 事件
	    .number - 自动将输入的字符串转为数字
	    .trim - 自动将输入的内容首尾空格去掉

- v-bind
1、作用：可以给html元素或者组件动态地绑定一个或多个特性，例如动态绑定style和class
2、举例：
   ```html
    <img v-bind:src="imageSrc">   
    <div v-bind:class="{ red: isRed }"></div>
    <div v-bind:class="[classA, classB]"></div>
    <div v-bind:class="[classA, { classB: isB, classC: isC }]">
    <div v-bind:style="{ fontSize: size + 'px' }"></div>
    <div v-bind:style="[styleObjectA, styleObjectB]"></div>
   ```
3、缩写形式
	```html
	<img :src="imageSrc">
    <div :class="{ red: isRed }"></div>
    <div :class="[classA, classB]"></div>
    <div :class="[classA, { classB: isB, classC: isC }]">
    <div :style="{ fontSize: size + 'px' }"></div>
    <div :style="[styleObjectA, styleObjectB]"></div>
	```
  
- v-for
	1、作用：通常是根据数组中的元素遍历指定模板内容生成内容
    2、用法举例：
    ```html
    <div v-for="item in items">
    	{{ item.text }}
    </div>
    ```
    ```javascript
    new Vuew({
    	data:{
    		items:[{text:'1'},{text:'2'}]                
    	}
    });
    ```
    3、可以为数组索引指定别名（或者用于对象的键）：
    ```html
    <div v-for="(item, index) in items"></div>
    <div v-for="(val, key) in user"></div>
    <div v-for="(val, key, index) in user"></div>  
    ```
    ```javascript
    new Vue({
    	data:{
    		items:[{text:'1'},{text:'2'}],
    		user:{uname:'ivan',age:32}
    	}
    });
    ```
    4、v-for 默认行为试着不改变整体(为了性能考虑的设计)，而是替换元素。迫使其重新排序的元素,在Vue2.0版本中需要提供一个 key 的特殊属性，在Vue1.0版本中需要提供一个 track-by="$index":
```html
 <div v-for="item in items" :key="item.id">
 	{{ item.text }}
 </div>
```

- v-if
1、作用：根据表达式的值的真假条件来决定是否渲染元素，如果条件为false不渲染（达到隐藏元素的目的），为true则渲染。在切换时元素及它的数据绑定被销毁并重建
2、示例：
```html
<!-- Handlebars 模板 -->
{{#if isShow}}
	<h1>Yes</h1>
{{/if}}
```
通常我们使用写法居多：
```html
<h1 v-if="isShow">Yes</h1>
```
也可以用 v-else 添加一个 “else” 块：
```html
<h1 v-if="isShow">Yes</h1>
<h1 v-else>No</h1>
```
注意：v-else 元素必须紧跟在 v-if 元素否则它不能被识别。
```javascript
new Vue({
	data:{
		isShow:true
	}
});
```

- v-show
1、根据表达式的真假值，切换元素的 display CSS 属性，如果为false，则在元素上添加 display:none来隐藏元素，否则移除display:none实现显示元素
2、示例：
```html
<h1 v-show="isShow">Yes</h1>
```
```javascript
 new Vue({
 	data:{
 		isShow:true
 	}
 });
```
     3、v-if和v-show的总结：
v-if和v-show 都能够实现对一个元素的隐藏和显示操作,但是v-if是将这个元素添加或者移除到dom中，而v-show是在这个元素上添加 style="display:none"和移除它来控制元素的显示和隐藏的

- v-on
1、作用：绑定事件监听，表达式可以是一个方法的名字或一个内联语句，如果没有修饰符也可以省略，用在普通的html元素上时，只能监听 原生 DOM 事件。用在自定义元素组件上时，也可以监听子组件触发的自定义事件。 
2、常用事件：
```javascript
v-on:click
v-on:keydown
v-on:keyup
v-on:mousedown
v-on:mouseover
v-on:submit
....
```
3、v-on提供了很多事件修饰符来辅助实现一些功能，例如阻止冒泡等
事件修饰符有如下：
.stop - 调用 event.stopPropagation()。
.prevent - 调用 event.preventDefault()。
.capture - 添加事件侦听器时使用 capture 模式。
.self - 只当事件是从侦听器绑定的元素本身触发时才触发回调。
.{keyCode | keyAlias} - 只当事件是从侦听器绑定的元素本身触发时才触发回调。
.native - 监听组件根元素的原生事件。

4、示例：

```html
<!-- 方法处理器 -->
<button v-on:click="doThis"></button>
<!-- 内联语句 -->
<button v-on:click="doThat('hello', $event)"></button>
<!-- 缩写 -->
<button @click="doThis"></button>
<!-- 停止冒泡 -->
<button @click.stop="doThis"></button>
<!-- 阻止默认行为 -->
<button @click.prevent="doThis"></button>
<!-- 阻止默认行为，没有表达式 -->
<form @submit.prevent></form>
<!--  串联修饰符 -->
<button @click.stop.prevent="doThis"></button>
```

5、v-on的缩写形式：可以使用@替代 v-on:
```html
<button @click="doThis"></button>
```

#### Vue在Chrome浏览器的调试工具Vue-Devtools
***
- 作用
> Vue-Devtools是Chrome浏览器的一个扩展，通过Vue-Devtools可以实现在Chrome浏览器的调试工具栏中查看到Vue开发页面的相关数据对象，方法，事件，状态信息，方便程序员监控和调试解决问题
- 地址
   - GitHub地址：https://github.com/vuejs/vue-devtools
   - Chrome插件地址：https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd?hl=zh-CN
- 通过Chrome插件地址安装插件(注意：这种方式需要翻墙)
  + 1、在Chrome浏览器中打开地址：https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd?hl=zh-CN
  + 2、点击里面的“+ 添加至CHROME” 按钮即可安装插件
  + 3、安装以后，在Chome浏览器中打开使用Vue开发的站点后按F12打开调试工具即可看到Vue调试工具

- 通过Vue-DevTools源码安装（需要先安装node.exe）
   + 1、https://nodejs.org/en/ 下载node.exe安装
   + 2、去https://github.com/vuejs/vue-devtools 下载到文件
   + 3、进入vue-devtools-master工程 先执行npm install再执行npm run build
   + 4、进入vue-devtools-master\shells\chrome文件夹中修改mainifest.json 中的persistant为true
   + 5、打开谷歌浏览器设置--->扩展程序-->勾选开发者模式--->加载已解压的扩展程序--->选择“vue-devtools-master\shells下的chrome”文件夹，至此恭喜已经安装成功！