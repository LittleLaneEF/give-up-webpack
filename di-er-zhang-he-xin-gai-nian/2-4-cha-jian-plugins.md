## 插件（Plugin）

`loader` 虽然可以对各种各样语言扩展工具进行转换，但是也有它不能解决的问题，特别是打包优化方面，比如：提取样式文件到独立分离的 `CSS` 文件以减少 `HTTP` 请求 「_[ExtractTextWebpackPlugin](https://github.com/webpack-contrib/extract-text-webpack-plugin)_」；创建一个在编译时可以配置的全局常量，常用于区分开发构建和发布构建，并对不同的构建模式进行不同的处理「_[DefinePlugin](https://webpack.docschina.org/plugins/define-plugin/)_」；「_[HtmlWebpackPlugin](https://github.com/jantimon/html-webpack-plugin)_」插件可以简化 `HTML` 文件的创建，特别是当有输出的文件名中包含每次会随着编译而发生变化哈希的 `webpack bundle` 时。

`plugins` 选项用于以各种方式自定义 `Webpack` 构建过程。`plugins` 基本上可以分为两大类：一类是 `Webpack` 内置插件，比如我们之前提到的「_[DefinePlugin](https://webpack.docschina.org/plugins/define-plugin/)_」。像这类内置的插件，我们可以通过 `webpack.[plugin-name]` 访问这些插件。另一类就是社区提供的第三方 `plugins`，这一类的插件就需要单独通过 `npm` 或者 `yarn` 进行安装，然后在 `Webpack` 配置文件中引入，最后按插件的配置规则配在 `plugins` 属性上就可以使用了。

> 注意：本页面仅讨论使用插件，如果你有兴趣深入了解插件或者想编写自己的插件，请访问[第七章 插件](/di-qi-zhang-shen-ru-loader.md)章节。

### plugins

在 `Webpack` 的配置文件中，有一个 `plugins` 属性「_他的类型是 Array_」用来专门配置插件相关的信息，以供 `Webpack` 使用。

例如，当多个 `bundle` 共享一些相同的依赖，`SplitChunksPlugin`「_在 Webpack 4 之前使用的是 CommonsChunkPlugin，Webpack 4 将其移除了_」 有助于提取这些依赖到共享的 `bundle` 中，来避免重复打包。可以像这样添加：

```javascript
module.exports = {
  plugins: [
    new Webpack.optimize.SplitChunksPlugin({
      
    })
  ]
}
```

`Plugin` 的配置很简单，`plugins` 配置项接受一个数组，数组里每一项都是一个要使用插件的实例，`Plugin` 需要的参数通过构造函数传入。

### 复杂使用例子

```javascript
var webpack = require('webpack');
// 导入非 webpack 自带默认插件
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var DashboardPlugin = require('webpack-dashboard/plugin');

// 在配置中添加插件
module.exports = {
  //...
  plugins: [
    // 构建优化插件
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      filename: 'vendor-[hash].min.js',
    }),
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false,
        drop_console: false,
      }
    }),
    new ExtractTextPlugin({
      filename: 'build.min.css',
      allChunks: true,
    }),
    new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/),
    // 编译时(compile time)插件
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': '"production"',
    }),
    // webpack-dev-server 强化插件
    new DashboardPlugin(),
    new webpack.HotModuleReplacementPlugin(),
  ]
};
```

使用 `Plugin` 的难点在于掌握 `Plugin` 本身提供的配置项，而不是如何在 `Webpack` 中接入 `Plugin`。

几乎所有 `Webpack` 无法直接实现的功能都能在社区找到开源的 `Plugin` 去解决，你需要善于使用搜索引擎去寻找解决问题的方法。

下一节：[2.6 模块（module）](/di-er-zhang-he-xin-gai-nian/26-mo-kuai-ff08-module.md)

 