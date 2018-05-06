# 前端基础 - Javascript

## 一个页面从输入URL到加载完成发生了什么
- 1.把URL分割成几个部分：协议、网络地址、资源路径。
  - 其中网络地址指示该连接网络上哪一台计算机，可以是域名或者IP地址，可以包括端口号；
  - 协议是从该计算机获取资源的方式，常见的是HTTP、FTP，不同协议有不同的通讯内容格式；
  - 资源路径指示从服务器上获取哪一项资源。
- 2.如果地址不是一个IP地址，通过DNS（域名系统）将该地址解析成IP地址。IP地址对应着网络上一台计算机，DNS服务器本身也有IP，你的网络设置包含DNS服务器的IP。
例如：www.guokr.com 不是一个IP，向DNS询问请求www.guokr.com 对应的IP，获得IP： 111.13.57.142。这个过程里，你的电脑直接询问的DNS服务器可能没有www.guokr.com 对应的IP，就会向它的上级服务器询问，上级服务器同样可能没有，就依此一层层向上找，最高可达根节点，找到或者全部找不到为止。
- 3.如果地址不包含端口号，根据协议的默认端口号确定一个。向IP和端口号发起网络连接。例如：向111.13.57.142的80端口发起连接
- 4.根据http协议要求，组织一个请求的数据包
- 5.服务器响应请求，将数据返回给浏览器。数据可能是根据HTML协议组织的网页，里面包含页面的布局、文字。数据也可能是图片、脚本程序等。如果资源路径指示的资源不存在，服务器就会返回著名的404错误。
- 6.如果（5）返回的是一个页面，根据页面里一些外链的URL，例如图片的地址，按照（1）－（5）再次获取。
- 7.开始根据资源的类型，将资源组织成屏幕上显示的图像，这个过程叫渲染，网页渲染是浏览器最复杂、最核心的功能。
- 8.将渲染好的页面图像显示出来，并开始响应用户的操作。

## HTTP协议
- 1xx：指示信息--表示请求已接收，继续处理
- 2xx：成功--表示请求已被成功接收、理解、接受
- 3xx：重定向--要完成请求必须进行更进一步的操作
- 4xx：客户端错误--请求有语法错误或请求无法实现
- 5xx：服务器端错误--服务器未能实现合法的请求

## 常用的DOM操作常用的DOM操作，新建、添加、删除、移动、查找
```Javascript
document.getElementById("id");//通过id查找，返回唯一的节点，如果有多个将会返回第一个，在IE6、7中有个bug，会返回name值相同的元素，所有要做一个兼容  
document.getElementsByClassName("class");//通过class查找，返回节点数组  
document.getElementsByTagName("div");  
// 创建节点
document.createDocumentFragment();//创建内存文档碎片  
document.createElement();//创建元素  
document.createTextNode();//创建文本节点  
// 添加节点
var ele = document.getElementById("my_div");  
var oldEle = document.createElement("p");  
var newEle=document.createElement("div");  
ele.appendChild(oldEle);  
// 移除  
ele.removeChild(oldEle);  
// 替换  
ele.replaceChild(newEle,oldEle);  
// 插入  
ele.insertBefore(oldEle,newEle);//在newEle之前插入 oldEle节点  
// 复制节点  
var cEle = oldEle.cloneNode(true);//深度复制，复制节点下面所有的子节点  
cEle = oldEle.cloneNode(false);//只复制当前节点，不复制子节点    
// 移动节点  
var cloneEle = oldEle.cloneNode(true);//被移动的节点  
document.removeChild(oldEle);//删除原节点  
document.insertBefore(cloneEle,newEle);//插入到目标节点之前  
```

## DOM的默认事件、事件模型、阻止默认事件、冒泡事件的方式
>DOM(文档对象模型)结构是一个树型结构，当一个HTML元素产生一个事件时，该事件会在元素结点与根节点之间按特定的顺序传播，路径所经过的节点都会收到该事件，这个传播过程可称为DOM事件流。

>事件不仅仅对触发的目标元素产生影响，它们还会对沿着DOM结构的所有元素产生影响。这就是大家所熟悉的事件转送。

```Javascript
// 跨浏览器的停止事件传递的方法
function someHandle(event) {
  event = event || window.event;
  if (event.stopPropagation){
    event.stopPropagation();
  } else {
    event.cancelBubble = true;  // IE
  }
}
```
```Javascript
// 取消事件传递的默认处理方法是：
function someHandle(event) {
  event = event || window.event;
  if (event.preventDefault){
    event.preventDefault();
  } else {
    event.returnValue = false;  // IE
  }
}
```

### 捕获型应用场合：
当一个系统组合键发生时，如果注册了系统全局快捷键监听器，该事件就先被操作系统层捕获，全局监听器就先于应用程序快捷键监听器得到通知，也就是全局的先获得控制权，它有权阻止事件的进一步传递。
### 冒泡型的应用场合：
可以说我们平时用的都是冒泡事件模型，因为IE只支持这模型。这里还是说说，在恰当利用该模型可以提高脚本性能。在元素一些频繁触发的事件中,如onmousemove,onmouseover,onmouseout,如果明确事件处理后没必要进一步传递，那么就可以大胆的取消它。此外，对于子结点事件监听器的处理会对父层监听器处理造成负面影响的，也应该在子结点监听器中禁止事件进一步向上传递以消除影响。

```Javascript
// 完整的事件处理兼容性函数
var EventUtil = {
  addHandler: function(element, type, handler){
    if (element.addEventListener){
      element.addEventListener(type, handler, false);
    } else if (element.attachEvent){
      element.attachEvent("on" + type, handler);
    } else {
      element["on" + type] = handler;
    }
  },
  removeHandler: function(element, type, handler){
    if (element.removeEventListener){
      element.removeEventListener(type, handler, false);
    } else if (element.detachEvent){
      element.detachEvent("on" + type, handler);
    } else {
      element["on" + type] = null;
    }
  },
  getEvent: function(event){
    return event ? event : window.event;
  },
  getTarget: function(event){
    return event.target || event.srcElement;
  },
  preventDefault: function(event){
    if (event.preventDefault){
      event.preventDefault();
    } else {
      event.returnValue = false;
    }
  },
  stopPropagation: function(event){
    if (event.stopPropagation){
      event.stopPropagation();
    } else {
      event.cancelBubble = true;
    }
  }
};
```

## 事件委托、事件绑定
- 事件委托和事件绑定的占用内存对比：
如果一个整体页面里有大量的需要绑定事件的元素，每个元素都要绑定一个函数，而每个函数都是对象，对象就会占用很多内存，内存中的对象越多，性能就越差。而事件委托，只在绑定事件的上级元素上绑定函数，次数十分有限（一般每次委托只有一次）；另外，在事件被触发时，才会取出触发事件的元素来进一步处理。总体来讲，非常节约内存。
- 使用“事件委托”时，需要注意的问题：
使用“事件委托”时，并不是说把事件委托给的元素越靠近顶层就越好。事件冒泡的过程也需要耗时，越靠近顶层，事件的”事件传播链”越长，也就越耗时。

## 作用域链（Scope Chain）
>在JavaScript中，函数也是对象，实际上，JavaScript里一切都是对象。函数对象和其它对象一样，拥有可以通过代码访问的属性和一系列仅供JavaScript引擎访问的内部属性。
其中一个内部属性是[[Scope]]，该内部属性包含了函数被创建的作用域中对象的集合，这个集合被称为函数的作用域链，它决定了哪些数据能被函数访问。

## 原型继承
>在Javascript中，每个函数都有一个原型属性prototype指向自身的原型，而由这个函数创建的对象也有一个__proto__属性指向这个原型，而函数的原型是一个对象，所以这个对象也会有一个__proto__指向自己的原型，这样逐层深入直到Object对象的原型，这样就形成了原型链。

## Ajax原理
Ajax的原理简单来说通过XmlHttpRequest对象来向服务器发异步请求，从服务器获得数据，然后用javascript来操作DOM而更新页面。这其中最关键的一步就是从服务器获得请求数据。
XMLHttpRequest是ajax的核心机制，它是在IE5中首先引入的，是一种支持异步请求的技术。简单的说，也就是javascript可以及时向服务器提出请求和处理响应，而不阻塞用户。达到无刷新的效果。

XMLHttpReques的属性有：
- onreadystatechange  每次状态改变所触发事件的事件处理程序。
- responseText  从服务器进程返回数据的字符串形式。
- responseXML  从服务器进程返回的DOM兼容的文档数据对象。
- status  从服务器返回的数字代码，比如常见的404（未找到）和200（已就绪）
- statusText  伴随状态码的字符串信息
- readyState  对象状态值
  - 0 (未初始化) 对象已建立，但是尚未初始化（尚未调用open方法）
  - 1 (初始化) 对象已建立，尚未调用send方法
  - 2 (发送数据) send方法已调用，但是当前的状态及http头未知
  - 3 (数据传送中) 已接收部分数据，因为响应及http头不全，这时通过responseBody和responseText获取部分数据会出现错误，
  - 4 (完成) 数据接收完毕,此时可以通过通过responseXml和responseText获取完整的回应数据。

```JavaScript
var xmlhttp;
function loadXMLDoc(url) {
xmlhttp=null;
if (window.XMLHttpRequest)
  {// code for all new browsers
  xmlhttp=new XMLHttpRequest();
  }
else if (window.ActiveXObject)
  {// code for IE5 and IE6
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
if (xmlhttp!=null)
  {
  xmlhttp.onreadystatechange=state_Change;
  xmlhttp.open("GET",url,true);
  xmlhttp.send(null);
  }
else
  {
  alert("Your browser does not support XMLHTTP.");
  }
}

function state_Change()
{
if (xmlhttp.readyState==4)
  {// 4 = "loaded"
  if (xmlhttp.status==200)
    {// 200 = OK
    // ...our code here...
    }
  else
    {
    alert("Problem retrieving XML data");
    }
  }
}

```

## Ajax跨域的实现原理
>ajax和jsonp其实本质上是不同的东西。ajax的核心是通过XmlHttpRequest获取非本页内容，而jsonp的核心则是动态添加script标签来调用服务器提供的js脚本(script标签的src属性是没有跨域的限制的)。

>WebSocket标准支持跨域的数据交换，也是一个将来可选的跨域数据交换的解决方案。

```JavaScript
$.ajax({
   type: "get",
   async: false,
   url: "http://flightQuery.com/jsonp/flightResult.aspx?code=CA1998",
   dataType: "jsonp",
   jsonp: "callback",//传递给请求处理程序或页面的，用以获得jsonp回调函数名的参数名(一般默认为:callback)
   jsonpCallback:"flightHandler",//自定义的jsonp回调函数名称，默认为jQuery自动生成的随机函数名，也可以写"?"，jQuery会自动为你处理数据
   success: function(json){
     alert('您查询到航班信息：票价： ' + json.price + ' 元，余票： ' + json.tickets + ' 张。');
   },
   error: function(){
     alert('fail');
   }
 });
```

## JavaScript arguments对象
- 在JavaScript中，arguments对象是比较特别的一个对象，实际上是当前函数的一个内置属性。如何转为数组？Array.prototype.slice.call/apply(arguments);
- arguments对象的长度是由实参个数而不是形参个数决定的。形参是函数内部重新开辟内存空间存储的变量，但是其与arguments对象内存空间并不重叠。
- 由JavaScript中函数的声明和调用特性，可以看出JavaScript中函数是不能重载的。
- arguments对象中有一个非常有用的属性：callee。arguments.callee返回此arguments对象所在的当前函数引用。在使用函数递归调用时推荐使用arguments.callee代替函数名本身。

## cookie 域概念
- 路径能解决在同一个域下访问 cookie 的问题，咱们接着说 cookie 实现同域之间访问的问题。
语法：document.cookie = "name=value;path=path;domain=domain"。
例如："www.qq.com" 与 "sports.qq.com" 公用一个关联的域名"qq.com"，我们如果想让 "sports.qq.com" 下的cookie被 "www.qq.com" 访问，我们就需要用到 cookie 的domain属性，并且需要把path属性设置为 "/"。
例：document.cookie = "username=Darren;path=/;domain=qq.com"。
注：一定的是同域之间的访问，不能把domain的值设置成非主域的域名。
- cookie 安全性
通常 cookie 信息都是使用HTTP连接传递数据，这种传递方式很容易被查看，所以 cookie 存储的信息容易被窃取。假如 cookie 中所传递的内容比较重要，那么就要求使用加密的数据传输。
所以 cookie 的这个属性的名称是“secure”，默认的值为空。如果一个 cookie 的属性为secure，那么它与服务器之间就通过HTTPS或者其它安全协议传递数据。
语法：document.cookie = "username=Darren;secure"把cookie设置为secure，只保证 cookie 与服务器之间的数据传输过程加密，而保存在本地的 cookie文件并不加密。如果想让本地cookie也加密，得自己加密数据。
注：就算设置了secure 属性也并不代表他人不能看到你机器本地保存的 cookie 信息。

## HTMl5的sessionStorage和localStorage
- html5中的Web Storage包括了两种存储方式：sessionStorage和localStorage。
Cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在 ，而Web Storage仅仅是为了在本地“存储”数据而生
 - sessionStorage用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。因此sessionStorage不是一种持久化的本地存储，仅仅是会话级别的存储。
 - localStorage用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。
- web storage和cookie的区别
Web Storage的概念和cookie相似，区别是它是为了更大容量存储设计的。Cookie的大小是受限的，并且每次你请求一个新的页面的时候Cookie都会被发送过去，这样无形中浪费了带宽，另外cookie还需要指定作用域，不可以跨域调用。

## jquery.ready事件和document.load事件的区别
页面加载完成有两种事件，一是ready($(document).ready())，表示文档结构已经加载完成（不包含图片等非文字媒体文件），二是onload，指示页面包含图片等文件在内的所有元素都加载完成。

## JS组成
我们都知道， javascript 有三部分构成，ECMAScript，DOM和BOM，根据宿主（浏览器）的不同，具体的表现形式也不尽相同，ie和其他的浏览器风格迥异。
1. DOM 是 W3C 的标准； [所有浏览器公共遵守的标准]
2. BOM 是 各个浏览器厂商根据 DOM在各自浏览器上的实现;[表现为不同浏览器定义有差别,实现方式不同]
3. window 是 BOM 对象，而非 js 对象；
DOM（文档对象模型）是 HTML 和 XML 的应用程序接口（API）。
BOM 主要处理浏览器窗口和框架，不过通常浏览器特定的 JavaScript 扩展都被看做 BOM 的一部分。
javacsript是通过访问BOM（Browser Object Model）对象来访问、控制、修改客户端(浏览器)，由于BOM的window包含了document，window对象的属性和方法是直接可以使用而且被感知的，因此可以直接使用window对象的document属性，通过document属性就可以访问、检索、修改XHTML文档内容与结构。
因为document对象又是DOM（Document Object Model）模型的根节点。可以说，BOM包含了DOM(对象)，浏览器提供出来给予访问的是BOM对象，从BOM对象再访问到DOM对象，从而js可以操作浏览器以及浏览器读取到的文档。其中DOM包含：window
从window.document已然可以看出，DOM的最根本的对象是BOM的window对象的子对象。
区别：DOM描述了处理网页内容的方法和接口，BOM描述了与浏览器进行交互的方法和接口。

## AMD & CMD
commonjs是用在服务器端的，同步的，如nodejs
amd, cmd是用在浏览器端的，异步的，如requirejs和seajs
其中，amd先提出，cmd是根据commonjs和amd基础上提出的。

CommonJS 加载模块是同步的，所以只有加载完成才能执行后面的操作。像Node.js主要用于服务器的编程，加载的模块文件一般都已经存在本地硬盘，所以加载起来比较快，不用考虑异步加载的方式，所以CommonJS规范比较适用。但如果是浏览器环境，要从服务器加载模块，这是就必须采用异步模式。所以就有了 AMD  CMD 解决方案。

1.对于依赖的模块AMD是提前执行，CMD是延迟执行。不过RequireJS从2.0开始，也改成可以延迟执行（根据写法不同，处理方式不通过）。

2.CMD推崇依赖就近，AMD推崇依赖前置。
AMD
AMD规范定义了一个自由变量或者说是全局变量 define 的函数。
define( id?, dependencies?, factory );    
第一个参数 id 为字符串类型，表示了模块标识，为可选参数。若不存在则模块标识应该默认定义为在加载器中被请求脚本的标识。如果存在，那么模块标识必须为顶层的或者一个绝对的标识。
 第二个参数，dependencies ，是一个当前模块依赖的，已被模块定义的模块标识的数组字面量。
 第三个参数，factory，是一个需要进行实例化的函数或者一个对象。

 ```JavaScript
define(['./a','./b'], function (a, b) {
    //依赖一开始就写好
    a.test();
    b.test();
});
```
CMD
在CMD中，一个模块就是一个文件，格式为：define( factory );
```JavaScript
define(function (requie, exports, module) {
    //依赖可以就近书写
    var a = require('./a');
    a.test();
     ...
    //软依赖
    if (status) {

        var b = requie('./b');
        b.test();
    }
});
```

## 0级DOM，2级DOM
>DOM0就是直接通过 onclick写在html里面的事件；
>DOM2是通过addEventListener绑定的事件, 还有IE下的DOM2事件通过attachEvent绑定;

## 细节
- 提示：JavaScript 是脚本语言。浏览器会在读取代码时，逐行地执行脚本代码。而对于传统编程来说，会在执行前对所有代码进行编译。
- 如果重新声明 JavaScript 变量，该变量的值不会丢失：
在以下两条语句执行后，变量 carname 的值依然是 "Volvo"：
var carname="Volvo";
var carname;
- var car = new String; 等价于var car = new String();
- 极大或极小的数字可通过科学（指数）计数法来写：var y=123e5;
- 所有 JavaScript 数字均为 64 位；
