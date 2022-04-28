

# 1、webpack是什么

> ​	webpack是一种前端资源构建工具，一个静态模块打包器。在webpack中，前端的所有静态文件(js/css/img/less/sass/..)都会作为模块处理。他将根据模块的依赖关系进行静态分析，打包生成对应的静态资源。

 # 2、webpack的5个核心概念

> + **Entry:**入口指示webpack以哪个文件为入口起点开始打包，分析构建内部依赖图
>
> + **Output:**输出指示webpack打包后的资源bundles输出到那里去，以及如何命名
>
> + **Loader:**loader让webpack能够去处理那些非javascript文件(webpack自身只理解javascript和json
>
>   )
>
> + **Plugins:** 插件可以用于执行范围更广的任务。插件的范围包括从打包优化和压缩，一直到重新定义环境中的变量等。
>
> + **Mode:**入口指示webpack以哪个文件为入口起点开始打包，分析构建内部依赖图
>
>   > |     选项      | 描述                                       |      特点       |
>   > | :---------: | ---------------------------------------- | :-----------: |
>   > | development | 会将process.NODE_ENV的值设为development。启用NameChunksPlugin和NameModulesPlugin。 | 能让代码本地调试运行的环境 |
>   > | production  | 会将process.NODE_ENV的值设为production。启用FlagDependencyUsagePlugin,FlagIncludedChunksPlugin，ModuleConcatenationPlugin,NoEmitOnErrorsPlugin，OccurrenceOrderPlugin,SideEffectsFlagPlugin和UglifyJsPlugin. | 能让代码优化上线运行的环境 |

# 3、常用的webpack的loader及用法

> + **style-loader:** 给webpack提供解析内联样式的能力,创建html的style标签并将样式写入到style标签里面
>
> + **css-loader:** 给webpack提供解析css文件的能力
>
> + **less-loader: ** 给webpack提供解析less文件的能力
>
> + **sass-loader:** 给webpack提供解析sass文件的能力
>
> + **file-loader:** 给webpack提供解析样式中的图片等文件的能力
>
> + **url-loader:** 作用同上，根据file-loader进一步封装而来，超过limit限制的文件使用filer-loader进行解析，不超过限制大小的会编译base64编码的图片
>
> + **html-loader:**  给webpack提供解析html的img标签中的图片文件的能力
>
> + **eslint-loader:** 设置js的语法检查
>
>   > ```
>   > /* webpack.config.js */
>   >
>   > module.exports = {
>   >     ......
>   >     
>   >     module: {// loader的配置
>   >         rules:[
>   >             // 需要在package.json中设置eslintConfig中设置
>   >             // 使用airbnb的代码规范 --> 需安装 eslint-config-airbnb-base eslint eslint-plugin-import
>   >             {
>   >                 test: /\.js$/, // 只检查js文件    
>   >                 exclude: /node_modules/, // 不需要检查第三方的npm包
>   >                 loader: 'eslint-loader',
>   >                 enforce: 'pre', // 优先执行
>   >                 options: {
>   >                     fix: true, //自动修复语法错误
>   >                 }
>   >             },
>   >         ], 
>   >     },
>   >     
>   >     ......
>   > }
>   >
>   >
>   > /* package.json,在eslintConfig中配置eslint */
>   >
>   >   "eslintConfig": {
>   >     "extends": "airbnb-base",
>   >     "env": {
>   >       "browser": true
>   >     }
>   >   }
>   > ```
>
> + **MiniCssExtractPlugin.loader:** 安装mini-css-extract-plugin并且在plugins中应用了之后生效，取代style-loader会将css文件打包到单独的文件中
>
> > ```javascript
> > const path = require("path");
> > const MiniCssExtractPlugin = require("mini-css-extract-plugin");
> >
> > process.env.NODE_ENV = 'development'; // 设置环境变量为开发环境
> > module.exports = {
> >     ......
> >     module: {// loader的配置
> >         rules:[
> >             {
> >                 // 解析css文件,需要下载style-loader,css-loader
> >                 test: /\.css$/, // 匹配规则
> >                 use: [
> >                     // "style-loader", // 打包时会创建html的style标签并将样式写入到style标签里面
> >                     MiniCssExtractPlugin.loader, // 替换style-loader，将css文件打包到独立的文件中，也可用于less文件
> >                     "css-loader",
> >                 ] // 要使用的解析器,从后到前执行
> >             }
> >         ], 
> >     },
> >     // 插件的配置
> >     plugins: [
> >         // 使用插件后，可以在处理css文件中的rule中使用MiniCssExtractPlugin.loader替换style-loader，并将css打包到一个独立的文件中
> >         new MiniCssExtractPlugin({
> >             filename: "css/build.css"  // 将打包后的css文件进行重命名
> >         }), 
> >     ],
> >     mode: "development"
> >
> > 	......
> > }
> > ```
>
> + postcss-loader:css的兼容处理, postcss-preset-env帮助postcss找到package.json中的browserslist里面的配置(默认production环境，开发环境需要设置process.env.NODE_ENV = 'development'),通过配置加载指定的css兼容性样式
>
> > ```javascript
> > const path = require("path");
> > const MiniCssExtractPlugin = require("mini-css-extract-plugin");
> >
> > process.env.NODE_ENV = 'development'; // 设置环境变量为开发环境
> > module.exports = {
> >     ......
> >     module: {// loader的配置
> >         rules:[
> >             {
> >                 // 解析css文件,需要下载style-loader,css-loader
> >                 test: /\.css$/, // 匹配规则
> >                 use: [
> >                     // "style-loader", // 打包时会创建html的style标签并将样式写入到style标签里面
> >                     MiniCssExtractPlugin.loader, // 替换style-loader，将css文件打包到独立的文件中，也可用于less文件
> >                     "css-loader",
> >                     // css的兼容处理, postcss-preset-env帮助postcss找到package.json中的browserslist里面的配置
> >                     // (默认production环境，开发环境需要设置process.env.NODE_ENV = 'development')，
> >                     // 通过配置加载指定的css兼容性样式            
> >                     {
> >                         loader: 'postcss-loader',
> >                         options: {
> >                             ident: 'postcss',
> >                             plugins: () => [
> >                                 require('postcss-preset-env')()
> >                             ]
> >                         }
> >                     }
> >                 ] // 要使用的解析器,从后到前执行
> >             }
> >         ], 
> >     },
> >     // 插件的配置
> >     plugins: [
> >         // 使用插件后，可以在处理css文件中的rule中使用MiniCssExtractPlugin.loader替换style-loader，并将css打包到一个独立的文件中
> >         new MiniCssExtractPlugin({
> >             filename: "css/build.css"  // 将打包后的css文件进行重命名
> >         }), 
> >     ],
> >     mode: "development"
> >
> > 	......
> > }
> > ```
>
> + **vue-loader: ** 给webpack提供解析.vue文件
> + **thread-loader:** 用于开启多进程打包
> + **babel-loader:** 将js转换成低版本的es语法,需下载babel-loader、babel-core、@babel/preset-env(根据自己的版本下载)
>
> > ```javascript
> > module.exports = {
> >     ......
> >   
> >     module: {// loader的配置
> >         rules:[
> >   			{
> >                 test: /\.js$/,
> >                 exclude: /node_modules/, // 不需要兼容第三方的npm包
> >                 loader: 'babel-loader',
> >                 include: path.resolve(__dirname, './src'),
> >                 options: {
> >                     // 指定babel做怎样的兼容处理
> >                     presets: [
> >                         [
> >                             '@babel/preset-env',
> >                             {
> >                                 useBuiltIns: 'usage', // 启用按需加载
> >                                 corejs: {
> >                                     version: 3 // 指定corejs的版本
> >                                 },
> >                                 // 指定兼容的具体的js版本
> >                                 targets: {
> >                                     chrome: '60',
> >                                     firefox: '60',
> >                                     ie: '8',
> >                                     safari: '10',
> >                                     edge: '17',
> >                                 }
> >                             }
> >                         ]
> >                     ],
> >                    cacheDirectory: true, // 开启js缓存
> >                 }
> >             },
> >         ], 
> >     }
> >     
> >     ......
> > } 
> > ```
>
> #### 例子：
>
> >  ```javascript
> >
> >  ```

# 4、常用的webpack插件及用法

> + **html-webpack-plugin:** 根据相应的html模板生成一个自动引入打包后的js文件的网页``
>
> > ```javascript
> > const HtmlWebpackPlugin = require("html-webpack-plugin");
> > module.export = {
> > 	plugins: [
> > 		// 自动创建一个html文件并引入打包后的js
> > 		new HtmlWebpackPlugin({
> >             template: "./src/index.html", // 会复制这个html文件作为模板
> >             minify: { // 压缩html代码
> >                 collapseWhitespace: true, // 移除空格
> >                 removeComments: true, // 移除注释
> >             }
> > 		}),
> > 	],
> > }
> > ```
>
> + **mini-css-extract-plugin:** 将css打包成额外的文件
>
> > ```javascript
> >         const MiniCssExtractPlugin = require("mini-css-extract-plugin");
> >         module.export = {
> >             module: {// loader的配置
> >                 rules:[
> >                    {
> >                     // 解析css文件,需要下载style-loader,css-loader
> >                     test: /\.css$/, // 匹配规则
> >                     use: [
> >                         // "style-loader", // 打包时会创建html的style标签并将样式写入到style标签里面
> >                         MiniCssExtractPlugin.loader, // 替换style-loader，将css文件打包到独立的文件中，也可用于less文件
> >                         "css-loader"
> >                         ] // 要使用的解析器,从后到前执行
> >                     },
> >                 ],
> >             },
> >             plugins: [
> >                 // 使用插件后，可以在处理css文件中的rule中使用MiniCssExtractPlugin.loader替换style-loader，并将css打包到一个独立的文件中
> >                 new MiniCssExtractPlugin({
> >                     filename: "css/build.css"  // 将打包后的css文件进行重命名
> >                 }),
> >              ],
> >         }
> > ```
>
> + **uglifyjs-webpack-plugin:** 代码压缩插件,webpack4以上production环境会默认开启
>
> + **workbox-webpack-plugin:** 可以用来创建pwa应用
>
> + **webpack.BannerPlugin:** webpack自带的版权声明插件
>
> + **optimize-css-assets-webpack-plugin:** css压缩插件
>
>   > ```javascript
>   > plugins: [
>   >         new OptimizeCssAssetsWebpackPlugin(),
>   >     ],
>   > ```
>
> + **webpack.DllPlugin:**  用于webpack.dll技术，将dll技术生成的包进行映射，并生成一个manifest.json映射文件 
>
>   > ```javascript
>   > //
>   > /* 
>   >   dll技术，用于将某些第三方库，如：vue、jquery、react进行单独打包
>   >   webpack --config ./webpack.dll.js
>   > */
>   >
>   > const { resolve } = require('path');
>   > const webpack = require('webpack');
>   >
>   > module.exports = {
>   >   entry: {
>   >     // key最终打包生成js文件的,value是要打包的库
>   >     jquery: ['jquery']
>   >   },
>   >   output: {
>   >     filename: '[name].js',
>   >     path: resolve(__dirname, './dll'),
>   >     library: '[name]', // 打包的js文件暴漏出去的变量的名称
>   >   },
>   >   plugins: [
>   >     // 打包生成一个 manifest.json 的映射文件
>   >     new webpack.DllPlugin({
>   >       name: '[name]_[hash]', // 映射库的暴漏的内容名称
>   >       path: resolve(__dirname, './dll/manifest.json'), // 输出的文件路径
>   >     })
>   >   ],
>   >   mode: 'production',
>   > };
>   > ```
>
> +   **webpack.DllReferencePlugin：** 将dll技术进行打包的文件的映射关系告诉webpack,webpack不会将这些库进行打包 
>
>   > ```javascript
>   > const { resolve } = require("path");
>   > const HtmlWebpackPlugin = require("html-webpack-plugin");
>   > const webpack = require("webpack");
>   >
>   > module.exports = {
>   >   	......
>   >     plugins: [
>   >         // 自动创建一个html文件并引入打包后的js
>   >         new HtmlWebpackPlugin({
>   >             template: "./src/index.html", // 会复制这个html文件作为模板
>   >             minify: { // 压缩html代码
>   >                 collapseWhitespace: true, // 移除空格
>   >                 removeComments: true, // 移除注释
>   >             }
>   >         }),
>   > 		// 将dll技术进行打包的文件的映射关系告诉webpack,webpack不会将这些库进行打包
>   >         new webpack.DllReferencePlugin({
>   >             manifest: resolve(__dirname, 'dll/manifest.json')
>   >         }),
>   >         // 将某个文件输出到打包目录并且自动引入到html文件中
>   >         new AddAssetHtmlWebpackPlugin({
>   >             filepath: resolve(__dirname, 'dll/jquery.js'),
>   >         }),
>   >     ],
>   > 	......
>   > }
>   > ```
>   >
>   > ​
>
> +  **add-asset-html-webpack-plugin:** 将指定文件输出并自动引入到html文件中
>
>   > ```javascript
>   > const { resolve } = require("path");
>   > const AddAssetHtmlWebpackPlugin = require("add-asset-html-webpack-plugin");
>   >
>   > module.exports = {
>   >   	......
>   >     plugins: [
>   >         // 将某个文件输出到打包目录并且自动引入到html文件中
>   >         new AddAssetHtmlWebpackPlugin({
>   >             filepath: resolve(__dirname, 'dll/jquery.js'),
>   >         }),
>   >     ],
>   > 	......
>   > }
>   > ```
>

#  5、性能优化

> #### 1、 优化方向
>
> - 开发环境性能优化侧重点
>   - **优化打包速度**
>   - **优化代码调试** 
> - 生产环境性能优化侧重点
>   - **优化打包速度** 
>   - **优化代码执行速度**
>
> #### 2、优化方法
>
> + **HRM**: 模块热更新(hot module replacement).
>
>   > **作用：** 一个模块变化时只会更新这一个模块，而不是打包所有模块，能够提升打包速度
>   >
>   > **样式文件：** 可以使用HMR功能，因为style-loader内部实现了，开发环境可以使用style-loader
>   >
>   > **html文件：** 默认不能使用HMR功能，同时会导致html功能不会热更新，解决方案将entry改为数组，同时将html文件引入
>   >
>   > **js文件：** 默认不能使用HMR功能，需要修改js代码添加支持,不能修改入口js文件，因为会导致重新引入其他文件
>   >
>   > > ```javascript
>   > > if (module.hot) {
>   > >   // 一旦module.hot存在说明开启了HMR功能
>   > >   module.hot.accept('./js/other.js', () => {
>   > >     // 监听other.js文件变化，此时其他模块不会重新打包构建，会执行这个回调函数，监听其他文件，则要继续按照这个格式添加
>   > >     console.log('./js/other.js变化了');
>   > >   });
>   > > }
>   > > ```
>   >
>   > ```javascript
>   > /* webpack.congfig.js */
>   > const path = require("path");
>   >
>   > module.exports = {
>   >     entry: "./src/main.js", // 打包入口文件
>   >     output: { // 打包输出配置
>   >         path: path.resolve(__dirname, "./build"),
>   >         filename: "js/main.js"
>   >     },
>   >     module: {
>   >         rules:[], 
>   >     },
>   >     // 插件的配置
>   >     plugins: [ ],
>   >     mode: "development",
>   >     // 启动本地服务,需安装webpack-dev-server
>   >     devServer: {
>   >         port: 3000, // 端口号
>   >         contentBase: path.join(__dirname, "build"), // 服务运行的路径
>   >         hot: true, // 开启热更新配置
>   >     }
>   > }
>   > ```
>   >
>   >  
>
> +  **oneOf:** 启用loader的oneOf配置
>
>   > ```javascript
>   > module.exports = {
>   >   ......
>   >     module: {
>   >   		rules:[	
>   >                 {/* loader1 */},
>   >             	{
>   >                 // 以下loader只会同时配置一个，所以不能同时配置两个loader处理同一种类型的文件
>   >                 	oneOf: [
>   >                  	  {/* loader1 */},
>   >                 	  {/* loader2 */}
>   >                	 	]
>   >             	}
>   >         	], 
>   > 	},
>   > ......
>   > }
>   > ```
>   >
>   > ​
>
>
> + **缓存：**使用缓存
>
>   > + **babel缓存:**在babel-loader中添加配置 `cacheDirectory: true`
>   >
>   >   > ```javascript
>   >   > module.exports = {
>   >   >   ......
>   >   >     module: {
>   >   >   		rules:[	
>   >   >             	{
>   >   >                 	oneOf: [
>   >   >                     {
>   >   >                         test: /\.js$/,
>   >   >                         loader: 'babel-loader',
>   >   >                        
>   >   >                         options: {
>   >   >                             ......
>   >   >                             cacheDirectory: true, // 开启js缓存
>   >   >   						  ......
>   >   >                         }
>   >   >                     },
>   >   >                	 	]
>   >   >             	}
>   >   >         	], 
>   >   > 	},
>   >   > ......
>   >   > }
>   >   > ```
>   >   >
>   >   > ​
>   >
>   > + **开启文件资源缓存:**用于生产环境  `强制缓存`和`协商缓存`
>   >
>   >   > ```javascript
>   >   > 需要修改js、css文件的名称，可配置打包的hash值生成不同的文件名称
>   >   > hash：每次webpack构建时会生成一个唯一的hash值，缺点是改动一个则所有的文件的hash都会改变 
>   >   > chunkhash:根据chunk生成的hash值。如果打包来自于同一个chunk,那么hash值就相同
>   >   > contenthash:根据文件的内容生成hash值。只有文件修改了，才会重新生成hash值
>   >   > ```
>   >   >
>   >   > ​
>
> +  **优化代码调试** 
>
>   > **source-map**是一种提供源代码到构建后的代码映射的技术，可以找到出问题的代码的源代码，方便问题追踪
>   >
>   > ```javascript
>   > module.exports = {
>   >   	......
>   >     devtool: 'source-map',
>   >   	......
>   > }
>   > ```
>   >
>   > > **devtool的取值：**[inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map
>   > >
>   > > *`tips:` 内联和外部的区别：1、外部会生成一个.map文件，内联则没有 2、内联构建速度更快*
>   > >
>   > > - **source-map:**生成外部source-map文件，能够准确提示代码错误信息和源代码的错误位置
>   > > - **inline-source-map:**生成内联的source-map,能够准确提示代码错误信息和源代码的错误位置 
>   > > - **hidden-source-map:**生成外部source-map文件，能够提示代码错误原因，但是没有错误位置，只能追踪到打包后代码的错误位置
>   > > - **eval-source-map:**每一个js文件都生成内联的source-map,能够准确提示代码错误信息和源代码的错误位置 
>   > > - **nosources-source-map:**生成外部source-map文件，只有错误信息，没有任何源代码的信息
>   > > - **cheap-source-map:**生成外部source-map文件，能够准确提示代码错误信息和源代码的错误位置,但是只能精确到行
>   > > - **cheap-module-source-map:**生成外部source-map文件，与cheap-source-map类似  
>   >
>   > ***开发环境建议使用： eval-source-map / eval-cheap-module-source-map***
>   >
>   > ***生产环境建议使用： source-map / cheap-module-source-map,如需隐藏代码则可以使用nosources-source-map（全部隐藏）或hidden-source-map（只隐藏源代码）***
>
> +  **tree-shaking:** 去除没有使用的代码，减少代码体积
>
>   > *`tips:`可能会将css静态资源移除掉，最好在package.json文件中配置*  `sideEffects": ["*.css", "*.less"]`，将不需要tree-shaking的资源保留
>   >
>   > 1. 必须使用es6的模块化
>   > 2. mode必须是production
>
> +  **代码分割：**将js打包成多个文件
>
>   > 1.   开启多入口打包
>   >
>   >    ```javascript
>   >    module.exports = {
>   >      	......
>   >      	entry: {
>   >      		a: "./src/a.js", // 打包生成a、b两个文件
>   >      		b: "./src/b.js",
>   >    	}, 
>   >      	......
>   >    }
>   >    ```
>   >
>   >    ​
>   >
>   > 2.  启用 `optimization` 配置
>   >
>   >    ```javascript
>   >    module.exports = {
>   >        ......
>   >       /*
>   >            1、将node_modules的文件单独打包成一个文件
>   >            2、仅在多入口时会自动分析依赖项将公共文件打包成一个单独的文件
>   >        */
>   >        optimization: {
>   >            splitChunks: {
>   >                chunks: 'all',
>   >            },
>   >        },
>   >        ......
>   >    }
>   >    ```
>   >
>   >    ​
>   >
>   > 3.   使用`import('xxx')`语法(需开启2的配置)
>   >
>   >    ```javascript
>   >    // /* webpackChunkName: 'test' */ 指定打包后的文件名
>   >    import(/* webpackChunkName: 'test' */ './js/other.js')
>   >      .then(v => console.log(v))
>   >      .catch(() => {});
>   >    ```
>   >
>   >    ​
>
> +   **懒加载和预加载(兼容性比较差)：**
>
>   > ```javascript
>   > // webpackChunkName: 'test'  指定打包后的文件名
>   > // webpackPrefetch: true   启用预加载
>   > import(/* webpackChunkName: 'test', webpackPrefetch: true */ './js/other.js')
>   >   .then(v => console.log(v))
>   >   .catch(() => {});
>   > ```
>   >
>   > ​
>
> +   **PWA:** 渐进式网络应用程序（只能使用http协议运行）
>
>   > ```javascript
>   > /* webpack.config.js */
>   > const WorkboxWebpackPlugin = require('workbox-webpack-plugin');
>   >
>   > module.exports = {
>   >   	......
>   >     plugins: [
>   >         /* 1、创建serviceworker（会生成一个配置文件），并快速启动 2、删除旧的serviceworker */
>   >         new WorkboxWebpackPlugin.GenerateSW({
>   >             clientsClaim: true,
>   >             skipWaiting: true,
>   >         }),
>   >     ],
>   >       ......
>   > }
>   >       
>   > /* js文件 */
>   > if ('serviceWorker' in navigator) {
>   >   window.addEventListener('load', () => {
>   >     navigator.serviceWorker
>   >       .register('/service-worker.js')
>   >       .then(() => console.log('sw注册成功了~'))
>   >       .catch(() => console.log('sw注册失败了~'));
>   >   }, false);
>   > }
>   > ```
>   >
>   > ​
>
> +  **多进程打包：** 代码较多时，效果才明显，代码少时，可能会起反作用。一般用于js转换
>
>   > ```javascript
>   > module.exports = {
>   > 	......
>   >     module: {// loader的配置
>   >         rules:[
>   >             {
>   >                 oneOf: [
>   >                     // 处理js的兼容性问题
>   >                     {
>   >                         test: /\.js$/,
>   >                         exclude: /node_modules/, // 不需要兼容第三方的npm包
>   >                         include: path.resolve(__dirname, './src'),
>   >                         use: [
>   >                             {
>   >                                 loader: 'thread-loader',
>   >                                 options: {
>   >                                     Workers: 2, // 要开启的进程数量
>   >                                 }
>   >                             },
>   >                             {
>   >                                 loader: 'babel-loader',
>   >                                 options: {
>   >                                     // 指定babel做怎样的兼容处理
>   >                                     presets: [
>   >                                         [
>   >                                             '@babel/preset-env',
>   >                                             {
>   >                                                 useBuiltIns: 'usage', // 启用按需加载
>   >                                                 corejs: {
>   >                                                     version: 3 // 指定corejs的版本
>   >                                                 },
>   >                                                 // 指定兼容的具体的js版本
>   >                                                 targets: {
>   >                                                     chrome: '60',
>   >                                                     firefox: '60',
>   >                                                     ie: '8',
>   >                                                     safari: '10',
>   >                                                     edge: '17',
>   >                                                 }
>   >                                             }
>   >                                         ]
>   >                                     ],
>   >                                     cacheDirectory: true, // 开启js缓存
>   >                                 }
>   >                             }
>   >                         ],
>   >                     },
>   >                 ]
>   >             }
>   >         ], 
>   >     },
>   > 	......
>   > }
>   > ```
>   >
>   > ​
>
> +  **externals: **  不将一些公共包打包到主包js中，需要通过cdn引入
>
>   > ```javascript
>   > module.exports = {
>   >     ......
>   >     externals: {
>   >       	// 包名：npm包名
>   >         // 拒绝将jquery打包进来,需要在html文件里面用script标签手动引入
>   >         jquery: 'jQuery'
>   >     }
>   >     ......
>   > }
>   > ```
>   >
>   > ​
>
> + **dll技术：** 不将一些公共包打包到主包js中,与`externals`的区别是不重复打包,`externals`是完全不打包，更适合cdn引入的方案
>
>   > ```javascript
>   > /* webpack.dll.js */
>   >
>   > /* dll技术，用于将某些第三方库，如：vue、jquery、react进行单独打包
>   >   先执行webpack --config ./webpack.dll.js
>   > */
>   >
>   > const { resolve } = require('path');
>   > const webpack = require('webpack');
>   >
>   > module.exports = {
>   >   entry: {
>   >     // key最终打包生成js文件的,value是要打包的库
>   >     jquery: ['jquery']
>   >   },
>   >   output: {
>   >     filename: '[name].js',
>   >     path: resolve(__dirname, './dll'),
>   >     library: '[name]', // 打包的js文件暴漏出去的变量的名称
>   >   },
>   >   plugins: [
>   >     // 打包生成一个 manifest.json 的映射文件
>   >     new webpack.DllPlugin({
>   >       name: '[name]_[hash]', // 映射库的暴漏的内容名称
>   >       path: resolve(__dirname, './dll/manifest.json'), // 输出的文件路径
>   >     })
>   >   ],
>   >   mode: 'production',
>   > };
>   >
>   >
>   > /* webpack.config.js */
>   > const { resolve } = require("path");
>   > const HtmlWebpackPlugin = require("html-webpack-plugin");
>   > const webpack = require("webpack");
>   > const AddAssetHtmlWebpackPlugin = require("add-asset-html-webpack-plugin");
>   >
>   > module.exports = {
>   >   	......
>   >     plugins: [
>   >         // 自动创建一个html文件并引入打包后的js
>   >         new HtmlWebpackPlugin({
>   >             template: "./src/index.html", // 会复制这个html文件作为模板
>   >             minify: { // 压缩html代码
>   >                 collapseWhitespace: true, // 移除空格
>   >                 removeComments: true, // 移除注释
>   >             }
>   >         }),
>   >         new webpack.DllReferencePlugin({ // 告诉webpack那些库不参与打包
>   >             manifest: resolve(__dirname, 'dll/manifest.json')
>   >         }),
>   >         // 将某个文件输出到打包目录并且自动引入到html文件中
>   >         new AddAssetHtmlWebpackPlugin({
>   >             filepath: resolve(__dirname, 'dll/jquery.js'),
>   >         }),
>   >     ],
>   > 	......
>   > }
>   > ```
>   >
>   > ​
>
>   ​
>
>





