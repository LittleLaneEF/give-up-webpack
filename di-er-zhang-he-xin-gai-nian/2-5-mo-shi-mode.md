## 模式（mode）

在前面的章节中，我们有提到 `Webpack` 作为前端炙手可热的打包工具，不仅可以给我们提供高可用的本地化开发服务，还可以为我们的项目投产做好优化，进行保驾护航！

在实际的项目开发中，我们会区分开发环境和生产环境，并根据不同的环境对源代码进行不同的处理，比如：在开发环境中，我们要在本地起一个 `HTTP` 服务，并实现「_模块热替换（hot module replacement）_」方便我们快速地进行项目的开发，同时为了是模块热替换的效率更高，我们会省去代码压缩、样式文件提取等相关耗时的优化处理。但是在生产环境我们就没有比较起 `HTTP` 服务了，那是都是通过 `nodejs` 或者 `nginx` 进行处理，「_模块热替换（hot module replacement）_」就更加没有实现的必要了，因为我们不会在生产环境去修改代码。由于我们部署的项目是直面用户的，为了给用户提供优良的体验，关于最终部署的代码肯定要做深度优化处理，例如代码压缩以使 `HTTP` 请求的文件更小，为了减少 `HTTP` 请求，我们做了公共样式、模块引入文件的提取并将较小的图片资源进行了 `base64` 处理。这些不同环境对代码的处理，`Webpack` 给我们提供了模式的处理方式。

### 用法

在 `Webpack` 中给我们提供了两种模式的区分：「_开发环境（development）_」和「_生产环境（production）_」。默认的模式是「_生产环境（production）_」。`mode` 属性值类型是 `string`。模式的指定分 `webpack.config.js` 和 「_CLI 参数传递_」两种形式。

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

### 
