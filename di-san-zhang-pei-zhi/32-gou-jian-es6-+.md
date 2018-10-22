## 3.2 构建 ES6+

`ES` 的全称是 `ECMAScript`。

`ECMAScript` 是一种由`Ecma国际`（前身为欧洲计算机制造商协会,英文名称是European Computer Manufacturers Association）通过`ECMA-262标准化`的脚本程序设计语言。

`ES6` 是 `ECMAScript` 的第六个版本，在 2015 年正式发布，又称 `ECMAScript2015`。

`ES6+` 指的是 `ECMAScript` 第六个版本及以后版本的统称。`ES6+` 在 `JavaScript` 中引入了新的语法和 `API` 来提升开发效率。

虽然 `ES6+` 语法更易于编写高效的代码，但是现在部分浏览器和 `Node.js` 环境的支持情况还是有限，普遍对 `ES5` 的支持度还是很高的，所以在实际项目中我们会使用 `ES6` 语法，但是最终会用工具将其转换成普遍支持的 `ES5` 语法。

### Babel

`Babel` 是一个 `JavaScript` 编译器，能将 `ES6` 语法的代码转为 ES5 语法的代码，让你在开发的时候使用最新的语言特性而不用担心兼容性问题，并且可以通过插件机制根据需求灵活的扩展。在 `Babel` 执行编译的过程中，会从项目根目录下的 `.babelrc` 或者 `.babelrc.js` 文件读取配置。`.babelrc` 是一个 `JSON` 格式的文件，`.babelrc.js` 是符合 `Node.js` 模块化的 `JavaScript` 文件。配置内容大致如下：

```json
{
  "plugins": [
    [
      "transform-runtime",
      {
        "polyfill": false
      }
    ]
   ],
  "presets": [
    [
      "es2015",
      {
        "modules": false
      }
    ],
    "stage-2",
    "react"
  ]
}
```

或者

```javascript
module.exports = {
  "plugins": [
    [
      "transform-runtime",
      {
        "polyfill": false
      }
    ]
   ],
  "presets": [
    [
      "es2015",
      {
        "modules": false
      }
    ],
    "stage-2",
    "react"
  ]
}
```

#### Plugins

`plugins` 属性可以配置多组插件信息，这些插件信息告诉 `Babel` 如何转换代码。比如说上面配置的 `transform-runtime` 信息对应的就是 `babel-plugin-transform-runtime` 插件，由于插件不是 `Webpack` 内置的，所以需要通过 `yarn` 或者 `npm` 单独进行安装。

`babel-plugin-transform-runtime` 是 `Babel` 官方提供的一个插件，作用是减少打包生成代码的冗余。`Babel` 在把 `ES6` 代码转换成 `ES5` 代码时，通常需要一些 `ES5` 写的辅助函数来完成新语法的实现，例如在转换 `class extent` 语法时会在转换后的 `ES5` 代码里注入 `_extent` 辅助函数用于实现继承：

```javascript
function _extent(target) {
  for (var i = 1; i < arguments.length; i++) {
    var source = arguments[i];
    for (var key in source) {
      if (Object.prototype.hasOwnProperty.call(source, key)) {
        target[key] = source[key];
      }
    }
  }
  return target;
}
```

这会导致每个使用了 `class extent` 语法的文件都被注入重复的 `_extent` 辅助函数代码，`babel-plugin-transform-runtime` 的作用在于不把辅助函数内容注入到文件里，而是注入一条导入语句： 

```javascript
var _extent = require('babel-runtime/helpers/_extent');
```

这样能减小 `Babel` 编译出来的代码的文件大小。

同时需要注意的是由于 `babel-plugin-transform-runtime` 注入了 `require('babel-runtime/helpers/_extent')` 语句到编译后的代码里，需要安装 `babel-runtime` 依赖到你的项目后，代码才能正常运行。也就是说 `babel-plugin-transform-runtime` 和 `babel-runtime` 需要配套使用，使用了 `babel-plugin-transform-runtime` 后一定需要 `babel-runtime`。

#### Presets

`presets` 属性告诉 `Babel` 要转换的源码使用了哪些新的语法特性，一个 `Presets` 对一组新语法特性提供支持，多个 `Presets` 可以叠加。`Presets` 其实是一组 `Plugins` 的集合，每一个 `Plugin` 完成一个新语法的转换工作。`Presets` 是按照 `ECMAScript` 草案来组织的，通常可以分为以下三大类：

已经被写入 ECMAScript 标准里的特性，由于之前每年都有新特性被加入到标准里，所以又可细分为：

es2015 包含在2015里加入的新特性；
es2016 包含在2016里加入的新特性；
es2017 包含在2017里加入的新特性；
env 包含当前所有 ECMAScript 标准里的最新特性。
它们之间的关系如图：










