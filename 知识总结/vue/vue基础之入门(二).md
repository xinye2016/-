### Vue基础入门(二)
***
主要内容
- v-on按键修饰符
- Vue自定义指令
- Vue过滤器

#### v-on按键修饰符
***
##### 作用说明

> 文档地址：http://cn.vuejs.org/v2/guide/events.html#按键修饰符

	在监听键盘事件时，我们经常需要监测常见的键值。 Vue 允许为 v-on 在监听键盘事件时添加按键修饰符：
	.enter
	.tab
	.delete (捕获 “删除” 和 “退格” 键)
	.esc
	.space
	.up
	.down
	.left
	.right

##### 可以自定义按键别名

	在Vue2.0 中默认的按键修饰符是存储在 Vue.config.keyCodes中
	// 例如在Vue2.0版本中扩展一个f1的按键修饰符写法：
	Vue.config.keyCodes.f1 = 112
	
	在1.0.17+ 中默认的按键修饰符是存储在Vue.directive('on').keyCodes中                                         
	
	// 例如在Vue1.0中扩展一个f1的按键修饰符写法：
	Vue.directive('on').keyCodes.f1 = 112

#### 自定义指令
***
> 当Vue提供的系统指令不能满足需求时，就需要自己定义指令来进行扩展，例如，定义一个v-focus指令来实现文本框的自动获取焦点功能

##### 自定义属性指令

- 写法格式

   定义指令：
  ```javascript
    Vue.directive('指令ID，不需要增加v-前缀',function(){
   		//实现指令的业务
   		this.el //代表使用这个指令的元素对象
   	});
  ```

   	使用指令(当做一个元素的属性使用)：
   	`<input type="text" v-指令ID />`

- （属性指令应用举例）利用自定义属性指令实现自动获取焦点功能

   定义指令：
   ```javascript
   //定义一个 v-focus的属性自定义指令
   	Vue.directive('focus',function(){
   		this.el.focus(); //实现文本框的自动获取焦点
   	});
   ```
   使用指令：
   `<input type="text" v-focus />`
##### 自定义元素指令

- 写法格式

	定义指令：
	```javascript
    Vue.elementDirective('指令id',{
       bind:function(){
       //实现指令的业务
       this.el //代表使用这个指令的元素对象
      }
    });
  ```

	使用指令：
	  <指令id></指令id> 

- （元素指令应用举例）利用自定义属性指令实现日期格式化

	定义指令：
	```javascript
    Vue.elementDirective('datefmt',{
        bind:function(){
          var v=this.el.attributes[0].value;	
  	      var date = new Date(this.vm[v]); 
          var year = date.getFullYear();
          var m = date.getMonth() + 1;
          var d = date.getDate();
          //输出： yyyy-mm-dd
          var fmtStr = year+'-'+m +'-'+d;
        
          this.el.innerText = fmtStr;
        }
    });    
	  new Vue({
        el:'#app',
        data:{
         time:new Date()
       }    	
	});
  ```
	
	使用指令：
```html
    <div id="app">
      <datefmt :dt="time"></datefmt>
    </div>
```

#### 过滤器
***
> Vue提供了一系列的固定逻辑来使程序员更加容易的实现这些功能，这些过滤器称之为系统过滤器，Vue也提供了一个接口用来供程序员定义属于自己的特殊逻辑，Vue称之为自定义过滤器

##### 系统过滤器

- 关于系统过滤器的使用参考请参考文档：http://v1-cn.vuejs.org/api/#过滤器
- 注意：系统过滤器是Vue1.0中存在的，在Vue2.0中已经删除了

##### 自定义过滤器

- 文档地址：http://v1-cn.vuejs.org/guide/custom-filter.html

##### 自定义私有过滤器

- 定义方式

   可以在 new Vue({filters：{}})中的filters中注册一个私有过滤器
   ​	
   ​	定义格式：
   ```javascript
    new Vue({
   		el:'#app',	
   		filters:{		
   		   '过滤器名称':function(管道符号|左边参数的值,参数1,参数2,....) {
   	     return 对管道符号|左边参数的值做处理以后的值
   		   })    
   		}
   	});
   ```
   用法

   `<span>{{ msg | 过滤器id('参数1' '参数2' ....) }}</span>`

- (应用示例)自定义全局过滤器实现日期格式化

	1、 定义全局的日期格式化过滤器：
	```javascript
    new Vue({
      el:'#app',
      data:{
        time:new Date()
      },
      filters:{
        //定义在 VM中的filters对象中的所有过滤器都是私有过滤器
        datefmt:function(input,splicchar){
          var date = new Date(input); 
              var year = date.getFullYear();
              var m = date.getMonth() + 1;
              var d = date.getDate();        	
              var fmtStr = year+splicchar+m +splicchar+d;
              return fmtStr; //返回输出结果
        }
      }
     });
  ```
	
	2、使用
	```html
    <div id="app">
      {{ time | datefmt('-') }} //Vue2.0传参写法
    </div>
  ```

##### 自定义全局过滤器

- 定义方式

   可以用全局方法 Vue.filter() 注册一个全局自定义过滤器，它接收两个参数：过滤器 ID 和过滤器函数。过滤器函数以值为参数，返回转换后的值	
   定义格式：
```javascript
  Vue.filter('过滤器名称', function (管道符号|左边参数的值,其他参数1,其他参数2,....) {
   	 return 对管道符号|左边参数的值做处理以后的值
   })
```
  使用:
   	```html
    <span>{{ msg | 过滤器名称('参数1' '参数2' ....}}</span>
     ```

- (应用示例)自定义全局过滤器实现日期格式化

	1、 定义全局的日期格式化过滤器：	
  ```javascript
   	Vue.filter('datefmt',function(input,splicchar){
   	   var date = new Date(input); 
   	   var year = date.getFullYear();
   	   var m = date.getMonth() + 1;
   	   var d = date.getDate();        	
   	   var fmtStr = year+splicchar+m +splicchar+d;
   	   return fmtStr; //返回输出结果
    });    
  ```
	
	2、使用
	```html
    <div id="app">
      {{ time | datefmt('-') }}
    </div>
	<script>  
      new Vue({
        el:'#app1',
        data:{
        time:new Date()
       }
     });
    </script>
  ```