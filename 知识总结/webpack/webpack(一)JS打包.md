##  打包JS

### 检验规范支持
webpack支持es6, CommonJS, AMD
```javascript
// ES6
import sum from "./vendor/sum";

// CommonJs
var minus = require("./vendor/minus");

// AMD
require(["./vendor/multi"], function(multi) {
  console.log("multi(1, 2) = ", multi(1, 2));
});
```
### 编写配置文件
webpack.config.js是 webpack 默认的配置文件名
```javascript
const path = require("path");
module.exports = {
  entry: { // 入口文件
    app: "./app.js"
  },
  output: { // 出口文件
  // js引用路径或者CDN地址，输出解析文件的目录，指定资源文件引用的目录 ，打包后浏览器访问服务时的 url 路径中通用的一部分
    publicPath: __dirname + "/dist/", 
 // 所有输出文件的目标路径;打包后文件在硬盘中的存储位置
    path: path.resolve(__dirname, "dist"), 
    filename: "bundle.js"
  }
};
```
path和publicPath的区别
1. path是webpack所有文件的输出的路径，必须是绝对路径，比如：output输出的js,url-loader解析的图片，HtmlWebpackPlugin生成的html文件，都会存放在以path为基础的目录
2. publicPath 并不会对生成文件的路径造成影响，主要是对你的页面里面引入的资源的路径做对应的补全，常见的就是css文件里面引入的图片，在localhost（译者注：即本地开发模式）里的css文件中边你可能用“./test.png”这样的url来加载图片，但是在生产模式下“test.png”文件可能会定位到CDN上并且你的Node.js服务器可能是运行在HeroKu上边的。这就意味着在生产环境你必须手动更新所有文件里的url为CDN的路径，然而你也可以使用Webpack的“publicPath”选项和一些插件来在生产模式下编译输出文件时自动更新这些url。(配置url-loader来做更新)
```javascript
// 开发环境：Server和图片都是在localhost（域名）下
.image { 
  background-image: url('./test.png');
 }
// 生产环境：Server部署下HeroKu但是图片在CDN上
.image { 
  background-image: url('https://someCDN/test.png');
 }
```

### 结尾
打包后的js文件会按照我们的配置放在dist目录下，这时，需要创建一个html文件，引用打包好的js文件。

