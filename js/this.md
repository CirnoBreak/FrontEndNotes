# this初步理解
当函数被调用时，会创建一个活动记录(上下文)，这个记录包括函数在哪被调用(调用栈)、函数调用方法、传入参数等信息，this就是记录其中的一个属性
误解：this指向函数自身，this指向函数函数的词法作用域。
而实际上，this是在函数被调用时发生的绑定，他的指向取决于函数在哪被调用

# this的绑定规则

## 默认绑定
``` javascript
function foo() {
    console.log( this.a );
}
var a = 2;
foo(); // 2
```
在调用foo()的时候this.a被解析成全局变量a，因为此时应用了this的默认绑定指向了全局  
为什么是默认绑定？因为foo()直接使用是不带任何修饰的函数进行引用调用，因此只能用默认绑定而不能用其他规则
``` javascript
function foo() {
"use strict";
console.log( this.a );
}
var a = 2;
foo(); // TypeError: this is undefined
```
使用了严格模式后，全局对象无法使用默认绑定，this会被绑定到undefined
``` javascript
function foo() {
    console.log( this.a );
}
var a = 2;
(function(){
    "use strict";
    foo(); // 2
})();
```
严格模式与foo的调用位置无关

## 隐式绑定
``` javascript
function foo() {
    console.log( this.a );
}
var obj = {
    a: 2,
    foo: foo
};
obj.foo(); // 2
```
foo()严格来说不属于obj，但这个调用位置会使用obj上下文去引用foo()。当函数引用有上下文对象的时候，隐式绑定会把this绑定到这个上下文
``` javascript
function foo() {
    console.log( this.a );
}
var obj2 = {
    a: 42,
    foo: foo
};
var obj1 = {
    a: 2,
    obj2: obj2
};
obj1.obj2.foo(); // 42
```
对象引用层只有最顶层或者最后一层会影响其调用位置

## 隐式丢失
``` javascript
function foo() {
    console.log( this.a );
}
var obj = {
    a: 2,
    foo: foo
};
var bar = obj.foo; // 函数别名！
var a = "oops, global"; // a 是全局对象的属性
bar(); // "oops, global"
```
bar虽然是obj.foo的一个引用，但实际上引用的是foo函数的本身，此时的bar是一个不带任何修饰的绑定，应用了默认绑定

在传入回调函数的时候也会失去绑定：
``` javascript
例子1:
function foo() {
    console.log( this.a );
}
function doFoo(fn) {
    // fn 其实引用的是 foo
    fn(); // <-- 调用位置！
}
var obj = {
    a: 2,
    foo: foo
};
var a = "oops, global"; // a 是全局对象的属性
doFoo( obj.foo ); // "oops, global"

例子2:
function foo() {
    console.log( this.a );
}
var obj = {
    a: 2,
    foo: foo
};
var a = "oops, global"; // a 是全局对象的属性
setTimeout( obj.foo, 100 ); // "oops, global"
实际上的setTimeout：
function setTimeout(fn,delay) {
    // 等待 delay 毫秒
    fn(); // <-- 调用位置！
}
```
总结： 回调函数丢失this的绑定很常见，调用回调函数的函数也可能会修改this

# 显式绑定
使用call()或者apply()方法实现，他们的第一个参数是一个对象，他们会把这个对象绑定到这个this，在调用函数的时候指定这个this，就可以指定this的绑定对象，这就是显式绑定。
``` javascript
function foo() {
console.log( this.a );
}
var obj = {
a:2
};
foo.call( obj ); // 2
```
通过foo.call(),调用时foo强制把this绑定到obj上，如果传入一个原始值(boolean、String、Number)当做this的绑定对象，这个原始值会被转成他的对象形式(new Boolean()、new String()、new Number())
## 硬绑定
显式绑定仍然无法解决绑定丢失，但显式绑定变种(硬绑定)可以解决
``` javascript
function foo() {
    console.log( this.a );
}
var obj = {
    a:2
};
var bar = function() {
    foo.call( obj );
};
bar(); // 2
setTimeout( bar, 100 ); // 2
// 硬绑定的 bar 不可能再修改它的 this
bar.call( window ); // 2
```
如何运行？ 先创建函数bar()，并在内部手动调用foo.call(obj),强制把foo的this绑定到obj,之后无论如何调用bar都会在obj上调用foo

硬绑定的应用场景就是创建一个包装函数，传入所有参数并返回所有收到的值。
例子:
``` javascript
function foo(something) {
    console.log( this.a, something );
    return this.a + something;
}
var obj = {
    a:2
};
var bar = function() {
    return foo.apply( obj, arguments );
};
var b = bar( 3 ); // 2 3
console.log( b ); // 5
```
另一种就是创建一个可以重复利用的辅助函数，例子:
``` javascript
function foo(something) {
    console.log( this.a, something );
    return this.a + something;
} 
// 简单的辅助绑定函数
function bind(fn, obj) {
    return function() {
    return fn.apply( obj, arguments );
};
}
var obj = {
    a:2
};
var bar = bind( foo, obj );
var b = bar( 3 ); // 2 3
console.log( b ); // 5
```
ES5提供了内置方法Function.prototype.bind实现硬绑定，会把参数设置为this的执行上下文并调用原始函数，例子:
``` javascript
function foo(something) {
    console.log( this.a, something );
    return this.a + something;
}
var obj = {
    a:2
};
var bar = foo.bind( obj );
var b = bar( 3 ); // 2 3
console.log( b ); // 5
```
# new绑定
使用new来调用函数会发生下列操作:
1. 创建（或者说构造）一个全新的对象。
2. 这个新对象会被执行原型连接。
3. 这个新对象会绑定到函数调用的 this。
4. 如果函数没有返回其他对象， 那么 new 表达式中的函数调用会自动返回这个新对象。
``` javascript
function foo(a) {
    this.a = a;
}
var bar = new foo(2);
console.log( bar.a ); // 2
```