### webpack 简介

webpack 是一个前端模块化打包工具，主要由入口，出口，loader，plugins 四个部分。

devDependencies 节点下的模块是我们在开发时需要用的，比如项目中使用的 gulp ，压缩 css、js 的模块。这些模块在我们的项目部署后是不需要的，所以我们可以使用 -save-dev 的形式安装



# [import、require、export、module.exports 混合使用详解](https://juejin.im/post/5a2e5f0851882575d42f5609)



### [Webpack4](https://juejin.im/post/5adea0106fb9a07a9d6ff6de)

1. `npm init`

2. `npm i webpack webpack-cli -D`

3. `npx webpack`

   ![1564131436261](assets/1564131436261.png)

   // node v8.2版本以后都会有一个npx
   // npx会执行bin里的文件

   ![1564131385046](assets/1564131385046.png)

   webpack 默认会将src/index.js 作为入口文件，打包至 dist/main.js

4. 创建一个webpack.config.js （默认，可修改）文件来配置项目

   ```js
   module.exports = {
     entry: '',               // 入口文件
     output: {},              // 出口文件
     module: {},              // 处理对应模块
     plugins: [],             // 对应的插件
     devServer: {},           // 开发服务器配置
     mode: 'development'      // 模式配置
   }
   ```

   启动 devServer 需要安装对应的模块

   `npm i webpack-dev-server -D`

   配置文件的入口和出口， 运行`npx webpack`

```js
// webpack.config.js

const path = require('path');

module.exports = {
    entry: './src/index.js',    // 入口文件
    output: {
        filename: 'bundle.js',      // 打包后的文件名称
        path: path.resolve('dist')  // 打包后的目录，必须是绝对路径
    }
}
```

![1564132262316](assets/1564132262316.png)

![1564132286115](assets/1564132286115.png)

上面就是简单的webpack配置了。接下来为了方便，我们来配置一下执行文件

![1564132384107](assets/1564132384107.png)

之后就可以使用 `npm run dev` 或者 `npm run build` 来运行



#### 多入口文件

多入口文件分为两种类型

- 多个入口文件，最终想打包成一个文件

```js
const path = require('path');

module.exports = {
    // 1.写成数组的方式就可以打出多入口文件，不过这里打包后的文件都合成了一个
    entry: ['./src/index.js', './src/js/a.js'],
    output: {
       filename: 'bundle.js',
    }
}
```

![1564132775553](assets/1564132775553.png)

- 每一个文件都单独打包

```js
let path = require('path');

module.exports = {
    // 2.真正实现多入口和多出口需要写成对象的方式
    entry: {
        index: './src/index.js',
        login: './src/login.js'
    },
    output: {
        // 2. [name]就可以将出口文件名和入口文件名一一对应
        filename: '[name].js',      // 打包后会生成index.js和login.js文件
        path: path.resolve('dist')
    }
}

```

![1564133023082](assets/1564133023082.png)

### 配置HTML模板

文件打包功能已经实现了，但是HTML文件怎么引入打包后的文件，我们需要一个html打包功能，可以引用打包好的文件路径。

安装一下这个插件：**html-webpack-plugin**

`npm i html-webpack-plugin -D`

在配置文件中配置该插件

```js
const path = require('path')

const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  // 多个文件打包成一个文件
  entry: ['./src/index.js', './src/js/a.js'],               // 入口文件
  output: {
    filename: 'bundle.[hash4]..js',
    path: path.resolve('dist')
  }, 
  module: {},    // 处理对应模块
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html',
      hash: true
    })
  ],             // 对应的插件
  devServer: {},           // 开发服务器配置
  mode: 'development'      // 模式配置
}
```

![1564134008171](assets/1564134008171.png)

![1564134031586](assets/1564134031586.png)

### 多页面开发配置

```js
const path = require('path')

const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  // 多个文件分别打包
  entry: {
    index: './src/index.js',
    a: './src/js/a.js'
  },
  output: {
    filename: '[name].[hash4].js',
    path: path.resolve('dist')
  },
  module: {},              // 处理对应模块
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html',
      hash: true,
      chunks: ['index']
    }),
    new HtmlWebpackPlugin({
      template: './src/a.html',
      hash: true,
      chunks: ['a']
    })
  ],             // 对应的插件
  devServer: {},           // 开发服务器配置
  mode: 'development'      // 模式配置
}
```

![1564134384515](assets/1564134384515.png)



### CSS 打包配置

页面配置完了，我们还需要配置CSS，这里需要用到loader，loader可以按一定的方式处理对应的文件。

**style-loader  和 css-loader**

**less 和 less-loader**

```
npm i style-loader css-loader -D
// 引入less文件的话，也需要安装对应的loader
npm i less less-loader -D
```



```js
module: {
    rules: [
      {
        test: /\.css$/, // 解析css
        use: ["style-loader", "css-loader"] // 从右向左解析
        /* 
            也可以这样写，这种方式方便写一些配置参数
            use: [
                {loader: 'style-loader'},
                {loader: 'css-loader'}
            ]
        */
      }
    ]
 }, // 处理对应模块
```

```js
// index.js 中引入样式
import './less/style.less';
import './less/style.css';


console.log('hello webpack')
```

