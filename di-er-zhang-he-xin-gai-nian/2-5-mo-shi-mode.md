## 模式（mode）

在前面的章节中，我们有提到 `Webpack` 作为前端炙手可热的打包工具，不仅可以给我们提供高可用的本地化开发服务，还可以为我们的项目投产做好优化，进行保驾护航！

在实际的项目开发中，我们会区分开发环境和生产环境，并根据不同的环境对源代码进行不同的处理，比如：在开发环境中，我们要在本地起一个 `HTTP` 服务，并实现「_模块热替换（hot module replacement）_」方便我们快速地进行项目的开发，同时为了是模块热替换的效率更高，我们会省去代码压缩、样式文件提取等相关耗时的优化处理。但是在生产环境我们就没有比较起 `HTTP` 服务了，那是都是通过 `nodejs` 或者 `nginx` 进行处理，「_模块热替换（hot module replacement）_」就更加没有实现的必要了，因为我们不会在生产环境去修改代码。由于我们部署的项目是直面用户的，为了给用户提供优良的体验，关于最终部署的代码肯定要做深度优化处理，例如代码压缩以使 `HTTP` 请求的文件更小，为了减少 `HTTP` 请求，我们做了公共样式、模块引入文件的提取并将较小的图片资源进行了 `base64` 处理。这些不同环境对代码的处理，`Webpack` 给我们提供了模式的处理方式。

### 用法

在 `Webpack` 中给我们提供了两种模式的区分：「_开发模式（development）_」和「_生产模式（production）_」。默认的模式是「_生产模式（production）_」。`mode` 属性值类型是 `string`。模式的指定分 `webpack.config.js` 和 「_CLI 参数传递_」两种形式。

`webpack.config.js` 配置

```javascript
module.exports = {
  mode: 'production',
};
```

`CLI 参数传递`

```
webpack --mode=production
```

`mode` 参数值说明：

|    选项      |                       描述                                 |
|-------------|---------------------------------------------------------------------------------------------|
| development | 会将 process.env.NODE_ENV 的值设为 development。启用 NamedChunksPlugin 和 NamedModulesPlugin。 |
| production | 会将 process.env.NODE_ENV 的值设为 production。启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 UglifyJsPlugin. |
| none | 不选用任何默认优化选项 |

> ⚠️ 记住如果不设置，`webpack` 会将 `production` 作为 `mode` 的默认值去设置。

### mode: development

```diff
// webpack.development.config.js
module.exports = {
+ mode: 'development'
- plugins: [
-   new webpack.NamedModulesPlugin(),
-   new webpack.NamedChunksPlugin(),
-   new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("development") }),
- ]
}
```

### mode: development

```diff
// webpack.production.config.js
module.exports = {
+  mode: 'production',
-  plugins: [
-    new UglifyJsPlugin(/* ... */),
-    new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("production") }),
-    new webpack.optimize.ModuleConcatenationPlugin(),
-    new webpack.NoEmitOnErrorsPlugin()
-  ]
}
```

### mode: none

```diff
// webpack.custom.config.js
module.exports = {
+  mode: 'none',
-  plugins: [
-  ]
}
```

### 实战
 
在实际项目中，我们是通过 「_npm 脚本（npm scripts）_」的形式来区分 `mode` 的，例如我们前面 [1.5 环境区分](/di-yi-zhang-ru-men-pei-zhi/15-huan-jing-qu-fen.md) 里面的案例就有用到。

首先我们在项目的 `package.json` 文件中配置了多个 「_npm 脚本（npm scripts）_」:

```json
"scripts": {
  "build": "webpack --mode development",
  "build:Pro": "cross-env NODE_ENV=production webpack",
  "build:Dev": "cross-env NODE_ENV=development webpack"
},
```

- 第一条 `build script` 使用的 `CLI 传参`的形式，设置了当前 `Webpack` 的打包环境是`开发模式（development）`。
- 第二条 `build script` 使用了 `cross-env` 插件给环境传递了 `NODE_ENV=production` 的参数，设置了当前 `Webpack` 的打包环境是`生产模式（production）`。
- 第三条 `build script` 使用了 `cross-env` 插件给环境传递了 `NODE_ENV=development` 的参数，设置了当前 `Webpack` 的打包环境是`开发模式（development）`。

然后在 `webpack.config.js` 里面，我们接受了 `cross-env` 插件传递的参数，并根据不同的参数值设置给了 `mode` 不同的值。

```javascript
module.export = {
  mode: process.env.NODE_ENV,
}
```

其中 `process.env` 返回一个当前 `node` 运行环境中包含用户环境信息的对象。`cross-env` 插件会将我们在 `npm scripts` 里面传递的值设置在这个对象中，然后在通过 `process.env.NODE_ENV` 获取属性值并将值赋值给 `mode`，当我们在执行不同的 `npm script` 时就对应的指定 `mode` 属性的，用以区分 `Webpack` 的打包模式了。

## 总结

这一小节比较简单，很多东西我们在 [1.5 环境区分](/di-yi-zhang-ru-men-pei-zhi/15-huan-jing-qu-fen.md) 一小节也已经提及了。我们主要了解了 `mode` 参数值有哪些和各表示的含义，还有介绍了通过 `webpack.config.js` 和 `CLI` 设置 `mode` 属性值，最后详细介绍了在实际项目中结合 `cross-env` 插件来设置 `mode` 区分 `Webpack` 打包模式。

外链接：

1、[process.env](https://segmentfault.com/a/1190000011683741)
2、[cross-env](https://www.npmjs.com/package/cross-env)
3、[webpack mode](https://webpack.docschina.org/concepts/mode/#%E7%94%A8%E6%B3%95)

下一节：[loader](/di-er-zhang-he-xin-gai-nian/loader.md)




