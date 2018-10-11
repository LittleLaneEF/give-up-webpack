## 入口起点（Entry Points）

`Webpack` 构建过程是根据一个文件入口来解析整个项目内部的依赖图，然后根据这个依赖图掌握各个模块的依赖关系，再进行模块拆分、解析、打包的过程。这个入口（Entry）的配置作为 `Webpack` 构建的开始时至关重要的。

我们可以在 `webpack.config.js` 文件的 `entry` 属性中指定一个或多个入口文件。在 `Webpack` 中，默认的构建入口文件时 `./src/index.js`。

> ⚠️ 注意：在项目打包时，可以不用配置 `entry` ，但是你的项目中必须要有 `./src/index.js` 文件，或者你在 `webpack.config.js` 配置 `entry` 指向你的入口文件，否则 ``Webpack 会报错退出！

### 单入口文件（Single Entry Point）

> 语法：entry: String | Array<String> | Object{[entryChunkName: string]: string|Array<string>}

形如我们之前在入门 `Demo` 里面配置的

`webpack.config.js`

```javascript
    module.exports = {
        entry: './src/index.js',
    }
```

对于上面的写法是下面写法的简写方式：

```javascript
    module.exports = {
        entry: {
            main: './src/index.js'
        },
    }
```

或者你也可以写成下面两中形式：

```javascript
    module.exports = {
        entry: {
            main: ['./src/index.js']
        },
    }
```

```javascript
    module.exports = {
        entry: ['./src/index.js'],
    }
```

从上面多种写法来看，`entry` 的配置是非常灵活的！对于上面这么多的写法到底有什么不同，他们的应用场景又是什么的？

下面来说一下上面各种形式写法的区分和应用场景。

#### 字符串形式（String）

形如：

```javascript
    module.exports = {
        entry: './src/index.js',
    }
```

字符串形式的写法只能配置一个入口起点。基本上没有扩展配置的灵活性，但是当你的应用程序或工具库只有一个入口起点时，这样形式的配置是一个很不错的选择。

#### 数组形式（Array<String>）

单个入口形如：

第一种

```javascript
    module.exports = {
        entry: ['./src/index.js'],
    }
```

第二种

```javascript
    module.exports = {
        entry: {
            main: ['./src/index.js']
        },
    }
```

上面两种形式的写法和前面字符串的写法没什么区别，还是以 `./src/index.js` 为单个入口文件，`main.js` 为输出文件名称。第一种形式的写法是第二种写法形式的简写。但是当数组里面的文件不止一个时，那就会有意思了。我们在_多入口文件_具体讲解。

#### 对象形式（Object{[entryChunkName: string]: string|Array<string>}）

形如：

```javascript
    module.exports = {
        entry: {
            main: './src/index.js'
        },
    }
```

```javascript
    module.exports = {
        entry: {
            main: ['./src/index.js']
        },
    }
```

这两种形式的写法都是对象的形式，区别在于属性的值一个是字符串，另一个是数组。当属性值为字符串时，也是指定了单入口文件，当属性值为数组时，可以指定单文件入口，也可以指定多文件入口。当然整个对象添加多个属性时，也是指定了多文件入口。

当对象形式指定的是单文件入口时，就和上面的字符串形式的写法没什么不同了。但是当对象形式指定的是多入口时，和数组形式指定多入口又会有所不同。大家接着往下看！

### 多入口文件（Multiple Entry Points）

```javascript
    module.exports = {
        entry: [
            './src/pageOne/index.js',
            './src/pageTwo/index.js',
            './src/pageThree/index.js'
        ],
    }
```

```javascript
    module.exports = {
        entry: {
            main: [
                './src/pageOne/index.js',
                './src/pageTwo/index.js',
                './src/pageThree/index.js'
            ]
        },
    }
```

```javascript
    module.exports = {
        entry: {
            pageOne: './src/pageOne/index.js',
            pageTwo: './src/pageTwo/index.js',
            pageThree: './src/pageThree/index.js'
        },
    }
```

多入口在实际项目中的使用还是很常见的，特别是我们在对项目打包进行优化时，我们会对项目中公共引用比较多的文件进行抽取，还有进行分包处理使所有包的大小差不多时。多入口的配置形式分以上三种，但是这三种之间也是有区别的。

- 1、第一种写法是第二种写法的简写方式。通过这两种形式的配置进行打包，都会创建多个主入口，但是 `Webpack` 它们的「依赖导向(graph)」到一个 `chunk`，这里是 `main.js`。当你希望将多个文件合并到一个主文件里面时，可以使用这种方式进行打包处理。

- 2、第三种写法适合于多页面的应用程序。上面的形式指定 3 个独立且分离的依赖图给 `Webpack`。`Webpack` 在进行打包时就会根据三个主入口进行模块依赖图的解析，最终形成三个独立的依赖图。通过这种方式可以使多页应用的代码能够大量的复用。当你需要对引用程序的 `bundle` 进行抽离时，这种形式的配置可能会派上大的用场。

### 入口文件的查找

看到这里，可能大家会产生一个疑问：在配置入口起点的时候，我们配置的都是相对路径的字符串形式，那 `Webpack` 它是怎么查找这些目录文件的呢？它又是怎么知道这个目录文件就是项目中的目录文件？

#### context

`context` 解释是上下文的意思。在 `Webpack` 中，`context` 是编译的基础目录，入口起点文件额查找就是基于此目录的。如果开发者或者配置人员没有在配置文件中显示的指定 `context` 的值，`Webpack` 就会启用默认的配置信息：

```javascript
this.set("context", process.cwd());
```

`process.cwd()` 即 `Webpack` 运行所在的目录（等同于 `package.json` 所在目录）。

如果你要修改 `context` 配置信息，那么在配置时一定要指定绝对路径。例如如果你需要指定 `Webpack` 编译的基础目录为 `src`，那你应该这样配置：

```json
context: path.resolve('./src),
entry: 'main.js',
```
此时所有的配置相对的目录就是 `src` 目录了，所以 `entry` 的配置就不能是 `./src/main.js` 。

** context 有什么实际作用？**官方文档的解释是：它可以使你的配置独立于工程目录 「This makes your configuration independent from CWD (current working directory)」。

**怎么理解？**举个例子，在分离开发生产配置文件时候，一般把 `webpack.config.js` 放到 `build` 文件夹下，此时 `entry` 却不用考虑相对于 `build` 目录来配置，仍然要相对于 `context` 来配置，这也就是所谓的独立于工程目录。

需要注意的是，`output` 的配置项和 `context` 没有关系，但是有些插件的配置项和 `context` 有关，下一小节会说明。

下一节：[输出（Output）](/di-er-zhang-he-xin-gai-nian/2-1-shu-chu-output.md)


















