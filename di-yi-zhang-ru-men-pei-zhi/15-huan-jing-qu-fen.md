经过前面几个小节的学习，我们了解了 `Webpack` 的依赖环境和基本工作原理，学会了基础的 `loader`、`plugin` 的配置。当然这些还只是开始！

关于这一小节的内容，我们之前有介绍过：通过在 `webpack.config.js` 里面配置 `mode` 参数来区分开发环境`（development）`和生产环境`（production）`。这种方式有一定的局限性，只能在配置里面写死，那我们平时根据执行的 `npm` 脚本传参来区分开发环境`（development）`和生产环境`（production）`又是怎么做的呢？

### 配置命令

在介绍具体的内容之前，我们先来了解一下 `npm` 脚本是怎么配置的。

在之前章节，我们有介绍过 `npm init` 生成的 `package.json` 配置文件信息。里面有一个 `scripts` `key` 值对象属性，我们没有介绍，这里详细介绍一下。

首先我们依然是安装 `Hello-Plugin` 项目目录结构新建一个 `Distinct-Environment` 项目，或者直接拷贝 `Hello-Plugin` 项目，然后重命名为 `Distinct-Environment`。然后，我们修改 `package.json` 里面 `scripts` 对象的信息为：

```json
"scripts": {
    "build": "webpack"
},
```

然后在 `Distinct-Environment` 项目根目录下执行 `npm` 脚本 -- `npm run build` （这里可能提示需要安装 `webpack-cli`，直接安装就好了！），发现和我们之前的项目一样依然能够正常打包，那和我们之前直接在控制台执行 `webpack` 有什么区别呢？

### 脚本原理

其实 `npm` 脚本的原理非常容易理解。

熟悉 `Shell` 脚本的同学都知道，我们将命令脚本写在一个 `Shell` 文件里面，然后执行这个 `Shell` 脚本文件，里面的命令就会执行了（前提是里面的脚本是 `Shell`（一般是 `Bash` ）可以运行的命令）。

每次在 `npm run` 时就会新建一个 `Shell` 文件，并将指定的脚本写入该 `Shell` 文件。因此在执行这个 `Shell` 脚本文件，里面的命令就会执行了。

比较特别的是，`npm run` 新建的这个 `Shell`，会将当前目录的 `node_modules/.bin` 子目录加入 `PATH` 变量，执行结束后，再将 `PATH` 变量恢复原样。

这意味着，当前目录的 `node_modules/.bin` 子目录里面的所有脚本，都可以直接用脚本名调用，而不必加上路径。比如：`"build": "webpack"` 而不必写成 `"build": "./node_modules/.bin/webpack"`。

由于 npm 脚本的唯一要求就是可以在 `Shell` 执行，因此它不一定是 `Node` 脚本，任何可执行文件都可以写在里面。

`npm` 脚本的退出码，也遵守 `Shell` 脚本规则。如果退出码不是 `0`，`npm` 就认为这个脚本执行失败。

说到这里，大家对 `npm` 脚本有了大概的认识了，详细可以查看底部的外链接哟！

### 环境区分

除了我们之前在 `webpack.config.js` 里面显示的指定 `mode` 的值用以区分打包环境外，接下来我们会介绍两种不同形式的 `npm` 脚本传参来区分 `Webpack` 打包环境。

#### npm 脚本传 Webpack 配置参数

通过 `npm` 脚本的形式传递 `Webpack` 的配置参数，也就是 `mode` 的值来指定 `Webpack` 打包的环境，这样比我们之前在 `webpack.config.js` 写死就稍微灵活一点了。

修改 `package.json` 里面 `scripts` 值为：

```json
"scripts": {
    "build": "webpack --mode production"
},
```

然后在控制台运行 `npm run build`，发现 `webpack` 以生产环境的模式打包了，生成的 `./dist/main.js` 文件和我们在 `Hello Webpack` 项目中使用默认配置打包生成的 `JavaScript` 文件形式是一样的。此时，即使你在 `webpack.config.js` 里面配置了 `mode` 的值，`Webpack` 打包时也不会使用这个配置，它会使用 `npm` 里面脚本的传参。

接下来，我们继续修改 `package.json` 里面 `scripts` 值为：

```json
"scripts": {
    "build": "webpack --mode development"
},
```

然后在控制台运行 `npm run build`，发现 `webpack` 以开发环境的模式打包了，生成的 `./dist/main.js` 文件和我们之前在 `Hello Plugin` 项目中使用 `mode: development` 配置打包生成的 `JavaScript` 文件形式是一样的。

这里提一嘴，`Webpack` 支持的 `mode` 值只有 `development` 和 `production` !

这种形式的 `mode` 配置就讲到这里了，还想了解更多，就去 `Webpack` 官网试试吧！

#### Webpack 插件传参

对于上面介绍的 npm 脚本传递 Webpack 配置参数和之前介绍的在 webpack.config.js 里面直接写死 mode 配置的形式，在我们实际的项目开发中是极少用到的，因为他们还不是足够的方便和灵活。

接下来，我们来介绍一个 npm scripts 传参工具 [cross-env](https://www.npmjs.com/package/cross-env) 和一个 Webpack 插件 [DefinePlugin](https://www.webpackjs.com/plugins/define-plugin/)，他们两个的结合可以定义出任何你需要的环境，接受你需要往 Webpack 打包时传递的任何参数。

- DefinePlugin：DefinePlugin 插件是 Webpack 内置的插件，所以他不需要单独安装。

_DefinePlugin 允许创建一个在编译时可以配置的全局常量。这可能会对开发模式和发布模式的构建允许不同的行为非常有用。如果在开发构建中，而不在发布构建中执行日志记录，则可以使用全局常量来决定是否记录日志。这就是 DefinePlugin 的用处，设置它，就可以忘记开发和发布构建的规则。_

                                                                                                                        -- Webpack DefinePlugin
                                          


-cross-env：

外链接：

npm scripts：http://www.ruanyifeng.com/blog/2016/10/npm_scripts.html
cross-env：https://www.npmjs.com/package/cross-env
DefinePlugin：https://www.webpackjs.com/plugins/define-plugin/