## 前言

学习了`webpack`之后，将自己的博客的前端进行重构，由于自己的博客是多页应用，所以研究了下多页应用的打包方案。在这里把最后配置的成果分享下，不足之处，请指正。（文字不多，全是代码，不是配置教程，所以没有特别详细的写，只是一个参考）

项目地址：[https://github.com/Ray-daydayup/MPA-webpack](https://github.com/Ray-daydayup/MPA-webpack)

## 文件目录结构

### 项目目录结构

首先先看下我的项目的目录结构

```
myblog-webpack
 ├── dist                     // 打包输出文件夹
 ├── src                      // 源代码
 │   ├── api                  // 请求文件夹，存放封装的请求方法
 │   ├── assets               // 静态资源文件夹
 │   ├── lib                  // 一些库
 │   ├── pages                // 页面
 │   │   ├── about            // 页面名称
 │   │   │   ├── index.html   // html模板
 │   │   │   └── index.js     // 入口js
 │   │   ├── category
 │   │   │   ├── index.html
 │   │   │   └── index.js
 │   │   ├── detail
 │   │   │   ├── index.html
 │   │   │   └── index.js
 │   │   ├── index
 │   │   │   ├── index.html
 │   │   │   └── index.js
 │   │   └── tag
 │   │       ├── index.html
 │   │       └── index.js
 │   ├── styles              // 样式文件
 │   └── utils               // 工具函数
 ├── package.json
 ├── README.md
 ├── package-lock.json
 ├── webpack.base.js        // 公共配置
 ├── webpack.dev.js         // 开发环境配置
 └── webpack.prod.js        // 生产环境配置
```

### 打包输出文件目录结构

最终打包输出的目录如下

```
dist
 ├── assets
 ├── css
 │   ├── commons672a1e57.css
 │   └── index6085d612.css
 ├── js
 │   ├── aboutfc723f0e.js
 │   ├── categorye4be3bd6.js
 │   ├── commons78f1dd3f.js
 │   ├── detail0df434c5.js
 │   ├── indexe1e985d9.js
 │   ├── markdown-it
 │   │   ├── highlight.vendors.js
 │   │   ├── markdown-it-integrated.js
 │   │   ├── markdown-it-integrated.min.js
 │   │   ├── markdown-it-integrated.min.js.LICENSE.txt
 │   │   └── markdown-it.vendors.js
 │   └── tagf1c1035c.js
 ├── about.html
 ├── category.html
 ├── detail.html
 ├── favicon.ico
 ├── index.html
 └── tag.html
```

## `webpack`配置文件

**注意：文中各部分所需的包名，并没有书写，请在附录中查找！！！相关配置的意义可以查找官方文档**

### 公共配置

#### 动态获取`entry`和设置`html-webpack-plugin` 

多页应用的打包思路是**每个页面对应一个`entry`，一个`html-webpack-plugin`**，但每次新增或删除页面需要改`webpack`配置，所以需要根据文件目录动态获取入口，来设置`html-webpack-plugin`。

利用一个库[`glob`](https://www.npmjs.com/package/glob)来实现对文件目录的读取
具体代码如下，相关配置的意义可以查找官方文档

```js
const path = require('path')
const glob = require('glob')
const HtmlWebpackPlugin = require('html-webpack-plugin')

const setMPA = () => {
  const entry = {}
  const htmlWebpackPlugins = []
  const entryFiles = glob.sync(path.join(__dirname, './src/pages/*/index.js')) // 匹配各页面的对应入口文件
  entryFiles.forEach((item) => {
    const pageName = item.match(/pages\/(.*)\/index.js/)[1]  // 获取文件夹名，即页面名称
    entry[pageName] = item                                   // 设置入口文件路径
    // 设置html-webpack-plugin数组
    htmlWebpackPlugins.push(
      new HtmlWebpackPlugin({
        template: path.join(__dirname, `./src/pages/${pageName}/index.html`),//模板地址
        filename: `${pageName}.html`, //输出文件名
        chunks: [pageName], // 插入的js chunk名称，和output有关
        inject: true,        
        favicon: path.join(__dirname, './src/assets/favicon.ico'), // 图标
        minify: { //压缩代码
          html5: true,
          collapseWhitespace: true,
          preserveLineBreaks: false,
          minifyCSS: true,
          minifyJS: true,
          removeComments: false
        }
      })
    )
  })
  return {
    entry,
    htmlWebpackPlugins
  }
}

const { entry, htmlWebpackPlugins } = setMPA()
```

#### 使用 `ES6` 和 `async、await`，以及`eslint`

使用 `ES6` 和 `async、await`以及`eslint`，需要借助`babel-loader`和 `eslint-loader`，其具体配置很简单

`babel`相关的库：`@babel/core`、`@babel/preset-env`、`@babel/plugin-transform-regenerator`、`@babel/plugin-transform-runtime`、
`eslint`相关的库：`babel-eslint`、`eslint`

- `module.rules`的配置

```js
{
    test: /\.js$/,
    use: ['babel-loader', 'eslint-loader']
}
```

- `.babelrc`文件的配置

```json
{
  "presets": [
    "@babel/preset-env"
  ],
  "plugins": [ //设置支持async和await
    "@babel/plugin-transform-runtime",
    "@babel/plugin-transform-regenerator"
  ]
}
```

- `.eslintrc.js`的配置

```js
module.exports = {
  parser: 'babel-eslint',
  extends: ['alloy'], // eslint标准请自行选择下载
  env: {
    browser: true,
    node: true
  },
  rules: {
    'no-new': 'off',
    'no-proto': 'off'
    // 'no-console': 'error'
  }
}

```
#### 加载图片

利用`url-loader`，具体`module.rules`配置如下

```js
{
    test: /\.(png|svg|jpg|gif)$/,
    use: [
      {
        loader: 'url-loader',
        options: {
          esModule: false,
          name: '[name][hash:8].[ext]', //文件指纹
          limit: 10240,                 // 转base64
          outputPath: './assets/images/' // 图片文件输出目录和publicPath有关
        }
      }
    ]
  }
```

####  清理打包目录和复制文件到打包目录

自动清理打包目录利用`clean-webpack-plugin`插件，复制文件利用`copy-webpack-plugin`

```js
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const CopyWebpackPlugin = require('copy-webpack-plugin')

const basePlugins = [
  ...htmlWebpackPlugins, //之前配置的htmlWebpackPlugin数组
  new CleanWebpackPlugin(), // 自动清理构建目录
  new CopyWebpackPlugin({
    patterns: [
      {
        from: path.join(__dirname, 'src/lib/markdown-it'), // 源
        to: path.join(__dirname, 'dist/js/markdown-it') // 目标位置
      }
    ]
  })
]
```

#### `.browserslistrc`配置

```
# Browsers that we support

last 2 version
> 1%
iOS 7
```

### 开发环境配置

#### 热更新以及`devServer`

具体配置如下：

```js
plugins: [...basePlugins, new webpack.HotModuleReplacementPlugin()], //热更新插件
  devServer: {
    contentBase: 'dist', // 开启服务的目录
    hot: true, //热更新开启
    proxy: {
      '/api': {
        target: 'http://raydaydayup.cn:3000', //代理
        pathRewrite: { '^/api': '' }
      }
    }
  }
```

#### `CSS`相关配置

`module.rules`的配置如下

```js
{
    test: /\.less$/,
    use: ['style-loader', 'css-loader', 'less-loader']
},
{
    test: /\.css$/,
    use: ['style-loader', 'css-loader']
}
```

#### 出口

```js
output: {
    filename: '[name].js',
    path: path.join(__dirname, 'dist')
}
```

### 生产环境配置

#### 出口

```js
output: {
    filename: 'js/[name][chunkhash:8].js',  // 统一输出到js文件夹
    path: path.join(__dirname, 'dist'),
    publicPath: '/' // 服务路径
}
```

#### `js`代码压缩

利用`terser-webpack-plugin`插件，配置`optimization`

```js
const TerserPlugin = require('terser-webpack-plugin')

optimization: {
   minimize: true,
   minimizer: [
     new TerserPlugin({
       include: [/\.js$/]
     })
   ]
}
```

#### `CSS`相关配置

- `CSS`代码压缩，利用`optimize-css-assets-webpack-plugin`和`cssnano`

```js
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')

plugins: [
  new OptimizeCSSAssetsPlugin({
      assetNameRegExp: /\.css$/g,
      cssProcessor: require('cssnano')
    })
]
```
- `CSS`分离单独文件，利用`mini-css-extract-plugin`

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

module: {
  rules: [
      ...baseModuleRules,
      {
        test: /\.less$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              esModule: true
            }
          },
          'css-loader',
          'less-loader'
        ]
      },
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              esModule: true
            }
          },
          'css-loader'
        ]
      }
    ]
},
plugins: [
    ...basePlugins,
    new MiniCssExtractPlugin({
      filename: 'css/[name][contenthash:8].css' // 输出文件名和地址
    }),
  ],

```

-  `PostCSS` 插件 `autoprefixer` 自动补齐`CSS3`前缀

```js
module: {
    rules: [
      ...baseModuleRules,
      {
        test: /\.less$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              esModule: true
            }
          },
          'css-loader',
          {
            loader: 'postcss-loader',
            options: {
              plugins: () => [require('autoprefixer')]
            }
          },
          'less-loader'
        ]
      },
    ]
  },
```

#### 分离公共文件（CSS和JS）

配置`optimization.splitChunks`

```js
optimization: {
    splitChunks: {
      cacheGroups: {
        commons: {
          name: 'commons',
          chunks: 'all',
          minChunks: 2 // 最少引用两次
        }
      }
    }
  }
```


## 使用相关注意

`html-webpack-plugin`模板使用以及`html-loader`

- 导入`html`片段，在相应位置写下面的代码就像，需要注意的是，`html-loader`加载的`html`片段内部`<%%>`语法会失效

```js
<%= require("html-loader!@/components/recent.html") %>
```

- `html`中图片的加载。在 `html`中相关的`img`标签的图片，`html-loader`加载时会自动调用`url-loader`，但是存在问题，加载出来的路径没有`""`。为了避免这个问题，我就没有再`html-loader`加载的`html`片段中使用图片，而是直接再`html`模板中直接用`require`

```html
<img src="<%= require('@/assets/logo362x82.png') %>" alt="" />
```

## 附录

### 配置文件完整版

- `webpack.base.js`

```js
const { CleanWebpackPlugin } = require('clean-webpack-plugin')

const path = require('path')
const glob = require('glob')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const CopyWebpackPlugin = require('copy-webpack-plugin')

const setMPA = () => {
  const entry = {}
  const htmlWebpackPlugins = []
  const entryFiles = glob.sync(path.join(__dirname, './src/pages/*/index.js'))
  entryFiles.forEach((item) => {
    const pageName = item.match(/pages\/(.*)\/index.js/)[1]
    entry[pageName] = item
    htmlWebpackPlugins.push(
      new HtmlWebpackPlugin({
        template: path.join(__dirname, `./src/pages/${pageName}/index.html`),
        filename: `${pageName}.html`,
        chunks: [pageName],
        inject: true,
        favicon: path.join(__dirname, './src/assets/favicon.ico'),
        minify: {
          html5: true,
          collapseWhitespace: true,
          preserveLineBreaks: false,
          minifyCSS: true,
          minifyJS: true,
          removeComments: false
        }
      })
    )
  })
  return {
    entry,
    htmlWebpackPlugins
  }
}

const { entry, htmlWebpackPlugins } = setMPA()

const baseModuleRules = [
  {
    test: /\.js$/,
    use: ['babel-loader', 'eslint-loader']
  },
  {
    test: /\.(png|svg|jpg|gif)$/,
    use: [
      {
        loader: 'url-loader',
        options: {
          esModule: false,
          name: '[name][hash:8].[ext]',
          limit: 10240,
          outputPath: './assets/images/'
        }
      }
    ]
  }
]

const basePlugins = [
  ...htmlWebpackPlugins,
  new CleanWebpackPlugin(),
  new CopyWebpackPlugin({
    patterns: [
      {
        from: path.join(__dirname, 'src/lib/markdown-it'),
        to: path.join(__dirname, 'dist/js/markdown-it')
      }
    ]
  })
]
module.exports = {
  entry,
  baseModuleRules,
  basePlugins
}

```

- `webpack.dev.js`

```js
const webpack = require('webpack')
const path = require('path')
const { entry, baseModuleRules, basePlugins } = require('./webpack.base.js')

module.exports = {
  mode: 'development',
  entry,
  output: {
    filename: '[name].js',
    path: path.join(__dirname, 'dist')
  },
  module: {
    rules: [
      ...baseModuleRules,
      {
        test: /\.less$/,
        use: ['style-loader', 'css-loader', 'less-loader']
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  },
  resolve: {
    // 设置别名
    alias: {
      '@': path.join(__dirname, 'src') // 这样配置后 @ 可以指向 src 目录
    }
  },
  plugins: [...basePlugins, new webpack.HotModuleReplacementPlugin()],
  devServer: {
    contentBase: 'dist',
    hot: true,
    proxy: {
      '/api': {
        target: 'http://raydaydayup.cn:3000',
        pathRewrite: { '^/api': '' }
      }
    }
  }
}

```

- `webpack.prod.js`

```js
const { entry, baseModuleRules, basePlugins } = require('./webpack.base.js')
const path = require('path')
const TerserPlugin = require('terser-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')
module.exports = {
  mode: 'production',
  entry,
  output: {
    filename: 'js/[name][chunkhash:8].js',
    path: path.join(__dirname, 'dist'),
    publicPath: '/'
  },
  resolve: {
    // 设置别名
    alias: {
      '@': path.resolve('src') // 这样配置后 @ 可以指向 src 目录
    }
  },
  module: {
    rules: [
      ...baseModuleRules,
      {
        test: /\.less$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              esModule: true
            }
          },
          'css-loader',
          {
            loader: 'postcss-loader',
            options: {
              plugins: () => [require('autoprefixer')]
            }
          },
          'less-loader'
        ]
      },
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              esModule: true
            }
          },
          'css-loader'
        ]
      }
    ]
  },
  plugins: [
    ...basePlugins,
    new MiniCssExtractPlugin({
      filename: 'css/[name][contenthash:8].css'
    }),
    new OptimizeCSSAssetsPlugin({
      assetNameRegExp: /\.css$/g,
      cssProcessor: require('cssnano')
    })
  ],
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        include: [/\.js$/]
      })
    ],
    splitChunks: {
      cacheGroups: {
        commons: {
          name: 'commons',
          chunks: 'all',
          minChunks: 2
        }
      }
    }
  }
}

```

### package.json文件

```json
{
  "name": "myblog-webpack",
  "version": "1.0.0",
  "description": "",
  "main": ".eslintrc.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack --config webpack.prod.js",
    "dev": "webpack-dev-server --config webpack.dev.js --open"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@babel/core": "^7.10.2",
    "@babel/plugin-transform-regenerator": "^7.10.3",
    "@babel/plugin-transform-runtime": "^7.10.3",
    "@babel/preset-env": "^7.10.2",
    "autoprefixer": "^9.8.4",
    "babel-eslint": "^10.1.0",
    "babel-loader": "^8.1.0",
    "clean-webpack-plugin": "^3.0.0",
    "copy-webpack-plugin": "^6.0.3",
    "css-loader": "^3.6.0",
    "cssnano": "^4.1.10",
    "eslint": "^7.2.0",
    "eslint-config-alloy": "^3.7.2",
    "eslint-loader": "^4.0.2",
    "file-loader": "^6.0.0",
    "glob": "^7.1.6",
    "html-loader": "^1.1.0",
    "html-webpack-plugin": "^4.3.0",
    "less-loader": "^6.1.2",
    "mini-css-extract-plugin": "^0.9.0",
    "optimize-css-assets-webpack-plugin": "^5.0.3",
    "postcss-loader": "^3.0.0",
    "style-loader": "^1.2.1",
    "terser-webpack-plugin": "^3.0.5",
    "url-loader": "^4.1.0",
    "webpack": "^4.43.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.11.0"
  },
  "dependencies": {
    "axios": "^0.19.2"
  }
}

```