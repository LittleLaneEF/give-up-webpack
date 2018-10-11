## 输出（Output）

前一小节我们详细的介绍了 Webpack 入口（Entry）的概念和用法。Webpack 的主要功能是源代码编译打包，那么就肯定会有代码文件的输出。这一小节我们就来详细的了解 Webpack 输出（Output）的概念和用法！

output 属性的配置最主要的作用是告诉 Webpack 在哪里输出它所创建的 bundles，以及按照何种规则命名这些文件。

output 属性的配置是一个 Object 对象。如果在项目中没有显示的配置 output 属性的值，那么它就会启用默认设置 ./dist/main.js，其他生成的文件默认的输出目录就是 ./dist。

output 属性的配置项还是比较多的，下面我们就来分别介绍一下。

### filename

output.filename <String> 属性配置的是输出文件的名称。如果只有一个输出文件，那么就可以把它设置成静态不变的：

```
filename: 'bundle.js',
```

但是如果有多个 Chunk 输出时，就要通过模板和变量进行设置了。比如说前一小节多入口配置会导致 Webpack 输出多个文件，如果要为每个 CHunk 都取对应的名称，就可以进行下面的配置：

```
filename: '[name].js',
```

通过上面的配置，Webpack 根据 [name] 模板变量动态的获取每个入口的配置来命名对应的输出 Chunk 名称。这时你可以把它看作一个字符串模块函数， 每个要输出的 Chunk 都会通过这个函数去拼接出输出的文件名称。

内置变量包括：

| 变量名 | 含义 |
|----------|--------------|
| id | Chunk 的唯一标识，从0开始|
| name | Chunk 的名称 |
| ext | Chunk 的名称后缀 |
| hash | Chunk 的唯一标识的 Hash 值 |
| chunkhash | Chunk 内容的 Hash 值 |

其中 hash 和 chunkhash 默认长度是20位，但是他们的长度是可以指定的，例如：[hash:8] 代表取前8位的 Hash 值，chunkhash 属性雷同。

### chunkFilename

output.chunkFilename 配置无入口的 Chunk 在输出时的文件名称。 chunkFilename 和上面的 filename 非常类似，但 chunkFilename 只用于指定在运行过程中生成的 Chunk 在输出时的文件名称。常见的会在运行时生成 Chunk 场景有在使用 CommonChunkPlugin、使用 import('path/to/module') 动态加载等时。 chunkFilename 支持和 filename 一致的内置变量。

### path

output.path 配置打包输出文件存放的目录，必须是 String 类型的绝对路径，否则 Webpack 会报错退出「The output directory as **absolute path** (required)」。通常我们会通过 Node.js 内置的 path 模块获取绝对路径：

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

这里的 hash 值是编译过程的 hash，如果被打包进来的内容改变了，那么 hash 值也会发生改变。这个可以用于版本回滚。你也可以配置为path.resolve(`./dist/${Date.now()}/`) 方便做持续集成等。

### publicPath



