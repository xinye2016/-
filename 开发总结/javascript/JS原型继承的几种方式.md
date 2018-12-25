### JS原型继承的几种方式

首先创建一个父类

```javascript
function Person (name) {
    this.name = name || '名字'
}
Person.prototype.run = function () {
    console.log('跑步')
}
```

#### 1.原型链继承

```javascript
function Student() {}

Student.prototype = new Person ()
Student.prototype.study = function () {
  console.log('学习')
}

let jim = new Student()
jim.study() // 学习
jim.run() // 跑步

jim instanceof Person // true
jim instanceof Student // true

Object.getPrototypeOf(jim) // Person {name: 名字, study: ƒ}
jim.__proto__ // Person {name: 名字, study: ƒ}

```

缺点：

1. 无法向父类构造函数传参

2. 父类的所有属性被共享，只要一个实例修改了属性，其他所有的子类实例都会被影响

#### 原型式继承
```javascript
function create(o) {
  function F() {}
  F.prototype = o
  return new F()
}

let obj = {
  gift: ['a', 'b']
}

let jon = create(obj)
let xiaoming = create(obj)

jon.gift.push('c')
xiaoming.gift // ['a', 'b', 'c']
```
缺点：
共享了属性和方法