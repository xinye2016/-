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

#### 借用构造函数（经典继承）

```javascript
function Programmer(name) {
  Person.call(this)
  this.name = name
}
let jon = new Programmer('jon')
jon.name // jon
jon.age // 18

jon.sleep() // Uncaught TypeError: jon.sleep is not a function
jon instanceof Person // false
jon instanceof Programmer // true
```
优点：
1. 可以为父类传参
2. 避免了共享属性
缺点：
1. 只是子类的实例，不是父类的实例
2. 方法都在构造函数中定义，每次创建实例都会创建一遍方法

#### 寄生式继承

```javascript
function createObj (o) {
  var clone = Object.create(o)
  clone.sayName = function () {
    console.log('hi')
  }
  return clone
}
```
缺点：跟借用构造函数模式一样，每次创建对象都会创建一遍方法

#### 组合继承

```javascript
function Programmer(age, name) {
  Person.call(this, age)
  this.name = name
}

Programmer.prototype = new Person()
Programmer.prototype.constructor = Programmer // 修复构造函数指向

let jon = new Programmer(18, 'jon')
jon.age // 18
jon.name // jon

let flash = new Programmer(22, 'flash')
flash.age // 22
flash.name // flash

jon.age // 18

jon instanceof Person // true
jon instanceof Programmer // true
flash instanceof Person // true
flash instanceof Programmer // true
```
优点：融合原型链继承和构造函数的优点，是 JavaScript 中最常用的继承模式
缺点：调用了两次父类构造函数

#### 寄生组合继承
子类构造函数复制父类的自身属性和方法，子类原型只接受父类的原型属性和方法
```javascript
function create(prototype) {
  function Super() {}
  Super.prototype = prototype
  return new Super()
}

function Programmer(age, name) {
  Person.call(this, age)
  this.name = name
}

Programmer.prototype = create(Person.prototype)
Programmer.prototype.constructor = Programmer // 修复构造函数指向

let jon = new Programmer(18, 'jon')
jon.name // jon
```
进阶封装
```javascript

function create(prototype) {
  function Super() {}
  Super.prototype = prototype
  return new Super()
}

function prototype(child, parent) {
  let prototype = create(parent.prototype)
  prototype.constructor = child // 修复构造函数指向
  child.prototype = prototype
}

function Person (age) {
  this.age = age || 18
}
Person.prototype.sleep = function () {
  console.log('sleeping')
}

function Programmer(age, name) {
  Person.call(this, age)
  this.name = name
}

prototype(Programmer, Person)

let jon = new Programmer(18, 'jon')
jon.name // jon
```
> 这种方式的高效率体现它只调用了一次 Parent 构造函数，并且因此避免了在 Parent.prototype 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用 instanceof 和 isPrototypeOf。开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。

#### ES6 extends（最佳）
```javascript
// 父类
class Person {
  constructor(age) {
    this.age = age
  }
  sleep () {
    console.log('sleeping')
  }
}

// 子类
class Programmer extends Person {
  constructor(age, name) {
    super(age)
    this.name = name
  }
  code () {
    console.log('coding')
  }
}

let jon = new Programmer(18, 'jon')
jon.name // jon
jon.age // 18

let flash = new Programmer(22, 'flash')
flash.age // 22
flash.name // flash

jon instanceof Person // true
jon instanceof Programmer // true
flash instanceof Person // true
flash instanceof Programmer // true
```
优点：不用手动设置原型。
缺点：新语法，只要部分浏览器支持，需要转为 ES5 代码。