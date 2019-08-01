# 介绍与安装
webpack 是一个基于Node.js的前端项目构建工具。  
webpack 能够处理js文件的相互依赖关系；  
能把浏览器不能识别的高级语法转化为浏览器能够识别的语法。

    <!--全局安装（推荐）-->
    npm install(i) webpack -g
    
    <!--在项目目录下安装-->
    npm i webpack --save-dev

**用webpack手动搭建项目的步骤**：
1. 创建一个项目文件夹，执行 `npm init -y` 命令
2. 在根目录下新建 src 文件夹和 dist 文件夹，在 src 中写代码
3. 在根目录中新建 webpack.config.js 配置文件，配置入口文件 main.js 和出口文件 bundle.js
4. 执行 `webpack` 命令打包编译后，可以在浏览器中运行项目
5. 安装 webpack-dev-server 实现自动打包编译，项目可以在 http://localhost:8080 上运行
6. 安装 html-webpack-plugin ，在内存中生成一个首页文件，并自动把打包好的 bundle.js 引入到页面中。



---
# 目录结构
- ### dist
  存放打包发布后的代码

- ### node_modules
  项目所需的依赖

- ### src  
    - css
    - images
    - js
    - index.html
    - main.js --- js入口文件

- ### package.json
  项目配置文件  
  

- ### webpack.config.js
  webpack配置文件，这个js文件通过Node中的模块操作，向外暴露一个对象。


### webpack.config.js
```
var path = require('path')

module.exports = {
    entry: path.join(__dirname, './src/main.js'),
    //项目入口文件，webpack 要打包的文件
    
    output: {//输出文件的相关配置
        path: path.join(__dirname, './dist'),
        //打包好的文件输出的路径
        filename: 'bundle.js'
        //输出的文件名
    }
}    
```
配置好这个文件之后，在终端执行 `webpack` 命令，就可以打包项目了。

### package.json
```
{
    
}
```


---
# 相关插件
### webpack-dev-server
使用这个插件可以实现webpack实时打包编译功能，不需要每次修改代码后都执行webpack命令进行打包操作。

1. 执行 `npm i webpack-dev-sever -D` 命令，将这个工具安装到项目的本地开发依赖

2. 由于本地安装的工具 不能在终端中直接通过命令使用，因此需要在 package.json 文件中配置 scripts 对象：
- `--open` 可以在执行完命令后自动打开浏览器预览
- `--port 3000` 可以指定端口号
- `--contentBase src` 指定 src 为启动的根目录
- `--hot` 热重载，避免每次打包都生成一个bundle.js，同时实现页面无刷新的情况下渲染新的样式
> package.json
```
{
    scripts:{
        "dev": "webpack-dev-server --open --port 3000 --contentBase src --hot"
    }
}
```
也可以通过 webpack.config.js 中的 devServer 节点来配置 （不推荐）：
> webpack.config.js

```
const webpack = require('webpack')

module.exports = {
    entry: ,
    output: {
        
    },
    devServer: {
        open: true,
        port: 3000,
        contentBase: 'src',
        hot: true
    },
    plugins:[
        new webpack.HotModuleReplacementPlugin()
        <!--new 一个热更新的模块对象-->
    ]
}
```
之后就可以通过**运行 `npm run dev` 命令**执行打包操作。

3. webpack-dev-server 这个工具依赖于 webpack，如果想要正常运行，要求必须在本地安装webpack：`npm i webpack -D`

4. webpack-dev-server 打包生成的 bundle.js 文件，并没有存放到实际的物理磁盘（dist文件夹）中，而是以一种虚拟的形式，托管到了电脑内存里（项目根目录下），虽然在根目录下看不到这个文件，但是引用的时候要用这个虚拟的 bundle.js 。  
`<script src="/bundle.js"></script>`


### html-webpack-plugin
这个插件的作用是：  
1. 根据指定的模版页面在电脑内存中（项目根目录下）生成一个页面；
2. 自动把打包好的 bundle.js 文件追加到页面中去。

使用方法：
1. 运行 `cnpm i html-webpack-plugin -D` 将插件安装到开发依赖
2. 配置 webpack.config.js
```
var path = require('path');
var htmlWebpackPlugin = requiree('html-webpack-plugin');

module.exports = {
    entry: ,
    output: {
        path: ,
        filename: ''
    },
    plugins:[
        <!--new一个在内存中生成html页面的工具，参数是一个配置对象-->
        new htmlWebpackPlugin({
            <!--指定一个模版页面-->
            template: path.join(__dirname, './src/index.html')，
            <!--指定生成的页面名称-->
            filename: 'index.html'
        })
    ]
}
```
3. 修改 package.json 中的 scripts 节点的 dev 指令为：`"dev": "webpack-dev-server"`，不需要再指定启动的根目录
4. 去掉 index.html 中的 script 标签，因为这个插件会自动把 bundle.js 追加到 index.html 中。

### loader
webpack 默认只能处理 js 文件，如果要处理非JS类型的文件，需要手动安装一些合适的第三方加载器。
1. **安装加载器**
2. **配置 webpack.config.js** 的 module 节点，这个对象上有一个 rules 属性，这个 rules 数组中存放了所有第三方文件的匹配和处理规则。  
3. 加载器后边可以加参数，loader?key=value
```
module.exports = {
    module: {
        rules:[
            {test: /\.css$/, use:['style-loader', 'css-loader']},
            {test: /\.less/, use:['style-loader', 'css-loader', 'less-loader']},
            {test: /\.scss/, use:['style-loader', 'css-loader', 'sass-loader']},
            {test: /\.(jpg|png|gif|bmg|jpeg)$/, use:'url-loader?limit=8000&name=[hash:n]-[name].[ext]'},
            {test: /\.(ttf|eot|svg|woff|woff2)$/, use:'url-loader'},
            {test: /\.js$/, use:'babel-loader', exclude: /node_modules/}
        ]
    }
}
```
- css加载器  
  - 要打包处理css文件，需要安装 `cnpm i style-loader css-loader -D`；  
  - less文件还要安装 `cnpm i less-loader less -D`；  
  - scss文件还要安装 `cnpm i sass-loader node-sass -D`。  
  less-loader 依赖于 less，sass-loader 依赖于 node-sass，因此还要安装相关依赖。  
  use 表示用哪些模块来处理 test 所匹配到的文件。**use 中相关loader模块的调用顺序是从后向前**。

- url加载器  
  要处理图片、字体库等的url路径，需要安装 `cnpm i  url-loader file-loader -D`。url-loader 依赖于 file-loader。  

  **limit** 限制图片大小，单位是byte（字节）。如果引用的图片小于limit的值，则会被转为base64的字符串，如果大于等于limit的值，就不会被转为base64格式。  
  **name** 可以设置图片的名称不被处理成hash值，为了避免出现相同名称的图片，可以在名称前连接一个 n 位的hash值，n 不能超过32。

- 字体库加载器  
  字体库的引入也要用到 url-loader 。

- babel加载器  
  webpack 只能处理一部分ES6的新语法，一些更高级的ES6或ES7的语法，需要借助第三方的loader来处理（如class等语法）。
  1. 安装babel相关的包  
     `cnpm i babel-core bable-loader babel-plugin-transform-runtime -D`  
     `cnpm i babel-preset-env babel-preset-stage-0 -D`
  2. 在 webpack.config.js 中配置匹配规则  
     `{test: /\.js$/, use: 'babel-loader', exclude: /node-modules/}`，注意要通过 exclude 将 node_modules 目录排除掉，否则 babel 会将 node_modules 中的所有js文件打包编译，导致项目无法运行。
  3. 在项目根目录下新建一个 .babelrc 的配置文件，这个文件属于 json 格式，因此在书写的时候必须符合json的语法规范。
     > .babelrc

     ```
     {
         "presets": ["env", "stage-0"],
         "plugins": ["transform-runtime"]
     }
     ```


---
# 命令
    npm init -y
npm初始化，在根目录下生成一个package.json文件。 加上 -y 表示所有参数信息使用默认回答，不用频繁地敲 yes。

    webpack 入口文件路径 输出文件路径
wepbpack 打包单个文件。一般通过 webpack.config.js 来配置。

    npm i xxx -s
=>npm i  --save，写入到 dependencies 对象（运行依赖）
    
    npm i xxx -D
=>npm i  --save-dev，写入到 devDependencies 对象（开发依赖）
