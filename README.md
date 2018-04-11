# grunt-to-css-js
压缩css,js
参考文献：https://www.cnblogs.com/beiz/p/5647780.html

现在会进行代码的合并和压缩已成为前端人员的必备知识，那么现在来介绍一个grunt的工具。grunt是个风靡世界的工具，它的首页是  http://www.gruntjs.net

这是个中文网站，有文档可以参考。

好了，废话不多说，直接发安装步骤和注意事项。

首先，要安装nodejs，grunt是依赖node滴。上 www.nodejs.org上下载适合你电脑的nodejs版本。

当安装好node.js以后，在命令行中输入
node -v
把 npm也安装上，npm为一个NodeJS的模块管理

同样，在命令行中输入 ,如果出现版本号，就说明安装成功了
ok,nodejs已经安装好了，在下载安装grunt之前，我们需要建立一个文件目录树，像下面的结构，目的是为了更好的层次关系和更便利的操作
Gruntfile.js是我们自己定义的，这个文件用来配置grunt

package.json这个文件是记录下载哪些插件，并配置在Gruntfile.js中，这个后面会详细介绍

src文件夹里面放的是一个css文件夹，一个js文件夹，css文件夹里面放的是演示的css文件，js文件夹里放的是演示用js文件。

dist 文件夹是放压缩以后的css和js文件

lib文件夹里放html文件
那package.json里面放什么呢？对了，放咱们配置项，刚开始，如果你用的编辑器没有这么智能的话，只能把下面的内容敲到文件中。
ok,万事俱备了，开始下载grunt命令行!

grunt命令行（CLI） 安装在全局环境下，所以命令是这么写！<br/>
<i>npm install -g grunt-cli</i>

 注意，mac os 系统、部分linux系统中，在这句话的前面加上“sudo ”指令。

当安装完了以后，进入我们建立的哪个grunt_test文件夹<br/>

cd grunt/grunt_test

这时，就可以安装grunt了输入:<br/>

npm install grunt  --save-dev

出现了一个node_modules文件夹。大家打开看看就知道里面是什么了。这充分表明grunt已经安装成功。

这时，在src下面的css和js中各建一个concat文件夹，用于存储合并后的文件

现在还需要几个插件：js、css文件链接成一个文件的插件、压缩的插件、错误检查的插件
安装方法一样

npm install grunt-contrib-concat --save-dev
回车安装，完成后安装下一个，依次类推

npm install grunt-contrib-csslint --save-dev
npm install grunt-contrib-cssmin --save-dev
npm install grunt-contrib-jshint --save-dev
npm install grunt-contrib-uglify --save-dev
npm install grunt-contrib-watch --save-dev
当我们把这些插件都装完后，我们再看package.json

所有的插件的版本号都有了。下一步，需要对Gruntfile.js 进行配置

module.exports = function(grunt){
    //初始化grunt 配置
    grunt.initConfig({
 
        //获取package.json的信息
        pkg: grunt.file.readJSON('package.json'),
        //concat插件的配置信息
        concat: {
            options:{
                stripBanners:true, //合并时允许输出头部信息
                banner:'/*!<%= pkg.name %> - <%= pkg.version %>-'+'<%=grunt.template.today("yyyy-mm-dd") %> */'
            },
            cssConcat:{
                src:['src/css/css1.css','src/css/css2.css'],
                dest:'src/css/concat/<%= pkg.name %> - <%= pkg.version %>.css' //dest 是目的地输出
            },
            jsConcat:{
                src:'src/js/*.js',
                dest:'src/js/concat/<%=pkg.name %> - <%= pkg.version %>.js'
            }
        },
        //压缩css
        cssmin:{
            options:{
                stripBanners:true, //合并时允许输出头部信息
                banner:'/*!<%= pkg.name %> - <%= pkg.version %>-'+'<%=grunt.template.today("yyyy-mm-dd") %> */\n'
            },
            build:{
                src:'src/css/concat/<%=pkg.name %> - <%=pkg.version %>.css',//压缩是要压缩合并了的
                dest:'dist/css/<%= pkg.name %> - <%= pkg.version %>.min.css' //dest 是目的地输出
            }
        },
        //压缩js
        uglify:{
            options:{
                stripBanners:true, //合并时允许输出头部信息
                banner:'/*!<%= pkg.name %> - <%= pkg.version %>-'+'<%=grunt.template.today("yyyy-mm-dd") %> */\n'
            },
            build:{
                src:'src/js/concat/<%=pkg.name %> - <%=pkg.version %>.js',//压缩是要压缩合并了的
                dest:'dist/js/<%= pkg.name %> - <%= pkg.version %>.min.js' //dest 是目的地输出
            }
        },
 
        jshint:{
            options:{
                jshintrc:'.jshint'
            },
            build:['Gruntfile.js','src/js/*js']
        },
 
        csslint:{
            options:{
                csslintrc:'.csslint'
            },
            build:['src/css/*.css']
 
        },
        //watch自动化
        watch:{
            build:{
                files:['src/js/*.js','src/css/*.css'],
                tasks:['jshint','csslint','concat','cssmin','uglify'],
                options:{spawn:false}
            }
        }
 
    });
    //告诉grunt我们将使用插件
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-cssmin');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-csslint');
    grunt.loadNpmTasks('grunt-contrib-watch');
    //告诉grunt当我们在终端输入grunt时需要做些什么
    grunt.registerInitTask('default',['jshint','csslint','concat','cssmin','uglify','watch']);//先进行语法检查，如果没有问题，再合并，再压缩
};

上面的注释都写的很清楚了，照着这个复制到你得Gruntfile.js里就可以了。

正式压缩前的最后一步：新建两个文件，写入内容，这两个文件是用于css和js代码检查的配置的。大家有兴趣可以从网上查查，这里就不多说了，大家用我这个就没有问题。一个是.csslint  ,一个是.jshint 

注意这两个文件是.开头的，具体为什么现在还没来得及研究，文件是text格式的,跟Gruntfile是同一级的。



.csslint文件的配置：

复制代码
{
    "adjoining-classes":false,
    "box-sizing":false,
    "box-model" : false,
    "compatible-vendor-prefixes": false,
    "floats":false,
    "font-sizes":false,
    "gradients":false,
    "important":false,
    "known-properties":false,
    "outline-none":false,
    "qualified-headings":false,
    "regex-selectors":false,
    "shorthand":false,
    "text-indent":false,
    "unique-headings":false,
    "universal-selector":false,
    "unqualified-attributes":false
}
复制代码
.jshint文件配置

复制代码
{
    "boss":false,
    "curly":true,
    "eqeqeq":true,
    "expr":true,
    "immed":true,
    "newcap":true,
    "noempty":true,
    "noarg":true,
    "undef":true,
    "regexp":true,

    "browser":true,
    "devel":true,
    "node":true
}
复制代码
最终最终最终的树结构：



这时，所有的配置都完成了，我们可以进行代码的检查、合并、压缩了~~~

大家在js文件里，css文件里随便写点内容，看看能不能合并压缩成功？

命令是 

grunt
然后回车。
