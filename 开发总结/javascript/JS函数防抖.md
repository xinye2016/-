## javascript函数防抖Debounce

#### 一、前提
```javascript
window.onresize = () => {  
    console.log('触发窗口监听回调函数')
}
```
​      当我们在PC上缩放浏览器窗口时，一秒可以轻松触发30次事件。手机端触发其他Dom时间监听回调时同理。这里的回调函数只是打印字符串，如果回调函数更加复杂，可想而知浏览器的压力会非常大，用户体验会很糟糕。resize或scroll等Dom事件的监听回调会被频繁触发，因此我们要对其进行限制。
​      函数去抖简单来说就是对于一定时间段的连续的函数调用，只让其执行一次，初步的实现思路如下：
第一次调用函数，创建一个定时器，在指定的时间间隔之后运行代码。当第二次调用该函数时，它会清除前一次的定时器并设置另一个。如果前一个定时器已经执行过了，这个操作就没有任何意义。然而，如果前一个定时器尚未执行，其实就是将其替换为一个新的定时器。目的是只有在执行函数的请求停止了一段时间之后才执行。
应用场景： 1. 每次 resize/scroll 触发统计事件；2. 文本输入的验证（连续输入文字后发送 AJAX 请求进行验证，验证一次就好）

#### 二、实现
1. 基础版本
```javascript
function debounce(method, context) {  
	clearTimeout(method.tId)  
	method.tId = setTimeout(
		() => {    
            method.call(context)  
        }, 1000)
}
function print() { 
	console.log('Hello World')
}
window.onresize = debounce(print)
```
2. 优化： 将定时器隔离，定义间隔时间
```javascript
function debounce(method, wait, context) {  
	let timeout  
	return function() {    
		if (timeout) {      
			clearTimeout(timeout)    
		}    
		timeout = setTimeout(
			() => {      
				method.call(context)    
			}, wait)  
	}
}
```
3. 优化： 自动调整this正确指向，不需要手动传入上下文context
```javascript
function debounce(method, wait) {  
	let timeout  
	return function() {    
		// 将method执行时this的指向设为debounce返回的函数被调用时的this指向
		let context = this    
		if (timeout) {      
			clearTimeout(timeout)    
		}    
		timeout = setTimeout(
			() => {      
				method.call(context)    
			}, wait)  
	}
}
```
4. 优化：函数可传入参数
```javascript
function debounce(method, wait) {  
	let timeout  
	// args为返回函数调用时传入的参数，传给method
	return function(...args) {    
		let context = this    
		if (timeout) {      
			clearTimeout(timeout)    
		}    
		timeout = setTimeout(() => {      
			// args是一个数组，所以使用fn.apply      
			// 也可写作method.call(context, ...args)
			method.apply(context, args)    
		}, wait)  
	}
}
```
5. 优化： 提供立即执行功能
```javascript
function debounce(method, wait, immediate) {  
	let timeout  
	return function(...args) {    
	let context = this    
	if (timeout) {      
		clearTimeout(timeout)    
	}    
	// 立即执行需要两个条件，一是immediate为true，二是timeout未被赋值或被置为null
	if (immediate) {      
       // 如果定时器不存在，则立即执行，并设置一个定时器，wait毫秒后将定时器置为null       
       // 这样确保立即执行后wait毫秒内不会被再次触发
        let callNow = !timeout      
        timeout = setTimeout(() => {        
            timeout = null      
        }, wait)      
        if (callNow) {        
            method.apply(context, args)      
        }    
     } else {      
     	// 如果immediate为false，则函数wait毫秒后执行
     	timeout = setTimeout(() => {        
     	// args是一个类数组对象，所以使用fn.apply        
     	// 也可写作method.call(context, ...args)
     		method.apply(context, args)      
     	}, wait)    
     }  
  }
}
```
6. 优化：提供取消功能
```javascript
function debounce(method, wait, immediate) {  
	let timeout  // 将返回的匿名函数赋值给debounced，以便在其上添加取消方法
	let debounced = function(...args) {
		let context = this    
		if (timeout) {
			clearTimeout(timeout)
		}    
		if (immediate) {
			let callNow = !timeout     
            timeout = setTimeout(() => {        
            	timeout = null      
            }, wait)      
            if (callNow) {        
            	method.apply(context, args)      
            }    
        } else {      
        	timeout = setTimeout(() => {        
        		method.apply(context, args)      
        	}, wait)    
        }  
    }  
    // 加入取消功能，使用方法如下  
    // let myFn = debounce(otherFn)  
    // myFn.cancel()  
    debounced.cancel = function() {    
    	clearTimeout(timeout)    
    	timeout = null  
    }
}
```
[^补充]. 需要防抖的函数如果存在返回值可以使用回调函数处理函数返回值，或者使用promise