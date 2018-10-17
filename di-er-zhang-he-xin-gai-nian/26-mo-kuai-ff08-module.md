## 模块（module）

在 `Webpack` 配置中，`module` 是一个单独的配置项，用于配置如何处理模块。在 `module` 有两大属性：一个是 `rules` ，是用来配置 `loader` 处理文件的，另一个是 `noParse` 属性，该属性的配置可以让 `Webpack` 忽略对部分没采用模块化的文件进行递归解析和处理。

接下来，我们将详细介绍这两个属性的配置信息。

### rules

rules 配置模块的读取和解析规则，通常用来配置 loader。其类型是一个数组，数组里每一项都描述了如何处理部分文件的规则。这些规则能够对模块(module)应用 loader，或者修改解析器(parser)。配置一项 rules 时大致通过以下方式：

- 1、条件匹配：通过 test 、include 、exclude 三个配置项来命中 Loader 要应用规则的文件。
- 2、应用规则：对匹配的文件通过 use 配置项来应用 Loader，可以只应用一个 loader 或者按照从后往前的顺序应用一组 Loader，同时还可以分别给 loader 传入参数。
- 3、应用顺序：一组 loader 的执行顺序默认是从右到左执行，通过 enforce 选项可以让其中一个 loader 的执行顺序放到最前或者最后。

形如我们在 [1.6 开发环境 devServer](/di-yi-zhang-ru-men-pei-zhi/16-kai-fa-huan-jing-devserver.md) 小节处理样式的例子：

```javascript
module: {
  rules: [
    {
      test: /\.css$/,
      
      // 注意这里 loader 配置的顺序，样式文件依次从右到左经过 use 配置 loader 的处理
      // css-loader 是解析样式文件
      // style-loader 是将解析后的样式渲染在页面上
      use: NODE_ENV === 'development' ? ['style-loader', 'css-loader'] : ExtractTextWebpackPlugin.extract({
        fallback: 'style-loader',
        use: ['css-loader']
      }),
    }
  ]
},
```

在这个例子中，我们使用了 rules 配置处理样式文件的一组 loader。在配置中用 test 属性匹配应用这组 loader 的 .css 后缀的样式文件，然后 use 属性配置了处理样式文件的 style-loader 和 css-loader 两个 loader 的信息和执行顺序。

我们可以看一个比较复杂的例子：

```javascript
module: {
  // 配置 loader 规则
  rules: [
    {
      // 命中 JavaScript 文件
      test: /\.js$/,
      
      // 用 babel-loader 转换 JavaScript 文件
      // ?cacheDirectory 表示传给 babel-loader 的参数，用于缓存 babel 编译结果加快重新编译速度
      use: ['babel-loader?cacheDirectory'],
      
      // 只命中src目录里的js文件，加快 Webpack 搜索速度
      include: path.resolve(__dirname, 'src')
    },
    {
      // 命中 SCSS 文件
      test: /\.scss$/,
      
      // 使用一组 Loader 去处理 SCSS 文件。
      // 处理顺序为从后到前，即先交给 sass-loader 处理，再把结果交给 css-loader 最后再给 style-loader。
      use: ['style-loader', 'css-loader', 'sass-loader'],
      
      // 排除 node_modules 目录下的文件
      exclude: path.resolve(__dirname, 'node_modules'),
    },
    {
      // 对非文本文件采用 file-loader 加载
      test: /\.(gif|png|jpe?g|eot|woff|ttf|svg|pdf)$/,
      use: ['file-loader'],
    },
  ]
}
```

### noParse