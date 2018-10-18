## 模块（module）

在 `Webpack` 配置中，`module` 是一个单独的配置项，用于配置如何处理模块。在 `module` 有两大属性：一个是 `rules` ，是用来配置 `loader` 处理文件的，另一个是 `noParse` 属性，该属性的配置可以让 `Webpack` 忽略对部分没采用模块化的文件进行递归解析和处理。

接下来，我们将详细介绍这两个属性的配置信息。

### rules

`rules` 配置模块的读取和解析规则，通常用来配置 `loader`。其类型是一个数组，数组里每一项都描述了如何处理部分文件的规则。这些规则能够对「_模块(module)_」应用 `loader`，或者修改「_解析器(parser)_」。配置一项 `rules` 时大致通过以下方式：

- 1、条件匹配：通过 `test` 、`include` 、`exclude` 三个配置项来命中 `loader` 要应用规则的文件。
- 2、应用规则：对匹配的文件通过 `use` 配置项来应用 `loader`，可以只应用一个 `loader` 或者按照从后往前的顺序应用一组 `loader`，同时还可以分别给 `loader` 传入参数。
- 3、应用顺序：一组 `loader` 的执行顺序默认是从右到左执行，通过 `enforce` 选项可以让其中一个 `loader` 的执行顺序放到最前或者最后。

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

在这个例子中，我们使用了 `rules` 配置处理样式文件的一组 `loader`。在配置中用 `test` 属性匹配应用这组 `loader` 的 `.css` 后缀的样式文件，然后 `use` 属性配置了处理样式文件的 `style-loader` 和 `css-loader` 两个 `loader` 的信息和执行顺序。

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
当 `loader` 传参比较多的时候，直接跟在 `loader` 后面就显得格外不方便和难维护，最好是通过 `Object` 对象的形式进行处理，例如下面的形式：

```
use: [
  {
    loader:'babel-loader',
    options:{
      cacheDirectory:true,
    },
    
    // enforce: 'post' 的含义是把该 loader 的执行顺序放到最后
    // enforce 的值还可以是 'pre'，代表把 loader 的执行顺序放到最前面
    enforce:'post'
  },
  // 省略其它 Loader
]
```

上面的例子中 `test` `include` `exclude` 这三个命中文件的配置项只传入了一个字符串或正则，其实它们还都支持数组类型，使用如下：

```javascript
{
  test:[
    /\.jsx?$/,
    /\.tsx?$/
  ],
  include:[
    path.resolve(__dirname, 'src'),
    path.resolve(__dirname, 'tests'),
  ],
  exclude:[
    path.resolve(__dirname, 'node_modules'),
    path.resolve(__dirname, 'bower_modules'),
  ]
}
```

数组里的每项之间是「或」的关系，即文件路径符合数组中的任何一个条件就会被命中。

### noParse

`noParse` 配置项可以让 `Webpack` 忽略对部分没有采用模块化的文件进行递归解析和处理，或者忽略大型的 `library` 的构建，这样做的好处是能提高构建性能。原因是一些库例如 `jQuery` 、`ChartJS` 它们庞大又没有采用模块化标准，让 `Webpack` 去解析这些文件耗时又没有意义。

`noParse` 是可选的配置项，类型默认支持 `RegExp` 和 `[RegExp]`，从 `Webpack 3.0` 版本开始支持 `Function` 类型参数配置。

```javascript
module.exports = {
  //...
  module: {
    // 正则表达式配置
    noParse: /jquery|lodash/,

    // Function 类型配资，从 webpack 3.0.0 开始
    noParse: function(content) {
      // content 代表一个模块的文件路径
      // 返回 true or false
      return /jquery|lodash/.test(content);
    }
  }
};
```

> 注意被忽略的文件里不应该包含 `import` 、 `require` 、 `define` 等模块化语句，不然会导致构建出的代码中包含无法在浏览器环境下执行的模块化语句。

### Rule.parser

因为 `Webpack` 是以模块化的 `JavaScript` 文件为入口，所以内置了对模块化 `JavaScript` 的解析功能，支持 `AMD`、`CommonJS`、`SystemJS`、`ES6`。 `parser` 属性可以更细粒度的配置哪些模块语法要解析哪些不解析，和 `noParse` 配置项的区别在于 `parser` 可以精确到语法层面，而 `noParse` 只能控制哪些文件不被解析。 

默认的插件解析器选项如下：

```javascript
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.js$/,
        use: ['babel-loader'],
        parser: {
          amd: false,                // 禁用 AMD
          commonjs: false,           // 禁用 CommonJS
          system: false,             // 禁用 SystemJS
          harmony: false,            // 禁用 ES2015 Harmony import/export
          requireInclude: false,     // 禁用 require.include
          requireEnsure: false,      // 禁用 require.ensure
          requireContext: false,     // 禁用 require.context
          browserify: false,         // 禁用特殊处理的 browserify bundle
          requireJs: false,          // 禁用 requirejs.*
          node: false,               // 禁用 __dirname, __filename, module, require.extensions, require.main 等。
          node: {...}                // 在模块级别(module level)上重新配置 node 层(layer)
        }
      }
    ]
  }
}
```

`parser` 里面每个属性的配置项值可设置为：

- 将选项设置为 `false`，将禁用解析器，也就是说默认解析器都是被禁用的。
- 将选项设置为 `true`，或不修改将其保留为 `undefined`，可以启用解析器。

然而，一些解析器(parser)插件可能不光只接收一个布尔值。例如，内部的 `NodeStuffPlugin` 差距，可以接收一个对象，而不是 `true`，来为特定的规则添加额外的选项。

上面就是 `module` 比较常用的配置属性了，欲查看更详细的配置项可以查看 [Webpack 模块（module）](https://webpack.docschina.org/configuration/module/)

下一节：[2.7 模块热替换（hot module replacement）](/di-er-zhang-he-xin-gai-nian/28-mo-kuai-re-ti-huan-ff08-hot-module-replacement.md)




