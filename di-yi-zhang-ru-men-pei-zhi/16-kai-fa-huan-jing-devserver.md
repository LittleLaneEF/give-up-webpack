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

根据上面的配置信息，我们在控制台执行 webpack，发现控制台输出了我们熟悉的日志信息，然后在项目根目录下生成了 dist 目录，打开 dist 目录发现此时有 index.css，index.html 和 main.js 三个文件，index.html 文件在我们之前的项目打包中是看不到的。再看看 index.html 文件的源码

```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Hello Plugin</title>
  <link href="index.css" rel="stylesheet"></head>
  <body>
    <div id="app"></div>
  <script type="text/javascript" src="main.js"></script></body>
  </html>
```

静态资源自动引入了，而且资源引入的路径和我们之前没有配置 output publicPath 参数的时候是一样的，证明了我们上面的解释。你是不是对 Webpack 越来越感兴趣了呢？

反正我是！

- 输出文件目录清空

对项目源代码进行打包是高重复性的操作，每次打包都会生成 dist 目录。对于 dist 目录里面的文件，我们希望前面打包生成的文件对本次打包的文件没有影响，毕竟我们是要上生产环境的。我们也不可能每次上生产的时候去删除 dist 目录吧，这样就不能体现程序猿懒得精神的，所以 [clean-webpack-plugin](https://www.npmjs.com/package/clean-webpack-plugin) 插件应运而生。

clean-webpack-plugin 是一个用来在 build 源代码前移除或清空目标目录的 Webpack 插件。

`npm install --save-dev clean-webpack-plugin` 或者 `yarn add --save-dev clean-webpack-plugin` 安装插件，然后安排配置：

引入：

```javascript
  const CleanWebpackPlugin = require('clean-webpack-plugin');
```

配置 plugins：

```javascript
  plugins: [
+   new CleanWebpackPlugin(['dist']),
    new ExtractTextWebpackPlugin('index.css'),
    new HtmlWebpackPlugin({
      // 要进行打包的 html 模板文件
      template: 'index.html',
    }),
  ],
```

配置好后，然后在控制台运行 webpack 进行打包。

![](/assets/cleanPlugin.gif)

效果新明显，发现在打包一开始 dist 目录就被删除了，然后打包完成后，dist 目录又生成了。

在看看控制台的输出，第一句话就是 
_clean-webpack-plugin: /Users/lane/StudyCloud/Webpack/give-up-webpack-cases/Dev-Server/dist has been removed._

大家可以私下试试效果，一定要动手操作哟！

#### 高阶 devServer

在进阶部分我们介绍了两个开发模式非常实用的两个插件，这让我们搭建的开发环境和我们这一小节的目标更近了一步。接下来，我们将加入极其重要的功能 -- hmr。

hmr(全称 hot module replacement 模块热替换)是 Webpack 提供的重要功能之一，它能监听文件的变化，然后修改更新的模块，在不刷新整个浏览器页面的同时，重新渲染变化部分。

废话不多说，我们一起来修改配置吧！

- 修改 package.json

```json
 "scripts": {
+  "start": "cross-env NODE_ENV=development webpack-dev-server",
   "build": "webpack --mode development",
   "build:Pro": "cross-env NODE_ENV=production webpack",
   "build:Dev": "cross-env NODE_ENV=development webpack"
 },
```

- 修改 webpack.config.js

```javascript
  const path = require('path');
  
  // 这个是要求项目本地要安装 webpack 的
+ const Webpack = require('webpack');

  const ExtractTextWebpackPlugin = require('extract-text-webpack-plugin');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const CleanWebpackPlugin = require('clean-webpack-plugin');
  
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
          use: ExtractTextWebpackPlugin.extract({
            fallback: 'style-loader',
            use: ['css-loader']
          }),
        }
      ]
    },
    plugins: [
      new CleanWebpackPlugin(['dist']),
      new ExtractTextWebpackPlugin('index.css'),
      new HtmlWebpackPlugin({
        // 要进行打包的 html 模板文件
        template: 'index.html',
      }),
      
      // 引入 HotModuleReplacementPlugin 启用模块热替换(Hot Module Replacement)
+     new Webpack.HotModuleReplacementPlugin(),

    ],
    devServer: {
    
      // 启用 webpack 的模块热替换特性，这个参数必须要配合 HotModuleReplacementPlugin 一起使用
      // 如果你是在命令行以 webpack-dev-server --hot 进行启动就不需要配置 HotModuleReplacementPlugin 了
+     hot: true,
    },
    mode: process.env.NODE_ENV,
  }
```

配置完成后，在控制台执行 npm run start 以开发模式启动 webpack-dev-server。等打包完成后 webpack-dev-server 会起一个监听 8080 端口的本地 HTTP 服务，我们直接在浏览器输入 localhost:8080 即可访问。

- 1.0 修改 ./src/index.js 测试效果

将 document.querySelector('#app').innerHTML = 'Hello Plugin!';  修改成 document.querySelector('#app').innerHTML = 'Hello Plugin update!'; 

效果如下图：

![](/assets/hmr1.gif)

我们发现当我们修改了 ./src/index.js 文件后点击保存，左下角的终端就立即开始进行打包了。Webpack 打包完成后，左上角的浏览器开始显示新的内容，但是显示效果是整个页面连同 Chrome 控制台整个刷新了。新的内容是显示的，但是这个显示的效果也忒不正常了吧，而且 Chrome 控制台还显示 Hot Module Replacement enable。这是怎么回事呢？命名显示 hmr 已经启动，但是效果却不是 hmr 的效果。

[hot-module-replacement guides](https://webpack.docschina.org/guides/hot-module-replacement/) 这里有例子描述：当我们在 webpack.config.js 里面配置 new webpack.HotModuleReplacementPlugin() 后，它会在全局暴露一个 module.hot 的监听，要在 App 的入口 JavaScript 文件加入这个监听。尝试一下准没错！

- 2.0 修改 ./src/index.js 测试效果

修改 ./src/index.js 文件

```diff
	import './style/index.css';

	document.querySelector('#app').innerHTML = 'Hello Plugin!'; 

+	if(module.hot) {
+		module.hot.accept()
+	}
```



![](/assets/hmr2.gif)

```
[WDS] App updated. Recompiling...
client:218 [WDS] App hot update...
log.js:24 [HMR] Checking for updates on the server...
log.js:24 [HMR] Updated modules:
log.js:24 [HMR]  - ./src/index.js
log.js:24 [HMR] App is up to date.
```
- 1.0 修改 ./src/style/index.css 测试效果

![](/assets/hmr3.gif)

```
[WDS] App updated. Recompiling...
client:218 [WDS] App hot update...
log.js:24 [HMR] Checking for updates on the server...
log.js:24 [HMR] Nothing hot updated.
log.js:24 [HMR] App is up to date.
```

- 2.0 修改 ./src/style/index.css 测试效果

![](/assets/hmr 4.gif)

```
[WDS] App updated. Recompiling...
client:218 [WDS] App hot update...
log.js:24 [HMR] Checking for updates on the server...
log.js:24 [HMR] Updated modules:
log.js:16 [HMR]  - ./src/style/index.css
log.js:24 [HMR] App is up to date.
```


### 总结


外链接：

- [devServer](https://www.webpackjs.com/configuration/dev-server/)
- [output publicPath](https://www.webpackjs.com/configuration/output/#output-publicpath)
- [webpack-dev-server](https://github.com/webpack/webpack-dev-server)
- [webpack-dev-middleware](https://github.com/webpack/webpack-dev-middleware)
- [html-webpack-plugin](https://www.webpackjs.com/plugins/html-webpack-plugin/)
- [hot-module-replacement](https://webpack.docschina.org/api/hot-module-replacement/)
