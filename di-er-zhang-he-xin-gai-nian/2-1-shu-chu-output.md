## 输出（Output）

前一小节我们详细的介绍了 `Webpack` 入口（Entry）的概念和用法。`Webpack` 的主要功能是源代码编译打包，那么就肯定会有代码文件的输出。这一小节我们就来详细的了解 `Webpack` 输出（Output）的概念和用法！

`output` 属性的配置最主要的作用是告诉 `Webpack` 在哪里输出它所创建的 `bundles`，以及按照何种规则命名这些文件。

`output` 属性的配置是一个 `Object` 对象。如果在项目中没有显示的配置 `output` 属性的值，那么它就会启用默认设置 `./dist/main.js`，其他生成的文件默认的输出目录就是 `./dist`。

`output` 属性的配置项还是比较多的，下面我们就来分别介绍一下。

### filename

`output.filename <String>` 属性配置的是输出文件的名称。如果只有一个输出文件，那么就可以把它设置成静态不变的：

```
filename: 'bundle.js',
```

但是如果有多个 `Chunk` 输出时，就要通过模板和变量进行设置了。比如说前一小节多入口配置会导致 `Webpack` 输出多个文件，如果要为每个 `Chunk` 都取对应的名称，就可以进行下面的配置：

```
filename: '[name].js',
```

通过上面的配置，`Webpack` 根据 `[name]` 模板变量动态的获取每个入口的配置来命名对应的输出 `Chunk` 名称。这时你可以把它看作一个字符串模块函数， 每个要输出的 `Chunk` 都会通过这个函数去拼接出输出的文件名称。

内置变量包括：

| 变量名 | 含义 |
|----------|--------------|
| id | Chunk 的唯一标识，从0开始|
| name | Chunk 的名称 |
| ext | Chunk 的名称后缀 |
| hash | Chunk 的唯一标识的 Hash 值 |
| chunkhash | Chunk 内容的 Hash 值 |

其中 `hash` 和 `chunkhash` 默认长度是20位，但是他们的长度是可以指定的，例如：`[hash:8]` 代表取前8位的 `Hash` 值，`chunkhash` 属性雷同。

### chunkFilename

`output.chunkFilename` 配置无入口的 `Chunk` 在输出时的文件名称。 `chunkFilename` 和上面的 `filename` 非常类似，但 `chunkFilename` 只用于指定在运行过程中生成的 `Chunk` 在输出时的文件名称。常见的会在运行时生成 `Chunk` 场景有在使用 `CommonChunkPlugin`、使用 `import('path/to/module')` 动态加载等时。 chunkFilename 支持和 `filename` 一致的内置变量。

### path

`output.path` 配置打包输出文件存放的目录，必须是 `String` 类型的绝对路径，否则 `Webpack` 会报错退出_「The output directory as **absolute path** (required)」_。通常我们会通过 `Node.js` 内置的 `path` 模块获取绝对路径：

```javascript
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve('./dist/[hash:8]/'),
    filename: '[name].js',
  },
};
```

这里的 `hash` 值是编译过程的 `hash`，如果被打包进来的内容改变了，那么 `hash` 值也会发生改变。这个可以用于版本回滚。你也可以配置为`path.resolve(`./dist/${Date.now()}/`)` 方便做持续集成等。

### publicPath

在复杂的项目中可能会对文件进行按需加载或者加载外部资源，加载这些异步资源需要对应 `URL` 地址，此时 `output.publicPath` 是一个重要的选项，稍有不小心就有可能导致资源加载404。

此选项指定在浏览器中所引用的此输出目录对应的公开 `URL`。相对 `URL(relative URL)` 会被相对于 `HTML` 页面（或 `<base>` 标签）解析。相对于服务的 `URL(Server-relative URL)`，相对于协议的 `URL(protocol-relative URL)` 或绝对 `URL(absolute URL)` 也可是可能用到的，或者有时必须用到，例如：当将资源托管到 `CDN` 时。

该选项的值是以 `runtime(运行时)` 或 `loader(载入时)` 所创建的每个 URL 为前缀。因此，在多数情况下，此选项的值都会以 `/` 结束。

此配置项的类型为 `String`，默认值是空字符串 `''`。

简单规则如下：`output.path` 中的 `URL` 以 `HTML` 页面为基准。

```javascript
const path = require('path');

module.exports = {
  //...
  output: {
    path: path.resolve(__dirname, 'public/assets'),
    publicPath: 'https://cdn.example.com/assets/'
  }
};
```

这时发布到线上的 `HTML` 在引入 `JavaScript` 文件时就需要：

```html
<script src='https://cdn.example.com/assets/a_12345678.js'></script>
```

说到这里，你有没有想起之前我们在 [1.6 开发环境 devServer](/di-yi-zhang-ru-men-pei-zhi/16-kai-fa-huan-jing-devserver.md) 里面就有配置过 `publicPath`，那是我们为了使用 `webpack-dev-server` 起一个本地的开发服务，那又是怎么做到的呢？原因是 `webpack-dev-server` 会默认从 `publicPath` 为基准，使用它来决定在哪个目录下启用服务，来访问 `webpack` 输出的文件。

`output.path` 和 `output.publicPath` 都支持字符串模版，内置变量只有一个：`hash` 代表一次编译操作的 `Hash` 值。

```javascript
module.exports = {
  //...
  output: {
    // One of the below
    publicPath: 'https://cdn.example.com/assets/', // CDN（总是 HTTPS 协议）
    publicPath: '//cdn.example.com/assets/', // CDN（协议相同）
    publicPath: '/assets/', // 相对于服务(server-relative)
    publicPath: 'assets/', // 相对于 HTML 页面
    publicPath: '../assets/', // 相对于 HTML 页面
    publicPath: '', // 相对于 HTML 页面（目录相同）
  }
};
```

在编译时(compile time)无法知道输出文件的 `publicPath` 的情况下，可以留空，然后在入口文件(entry file)处使用[自由变量(free variable)](https://stackoverflow.com/questions/12934929/what-are-free-variables) __webpack_public_path__，以便在运行时(runtime)进行动态设置。

```javascript
__webpack_public_path__ = myRuntimePublicPath;

// 应用程序入口的其他部分
```

### crossOriginLoading

`Webpack` 输出的部分代码块可能需要异步加载，而异步加载是通过 JSONP 方式实现的。 [JSONP](https://zh.wikipedia.org/wiki/JSONP) 的原理是动态地向 `HTML` 中插入一个 `<script src="url"></script>` 标签去加载异步资源。`output.crossOriginLoading` 则是用于配置这个异步插入的标签的 `crossorigin` 值。

`script` 标签的 `crossorigin` 属性可以取以下值：

- `anonymous(默认)` 在加载此脚本资源时不会带上用户的 `Cookies`；
- `use-credentials` 在加载此脚本资源时会带上用户的 `Cookies`。
通常用设置 `crossorigin` 来获取异步加载的脚本执行时的详细错误信息。

### libraryTarget 和 library

当用 `Webpack` 去构建一个可以被其他模块导入使用的库时需要用到它们。

- `output.libraryTarget` 配置以何种方式导出库。
- `output.library` 配置导出库的名称。
它们通常搭配在一起使用。

`output.libraryTarget` 是字符串的枚举类型，支持以下配置。

#### var (默认)

编写的库将通过 `var` 被赋值给通过 `library` 指定名称的变量。

假如配置了 `output.library='LibraryName'`，则输出和使用的代码如下：

```javascript
// Webpack 输出的代码
var LibraryName = lib_code;

// 使用库的方法
LibraryName.doSomething();
```

假如 `output.library` 为空，则将直接输出：

```
lib_code
```

其中 `lib_code` 代指导出库的代码内容，是有返回值的一个自执行函数。

#### commonjs

编写的库将通过 `CommonJS` 规范导出。

假如配置了 `output.library='LibraryName'`，则输出和使用的代码如下：

```javascript
// Webpack 输出的代码
exports['LibraryName'] = lib_code;

// 使用库的方法
require('library-name-in-npm')['LibraryName'].doSomething();
```

其中 `library-name-in-npm` 是指模块发布到 `Npm` 代码仓库时的名称。

#### commonjs2

编写的库将通过 `CommonJS2` 规范导出，输出和使用的代码如下：

```javascript
// Webpack 输出的代码
module.exports = lib_code;

// 使用库的方法
require('library-name-in-npm').doSomething();
```

`CommonJS2` 和 `CommonJS` 规范很相似，差别在于 `CommonJS` 只能用 `exports` 导出，而 `CommonJS2` 在 `CommonJS` 的基础上增加了 `module.exports` 的导出方式。

在 `output.libraryTarget` 为 `commonjs2` 时，配置 `output.library` 将没有意义。

#### this

编写的库将通过 `this` 被赋值给通过 `library` 指定的名称，输出和使用的代码如下：

```javascript
// Webpack 输出的代码
this['LibraryName'] = lib_code;

// 使用库的方法
this.LibraryName.doSomething();
```

#### window

编写的库将通过 `window` 被赋值给通过 `library` 指定的名称，即把库挂载到 `window` 上，输出和使用的代码如下：

```javascript
// Webpack 输出的代码
window['LibraryName'] = lib_code;

// 使用库的方法
window.LibraryName.doSomething();
```

#### global

编写的库将通过 `global` 被赋值给通过 `library` 指定的名称，即把库挂载到 `global` 上，输出和使用的代码如下：

```javascript
// Webpack 输出的代码
global['LibraryName'] = lib_code;

// 使用库的方法
global.LibraryName.doSomething();
```

#### libraryExport

`output.libraryExport` 配置要导出的模块中哪些子模块需要被导出。 它只有在 `output.libraryTarget` 被设置成 `commonjs` 或者 `commonjs2` 时使用才有意义。

假如要导出的模块源代码是：

```javascript
export const a=1;
export default b=2;
```

现在你想让构建输出的代码只导出其中的 `a`，可以把 `output.libraryExport` 设置成 `a`，那么构建输出的代码和使用方法将变成如下：

```javascript
// Webpack 输出的代码
module.exports = lib_code['a'];

// 使用库的方法
require('library-name-in-npm')===1;
```

以上只是 `output` 里常用的配置项，还有部分几乎用不上的配置项没有一一列举，你可以在 [Webpack 官方文档](https://webpack.docschina.org/configuration/output/#output-publicpath) 上查阅它们。


