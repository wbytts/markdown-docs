# Webpack 介绍

webpack是一种前端资源构建工具，一个静态模块打包器（module bundle）
在webpack看来，前端所有的资源文件都会作为模块处理，它将根据模块的依赖关系进行静态分析，打包生成对应的静态资源（bundle）

webpack可以看作是模块打包机：他做的事情是，分析你的项目结构，找到js模块以及其他的一些不能直接解析的扩展资源，将其大包围合适的格式供使用
可以做的事情：代码转换，文件优化，代码分割，模块合并，自动刷新，代码校验，自动发布等等

# Webpack打包命令的使用

安装Webpack（也可以直接使用 `npx webpack`）：
```cmd
npm install webpack webpack-cli -D
```

## 零配置打包方式

默认的打包入口：`./src/index.js`
默认的输出文件：`./dist/main.js`

自己试着编写一下 `./src/index.js` ，写点代码
然后执行打包命令：`npx webpack`，查看 `./dist/main.js` 的输出文件

> 说明：Webpack默认只能打包和分析 js 和 json 格式的文件，如果要处理其他文件，需要安装或编写对应的 loader或者插件

## 文件监听

当源码发生变化时，webpack可以自动重新构建出新的输出文件

webpack开启监听模式，有两种方式：
- 在启动命令上添加 `--watch` 参数
- 在配置里添加 `watch: true` （默认是false，也就是不开启）

`watchOptions` 选项：
- `ignored: /node_modules/`,   默认为空，不监听的文件或者文件夹，支持正则匹配
- `aggregateTimeout: 300`,   监听到变化后会等待一段时间再去执行，默认是 300毫秒
- `poll: 1000`,   判断文件是否发生变化是通过不停询问系统指定文件有没有变化实现的，默认每秒问 1000 次

## 配置 npm scripts

将打包命令配置在 `package.json` 的 scripts选项里，通过 `npm run xxx` 的方式执行
```json
"scripts": {
  "build": "webpack",
  "build:watch": "webpack --watch"
},
```

接下来打包只需要执行：`npm run build` 就好了

可以通过在 `npm run build` 命令与参数之间添加两个连接符的方式向 webpack 传递自定义参数，例如：`npm run build -- --color`

# 编写 webpack 配置文件

## 配置文件结构

webpack配置文件默认在项目根目录，名称为 `webpack.config.js`

```js
// 导出一个对象，配置的内容写在这个对象里面
module.exports = {
	// 配置的具体内容
};
```

配置文件运行在 node.js 环境下，所以导入其他包需要使用 require，导出对象需要用 `module.exports`

配置项的基本说明：
```js
const path = require('path');

// 导出配置对象
module.exports = {
  // 环境
  mode: 'development',
  // 打包的入口文件
  entry: './src/index.js',
  // 打包的输出
  output: {
    filename: 'main.js', // 打包输出的文件名
    path: path.resolve(__dirname, 'dist'), // 打包文件放的路径
  },
  // 模块配置
  module: {
    // Loader配置
    rules: []
  },
  // 插件配置
  plugins: [],
  // 开发服务器配置
  devServer: {
    port: 3000,
    static: 'public'
  },
};
```

## 打包入口 entry

## 打包输出 output

# 样式处理

// ......

## CSS基本处理

安装 loader：
```cmd
npm install -D style-loader css-loader
```

基本配置：
```js
// 处理css资源
{
  // 所有以 .css 结尾的文件，都将被提供给 style-loader 和 css-loader
  test: /\.css$/i,
  // 注意：css-loader 在后，style-loader 在前
  // css-loader：
  // style-loader：含有 CSS 字符串的 <style> 标签，将被插入到 html 文件的 <head> 中
  use: ['style-loader', 'css-loader'],
},
```

`css-loader`：用于加载处理css文件，并且转换成 commonjs对象
`style-loader`： 将样式通过style标签插入到head标签中

## 处理 LESS

安装less编译工具和less文件的loader
```cmd
npm install less less-loader --save-dev
```

修改配置：
```js
{
  test: /\.less$/i,
  loader: [
    // compiles Less to CSS
    'style-loader',
    'css-loader',
    'less-loader',
  ],
},
```

## 处理 SASS/SCSS

## 处理 Stylus

# 文件指纹

# Source Map
提供了一种源代码到构建后代码映射的技术
如果构建后的代码出错了，可以反向追踪找到源代码中对应的位置

使用：webpack.config.js中添加
```js
devtool: 'source-map'
```

source-map取值：`[inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map`

选项解释：
- inline：将 .map 作为 DataURI嵌入，不单独生成 .map 文件
- eval：使用eval包裹模块代码
- cheap：不包含列信息
- module：包含loader的sourcemap
- source-map：产生 .map 文件
- hidden：
- nosources：

| devtool | 首次构建 | 二次构建 | 是否适合生产环境 | 可以定位的代码 |
| --- | --- | --- | --- | --- |
| 无 | +++ | +++ | yes | 最终输出的代码 |
| eval | +++ | +++ | no | webpack生成的代码（一个个的模块） |
| cheap-eval-source-map | + | ++ | no | 经过loader转换后的代码（只能看到行） |
| cheap-module-eval-source-map | o | ++ | no | 源代码（只能看到行） |
| eval-source-map | -- | + | no | 源代码 |
| cheap-source-map | + | o | yes | 经过loader转换后的代码（只能看到行） |
| cheap-module-source-map | o | - | yes | 源代码（只能看到行） |
| inline-cheap-source-map | + | o | no | 经过loader转换后的代码（只能看到行） |
| inline-cheap-module-source-map | o | - | no | 源代码（只能看到行） |
| source-map | -- | -- | yes | 源代码 |
| inline-source-map | -- | -- | no | 源代码 |
| hidden-source-map | -- | -- | yes | 源代码 |

# 处理HTML

在 src文件夹添加HTML模板：`./src/index.html`


安装插件：
```cmd
npm install html-webpack-plugin -D
```
> 详细的配置项可以查看插件文档：https://github.com/jantimon/html-webpack-plugin#options

修改配置：
```js
// 处理HTML资源：npm install html-webpack-plugin -D
const HtmlWebpackPlugin = require('html-webpack-plugin');

// ======================================================================

// plugin 配置，插件的配置
plugins: [
    // 默认会创建一个HTML文件，自动引入打包输出的所有资源(无配置时）
    new HtmlWebpackPlugin({ // html-webpack-plugin
        // 使用指定的html文件作为基础模板(不要手动再引入资源了)，不再创建一个新的文件
        template: './src/index.html'
    }),
],
```

# 构建时服务

## webpack-dev-server
安装：`npm install -D webpack-dev-server`

WDS不输出文件，而是放在内存中

常用配置：
```js
// 开发服务器配置
devServer: {
    // 上下文路径
    contentBase: path.resolve(__dirname, "build"),
    // gzip 压缩
    compress: true,
    // 进度条
    progress: true,
    // 服务端口
    port: 3000,
    // 是否自动打开浏览器
    open: false,
    // 是否开启热更新（需要配置 webpack.HotModuleReplacementPlugin 插件）
    hot: true,
}
```

使用：`npx webpack-dev-server`   （webpack-cli 3）
如果安装的是新版本的webpack-cli(4或5) 要使用：`npx webpack serve` 来运行

> 其实，直接运行 npx webpack serve 的话，如果没有安装 webpack-dev-server，会提示你进行安装的

### 常见错误

#### `Error: Cannot find module 'webpack-cli/bin/config-yargs'`

最新版本的 webpack-cli 和webpack-dev-server 有问题，可以安装3版本的 webpack-cli
```js
npm install webpack-cli@3 -D
```


## 热更新 webpack.HotModuleReplacementPlugin

## webpack-dev-middleware

# 多页面打包

# SSR配置

# 其他插件
