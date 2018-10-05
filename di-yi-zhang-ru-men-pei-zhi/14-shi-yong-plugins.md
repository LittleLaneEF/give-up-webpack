在开始这一小节的介绍之前，我们先来分析一下，[前一小节 接触 loader](/di-yi-zhang-ru-men-pei-zhi/1-1-huan-jing-an-zhuang.md) 项目里面由 `Webpack` 编译后最终生成的代码！

拷贝 `Hello-Loader` 项目的代码，生成 `Hello-Plugin` 项目，并在 `Hello-Plugin` 项目根目录下执行 `webpack` 命令进行源代码打包。等打包结束后，会在 `dist` 目录输出最终的代码文件 `main.js`。

当然此时打开 `main.js` 里面的源代码是不易查看的，代码做了压缩处理（这里我就不粘出来了）。原因我们之前也有提及：我们在执行 `webpack` 打包时，没有指定编译的环境，`Webpack` 就默认为 `production`。

`production` 是一个什么概念呢？它指的是投产环境的编译模式。在投产环境中，我们希望源代码被丑化、压缩以减少 `HTTP` 请求，所以这里也会做这些事情。

与之对应的就是 `development`。`development` 是指开发环境的编译模式，为了实现开发环境打包快的目标，丑化、压缩代码的步骤都不会发生。

那我们就在 `webpack.config.js` 里面加上 `mode: 'development'`。

```javascript
const path = require('path');

module.exports = {
  entry: './src',
  output: {
    path: path.resolve(__dirname, './dist'),
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        
        // 注意这里 loader 配置的顺序，样式文件依次从右到左经过 use 配置 loader 的处理
        // css-loader 是解析样式文件
        // style-loader 是将解析后的样式渲染在页面上
        use: ['style-loader', 'css-loader'],
      }
    ]
  },
  plugins: [],
  devServer: {},
  
  // 环境编译模式
  mode: 'development',
}
```

再执行 `webpack` 命令，就会生成比较正常的代码！这里我粘出核心的代码。

```javascript
/* ./src/index.js */
(function(module, __webpack_exports__, __webpack_require__) {
    "use strict";
    eval("__webpack_require__.r(__webpack_exports__);\n/* harmony import */ var _style_index_css__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__(/*! ./style/index.css */ \"./src/style/index.css\");\n/* harmony import */ var _style_index_css__WEBPACK_IMPORTED_MODULE_0___default = /*#__PURE__*/__webpack_require__.n(_style_index_css__WEBPACK_IMPORTED_MODULE_0__);\n\n\ndocument.querySelector('#app').innerHTML = 'Hello Loader!'; \n\n//# sourceURL=webpack:///./src/index.js?");
}),

/* ./src/style/index.css */
(function(module, exports, __webpack_require__) {
    eval("\nvar content = __webpack_require__(/*! !../../node_modules/css-loader!./index.css */ \"./node_modules/css-loader/index.js!./src/style/index.css\");\n\nif(typeof content === 'string') content = [[module.i, content, '']];\n\nvar transform;\nvar insertInto;\n\n\n\nvar options = {\"hmr\":true}\n\noptions.transform = transform\noptions.insertInto = undefined;\n\nvar update = __webpack_require__(/*! ../../node_modules/style-loader/lib/addStyles.js */ \"./node_modules/style-loader/lib/addStyles.js\")(content, options);\n\nif(content.locals) module.exports = content.locals;\n\nif(false) {}\n\n//# sourceURL=webpack:///./src/style/index.css?");
})
```

目标代码层次很分明。粘出的代码中，前半部分是关于 `/src/index.js` 的处理，后半部分是关于 `/src/style/index.css` 样式文件的处理。也就是说 `JavaScript` 代码和样式文件打在了一个文件里面。

有人说：我想把样式文件和 `JavaScript` 文件分开打包，将所有样式文件抽取到单独的一个文件里面，那怎么办呢？

这就是我们这一小节的任务了。

### 配置参数

我们这里是使用 [ExtractTextWebpackPlugin](https://www.webpackjs.com/plugins/extract-text-webpack-plugin/) 插件来提取样式文件的。

#### 安装插件

这个插件的安装，我要着重强调一下，我自己都差点搞错了！

```bash
# https://github.com/webpack-contrib/extract-text-webpack-plugin/issues/701
# 注意：安装的时候按版本对应，否则打包会报错哟

# 对于 webpack 4
npm install --save-dev extract-text-webpack-plugin@next

# 对于 webpack 3
npm install --save-dev extract-text-webpack-plugin

# 对于 webpack 2
npm install --save-dev extract-text-webpack-plugin@2.1.2

# 对于 webpack 1
npm install --save-dev extract-text-webpack-plugin@1.0.1
```

这里大家一定要注意对应版本，否则打包会报错。

插件是需要单独引入的，而 loader 不需要。可以说 loader 是解析到指定文件的时候就去加载对应的 loader 的，而插件是事先引入，然后配置，最后才用的。

修改 webpack.config.js 里面配置为：

```javascript
const path = require('path');

// 引入 extract-text-webpack-plugin
const ExtractTextWebpackPlugin = require('extract-text-webpack-plugin');

module.exports = {
  entry: './src',
  output: {
    path: path.resolve(__dirname, './dist'),
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        
        // 注意这里 loader 配置的顺序，样式文件依次从右到左经过 use 配置 loader 的处理
        // style-loader 是将解析后的样式渲染在页面上
        // css-loader 是解析样式文件
        use: ExtractTextWebpackPlugin.extract({
          fallback: 'style-loader',
          use: ['css-loader']
        }),
      }
    ]
  },
  plugins: [
    // 配置插件，参数是最终生成的样式文件名称 index.css
    new ExtractTextWebpackPlugin('index.css'),
  ],
  mode: 'development',
}
```

### build

在控制台运行 webpack 。

![](/assets/hello-plugin.png)

我们看到控制台输出了两个文件：main.js 和 index.css，入口文件（Entrypoint）也变成了两个 main.js 和 index.css，对一个的 dist 目录也同样有了两个文件：index.css 和 main.js。

### 引入样式

在 index.html 引入 dist/index.css，发现样式在页面上起作用了。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Hello Plugin</title>
  <link href="./dist/index.css" rel="stylesheet"/>
</head>
<body>
  <div id="app"></div>
  <script src="./dist/main.js"></script>
</body>
</html>
```

### 总结

到这里为止，我们已经看到了 Webpack 的强大和高可配置性。按照每个插件、loader 提供的配置，配置一下即可使用，岂不美哉！当然这还只是我们接触 Webpack 的九牛一毛，对于后面的内容也会越来越复杂，希望能够坚持下去！大家也一样，动手多实践，多修改配置，跑跑试试看，不准会获得意外的惊喜哟！大家加油！

下一节：[环境区分](/di-yi-zhang-ru-men-pei-zhi/15-huan-jing-qu-fen.md)
