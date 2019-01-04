### 概述
***
组件件的通信主要有以下几种：
* `Prop`（常用）
* `$emit` (组件封装用的较多)
* `.sync`语法糖 （较少）
* `$attrs` 和 `$listeners` (组件封装用的较多)
* `provide` 和 `inject` （高阶组件/组件库用的较多）
* 其他方式通信

详述
***
1. Prop
  通过 Prop 向子组件传递数据。
  Vue 的设计理念之单向数据流，父子组件之间的数据传递相当于自上而下的下水管子，只能从上往下流，不能逆流。
  ```html
   <div id="app">
     <child :content="message"></child>
   </div>
  ```
  ```javascript
  // Js
    let Child = Vue.extend({
      template: '<h2>{{ content }}</h2>',
      props: {
        content: {
          type: String,
          default: () => { return 'from child' }
        }
      }
    })
  
    new Vue({
      el: '#app',
      data: {
        message: 'from parent'
      },
      components: {
        Child
      }
    })
  ```

  2. $emit
  	 	触发当前实例上的事件,附加参数都会传给监听器回调，子组件向父组件传值
  ```html
  <div id="app">
    <my-button @greet="sayHi"></my-button>
  </div>
  ```
 ```javascript
   let MyButton = Vue.extend({
      template: '<button @click="triggerClick">click</button>',
      data () {
       return {
         greeting: 'vue.js!'
       }
     },
     methods: {
       triggerClick () {
         this.$emit('greet', this.greeting)
       }
     }
   })

    new Vue({
      el: '#app',
      components: {
        MyButton
      },
      methods: {
        sayHi (val) {
          alert('Hi, ' + val) // 'Hi, vue.js!'
        }
      }
    })
 ```

3. .sync 修饰符
 .sync 修饰符,只是作为一个编译时的语法糖存在。
 它会被扩展为一个自动更新父组件属性的 v-on 监听器。说白了就是让我们手动进行更新父组件中的值了，从而使数据改动来源更加的明显。

> 在有些情况下，我们可能需要对一个 prop 进行“双向绑定”。不幸的是，真正的双向绑定会带来维护上的问题，因为子组件可以修改父组件，且在父组件和子组件都没有明显的改动来源。

```html
<text-document
    v-bind:title="doc.title"
    v-on:update:title="doc.title = $event">
</text-document>
```
使用.sync语法糖
```html
<text-document v-bind:title.sync="doc.title"></text-document>
```
实现双向绑定的效果
```html
<div id="app">
  <login :name.sync="userName"></login> {{ userName }}
</div>
```
```javascript
let Login = Vue.extend({
  template: `
    <div class="input-group">
      <label>姓名:</label>
      <input v-model="text">
    </div>
  `,
  props: ['name'],
  data () {
    return {
      text: ''
    }
  },
  watch: {
    text (newVal) {
      this.$emit('update:name', newVal)
    }
  }
})

new Vue({
  el: '#app',
  data: {
    userName: ''
  },
  components: {
    Login
  }
})
```
官方语法是：update:myPropName 其中 myPropName 表示要更新的 prop 值。当然如果你不用 .sync 语法糖使用上面的 .$emit 也能达到同样的效果。

4. $attrs 和 $listeners
   $attrs 和 $listeners 属性像两个收纳箱，一个负责收纳属性，一个负责收纳事件，都是以对象的形式来保存数据。官方的解释是：
> 包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件——在创建高级别的组件时非常有用。

> 包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="$listeners" 传入内部组件——在创建更高层次的组件时非常有用。
```html
<div id="app">
  <child 
    :foo="foo" 
    :bar="bar"
    @one.native="triggerOne"
    @two="triggerTwo">
  </child>
</div>
```
这里有俩属性和俩方法，区别是属性一个是 prop 声明，事件一个是 .native 修饰器。
```javascript
let Child = Vue.extend({
  template: '<h2>{{ foo }}</h2>',
  props: ['foo'],
  created () {
    console.log(this.$attrs, this.$listeners)
    // -> {bar: "parent bar"}
    // -> {two: fn}
    
    // 这里我们访问父组件中的 `triggerTwo` 方法
    this.$listeners.two()
    // -> 'two'
  }
})

new Vue({
  el: '#app',
  data: {
    foo: 'parent foo',
    bar: 'parent bar'
  },
  components: {
    Child
  },
  methods: {
    triggerOne () {
      alert('one')
    },
    triggerTwo () {
      alert('two')
    }
  }
})
```
我们可以通过 $attrs 和 $listeners 进行数据传递，在需要的地方进行调用和处理，还是很方便的。当然，我们还可以通过 v-on="$listeners" 一级级的往下传递，子子孙孙无穷尽也！

当我们在组件上赋予了一个非Prop 声明时，编译之后的代码会把这些个属性都当成原始属性对待，添加到 html 原生标签上
```html
<h2 bar="parent bar">parent foo</h2>
```
给组件加上inheritAttrs 属性就能去掉了
```javascript
// 源码
let Child = Vue.extend({
  ...
  inheritAttrs: false, // 默认是 true
  ...
})
```

5. provide / inject
   官方对 provide / inject 的描述：
> provide 和 inject 主要为高阶插件/组件库提供用例。并不推荐直接用于应用程序代码中。并且这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。
```html
<div id="app">
  <son></son>
</div>
```
```javascript
let Son = Vue.extend({
  template: '<h2>son</h2>',
  inject: {
    house: {
      default: '没房'
    },
    car: {
      default: '没车'
    },
    money: {
      // 长大工作了虽然有点钱
      // 仅供生活费，需要向父母要
      default: '￥4500'
    }
  },
  created () {
    console.log(this.house, this.car, this.money)
    // -> '房子', '车子', '￥10000'
  }
})

new Vue({
  el: '#app',
  provide: {
    house: '房子',
    car: '车子',
    money: '￥10000'
  },
  components: {
    Son
  }
})
```

6. 其他通信方式
   - EventBus
   声明一个全局Vue实例变量 EventBus , 把所有的通信数据，事件监听都存储到这个变量上。
```html
<div id="app">
  <child></child>
</div>
```
```javascript
   // 全局变量
let EventBus = new Vue()

// 子组件
let Child = Vue.extend({
  template: '<h2>child</h2>',
  created () {
    console.log(EventBus.message)
    // -> 'hello'
    EventBus.$emit('received', 'from child')
  }
})

new Vue({
  el: '#app',
  components: {
    Child
  },
  created () {
    // 变量保存
    EventBus.message = 'hello'
    // 事件监听
    EventBus.$on('received', function (val) {
      console.log('received: '+ val)
      // -> 'received: from child'
    })
  }
})
```

- vuex
- $parent
  父实例，如果当前实例有的话。通过访问父实例也能进行数据之间的交互，但极小情况下会直接修改父组件中的数据。
- $root
  当前组件树的根 Vue 实例。如果当前实例没有父实例，此实例将会是其自己。通过访问根组件也能进行数据之间的交互，但极小情况下会直接修改父组件中的数据。