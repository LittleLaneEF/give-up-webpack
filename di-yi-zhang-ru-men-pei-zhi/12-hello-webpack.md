从这一节开始我们就这个是开始接触 `Webpack` 了，想想接下来的东西，都有些激动呢！

上一小节我们介绍了 `Webpack` 依赖环境和 `Webpack` 本尊的安装，还没有尝试的同学可以先试试，再来看这一节的内容哟！

### 搭建项目

`Webpack` 作为有个项目的打包工具，当然离开了项目，`Webpack` 的价值就大打折扣了。所以我们这里先新建一个项目。

![](/assets/hello-webpack.png)

这里我在我的磁盘上建了一个 `give-up-webpack-cases` 的工程，用来跟我的 `github` 同步，然后在工程下新建了 `Hello-Webpack` 的项目。文件比较多，大家主要关注 `src` 目录、`index.html`、`package.json`。

#### index.html执行

```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Hello Webpack</title>
  </head>
  <body>
    <div id="app"></div>
    <script src="./dist/main.js"></script>
  </body>
  </html>
```

在 `index.html` 里面，我们建了一个 `id` 为 `app` 的容器，然后用 `script` 标签引入了即将打包生成在 `dist` 目录下面的目标 `JavaScript` 文件 `main.js`。

#### src/index.js

```javascript
  document.querySelector('#app').innerHTML = 'Hello Webpack!';
```

在 `src/index.js` 文件中，我们用 `document.querySelector` 选取了 `id` 为 `app` 的容器，然后向里面设置了 `Hello Webpack`。

#### package.json

```json
{
  "name": "hello-webpack",
  "version": "0.0.1",
  "description": "hello-webpack",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/LittleLaneEF/give-up-webpack-cases.git"
  },
  "keywords": [
    "webpack"
  ],
  "author": "littleLane",
  "license": "MIT",
  "bugs": {
    "url": "https://github.com/LittleLaneEF/give-up-webpack-cases/issues"
  },
  "homepage": "https://github.com/LittleLaneEF/give-up-webpack-cases#readme",
  "devDependencies": {
    "webpack": "^4.20.2"
  }
}

```

从 `package.json` 中可以看到，我们已经通过 `npm install webpack --save-dev` 安装了 `Webpack` 。其他的配置信息我们之前都有介绍，这里就不赘述了！

有人可能会问，那个叫 `package-lock.json` 文件又是什么呢？这个文件是在我们安装依赖包文件时生成的版本锁定文件。当我们将项目代码上传到 `gitlab` 或者 `github` 或其他代码管理平台供别人下载使用时，别人拿到源代码后的第一个操作就是通过 `npm install` 安装项目的依赖包文件。`npm` 首先会去 `package-lock.json` 查找项目所有依赖包的关联关系和版本信息，然后根据这个信息去安装包文件。如果没有这个文件，`npm` 安装包文件会慢一些，因为他要边下载包文件，边解析包文件的依赖关系。用 `yarn` 安装包文件也会生成 `yarn-loack.json` 文件，他们的作用是一样的！

### build 源代码

在 `Hello-Webpack` 项目根目录运行 `webpack` 命令，然后可以看到类似下面的图示：

![](/assets/hello-webpack-build.png)

从图中不难看出，我们在终端或者控制台执行 `webpack` 后，命令行一次输出了：

- `Hash`：入口文件的 `hash` 值，这个 `hash` 值是根据文件的众多信息生成的，如果 `src/index.js` 内容不变，不管你运行多少次 `webpack` ，生成的 `hash` 值都是一样的。

- `Version`：此次编译使用 `Webpack` 的版本信息。大家可能已经注意到了，我项目安装的 `Webpack` 的版本号是 `4.20.2`，而这里输出的是 `4.8.3`，怎么回事呢？那是因为当我们在终端或者控制台执行 `Webpack` 时，系统默认是找我们全局安装的 `Webpack` 的，我全局安装的就是 `Webpack 4.8.3` 版本的，不行你试试！（_这里顺便提一嘴，如果当你运行 `Webpack` 时，输出说找不到 `Webpack` 或者 `Webpack cli` 命令，只需要 `npm install webpack-cli -g` 或者 `yarn add webpack-cli -g` 就可以了_）。

- `Time`：表示此次打包所需要的时间，这个时间基本上每次都不一样，由机器的配置、是否打包过、文件的打包、项目的复杂度等众多因素决定。

- `Build at`：打包开始时间，这个比较简单。

> 下面就是重点了：

- 第一个红框里面输出了生成文件的相关信息。
  - `Asset`：生成的静态资源名称，这里是 `main.js`。
  - `Size Chunks`：`chunk` 包的大小 `602 bytes`。
  - `Chunks Name`：`chunk` 包的名称 `main`。 
  
为什么默认执行 `Webpack` 会这样输出呢？
还有他又是怎么知道我们要打包 `src/index.js` 的呢？
太多疑问了，我们还是去官网找答案吧！

在[概念入口](https://www.webpackjs.com/concepts/#%E5%85%A5%E5%8F%A3-entry-)一节，我们可以发现 `Webpack` 默认的入口 `entry` 的值是 `./src`，也就是 `./src/index.js`（这里不要问我为什么知道的，`Webpack` 是基于 `node` 的，肯定使用了 `node` 的[模块机制](http://www.infoq.com/cn/articles/nodejs-module-mechanism/)，这个就不在本次分享的范围内了，想要了解的可以自己去搜索。），`Webpack` 打包从这个文件开始，所以第二个问题解决了。

在[概念出口](https://www.webpackjs.com/concepts/#%E5%87%BA%E5%8F%A3-output-)一节描述了 `Webpack` 默认的输出目录是 `./dist` 目录，如果项目中没有改目录就会新建一个。至于为什么输出的是 `main.js`，我觉得当我们什么配置都不写时，`Webpack` 默认给我们配置的入口是这样的：

```json
entry: './src'
```

`entry` 属性的单个入口语法，是下面的简写，详细请看[这里](https://www.webpackjs.com/concepts/entry-points/#%E5%8D%95%E4%B8%AA%E5%85%A5%E5%8F%A3-%E7%AE%80%E5%86%99-%E8%AF%AD%E6%B3%95)

```json
entry: {
  main: './src'
}
```

这个配置指定了，输出的 `chunk` 包名称是 `main`，入口文件时 `./src/index`。

关于 `entry` 和 `output` 详情我们后续在单独介绍。

到这里我们的疑问就都解决了，继续看控制台输出。

- 第二个红框告诉我们此次打包的入口文件时 `./src/index.js` 文件。

- 那下面的一戳黄色的日志又是什么？它警告我们说：此次编译的环境`（mode）`还没有指定，`Webpack` 将默认为生产环境`（production）``build`。我们可以通过设置 mode 值为 `production` 或者 `development` 来指定打包的环境，设置不同值时会开启不同的打包模式，启用不同的代码打包优化的插件功能。这个参数的设置我们后续详细讨论。

> ⚠️ 以上输出信息可能因为 Webpack 版本不同而不同，但是大致都差不多！

在 `Webpack` 编译过程中不出现 `Error` 就是 `OK` 的，警告什么都可以不用管的。如果你打包成功了，在浏览器里面打开 `index.html` 就会看到页面上显示 `Hello Webpack` 字样了！

### 结语

这一小节，我们初次接触了 `Webpack` 的打包，了解了 `Webpack` 打包的日志输出，对 `entry` 和 `output` 做了很浅显的了解。其他还有一个概念，在这里就不做介绍了，我们后续都会开出单独的小节介绍，比如 `chunk`、`entry`、`output` 等等。

下一小节：[接触loader](/di-yi-zhang-ru-men-pei-zhi/1-3-jie-chu-loader.md)