## loader

`Webpack` 默认只能对 `JavaScript` 文件进行打包处理，而在实际的项目中为了达到快速开发，代码高可管理、高可迭代，我们会使用各种各样语言的扩展工具进行开发，比如 `TypeScript`，`React`，`Angular`，`Vuejs`，`Less`，`Scss`，`ES6+`，`ejs` 等等。他们的语法和规则都是浏览器所不能直接解析的，以至于我们要通过相关的工具进行转换和处理，毕竟我们最终的代码是要被浏览器解析和运行的。在这里我们肯定要借助 `Webpack` 进行处理，因为 `Webpack` 是现阶段前端炙手可热的打包工具嘛。但是 `Webpack` 本身也是不能直接对这些文件和代码进行处理的。为了解决这样一个问题，官方和社区出现了各种工具和扩展语言的处理工具 -- `loader`！

`loader` 主要用于对模块的源代码进行转换。`loader` 可以使你在 `import` 或"加载"模块时对文件进行预处理。因此，`loader` 类似于其他构建工具中「“任务(task)”」，并提供了处理前端构建步骤的强大方法。`loader` 可以将文件从不同的语言（如 `TypeScript`）转换为 `JavaScript`，或将内联图像转换为 `data URL`。`loader` 甚至允许你直接在 `JavaScript` 模块中 `import CSS` 文件！

### 实例

在前面 [1.3 接触 loader](/di-yi-zhang-ru-men-pei-zhi/13-jie-hong-loader.md) 小节，我们使用 `css-loader` 和 `style-loader` 处理了样式文件。这里我们再回顾一下：

1、因为 `Webpack` 不内置 `loader`，所以我们必须单独安装

```bash
npm install css-loader --save-dev
npm install style-loader --save-dev
···

因为 `loader` 是依赖于 `Webpack` 而工作的，所以安装的时候必须和 `Webpack` 的版本匹配，否则可能在打包过程中会报错退出。

2、然后我们在 `webpack.config.js` 配置文件中为相关的 `loader` 指定要处理的文件

```javascript
module.exports = {
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
}
```

这里我们用 `rules` 配置多个类型的 `loader` 配置，`test` 执行匹配 `.css` 样式文件，`use` 指定需要使用的 `loader` 配置。

### loader 的使用

目前，我们有三种形式的配置来使用 `loader` 转换文件。

- 配置（推荐使用）：在 `webpack.config.js` 文件中对 `loader` 进行配置
- 内联：在每个使用 `import` 语句导入文件时指定相关的 `loader`
- CLI：在 `npm scripts` 脚本命令中指定 `loader`

#### 配置[Configuration] 

`webpack.config.js` 里面的 `module.rules` 允许你指定多个 `loader`。在这里进行配置有助于使代码变得简洁。同时让你对各个 `loader` 有个全局概览：

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          { loader: 'style-loader' },
          {
            loader: 'css-loader',
            options: {
              modules: true
            }
          }
        ]
      }
    ]
  }
};
```

#### 内联

可以在 `import` 语句或任何等效于 「_"import" 的方式_」中指定 `loader`。使用 ! 将资源中的 `loader` 分开。分开的每个部分都相对于当前目录解析。

```javascript
import Styles from 'style-loader!css-loader?modules!./styles.css';
```

通过前置所有规则及使用 !，可以将源文件对应重载到配置中的任意 `loader` 中。

选项可以传递查询参数，例如 `?key=value&foo=bar`，或者一个 `JSON` 对象，例如 _?{"key":"value","foo":"bar"}_。
  
#### CLI 

当然你也可以通过 `CLI` 使用 `loader`：

```
webpack --module-bind 'css=style-loader!css-loader'
```

这里配置会对 `.css` 文件使用 `style-loader` 和 `css-loader` 进行转换和处理。

> 以上就是 `loader` 配置常用的三种方式了。我们这里极力推荐使用「_配置[Configuration]_」形式，因为这样可以减少源码中的代码量，并且可以在出错时，更快地调试和定位 `loader` 中的问题。

### loader 特性

- `loader` 支持链式传递。`loader` 链中每个 `loader`，都对前一个 `loader` 处理后的资源进行转换。`loader` 链会按照相反的顺序执行。第一个 `loader` 将（应用转换后的资源作为）返回结果传递给下一个 `loader`，依次这样执行下去。最终，在链中最后一个 `loader`，返回 `webpack` 所预期的 `JavaScript`。
- `loader` 可以是同步的，也可以是异步的。
- `loader` 运行在 `Node.js` 中，并且能够执行任何可能的操作。
- `loader` 接收查询参数。用于对 `loader` 传递配置。
- `loader` 也能够使用 `options` 对象进行配置。
- 除了使用 `package.json` 常见的 main 属性，还可以将普通的 `npm` 模块导出为 `loader`，做法是在 `package.json` 里定义一个 `loader` 字段。
- _插件(plugin)_可以为 `loader` 带来更多特性。
- loader 能够产生额外的任意文件。

`loader` 通过（loader）预处理函数，为 `JavaScript` 生态系统提供了更多能力。 用户现在可以更加灵活地引入细粒度逻辑，例如压缩、打包、语言翻译和其他更多。

### 解析 loader

`loader` 遵循标准的模块解析。多数情况下，`loader` 将从模块路径（通常将模块路径认为是 `npm install`, `node_modules`）解析。

`loader` 模块需要导出为一个函数，并且使用 `Node.js` 兼容的 `JavaScript` 编写。通常使用 `npm` 进行管理，但是也可以将自定义 `loader` 作为应用程序中的文件。按照约定，`loader` 通常被命名为 `xxx-loader`_（例如 json-loader）_。有关详细信息，请查看[第七章 深入loader](/di-qi-zhang-shen-ru-loader.md)？。
