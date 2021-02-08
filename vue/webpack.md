# 目录

[TOC]

## webpack

### 全局安装webpack

```shell
#全局安装webpack 默认安装最新版 @webpack版本号 安装指定版本的wenpack
npm install webpack -g
```

### 局部安装webpack

```shell
#--save-dev 开发时依赖
npm install webpack@3.6.0 --save-dev
```

### 初始化package.json

```shell
npm init
```

### 指定mode 

```shell
module.exports = {
    mode: 'development' #设置mode
}
```

### 打包

```shell
webpack ./src/index.js ./dist/bundle.js
```

简化打包

```shell
const path = require('path');
module.exports = {
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js'
    },
    mode: 'development' #  设置mode
}
```

```shell
#打包
webpack
```

### webpack处理css文件

#### 安装loader

```shell
npm install --save-dev css-loader
npm install --save-dev style-loader
```

#### 配置loader

```javascript
const path = require('path');
module.exports = {
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js'
    },
    mode: 'development',
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {loader: 'style-loader'},
                    {loader: 'css-loader'}
                ]
            },]

    }
}
```

#### 引入css文件依赖

```javascript
//index.js
require('./css/index.css')
```