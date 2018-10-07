接触过实际项目开发的同学都知道，开发环境有很多强大而又友好的功能，比如本地开发服务，`hmr`，模拟测试、线上打包等！这一小节，我们就来搭建一个极其简单的开发环境（也叫 [devServer](https://www.webpackjs.com/configuration/dev-server/)）。

### 项目搭建

按照我们熟悉的步骤或依照 `Distinct-Environment` 自己手动创建或拷贝 `Distinct-Environment` 项目重命名为 `Dev-Server`。这样做也是为了让我们的学习一步接一步，在运行该小节的项目是，顺便温故一下之前小节的项目，希望大家都能动手操作！

该小节的基本演示项目就已经搭建好了。那我们就根据这小节的目标对项目进行改造吧！

接下来介绍依赖的插件。

### 依赖插件

[devServer](https://www.webpackjs.com/configuration/dev-server/) 环境的搭建是基于 [webpack-dev-server](https://github.com/webpack/webpack-dev-server) 的，所以我们事前必须先安装插件！

```bash
npm install webpack-dev-server --save-dev
or
yarn add webpack-dev-server --save-dev

```

- [webpack-dev-server](https://github.com/webpack/webpack-dev-server) 提供了实时重新加载的开发服务器与 `Webpack` 搭配使用加快了开发速度，解放了开发者的重复刷新页面的操作。不过这种模式只实用于开发。它使用 [webpack-dev-middleware](https://github.com/webpack/webpack-dev-middleware) 提供对 `Webpack` 打包后的静态资源的快速内存访问。

- [webpack-dev-middleware](https://github.com/webpack/webpack-dev-middleware) 是一种快速式开发的中间件，用于 `Webpack` 进行打包，并且允许提供服务给`Webpack` 打包后的文件。这应该仅用于开发。运用这个中间件可以带来一些好处：

 - `Webpack` 打包后的文件不会写到磁盘上，而是在内存中进行处理
 - 如果文件在监视模式下更改，则中间件会延迟请求，直到编译完成
 - 支持模块热重载`（hmr）`
 
说到这里该小节的目标是不是有了头绪！`webpack-dev-server` 相当于是对 `webpack-dev-middleware` 进行了一层封装，`webpack-dev-middleware` 里面的原理我们不需要知道，我们只要知道它提供的功能。然后我们只需要对 `webpack-dev-server` 进行配置就好了，想一想是不是这样的！

### devServer 配置

> 删除本小节演示项目下的 `dist` 目录，如果有的话。

#### 入门 devServer

> 先来个极其简单的 [devServer](https://www.webpackjs.com/configuration/dev-server/) 开发吧 -- 可以起一个开发服务，修改文件自动重载。

由于 `webpack-dev-server` 是一个单独的命令，所以我们要在 `package.json` 里面单独配置：

```json
"scripts": {
+  "start": "webpack-dev-server",
   "build": "webpack --mode development",
   "build:Pro": "cross-env NODE_ENV=production webpack",
   "build:Dev": "cross-env NODE_ENV=development webpack"
},
```

然后在 `webpack.config.js` `output` 对象里面添加：

```javascript
output: {
   path: path.resolve(__dirname, './dist'),
+  publicPath: "/dist/",
},
```

⚠️ 注意：这里特别要注意添加的这个参数配置`（publicPath: "/dist/"）`。

这项配置指定了在浏览器中所引用的「此输出目录对应的公开 URL」。相对 `URL(relative URL)` 会被相对于 `HTML` 页面（或 `<base>` 标签）解析。相对于服务的 `URL(Server-relative URL)`，相对于协议的 `URL(protocol-relative URL)` 或绝对 `URL(absolute URL)` 也可是可能用到的，或者有时必须用到。

在这里相当于指定了开发服务器静态资源引用的目录，所以必须与 `index.html` 引入的资源路径的目录一致，否则自动重载就不会生效。该配置默认为空，如果你不想配置的话，在 `index.html` 引入资源文件就应该是这样的：

```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Hello Plugin</title>
    <link href="./index.css" rel="stylesheet"/>
  </head>
  <body>
    <div id="app"></div>
    <script src="./main.js"></script>
  </body>
  </html>
```

说到这里，如果你配置好了，就去控制台运行 `npm run start` 试试，如果控制台输出正常，会有一段日志：`Project is running at http://localhost:8080/`。打包完成后在浏览器打开 `localhost:8080` 就能显示项目的 `index.html` 页面了，然后再修改 `./src/index.js` 和 `./src/style/index.css` 看看效果吧！

这就是最简单的开发服务了，但是离真正高可用的开发服务环境还有一大截呢！我们慢慢来！

#### 进阶 devServer

前面我们搭建了一个具有服务，可自动重载的开发服务，虽然比较简单，但是我们毕竟进步了一大截了！

接下来，我们就加入 `HTML` 文件创建、重新编译清空 `dist` 目录等进阶功能为下面的高阶做准备吧！

- `HTML` 文件的创建，这里推荐 [HtmlWebpackPlugin](https://www.webpackjs.com/plugins/html-webpack-plugin/) 插件，这个插件会极其的简化最终 `HTML` 文件的创建，尤其是当我们做分包处理、引入很多静态资源时，只需要专注开发，资源文件的引入就插件解决就好，我们提供一个 `HTML` 模板文件就可以了！

该插件不是 `Webpack` 内置的，所以需要单独安装 `npm install --save-dev html-webpack-plugin` 或者 `yarn add --save-dev html-webpack-plugin` 就可以了，然后修改 `webpack.config.js` 里面的配置：

先引入 `html-webpack-plugin` 插件

```javascript
  const HtmlWebpackPlugin = require('html-webpack-plugin');
```

然后在 `plugins` 配置

```javascript
  output: {
    path: path.resolve(__dirname, './dist'),
-   publicPath: '/dist/',
  },
  
  .....
  
  plugins: [
    new ExtractTextWebpackPlugin('index.css'),
+   new HtmlWebpackPlugin({
+     // 要进行打包的 html 模板文件
+     template: 'index.html',
+   }),
  ],
```

最后把 `index.html` 里面之前引入静态资源的地方都删掉

```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Hello Plugin</title>
  </head>
  <body>
    <div id="app"></div>
  </body>
  </html>
```

```
⚠️ 注意：

1、`html-webpack-plugin` 插件里面必须指定 `HTML` 模板文件，否则打包会成功，但是浏览器会报错找不到可操作的 `HTML` 文件
2、因为 `html-webpack-plugin` 插件会处理 `HTML` 文件中静态资源的导入，所以如果你在 `HTML` 模板文件中引入静态资源是画蛇添足，而且打包后浏览器会报错找不到 `HTML` 模板文件里面引入的静态资源。
3、当加了 `html-webpack-plugin` 插件后，`output` 里面 `publicPath` 的配置就可以去掉了，当然也可以设置。这是因为在没有使用 `html-webpack-plugin` 插件时，`index.html` 是没有参与 `Webpack` 打包的，也就是说 `dist` 目录下没有 `index.html` 文件。同时，静态资源文件的引入也是我们在 `index.html` 里面手动操作的，为了保证静态资源引入文件路径的正确性，我们必须指定静态资源打包输出的目录。而当我们使用了 `html-webpack-plugin` 插件后，`index.html` 文件就会参与 `Webpack` 的打包，所有的静态资源引入 `html-webpack-plugin` 插件都会根据 `output` 配置动态处理掉，这时我们就不必关心静态资源打包输出的目录了，而只需提供 `HTML` 文件模板就行了！
4、还有一点：在这种环境下是没有 `dist` 目录生成的。我们一开始也介绍过 `webpack-dev-middleware` 的特性，它会使得 `Webpack` 打包后的文件不会写到磁盘上，而是在内存中进行处理。所有的文件操作都是在内存中进行的，所以并不会产生 `dist` 目录。
```

此时我们去控制台运行 `npm run start` 试试，效果和之前一样的，不过相对之前，我们关注点更少了！

- 输出文件目录清空

根据上面的



#### 高阶 devServer

### 总结


外链接：

- [devServer](https://www.webpackjs.com/configuration/dev-server/)
- [webpack-dev-server](https://github.com/webpack/webpack-dev-server)
- [webpack-dev-middleware](https://github.com/webpack/webpack-dev-middleware)
- [output publicPath](https://www.webpackjs.com/configuration/output/#output-publicpath)
- [html-webpack-plugin](https://www.webpackjs.com/plugins/html-webpack-plugin/)
