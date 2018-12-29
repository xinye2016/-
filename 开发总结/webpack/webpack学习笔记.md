# webpack 入门
学习webpack4.0基于官网的文档做的整理
## 安装
### 本地安装
1. 安装最新版本或特定版本
```
npm install --save-dev webpack
npm install --save-dev webpack@<version>
```
2. 使用 webpack v4+ 版本，你还需要安装 CLI
```
npm install --save-dev webpack-cli
npm install --save-dev webpack-command
```

[^补充]: 大部分项目推荐本地安装，可以使我们在引入破坏式变更(breaking change)的依赖时，更容易分别升级项目，webpack 通过运行一个或多个npm scripts

```
"scripts": {
    "start": "webpack --config webpack.config.js"
}
```

### 全局安装

```
npm install --global webpack
```
[^补充]: 不推荐全局安装 webpack。这会将你项目中的 webpack 锁定到指定版本，并且在使用不同的 webpack 版本的项目中，可能会导致构建失败。

## 起步

创建目录，初始化npm，本地安装webpack，接着安装webpack-cli。

```
mkdir webpack-demo && cd webpack-demo
npm init -y
npm install webpack webpack-cli --save-dev
```



