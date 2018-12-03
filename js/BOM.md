参考:
> 《JavaScript高级程序设计 第三版》

# 全局作用域

BOM的核心对象是window，他表示浏览器的实例。而所有在全局作用域中声明的`变量、函数`都会变成window`对象`的`属性和方法`，比如:
```javascript
var age = 29;
function sayAge() {
    alert(this.age);
}
alert(window.age); // 29
sayAge(); // 29
window.sayAge(); // 29
```
由于sayAge() 存在于`全局作用域`中，因此this.age被`映射到window.age`。

全局的变量`不能`通过delete删除，而`window对象`上定义的属性可以。
```javascript
var age = 29;
window.color = 'red';
delete window.age; // IE < 9抛出异常，其他浏览器返回false
delete window.color; // IE < 9抛出异常，其他浏览器返回true
alert(window.age); // 29
alert(window.color); // undefined
```
因为通过var添加的window属性有[[Configurable]]的特性，这个特性被默认设置为false，所以定义的属性不能被直接使用delete操作符删除。

# 窗口位置

IE、Safari、Opera、Chrome等浏览器都提供了`screenLeft跟screenTop`，分别是位于屏幕左边跟上边的位置，Firefox、Safari、Chrome则提供了相同的方法ScreenX跟ScreenY，Opera虽然支持但是与screenLeft跟screenTop属性不对应。
```javascript
var leftPos = (typeof window.screenLeft == "number") ?
                window.screenLeft : window.screenX;
var topPos = (typeof window.screenTop == "number") ?
                window.screenTop : window.screenY;
```

# 窗口大小

outerWidth跟outerHeight返回浏览器窗口本身尺寸

innerWidth跟innerHeight返回容器中页面视图区(viewport)的大小(减去边框宽度)

clientWidth跟clientHeight返回页面信息内容的总大小

# 间歇调用和超时调用

## 超时调用(setTimeOut)

超时调用指的是window对象的setTimeOut()方法，其中第一个参数是一个包含js代码的字符串，或者是一个函数，第二个参数是等待多久执行的毫秒数，但是经过一定时间后代码`不一定执行`。因为一段时间只能执行一段代码，为了控制执行的代码，就要有一个JavaScript任务队列，任务会按照他们`添加到队列的顺序`执行。第二个参数则代表多长时间后把它添加到任务队列中。如果队列为空，那么添加的代码会立即执行，否则要等前面代码执行完才执行。

调用setTimeOut会返回一个数值ID，表示超时调用，是计划执行的唯一标识符，可以用它来取消超时调用。要取消尚未执行的超时调用计划，可以调用clearTimeOut()方法并将超时调用ID传给它当参数用于取消。
```javascript
// 设置超时调用
var timeoutId = setTimeOut(function() {
    alert("Hello World!")
}, 1000);
// 取消超时调用
clearTimeOut(timeoutId);
```
在指定时间`未过去之前`可以调用clearTimeOut()，就可以完全取消超时调用。

注: 超时调用的代码都是在`全局作用于执行`，因此函数中的this的值在`非严格模式`下是指向`window对象`，在`严格模式`下是`undefined`

## 间歇调用(setInterval)

间歇调用与超时调用类似，只不过按指定时间间隔`重复执行代码`。setInterval同样会返回一个间歇调用ID，该ID用于在某个时刻取消间歇调用，要取消尚未执行的间歇调用，可以使用clearInterval()方法传入相对应的间歇调用ID。间歇调用在不干涉的情况下会一直执行到页面卸载。
```javascript
var num = 0;
var max = 10;
var intervalId = null;

function incrementNumber() {
    num++;
    
    // 执行次数达到max则取消后续未执行的调用
    if (num == max) {
        clearInterval(intervalId);
        alert("Done");
    }
}

intervalId = setInterval(incrementNumber, 500);
```
超时调用模拟间歇调用:
```javascript
var num = 0;
var max = 10;

function incrementNumber() {
    num++;
    // 如果执行次数未达到max设定的值，则设置另一次超时调用
    if (num < max) {
        setTimeOut(incrementNumber, 500);
    } else {
        alert("Done!");
    }
}
setTimeOut(incrementNumber, 500);
```
使用超时调用时，没必要跟踪超时调用ID，因为每次执行完之后，不再设置另一次超时调用的话，调用会自行停止。使用超时调用模拟间歇调用是一种最佳模式。在开发环境中，`很少使用真正的间歇调用`，原因是一个间歇调用可能会在前一个间歇调用结束之前启动。而像前面事例中使用超时调用，可以完全避免这一点。所以最好不要使用间歇调用。

# location对象

location对象提供了与当前窗口中加载文档有关信息，还提供了一些导航功能。它既是window对象的属性，也是document对象的属性，换句话说window.location和document.location引用的是同一个对象。它的用处不只表现它保存着当前文档的信息，还表现在它将URL解析为独立的片段。

属性名 | 例子
---|---
hash | #contents
post | www.wrox.com:80
hostname | www.wrox.com
href | http://www.wrox.com
pathname | /WilleyCDA
port | 8080
protocol | http:
search | ?q=javascript

```javascript
function getQueryStringArgs() {
    var qs = (location.search.length > 0 ? location.search.substring(1) : ""),
        args = {},
        items = qs.length ? qs.split("&") : [],
        item = null,
        name = null,
        value = null,
        i = 0,
        len = items.length;
    for (i = 0; i < len; i++) {
        item = items[i].split("=");
        name = decodeURIComponent(item[0]);
        value = decodeURIComponenet(item[1]);
        
        if (name.length) {
            args[name] = value;
        }
    }
    return args;
}
```

