在上一章节中，我们从简单到复杂的入门了 Webpack 打包环境的配置，大家都实操了吗？

在本大章中，我们将会学习到 Webpack 几大核心的概念。虽然在上一章节中的入门配置中，我们也提及了相关设计到的概念知识，但是那都是一笔带过而已！接下来，我们将分如下八个小节讲解 Webpack 的核心概念：

- [2.1 入口（Entry）](/di-er-zhang-he-xin-gai-nian/2-1-ru-kou-entry.md)：指定 Webpack 打包时，解析模块开始的入口文件。可以配置单一或多个入口。
- [2.2 输出（Output）](/di-er-zhang-he-xin-gai-nian/2-1-shu-chu-output.md)：该属性告诉 Webpack 在哪里输出它所创建的 bundles，以及如何命名这些文件。
- [2.3 模式（mode）](/di-er-zhang-he-xin-gai-nian/2-5-mo-shi-mode.md)：指定 Webpack 打包时的环境，每个环境对应着不同的优化配置。
- [2.4 loader](/di-er-zhang-he-xin-gai-nian/loader.md)：由于 Webpack 默认只能处理 JavaScript 文件，loader 能够让 Webpack 处理那些非 JavaScript 文件，并且先将它们转换为有效模块，然后添加到依赖图中供应用程序使用。
- [2.5 插件（Plugin）](/di-er-zhang-he-xin-gai-nian/2-4-cha-jian-plugins.md)：通过配置不同的插件来达到 Webpack 打包时的优化、资源管理和注入环境变量等功能。
- [2.6 模块（module）](/di-er-zhang-he-xin-gai-nian/26-mo-kuai-ff08-module.md)：模块是 Webpack 打包的基础，也是应用开发功能基本思想。
- [2.7 代码块（Chunk）](/di-er-zhang-he-xin-gai-nian/2-6-dai-ma-kuai-chunks.md)：Chunk 对分包优化有着至关重要的作用。
- [2.8 模块热替换（hot module replacement）](/di-er-zhang-he-xin-gai-nian/28-mo-kuai-re-ti-huan-ff08-hot-module-replacement.md)：模块热替换在开发环境起着非常重要的作用，它会实现应用程序运行过程中替换、添加或删除模块，而无需重新加载整个页面。