### vue核心之render函数

[vue render函数](https://cn.vuejs.org/v2/api/#render)

* 类型：(createElement: () => VNode) => VNode
* 详情:
Vue 选项中的 render 函数若存在，则 Vue 构造函数不会从 template 选项或通过 el 选项指定的挂载元素中提取出的 HTML 模板编译渲染函数。
组件中的 template 会被编译成 render function。
createElement常简写为h，是 vue 虚拟 DOM 的概念，创建出来的并不是 html 节点，而是 VNode 的一个类，类似 DOM 结构的一个结构，并存在内存中，它会和真正的 DOM 进行对比，若发现需要更新的 DOM，才会去转换这部分 DOM 内容，并填到真正的 DOM 中，从而提高性能。

#### createElement 可使用的指令
1. 添加子元素和attrs
   ```javascript
   render (createElement) {
       return createElement(
         'comp-one', // 父元素名
         {
           props: { // props 传值
             props1: this.value
           }
           attr: { // 设置ID
               id: 'app'
           }
         },
         [ // 子元素数组
           createElement('span', {
             ref: 'span'
           }, this.value)
         ])
   }
   ```
2. ref和class、其他顶层属性
   ``` javascript
   render (createElement) {
       return createElement(
         'comp-one',
         {
           ref: 'comp',
           key: 'myKey',
           class: { // 和v-bind:class一样
             foo: true
           }
         }
       ）
     }
   ```
3. 样式和自定义指令

   ```javascript
   render (createElement) {
       return createElement(
         'comp-one', // 父元素名
         {
           style: {
               color: red,
               fontSize: '12px'
           },
           directives: [
               {
                 name: 'my-custom-directive',
                 value: '2',
                 expression: '1 + 1',
                 arg: 'foo',
                 modifiers: {
                   bar: true
                 }
               }
             ]
         }
   }
   ```
   4.v-model
   ```javascript
        Vue.component('el-input', {
            render: function(createElement) {
                var self = this;
                return createElement('input', {
                    domProps: {
                        value: self.name
                    },
                    on: {
                        input: function(event) {
                            self.$emit('input', event.target.value);
                        }
                    }
                })
            },
            props: {
                name: String
            }
    和下面的写法作用一样
    <el-input :name="name" @input="val=>name=val"></el-input>
   ```
5. 绑定事件
	* on
	```javascript
	render (createElement) {
      return createElement(
        'comp-one',
        {
          ref: 'comp',
          on: { // 事件监听(一)，这里是组件上监听，需要 $emit
            click: this.handleClick
          },
        },
        [
          createElement('span', {
            ref: 'span'
          }, this.value)
        ])
  	  }
	```
	* nativeOn
	```javascript
	render (createElement) {
      return createElement(
        'comp-one',
        {
          ref: 'comp',
          nativeOn: { // nativeOn 也是绑定到组件上，但是不需要组件发 $emit会自动绑定到这个组件的根节点的原生 DOM 上，如果本身就是原生 DOM，直接绑定。
            click: this.handleClick
          },
        },
        [
          createElement('span', {
            ref: 'span'
          }, this.value)
        ])
  	  }
	```
6. slot
	```javascript
	// 组件中
    render (createElement) {
        return createElement('div', {
          style: this.style,
          on: {
            click: () => { this.$emit('click') }
          }
        }, [
          this.$slots.header, // 通过 $slot拿到 header，如果没命名就是 default
          this.props1
        ])
      }
      // new Vue（）
      [
        createElement('span', {
          ref: 'span',
          slot: 'header' // 指定具名插槽
        }, this.value)
      ]
	```
	* 作用域插槽
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>render</title>
        <script src="https://cdn.bootcss.com/vue/2.3.4/vue.js"></script>
    </head>
    <body>
        <div id="app">
            <ele>
                <template scope="props">
                    <span>{{props.text}}</span>
                </template>
            </ele>
        </div>
        <script>
            Vue.component('ele', {
                render: function(createElement) {
                    // 相当于<div><slot :text="msg"></slot></div>
                    return createElement('div', [
                        this.$scopedSlots.default({
                            text: this.msg
                        })
                    ]);
                },
                data: function() {
                    return {
                        msg: '来自子组件'
                    }
                }
            });
            new Vue({
                el: '#app'
            });
        </script>
    </body>
    </html>
    ```
    * 向子组件中传递作用域插槽
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>render</title>
        <script src="https://cdn.bootcss.com/vue/2.3.4/vue.js"></script>
    </head>
    <body>
        <div id="app">
            <ele></ele>
        </div>
        <script>
            Vue.component('ele', {
                render: function(createElement) {
                    return createElement('div', [
                        createElement('child', {
                            scopedSlots: {
                                default: function(props) {
                                    return [
                                        createElement('span', '来自父组件'),
                                        createElement('span', props.text)
                                    ];
                                }
                            }
                        })
                    ]);
                }
            });
            Vue.component('child', {
                render: function(createElement) {
                    return createElement('b', this.$scopedSlots.default({text: '我是组件'}));
                }
            });
            new Vue({
                el: '#app'
            });
        </script>
    </body>
    </html>
    ```
7. domProps
  类似原生 DOM 操作，把 span 覆盖掉了。

  ```javascript
  [
        createElement('span', {
          ref: 'span',
          slot: 'header',
          domProps: {
            innerHTML: '<span>345</span>'
          }
        }, this.value)
    ]
  ```