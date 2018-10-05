在节目开始之前，我们希望你了解一下节目的背景。对于里面的一下知识如果有不了解的，希望下去后自行理解，否则你更本不知道节目将的是什么。

#### node 环境依赖

`node` 环境依然成为前端开发必须依赖的环境了，如果你电脑上还没有安装 `node` 环境那就赶紧加快脚步吧！安装步骤和普通的软件安装差不多，不知道的可以去[百度](https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&rsv_idx=2&tn=baiduhome_pg&wd=node%20&rsv_spt=1&oq=webpack&rsv_pq=94234cc300038cbe&rsv_t=cfc0c7eVE6pzn7akl7xgYp1Wh5J5x1E69zmm2nA7k7PuuXJ8%2FSnnOzC76Fzf70dw99v9&rqlang=cn&rsv_enter=1&inputT=2791&rsv_sug3=14&rsv_sug1=12&rsv_sug7=100&rsv_sug2=0&rsv_sug4=2791&rsv_sug=1)，我在这里就不赘述了。

如果有同学想要在一台主机上管理多版本的 `node` ，强烈推荐 [nvm](https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&rsv_idx=2&tn=baiduhome_pg&wd=nvm&rsv_spt=1&oq=mvn&rsv_pq=81173879000114b9&rsv_t=1ac3WezslGTE4zQOs9nj4sOS2%2F%2BP9wvaj4wxv2rN95zWtxtTM0f24LWYoTDJtOnto%2FCd&rqlang=cn&rsv_enter=1&inputT=783&rsv_sug3=19&rsv_sug1=16&rsv_sug7=100&bs=mvn)，随意安装、切换 `node` 版本，不要太方便！

大家安装好 `node` 后，在终端或控制台执行 `node -v` 已检查 `node` 环境是否安装成功！

#### npm 包管理工具

`npm` 是什么？npm 是 `JavaScript` 包管理工具，来自全世界的开发者在这里互相分享与借鉴。在 `npm` 大世界里面<span data-type="color" style="color:rgb(51, 51, 51)"><span data-type="background" style="background-color:rgb(255, 255, 255)">每星期大约有 30 亿次的下载量，包含超过 600000 个包，可想而知其应用范围有多广！</span></span>

大家不用担心 `npm` 怎么安装，他是集成在 `node` 里面的，随着 `node` 的安装而安装！

如果大家安装了 `node` ，不妨在终端或控制台执行 `npm -v` 试试，看打印出的版本号是多少！

这里推荐一个 `npm registry` 管理工具 - [nrm](https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&rsv_idx=2&tn=baiduhome_pg&wd=nrm&rsv_spt=1&oq=markdown%2520%25E5%25B1%2585%25E5%258F%25B3&rsv_pq=b2526721000408e0&rsv_t=dbccy5%2BbdSNM0mrVUWfuLddUwjjRxklHI0%2BwiTGFwtSaUl9dL1i3axtDozTvsLQGpO2J&rqlang=cn&rsv_enter=1&inputT=1323&sug=markdown&rsv_sug3=131&rsv_sug1=99&rsv_sug7=100&rsv_sug2=0&rsv_sug4=1323)。`nrm` 和 `nvm` 有点功能类似，可以很方便的查看、添加、修改、切换 `npm registry`。

到这里两个必须的工具就安装好了！

#### yarn 包管理工具

由于 `npm` 的服务器是搭建在国外的。对于国内的开发者来说，网络慢、下载速度慢是非常痛苦的事情，为此淘宝搭建了供国内开发者使用的 `cnpm` 淘宝镜像。`cnpm` 下载包文件挺快的，但是感觉下载依赖包文件有些问题，所以我个人不喜欢使用。

由于 `npm` 下载依赖包文件的种种诟病，国外人有构建了快速、可靠、安全的依赖管理工具 - `yarn`。`yarn` 的使用和 `npm` 差不多，我这里就不介绍了，有兴趣的可以去[搜索](https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&rsv_idx=2&tn=baiduhome_pg&wd=yarn&rsv_spt=1&oq=cnpm&rsv_pq=9c3b42880004efd0&rsv_t=5deed8kUI6bL42Ytde%2B3FywZKdK3q3qCcYCCRcR%2FdP63Zxpz8WdHJDGdttxH02r1SePv&rqlang=cn&rsv_enter=0&inputT=3654&sug=markdown&rsv_sug3=142&rsv_sug4=3654&rsv_jmp=slow)。

我特别喜欢使用 `yarn`，因为它快！

#### webpack 

`webpack` 在 `npm` 上也发布了安装包，所以我们完全可以通过 `npm` 或者 `yarn` 进行下载。

- `Global` 全局安装

```
npm install webpack -g
yarn add webpack -g
```

- `Local` 局部安装

在项目的根目录下局部安装 `webpack` ，前提是项目的根目录下必须有 `package.json` 配置文件也就是由 `npm init` 生成的文件。

```
npm install webpack --save
yarn add webpack --save
```

虽然介绍了以上两种安装方式，但是我们推荐安装到本项目，原因是可防止不同项目依赖不同版本的 `Webpack` 而导致冲突。

##### 附录资料

- `package.json`

`package.json` 是 `npm init` 命令生成的配置文件。

![](/static/package.gif)

```json
{
"name": "test", // 项目的名称
"version": "1.0.0", // 项目版本号
"description": "", // 项目的描述信息
"main": "index.js", // 项目的主文件入口
"scripts": { // 运行项目的 `shell` 命令，我们之后的演示项目会用到

},
"keywords": [], // 项目的关键词
"author": "", // 项目的开发作者
"license": "ISC", // 项目的 `license` 协议，这里我们不做过多的介绍
"devDependencies": { // 项目开发过程中开发环境所需要的依赖包文件

},
"dependencies": { // 项目投产环境所需要的依赖包文件也就是被打包的包文件

}
}
```

这个配置文件可是整个项目的配置和描述文件，无论是普通的项目还是你要发布 `npm` 包文件都是需要的。后续一些项目的配置，我们也会配置在这个文件里面。详细可以看上面给出的注释或者想要查看更详细的配置信息也可以去网上搜索。

- `--save` && `--save-dev`

在介绍这两个命令参数之前，我们先回顾上面介绍 `package.json` 的内容：我们说 `package.json` 文件是我们通过在项目根目录运行 `npm init` 生成的，在配置文件里面有两个依赖的对象，一个是 `devDependencies`（项目开发过程中开发环境所需要的依赖包文件
），另一个是 `dependencies`（项目投产环境所需要的依赖包文件也就是被打包的包文件
）。其实在有些项目中除了上面两个配置对象外还会有一个 `optionalDependencies` 配置对象。

那这三个对象我们是怎么区分的呢？

> `devDependencies`：我们给出的注释是项目开发过程中所依赖的包文件。参与过项目开发的同学一定会知道，我们在开发过程中会启动一个开发的服务。在这个服务里我们的代码也会通过一些工具转换和编译，做转换和编译的这些工具包文件我们是不需要打入到最终代码里面的，我们就成这些包文件为开发包文件。我们可以手动配置包文件信息到 `devDependencies` 对象下进行安装，也可以在 `npm install` 或 `yarn add` 后加 `--save-dev` 来指定包文件的安装信息配置在 `devDependencies` 对象下。我推荐命令安装的形式。

> `dependencies`：项目投产环境所需要的依赖包文件也就是被打包的包文件。我们开发时编写的代码有很多是浏览器无法直接识别的，在投产过程中我们会使用很多工具对源代码进行转换和编译。工具转换的源代码又使用了很多框架、类库，这些框架、类库安装的配置信息就应该在 `dependencies` 下，这样 `Webpack` 在 `build` 过程中查找包文件就会便捷很多。所以说这个对象配置的包文件就是投产环境所需要的依赖包文件。

> `optionalDependencies`：根据名称可以看出，这个对象下配置的包文件信息在项目中是可选的。在我们添加项目的测试用例时，有时为了做模拟浏览器事件的测试我们会安装 `puppeteer`，他只会在我们运行测试用例的时候用到，其他什么开发呀，源代码 `build` 呀都用不到，所以是可选的，我们就将安装信息配置在这个参数对象下。


外链资料：

* [node 官网](https://nodejs.org/zh-cn/)
* [npm 官网](https://www.npmjs.com/)
* [yarn 官网](https://yarn.bootcss.com/)
* [webpack 英文版官网](https://webpack.js.org/)
* [webpack 中文版官网](https://webpack.docschina.org/)
* [license 协议](http://www.ruanyifeng.com/blog/2011/05/how_to_choose_free_software_licenses.html)