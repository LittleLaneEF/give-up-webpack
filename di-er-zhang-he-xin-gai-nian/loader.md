## loader

Webpack 默认只能对 JavaScript 文件进行打包处理，而在实际的项目中为了达到快速开发，代码高可管理、高可迭代，我们会使用各种各样语言的扩展工具进行开发，比如 TypeScript，React，Angular，Vuejs，Less，Scss，ES6+，ejs 等等。他们的语法和规则都是浏览器所不能直接解析的，以至于我们要通过相关的工具进行转换和处理，毕竟我们最终的代码是要被浏览器解析和运行的。在这里我们肯定要借助 Webpack 进行处理，因为 Webpack 是现阶段前端炙手可热的打包工具嘛。但是 Webpack 本身也是不能直接对这些文件和代码进行处理的。为了解决这样一个问题，官方和社区出现了各种工具和扩展语言的处理工具 -- loader！

loader 主要用于对模块的源代码进行转换。loader 可以使你在 import 或"加载"模块时对文件进行预处理。因此，loader 类似于其他构建工具中“任务(task)”，并提供了处理前端构建步骤的强大方法。loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript，或将内联图像转换为 data URL。loader 甚至允许你直接在 JavaScript 模块中 import CSS 文件！