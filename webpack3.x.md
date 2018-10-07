# webpack3.x-node
webpack3.x笔记

### 安装

不建议全局安装

```
npm install webpack --save-dev
```

按照需要还要安装各种loader 如style-loader css-loader


```
npm install --save-dev style-loader css-loader
```

## webpack.config.js


```js
const path = require('path');
const webpack = require('webpack');
//自动构建HTML
const HtmlWebpackPlugin = require('html-webpack-plugin');
//清理 /dist 文件夹
const CleanWebpackPlugin = require('clean-webpack-plugin');
  module.exports = {
    //多入口文件
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },  
    //出口文件
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    },
    //插件
    plugins: [
      new HtmlWebpackPlugin({
        title: 'Output Management',
        filename: 'index.html',
        template: 'src/index.html'
      }),
      new CleanWebpackPlugin(['dist']),
      //压缩代码
      new webpack.optimize.UglifyJsPlugin({
      sourceMap: options.devtool && (options.devtool.indexOf("sourcemap") >= 0 || options.devtool.indexOf("source-map") >= 0)
    })
    ],
    //解析
    resolve: {
     //自动解析扩展
     extensions: ['.js', '.vue', '.json'],
     //创建目录别名
     alias: {
       'vue$': 'vue/dist/vue.esm.js',
       '@': resolve('src')
     }
    },
    module: {
    //loader
     rules: [
       // css加载器
       {
         test: /\.css$/,
         use: [
           'style-loader',
           'css-loader'
         ]
       },
       //scss
       { 
          test: /\.scss$/,
          loader: 'style!css!sass?sourceMap'
       },
       //vue
       {
        test: /\.vue$/,
        loader: 'vue-loader',
        options: vueLoaderConfig
      },
      //js
      {
        test: /\.js$/,
        loader: 'babel-loader',
        include: [resolve('src'), resolve('test')]
      },
      //图片
      {
        test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 10000,
          name: utils.assetsPath('img/[name].[hash:7].[ext]')
        }
      },
      //视频音频
      {
        test: /\.(mp4|webm|ogg|mp3|wav|flac|aac)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 10000,
          name: utils.assetsPath('media/[name].[hash:7].[ext]')
        }
      },
      //字体
      {
        test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 10000,
          name: utils.assetsPath('fonts/[name].[hash:7].[ext]')
        }
      }
     ]
    }
  };
```

### webpack-dev-server

webpack-dev-server 为你提供了一个服务器和实时重载（live reloading） 功能

#### 安装

```
npm install --save-dev webpack-dev-server
```
#### 运行

```
webpack-dev-server --inline --hot --172.0.0.1 --port 9090
```
### 生产环境构建

一般有

```

webpack.base.conf.js  //公用配置

webpack.dev.conf.js   //开发环境配置

webpack.prod.conf.js   //生产环境配置
```

通过webpack-merge 将公用配置引入开发环境和生产环境配置中

#### example

webpack.base.conf.js 

```js
module.exports = {
  entry: {
    'polyfills': './src/polyfills.ts',
    'vendor': './src/vendor.ts',
    'main': './src/main.ts'
  },

  output: {
    path: path.join(__dirname, '/../dist/assets'),
    filename: '[name].bundle.js',
    publicPath: publicPath,
    sourceMapFilename: '[name].map'
  },
}
```

webpack.dev.conf.js 

```js
const Merge = require('webpack-merge');
const CommonConfig = require('./webpack.common.js');

module.exports = Merge(CommonConfig, {
  plugins: [
    new webpack.LoaderOptionsPlugin({
      minimize: true,
      debug: false
    }),
    new webpack.DefinePlugin({
      'process.env': {
        'NODE_ENV': JSON.stringify('production')
      }
    }),
    new webpack.optimize.UglifyJsPlugin({
      beautify: false,
      mangle: {
        screw_ie8: true,
        keep_fnames: true
      },
      compress: {
        screw_ie8: true
      },
      comments: false
    })
  ]
})
```



