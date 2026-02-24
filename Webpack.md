```javascript
// 引入 defineConfig 获得类型提示（Vue CLI 新版内置，无需额外安装）
const { defineConfig } = require('@vue/cli-service');
// 引入内置的 terser-webpack-plugin（直接用，无需 npm install）
const TerserPlugin = require('terser-webpack-plugin');

module.exports = defineConfig({
  // 可选：生产环境关闭 sourceMap，避免源码暴露，同时减小打包体积
  productionSourceMap: false,
  // webpack 链式配置（推荐，精准控制生产环境）
  chainWebpack: (config) => {
    // 仅对生产环境生效，开发环境保留所有 console
    if (process.env.NODE_ENV === 'production') {
      // 配置 terser 压缩插件，移除 console 和 debugger
      config.optimization.minimizer('terser').tap((args) => {
        // 扩展 terser 压缩配置
        args[0].terserOptions.compress = {
          ...args[0].terserOptions.compress,
          drop_debugger: true, // 强制移除所有 debugger 语句
          drop_console: true,  // 开启 console 剔除开关
          // 精准控制：仅移除 log/info，保留 error/warn（生产环境排错必备）
          pure_funcs: ['console.log', 'console.info']
        };
        return args;
      });
    }
  }
});
```



#### 4.1 Webpack 基本概念

##### 4.1.1 介绍

- ==在 `webpack` 中一切皆**模块**，如  `css、js` 等==
- 集成了 `Nodejs` 环境（基于 Node 开发）
- 依赖：**`webpack、wbpack-cli`**，通过 `npm webpack` 打包项目
- **Loader**：用于**转换文件**
- **Plugin**：**注入钩子**函数

- **`webpack.config.js`**：`webpack` 配置文件 --> ==注意：`webpack.config.js`需要遵循 `CommonJs` 规范！！！==

#### 4.1 使用 entry 

- `entry`：用于指定打包的入口文件，有三种不同的配置方式

```javascript
// 1. 使用字符串：指定单个入口文件
entry:"./index.js"

// 2. 使用数组形式：将多个入口文件打包为一个文件
entry:['./a.js', './b.js']

// 3. 对象形式：多各不同的入口文件分别打包为对应的文件
entry:{
    a: './a.js',  // 被打包为 a.js
    b: './b.js',  // 被打包为 b.js
}
```

#### 4.2 使用 output

- `output`：用于指定打包的出口，是一个**对象**
  - `filename` 属性：指定打包后的文件名，默认为 `main.js`
    - 如果配置了`entry`打包为多个文件，则需要修改为 `filename：[name].js`
  - `path` 属性：指定打包后的输出路径，默认为  `dist` 目录
  - `clean` 属性：是否自动清理打包目录

```javascript
const path = require('path')

output:{
    // 输出文件名为 'bundle.js'
    filename: 'bundle.js'，
    // 输出路径
    path:path.resolve(__dirname, ',.dist')
}
```

#### 4.3 使用 Loader

- 在 `webpack` 中**==原生只支持解析 `js` 文件==**，因此我们需要通过 `Loader` 来**解析其他类型的文件如 `.css/.vue/.png ...`**
- ==`webpack` 遵循**单一职责**，一个 `loader` 只负责处理一件事==
- 解析 `.css` 所需包： **`style-loader/css-loader`**
- 配置： 

```javascript
module:{
    // rules 用于配置不同的处理方案，每个配置都是一个对象
    rules:[
        {
            // 通过正则表达式去匹配所有的 css 文件
            test: /\.css$/i,
            // 使用 style-loader 和css-loader 进行处理
            // 注意： 必须先 style 才能 css 否则报错！！！
            use:['style-loader', 'css-loader']
        }，
        {
        	test: /\.(png|jpg|jrpg|gif)$/i,
        	// 注意：webpack 原生包含了图片解析的 loader 不需要额外安装，只需要配置 type 即可
        	type:"assets/resource"
        }
    ]
}
```

#### 4.4 使用 Bable

- `Bable` 用于将 ES6+ 的代码转换为低版本代码，用于**处理浏览器兼容问题**
- 注意： ==`Bable` 是 `webpack` 的 `Loader`！！==
- 所需包：`bable-loader @bable/core @bable/preset-env`

```javascript
{
    // 匹配所有以 .js 或者 .mjs 结尾的文件
    test:/\.m?js$/,
    // 排除 node_modules 和  bower_components
    exclude: /(node_modules | bower_components)/,  
    use:{
        // 使用 bable_loader 作为加载器
        loader:"bable_loader",
        options:{
            // 使用 bable 的默认配置(如所需环境等)
            presets:["@bable/preset-env"]
        }
    }
}
```

- ==处理浏览器兼容==：需要配置 `package.json` 文件(可参考[github](https://github.com/browserslist/browserslist))

```javascript
"browserslist": [
    "> 1%", // 代表全球超过1%使用的浏览器
    "last 2 versions", // 所有浏览器兼容到最后两个版本
    "Firefox ESR", // 火狐最新版本
    "Chrome >= 80", // 指定浏览器的版本范围
    "not ie <=8" // 不兼容 ie8 以下的浏览器
  ]
```

#### 4.5 使用 Plugin

- `Plugin` 是用来**扩展 `Webpack` 功能**的如：**代码压缩**、**代码分离**

- 代码压缩插件：`optimize-css-assets-webpack-plugin`
- 代码分离：`mini-css-extract-plugin`、`html-webpack-plugin`
- 配置：

```javascript
// 分离 CSS
const MiniCssExtractplugin = require('mini-css-extract-plugin')
// 压缩 CSS
const OptimizeCSS = require('optimize-css-assets-webpack-plugin')
// 分离 HTML 实现自动引用 
const HTMLPLugin = require('html-webpack-plugin')

plugins:[
    // 分离 CSS
    new MiniCssExtractplugin({
		// 打包后的
        filename:'main.css'
    })，
    // 压缩 CSS
    new OptimizeCSS()，
    // 分离 HTML：在出口文件夹下自动生成 index.html文件
    new HTMLPLugin({
        // 指定 index.html 的标题
        title:"hello world"
        // 参考模板的文件路径
        template: './public/index.html'
    })
]

// 注意：使用 'mini-css-extract-plugin' 后需要修改 rules
module:{
    // rules 用于配置不同的处理方案，如 css js es6 等
    rules:[
        {
            // 通过正则表达式去匹配所有的 css 文件
            test: /\.css$/,
            // 
            use:[{
                loader: MiniCssExtractplugin.loader
            },
                "css-loader"
            ]
        }
    ]
}
```

#### 4.5 使用 DevServer

- `DevServer` 创建**开发服务器**使得我们能够以**服务器**的方式运行网页
- `webpack` 基于 **`WebSocket` 协议**自动刷新网页实现实时预览

- 依赖：`webpack-dev-server`
- 配置：

```javascript
devServer:{
	// 服务器打开目录
	contentBase: path.join(__dirname, 'dist'),
	// 是否进行压缩
	compress: true,
    // 运行端口
	port: 8080,
    // 是否热更新
	hot: true,
    // 是否在服务器启动后自动打开浏览器 
	open: true
}
```

- 修改 `package.json`

```javascript
”scripts“：{
    "dev": "webpack-dev-server "
}
```

#### 4.6 webpack 构建优化

##### 4.6.1 优化构建速度

- 

##### 4.6.2 优化输出质量 -- 压缩文件体积

- 压缩不同的文件资源

  - 压缩 `js`：使用 `terser-webpack-plugin` 压缩
  - 压缩 css：使用 `opimize-css-assets-webpack-plugin` 压缩

  ```javascript
  new OptimizeCssAssetsPlugin({
    assetNameRegExp: /\.optimize\.css$/g,
    cssProcessor: require('cssnano'),
    cssProcessorPluginOptions: {
      preset: ['default', { discardComments: { removeAll: true } }],
      //autoprefixer: { browsers: CSS_BROWSERS }, 也是可以指定前缀的
    },
    canPrint: true
  })
  ```

  - 压缩图片：使用 `image-webpack-loader` 压缩
  - gzip 压缩：使用 `compression-webpack-plugin` 压缩

- 通过 `Tree Shaking` 剔除无用 `js` 代码
