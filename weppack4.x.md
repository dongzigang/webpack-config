```js
const path = require('path')
const { VueLoaderPlugin } = require('vue-loader')
const HTMLPlugin = require('html-webpack-plugin') 
const webpack = require('webpack')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
// 安装cross-env可以配置环境变量，此处用来判断是生产环境还是开发环境,看下面的package.json
const isDev = process.env.NODE_ENV ==='development'
const config = {
    target:'web',
    entry: path.resolve(__dirname,'src/index.js'),
    devServer:{
        contentBase:'./dist'
    },
    output:{
        filename:'bundle.[hash:8].js',
        path:path.resolve(__dirname,'dist')
    },
    module:{
        rules:[
            {
                test: /\.vue$/,
                use: ['vue-loader'],
               
            },
            {
                test: /\.jsx$/,
                use: ['babel-loader'],

            },
            {
                test: /\.css$/,
                use: [
                    'vue-style-loader',                //可以不要
                    'style-loader',
                    'css-loader'
                ]
            },
            
            {
                test:/\.gif|jpg|jpeg|svg|png$/,
               use:{
                   loader:'url-loader',
                   options:{
                       limit:1024,
                       name:'[name].[ext]',
                   }
               }

            }
        ]
    },
    plugins: [
        // new webpack.HotModuleReplacementPlugin(),
        new VueLoaderPlugin(),
        new webpack.DefinePlugin({
            'process.env':{
                NODE_ENV:isDev?'"development"':'"production"'
            }
        }),
        new HTMLPlugin()

    ],
  
   
}

if(isDev){

    config.devtool = "#cheap-moudule-eval-source-map"  // source-map
    config.mode= 'development',
    config.module.rules.push(
        {
            test:/\.styl/,
            use:[
                'style-loader',
                'css-loader',
                {
                    loader:'postcss-loader',
                    options:{
                        sourceMap:true
                    }
                },
                'stylus-loader'
            ]
            },
    ) 
    // webpack3.x后自带热加载
    config.devServer = {
        port:'8000',
        host:'127.0.0.1',
        overlay:{
            errors:true
        },
        hot:true  //开启热加载
    }
    config.plugins.push(
        new webpack.HotModuleReplacementPlugin(),
        new webpack.NoEmitOnErrorsPlugin()
    )
}
else{
    config.entry = {
        app: path.resolve(__dirname, 'src/index.js'),
        vendor:['vue']
    }
    config.mode = 'production',
    config.output.filename = '[name].[chunkhash:8].js'
    config.module.rules.push(
        {
                test:/\.styl/,
                
                use: [
                    MiniCssExtractPlugin.loader,
                    'css-loader',
                    {
                        loader: 'postcss-loader',
                        options: {
                            sourceMap: true,
                            
                        }
                    },
                    'stylus-loader'
                    ]

                })
    config.plugins.push(
        new MiniCssExtractPlugin({
           filename:'styles.[contenthash:8].css'
        }))
    config.optimization = {
        splitChunks: {
            cacheGroups: {
                commons: {
                    chunks: 'initial',
                    minChunks: 2, maxInitialRequests: 5,
                    minSize: 0
                },
                vendor: {
                    test: /[\\/]node_modules[\\/]/,
                    chunks: 'initial',
                    name: 'vendor',
                    priority: 10,
                    enforce: true
                }
            }
        },
        runtimeChunk: true
    }

}
module.exports = config;
```

package.json

```json

{
  "name": "vue-webpack-todo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "cross-env NODE_ENV=production webpack  --config webpack.config.js",
    "dev": "cross-env NODE_ENV=development webpack-dev-server --config webpack.config.js"
  },
  "author": "niuyi",
  "license": "MIT",
  "dependencies": {
    "@babel/core": "^7.0.0",
    "autoprefixer": "^9.1.5",
    "babel-core": "^6.26.3",
    "babel-helper-vue-jsx-merge-props": "^2.0.3",
    "babel-loader": "^8.0.4",
    "babel-plugin-syntax-jsx": "^6.18.0",
    "babel-plugin-transform-vue-jsx": "^3.7.0",
    "babel-preset-env": "^1.7.0",
    "cross-env": "^5.2.0",
    "css-loader": "^1.0.0",
    "extract-text-webpack-plugin": "^3.0.2",
    "file-loader": "^2.0.0",
    "html-webpack-plugin": "^3.2.0",
    "mini-css-extract-plugin": "^0.4.3",
    "postcss-loader": "^3.0.0",
    "style-loader": "^0.23.0",
    "stylus": "^0.54.5",
    "stylus-loader": "^3.0.2",
    "url-loader": "^1.1.1",
    "vue": "^2.5.17",
    "vue-loader": "^15.4.2",
    "vue-template-compiler": "^2.5.17",
    "webpack": "^4.20.2"
  },
  "devDependencies": {
    "webpack-cli": "^3.1.2"
  }
}
```

