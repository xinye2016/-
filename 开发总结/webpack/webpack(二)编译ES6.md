## 编译ES6

### babel

#### babel相关

1. `babel-loader`: 负责es6语法转化

2. `babel-preset-env`: 包含es6、7等版本的语法转化规则

3. `babel-polyfill`: es6内置方法和函数转化垫片

4. `babel-plugin-transform-runtime`: 避免polyfill污染全局变量

[^注意]: babel-loader和babel-polyfill。前者负责语法转化，比如：箭头函数；后者负责内置方法和函数，比如：new Set()。
5. 相关库
```javascript
{
  "devDependencies": {
    "babel-core": "^6.26.3",
    "babel-loader": "^7.1.5",
    "babel-plugin-transform-runtime": "^6.23.0",
    "babel-preset-env": "^1.7.0",
    "webpack": "^4.15.1"
  },
  "dependencies": {
    "babel-polyfill": "^6.26.0",
    "babel-runtime": "^6.26.0"
  }
}
```
#### webpack中使用babel
babel的相关配置，推荐单独写在.babelrc文件中
相关配置
```javascript
{
  "presets": [
    [
      "env",
      {
        "targets": {
          "browsers": ["last 2 versions"]
        }
      }
    ]
  ],
  "plugins": ["transform-runtime"]
}
```
在webpack配置文件中，关于babel的调用需要写在module模块中。对于相关的匹配规则，除了匹配js结尾的文件，还应该去除node_module/文件夹下的第三库的文件（发布前已经被处理好了）。
```javascript
module.exports = {
  entry: {
    app: "./app.js"
  },
  output: {
    filename: "bundle.js"
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /(node_modules)/,
        use: {
          loader: "babel-loader" 
        }
      }
    ]
  }
};
```
#### babel-polyfill
babel-polyfill。它需要在我们项目的入口文件中被引入，或者在webpack.config.js中配置。
在入口文件中引入
```javascript
import "babel-polyfill";
let func = () => {};
const NUM = 45;
let arr = [1, 2, 4];
let arrB = arr.map(item => item * 2);

console.log(arrB.includes(8));
console.log("new Set(arrB) is ", new Set(arrB));
```
命令行中进行打包，然后编写html文件引用打包后的文件即可在不支持es6规范的老浏览器中看到效果了。

