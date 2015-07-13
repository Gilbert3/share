# 简单的介绍一下javascript

>  - 主要用来向HTML页面添加交互行为。 
>  - 可以直接嵌入HTML页面，但写成单独的js文件有利于结构和行为的分离。

* script中写代码
```
<script type="text/javascript"> 
function dump(str){
    (typeof console !== 'undefined') && console.log(str);
}     
dump("Hello, shengyuan!”);
</script>
```
一个JS文件
```
<script src=“/dump.js”> </script>
<script type=‘text/javascript’>
dump(‘yige ’);
</script>
```
多个文件,依次加载
```
<script src=“/elements.js”> </script>
<script src=“/events.js”> </script>
<script src=“/dump.js”> </script>
<script type=‘text/javascript’>
dump(‘niubide’);
</script>
```
### 缺点
* 代码不能复用, 开发成本太高
* 文件臃肿  80k/2392L
* 请求过多, 代码之间强依赖
* 合并, 冗余代码

### ? 代码越来越庞大，越来越复杂 ?

***
# 模块化

 * 模块就是实现特定功能的一组方法。

    >  * 原始写法  
    >  * 对象写法 
    >  * 立即执行函数写法  
    >  * 放大模式  
    >  * 宽放大模式  
    >  * 输入全局变量

一、原始写法

把不同的函数（以及记录状态的变量）简单地放在一起
```
function m1(){}
function m2(){}
```
上面的函数m1()和m2()，组成一个模块。使用的时候，直接调用就行了。
这种做法的缺点很明显：**"污染"了全局变量，无法保证不与其他模块发生变量名冲突，而且模块成员之间看不出直接关系**。

二、对象写法
为了解决上面的缺点，可以把模块写成一个对象，所有的模块成员都放到这个对象里面。
```
var moudle1 = {
    _count: 0,
    m1: function(){},
    m2: function(){}
};
```
上面的函数m1()和m2(），都封装在module1对象里。使用的时候，就是调用这个对象的属性。
　　``module1.m1();``
但是，这样的写法会**暴露所有模块成员，内部状态可以被外部改写。比如，外部代码可以直接改变内部计数器的值**。
　　``module1._count = 5;``
　　
三、立即执行函数写法
使用"立即执行函数"（Immediately-Invoked Function Expression，IIFE），可以达到**不暴露私有成员**的目的。

```
var module1 = (function(){
    var _count = 0;
    var m1 = function(){}
    var m2 = function(){}

    return {
        m1: m1,
        m2: m2
    };
})();
```
　　
使用上面的写法，外部代码无法读取内部的_count变量。
　　``console.info(module1._count); //undefined``
module1就是Javascript模块的基本写法。下面，再对这种写法进行加工。

四、放大模式
如果**一个模块很大，必须分成几个部分，或者一个模块需要继承另一个模块**，这时就有必要采用"放大模式"（augmentation）。
```
var module1 = (function(mod){
    mod.m3 = function(){}

    return mod;
})(module1);
```
上面的代码为module1模块添加了一个新方法m3()，然后返回新的module1模块。

五、宽放大模式（Loose augmentation）
在浏览器环境中，模块的各个部分通常都是从网上获取的，**有时无法知道哪个部分会先加载**。如果采用上一节的写法，第一个执行的部分**有可能加载一个不存在空对象**，这时就要采用"宽放大模式"。
```
var module1 = (function(mod){
    mod.m3 = function(){}

    return mod;
})(window.module1 || {});
```
与"放大模式"相比，＂宽放大模式＂就是"立即执行函数"的参数可以是**空对象**。

六、输入全局变量
独立性是模块的重要特点，模块内部最好不与程序的其他部分直接交互。
为了在模块内部调用全局变量，必须显式地将其他变量输入模块。
```
var module1 = (function($, layer){
    mod.m3 = function(){}

    return mod;
})(jquery, layer);
```
上面的module1模块需要使用jQuery库和layer库，就把这两个库（其实是两个模块）当作参数输入module1。这样做除了保证模块的独立性，还使得模块之间的依赖关系变得明显。


***
# 模块的规范
 - 先想一想，为什么模块很重要？
     -  因为有了模块，我们就可以更方便地使用别人的代码，想要什么功能，就加载什么模块。
     -  以同样的方式编写模块
     
## 通行的模块规范
> CommonJS和AMD

我主要介绍AMD，但是要先从CommonJS讲起。

在CommonJS中，有一个全局性方法**require**()，用于**加载模块**。假定有一个数学模块math.js，就可以像下面这样加载。
```
var math = require('math');
```
然后，就可以调用模块提供的方法：
```
var math = require('math');
math.add(2,3); // 5
```

> 这种写法在服务端不是一个问题, 因为所有的模块都存放在本地硬盘，可以同步加载完成，等待时间就是硬盘的读取时间

> 对于浏览器，这却是一个大问题，因为模块都放在服务器端，等待时间取决于网速的快慢，可能要等很长时间，浏览器处于"假死"状态

**浏览器端的模块，不能采用"同步加载"（synchronous），只能采用"异步加载"（asynchronous）**。

这就是**AMD规范**诞生的背景。
　　
***
# AMD
> Asynchronous Module Definition

> 异步模块定义

它采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，**都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行**。

AMD也采用**require**()语句**加载模块**，但是不同于CommonJS，它要求两个参数：
```
require([module], callback);
```
参数介绍
* [module]，是一个数组，里面的成员就是要加载的模块；
* callback，则是加载成功之后的回调函数。

如果将前面的代码改写成AMD形式，就是下面这样：
```
require(['math'], function(math){
    math.add(2, 3);
});
```

math.add()与math模块加载不是同步的，浏览器不会发生假死。
所以很显然，AMD比较适合浏览器环境。

### 常用类库介绍

 - require.js
 - curl.js

我们采用的是require.js,  下面将结合我们的开发, 进一步讲解AMD的用法, 以及如何将使用模块化编程.

***
### require.js解决的问题
 - 实现js文件的异步加载，避免网页失去响应。 
 - 管理模块之间的依赖性，便于代码的编写和维护。

### 为什么要用
> 试想一下，如果一个网页有很多的js文件，那么浏览器在下载该页面的时候会先加载js文件，从而停止了网页的渲染，如果文件越多，浏览器可能失去响应。

> 其次，要保证js文件的依赖性，依赖性最大的模块（文件）要放在最后加载，当依赖关系很复杂的时候，代码的编写和维护都会变得困难。

### 加载require.js
1. 官方网站下载最新版本/或使用百度提供的cdn
2. 引入require.js
```
<script src="js/require.js"></script>
```
防止网页在加载require.js文件时失去响应
```
/*放在尾部*/
<script src="js/require.js"></script> 

/*使用async="true", defer属性 */
/*IE不支持async属性，只支持defer，所以把defer也写上*/
<script src="js/require.js" defer async="true" ></script>
```

加载自己的js文件, 假设我们自己的代码文件是main.js
```
<script src="js/require.js" data-main="js/main"></script>
```
> data-main属性的作用是，指定网页程序的主模块
> 默认的文件后缀名是js，所以可以把main.js简写成main

### 主模块的写法
刚才说的 main.js，我把它称为"主模块"，意思是整个网页的入口代码。它有点像C语言的main()函数，**所有代码都从这儿开始运行。**


如果我们的代码不依赖任何其他模块，那么可以直接写入javascript代码。
```
alert("加载成功！");
```

但这样的话，就没必要使用require.js了。真正常见的情况是，**主模块依赖于其他模块**，这时就要使用AMD规范定义的的require()函数。
```
// main.js
require(['moduleA', 'moduleB', 'moduleC'], function (moduleA, moduleB, moduleC){
    // some code here
});
```
> require()异步加载moduleA，moduleB和moduleC，浏览器不会失去响应；
> 它指定的回调函数，只有前面的模块都加载成功后，才会运行，解决了依赖性的问题。

假定主模块依赖jquery、underscore和backbone这三个模块，main.js就可以这样写：
```
require(['jquery', 'underscore', 'backbone'], function ($, _, Backbone){
    // some code here
});
```
require.js会先加载**jQuery、underscore和backbone**，然后再运行回调函数。**主模块的代码就写在回调函数中**。

### 模块的加载
> 使用require.config()方法，我们可以对模块的加载行为进行自定义。
```
require.config({
　　paths: {
　　　  "jquery": "jquery.min"
　　}
});
```

路径默认与main.js在同一个目录（js子目录）。如果这些模块在其他目录，比如js/lib目录，则有两种写法。一种是逐一指定路径
```
require.config({ 
    paths: { 
        "jquery": "lib/jquery.min", 
        "underscore": "lib/underscore.min", 
        "backbone": "lib/backbone.min" 
    } 
});
```

另一种则是直接改变基目录（baseUrl）。
```
require.config({ 
    baseUrl: "js/lib",
    paths: { 
        "jquery": "lib/jquery.min", 
        "underscore": "lib/underscore.min", 
        "backbone": "lib/backbone.min" 
    } 
});　　
```

如果某个模块在另一台主机上，也可以直接指定它的网址，比如：
```
require.config({
　　paths: {
　　"jquery": "https://ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min"
　  }
});
```

> require.js要求，每个模块是一个单独的js文件
> 这样的话，如果加载多个模块，就会发出多次HTTP请求，会影响网页的加载速度。
> 因此，require.js提供了一个优化工具，当模块部署完毕以后，可以用这个工具将多个模块合并在一个文件中，减少HTTP请求数。

```
node tools/web_build.js [结尾附录有web_build构造] 
```

### AMD模块的写法
> 就是模块必须采用特定的**define**()函数来定义。如果一个模块不依赖其他模块，那么可以直接定义在define()函数之中。

```
// math.js
define(function (){
　  var add = function (x,y){
　　    return x+y;
　  };
　　return {
　　    add: add
　　};
});

/*加载方法如下*/
require(['math'], function (math){
    alert(math.add(1,1));
});

/*如果这个模块还依赖其他模块，那么define()函数的第一个参数，必须是一个数组，指明该模块的依赖性。*/

define(['myLib'], function(myLib){
    function foo(){
        myLib.doSomething();
    }
    return {
        foo : foo
    }
});
```
### define() 函数

> AMD规范只定义了一个函数

> define，它是全局变量。函数的描述为：

```
define(id?, dependencies?, factory);

/**
参数说明：

id：指定义中模块的名字[非必填]
依赖dependencies：是一个当前模块依赖的，已被模块定义的模块标识的数组字面量。[非必填]
工厂方法factory，模块初始化要执行的函数或对象。如果为函数，它应该只被执行一次。如果是对象，此对象应该为模块的输出值
*/
```

### 使用 require 和 exports

创建一个名为"alpha"的模块，使用了require，exports，和名为"beta"的模块:

```
 define("alpha", ["require", "exports", "beta"], function (require, exports, beta) {
    exports.verb = function() {
        return beta.verb();
        //Or:
        return require("beta").verb();
    }
});

/*当id, dependencies 都为空的时候 */
 define(function (require, exports) {
    var beta = require('beta');
    
    exports.verb = function() {
        return beta.verb();
        //Or:
        return require("beta").verb();
    }
});

```

require API 介绍： https://github.com/amdjs/amdjs-api/wiki/require

AMD规范中文版：https://github.com/amdjs/amdjs-api/wiki/AMD-(%E4%B8%AD%E6%96%87%E7%89%88)



### 加载非规范的模块
> 理论上，require.js加载的模块，必须是按照AMD规范、用define()函数定义的模块。

> 虽然已经有一部分流行的函数库（比如jQuery）符合AMD规范，更多的库并不符合

> require.js是否能够加载非规范的模块呢？，
> 非AMD规范的模块在用require()加载之前, 要先用require.config()方法来定义他们的一些特征

* 举例来说，underscore和backbone这两个库，都没有采用AMD规范编写。如果要加载它们的话，必须先定义它们的特征。
```
require.config({
    shim: {
        'underscore':{
　　　　    exports: '_'
        },
        'backbone': {
　　　　    deps: ['underscore', 'jquery'],
　　　　    exports: 'Backbone'
        }
    }
});

/**
shim属性介绍
shim是专门用来配置不兼容的模块。
参数定义:
（1）exports值（输出的变量名），表明这个模块外部调用时的名称；
（2）deps数组，表明该模块的依赖性。
*/
```

### 代码约定

```
    var $ = require('jquery'),
        layer = require('layer');

    var total, values = [], obj = null;

    for(var i = 0; i <= values.length; i++ ){
        console.log(i);
    }

    console.log("Welcome !");
```

### 添加一个模块

```
    new file:   template/components.html
    new file:   webroot/static/scripts_dev/demo/components.js
    new file:   webroot/static/scripts_dev/demo/components/main.js

    modified:   deploy/tools/web_build.js
    modified:   template/components.html
    modified:   template/plugins/requirejs.html
    modified:   webroot/static/scripts_dev/demo/components.js
    
/*demo/components.js*/
    require(['jquery', 'layer'], function($, layer){
        var $       = require('jquery'),
            layer   = require('layer');
    
        console.log($);
        console.log(layer);
    
        layer.alert("Hello World!");
    });
```

#### 咱们系统中webbuild.js的配置
```
/*模块合并*/
    node tools/r.js -o tools/web_build.js;
/*配置文件*/
{
    appDir    : "../../webroot/static/scripts_dev",
    baseUrl   : "libs",
    dir       : "../../webroot/static/scripts",
    //separateCSS: true,
    buildCSS: true,
    //optimizeCss: "none",
    modules: [
        { name : "users_login" },
        { name : "users_signup" },

        { name : "offer_template" },
        { name : "offer_send" },

        { name : "demo_components" },
        { name : "demo_assert" }
    ],
    fileExclusionRegExp: /^((r|web_build)\.js)/,
    paths: {
        jquery  : 'empty:',
        layer   : 'empty:',

        users_login: '../users/login',
        users_signup: '../users/signup',

        offer_template: '../offer/template',
        offer_send: '../offer/send',

        demo_components: "../demo/components",
        demo_assert: "../demo/assert"
    }
}

```
#### 咱们系统中require.js的配置
```
<script src="{image_domain}/static/{$scripts_dir}/require.js" data-main="{$file}"></script>
<script>
    try{
        require.config({
            baseUrl: "{image_domain}/static/{$scripts_dir}/libs",
            paths: {
                jquery : '/static/lib/jquery/dist/jquery.min',
                layer  : '/static/lib/layer/layer.min',
                'socket.io' : '/static/lib/socket.io/1.2.1/socket.io',

                users_login: '../users/login',
                users_signup: '../users/signup',

                offer_template: '../offer/template',
                offer_send: '../offer/send',

                demo_assert : "../demo/assert",
                demo_components : "../demo/components"


            },
            shim: {
                'layer': {
                    deps: ['jquery'],
                    exports: 'layer'
                },
                'socket.io' : {
                    exports: "io"
                }
            }
        });
    }catch(e){
        console.log(e);
    }
</script>
```

参考资料:
> @http://www.ituring.com.cn/article/1091
> @http://justineo.github.io/singles/writing-modular-js/





