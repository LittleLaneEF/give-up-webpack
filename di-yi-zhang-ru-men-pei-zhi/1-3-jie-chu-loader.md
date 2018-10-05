上一小节我们喜提 `Hello Webpack`，大家都没有动手操作和消化理解呢？这一小节我们将使用 `loader` 来给我们的页面添加些许样式，大家准备好了吗？

> Let's get starting it!

### 新建项目

按照之前新建项目的方式新建一个项目叫 `Hello-Loader`，目录结构如下：

![](/assets/hello-loader-build.png)

从图的左侧我们可以看到，我们在原有的项目目录下新加了 `src/style/index.css` 样式文件。

```css
body{
  background-color: red;
  color: #fff;
}
```

在 `src/index.js` 里面我们添加了引入样式的代码

```javascript
import './style/index.css';

document.querySelector('#app').innerHTML = 'Hello Loader!'; 
```

### build 源码

我们依然在控制台或者终端执行 `webpack` 进行编译，发现输出报错了。错误显示 `Webpack` 在编译是遇到了问题，有些文件内容不认识，无法处理。这些文件内容在输出日志中一览无余就是我们新增加的样式文件。

遇到这个问题也是意料之中的事情，我们之前也说过，`Webpack` 是 `JavaScript` 模块化打包工具，默认只认识 `JavaScript` 文件，也就是只能处理 `JavaScript` 文件，对于其他文件的处理我们必须加入对应的 `loader`。

### 配置 loader

记住一个 `loader` 只做一件事情。样式文件要起作用也有两步要走，第一步先解析样式文件(css-loader)，第二步使解析解析后的样式文件起作用(style-loader)！所以，我们在这里也要配置两个 `loader` 。

- 安装 `css-loader` `style-loader`

> npm install --save-dev css-loader style-loader

- 配置 `css-loader` `style-loader`

这一小节的配置信息有点复杂，我们引入 `webpack.config.js` ，在 `Hello-Loader` 项目根目录下新建 `webpack.config.js` 配置文件。

```javascript
// node 提供的模块，处理路径的，导入方式是遵循 `commonjs` 规范的，按照这样导入就行了
const path = require('path');

// 因为 Webpack 的编译是在 node 环境下执行的，所以这里的导出方式也要遵循 commonjs 规范
module.exports = {
  // 配置入口文件，和上一小节保持一致
  entry: './src',
  
  // 输出配置，和上一小节保持一致
  output: {
    path: path.resolve(__dirname, './dist'),
  },
  
  // 这里配置模块的编译方式，也就是 loader 的配置
  module: {
    rules: [
      {
        // loader 处理匹配的文件
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

配置完成后再在控制台一如既往的执行 `webpack`，是不是没有打印之前的报错了，和我们 `Hello Webpack` 一节打印的日志很类似嘛！

![](/assets/hello-loader-build2.png)

从我们给出的图示可以看到，日志打印出处理的 `./src/index.js` 上面出现了处理 `./src/style/index.css` 的两条记录。然后我们在浏览器打开 `index.html`，发现浏览器的背景色变成红色了，`Hello Loader` 字样也变成白色了，证明我们打包成功了！

### 注意

1、`webpack.config.js`：该文件是配置 `Webpack` 打包行为的文件。在项目中的 `Webpack` 打包配置是很复杂的，我们必须要配置文件放在单独的甚至多个文件中，方便我们管理。对于极其简单的演示项目，比如我们之前的 `Hello Webpack` 项目，我们可以直接运行 `webpack` 进行打包，还有一个原因是 `Webpack 4` 给我们设置了很多的默认打包配置项。

2、上面我们为样式文件配置 `loader` 时，配置了多个 `loader（style-loader、css-loader）`，那多个 `loader` 顺序的问题，我们必须注意。在[官网文档 loader -> loader 特性一节](https://www.webpackjs.com/concepts/loaders/)有这样一句描述：

_loader 支持链式传递。能够对资源使用流水线(pipeline)。一组链式的 loader 将按照相反的顺序执行。loader 链中的第一个 loader 返回值给下一个 loader。在最后一个 loader，返回 webpack 所预期的 JavaScript。_

多个 `loader` 时，执行顺序是从右到左的，依次链式调用下去，最左边的 `loader` 会返回预期的最终代码。

在之前我们也说过，处理样式文件的步骤依次是：解析样式文件`（css-loader）`，然后将样式文件插入到最终的 `JavaScript` 代码中，使其在浏览器中能够生效`（style-loader）`。所以最终的 `loader` 配置顺序是 `['style-loader', 'css-loader']`，不信你可以调换顺序试试嘛！

控制台错误日志有输出对吧！

### 答疑

1、之前在 `Hello Webpack` 一节，我们没有配置 `webpack.config.js` 文件，打包也正常运行了，这一节，我们加了这个配置文件也可以运行，而且输出的日志都差不多，这里面到底有什么奥秘之处？

首先当我们在控制台运行 `webpack` 进行源码打包时，`webpack` 会默认去搜索项目下的 `webpack.config.js` 配置文件。如果搜索到了，`webpack` 会读取该配置文件的信息进行打包工作，再如果我们的项目中没有这样的配置文件，`webpack` 就会启用它预设的配置信息进行打包处理。当然预设的配置信息可能和我们的项目代码结构不一致，打包不成功，所以预设值局限性还是很大的，但是这也体现了 `Webpack` 的人性化，不至于找不到配置文件就不工作了。

输出的日志差不多，是因为这里给出的配置信息大多是 `Webpack` 默认的，我们只是增加了 `loader` 处理样式文件而已。

2、为什么我运行 `webpack` 时，日志输出提示我要安装 `webpack-cli` ?

_webpack CLI is a CLI tool for providing a flexible set of commands for developers to increase speed when setting up a custom webpack project. As of webpack v4, webpack is not expecting a configuration file but often, developers want to create a more custom webpack configuration based on their use-cases and needs. Exactly all these cases with webpack CLI we are providing a set of tools to improve the setup of custom webpack configuration._

                                                        -- webpack-cli
                                                        
`webpack-cli` 是 `webpack` 的命令行工具，用于为开发人员提供一组灵活的命令，以便在设置自定义`webpack` 项目时提高速度。在 `webpack 4` 之前 `webpack-cli` 是必须安装的。

当你的控制台提示要安装 `webpack-cli` 时，那你的 `webpack` 版本一定是 `<4` 的。你有两个选择：一是升级你的 `webpack` 版本，二是安装 `webpack-cli` 。

下一节：[使用 `plugins`](/di-yi-zhang-ru-men-pei-zhi/1-4-shi-yong-plugins.md)




