#webpack使用方法
<h3>1.安装</h3>
```
   npm install webpack -g                                    //全局安装webpack
   npm install -g webpack-dev-server                         //全局安装webpack服务器
   cd 目录
    npm init 
    npm install webpack --save-dev                            //本地安装webpack ，装好引入之后可以使用 UglifyJsPlugin 文件压缩插件
    npm install --save-dev style-loader css-loader            //安装css加载器
    npm install --save-dev jsx-loader                         //安装js加载器
    npm install --save-dev sass-loader node-sass              //安装scss加载器
    npm install --save-dev url-loader                         // 本地安装图片加载器
    npm install --save-dev jquery                             //本地安装jquery
    npm install --save-dev html-webpack-plugin@2              //安装自动构建html页面插件
    npm install --save-dev webpack-dev-server                 //本地安装webpack服务器    要启动直接在命令行输入     
                                                              //    $ webpack-dev-server --inline
                                                              //热加载     $ webpack-dev-server --line--hot
```
<h3>编译命令</h3>
```
webpack – for building once for development
webpack -p – for building once for production (minification)
webpack --watch – for continuous incremental build
webpack -d – to include source maps
webpack --colors – for making things pretty

$ webpack-dev-server --inline --172.0.0.1 --port 9090   //修改默认端口号
```
<h3>3.常用webpack.config.js配置</h3>
```js
    /*其实整个文件的各种功能模块就是js中的一个个对象，所以要注意 "  ,  "不能少,缺了逗号会报错    */
    // webpack.config.js
    var path = require("path")                                 //兼容路径
    var webpack = require('webpack')                           //引入之后可以使用 UglifyJsPlugin 文件压缩插件
    var HtmlWebpackPlugin = require('html-webpack-plugin')     //html自动构建
    
    
    
    
    
    
	module.exports = {
	    // 入口文件的引入有三种写法
//	    entry: ['./src/js/index'], 
	    entry: { 
	    	  index: './src/js/index.js',
	    	  vendors: ['jquery']                               //将引入的第三方库打包
	    	},
	       
	    // 输出
	    output: {
	        path: './dist/js',
	      //path: path.join(__dirname, 'dist/js'),
	        filename: "bundle.js",                                   //输出文件名
	    },
	    
	    
	    
	    
	    
	    
	    
	    //plugins - 保存插件的数组
		plugins: [
		    new webpack.optimize.UglifyJsPlugin({               //压缩代码抑制警告信息
		      compressor: {
		        warnings: false,
		      },
		    }),
	        new webpack.optimize.OccurenceOrderPlugin(),           /*通过计算模块出现次数来分配模块。这个经常被使用可以较快地获得模块。
		                                                                                                                                    这使得模块可以预读，建议这样可以减少总文件大小。
		                                                     */
		    new HtmlWebpackPlugin({                                //html自动构建
		      template: './src/index.html'
		    }),
		    new webpack.ProvidePlugin({                            //配置jquery
		      $:"jquery",
		      jQuery:"jquery",
		      "window.jQuery":"jquery"
		    }),
		    new webpack.optimize.CommonsChunkPlugin('vendors', 'vendors.js'),//将引入的第三方库打包.这个要放到最后？
		],
		
		
		
		
		
		
		//存放加载器的地方，暂时是这样的QAQ
		/*test: /\.css$/  是一个正则表达式
		 * 左侧和右侧的   /    /  是正则的
		 *里面的第一个 \ 是转译后面的   . 符号                          
		 * 
		 * */
		module: {
		        //加载器配置
		        loaders: [
		            //.css 文件使用 style-loader 和 css-loader 来处理
		            { test: /\.css$/, loader: 'style-loader!css-loader' },
		            //.js 文件使用 jsx-loader 来编译处理
		            { test: /\.js$/, loader: 'jsx-loader?harmony' },
		            //.scss 文件使用 style-loader、css-loader 和 sass-loader 来编译处理
		            { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
		            //图片文件使用 url-loader 来处理，小于8kb的直接转为base64
		            { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
		        ]
		    },
		resolve: {
		        //查找module的话从这里开始查找
		        root: 'E:/github/flux-example/src', //绝对路径
		        //自动扩展文件后缀名，意味着我们require模块可以省略不写后缀名
		        extensions: ['', '.js', '.json', '.scss'],
		        //模块别名定义，方便后续直接引用别名，无须多写长长的地址
		        alias: {
		            AppStore : 'js/stores/AppStores.js',//后续直接 require('AppStore') 即可
		            ActionType : 'js/actions/ActionType.js',
		            AppAction : 'js/actions/AppAction.js'
		        }
		    },
	};   
```	
