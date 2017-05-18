# Coding Standards

---
## javascript 代码规范
代码规范我们应该遵循古老的原则：“能做并不意味着应该做”。

### 全局命名空间污染
总是将代码包裹在一个立即的函数表达式里面，形成一个独立的模块。
#### 不推荐
```js
    var x = 10,
    y = 100;
    console.log(window.x + ' ' + window.y);
```
#### 推荐
```js
    ;(function(window){
        'use strict';
        var x = 10,
            y = 100;
        console.log(window.x + ' ' + window.y);
    }(window));
``` 
### 立即执行函数
在立即执行函数里面，如果有用到全局变量应该通过变量传递的方式，让立即执行函数的函数体在调用时，能以局部变量的形式调用，在一定程度上提升程序性能。 
并且应该在立即执行函数的形参里加上undefined，在最后一个位置，这是因为ES3里undefined是可以读写的，如果在全局位置更改undefined的值，你的代码可能得不到逾期的结果。 
另外推荐在立即执行函数开始跟结尾都添加上分号，避免在合并时因为别人的代码不规范而影响到我们自己的代码。
#### 不推荐
```js
    (function(){
        'use strict';
        var x = 10,
            y = 100,
            c,
            elem=$('body');
        console.log(window.x + ' ' + window.y);
        $(document).on('click',function(){
     
        });
        if(typeof c==='undefined'){
            //你的代码
        }
    }());
```
#### 推荐
```js
    ;(function($,window,document,undefined){
        'use strict';
        var x = 10,
            y = 100,
            c,
            elem=$('body');
        console.log(window.x + ' ' + window.y);
        $(document).on('click',function(){
     
        });
        if(typeof c==='undefined'){
            //你的代码
        }
    }(jQuery,window,document));
```
### 严格模式
ECMAScript 5 严格模式可在整个脚本或独个方法内被激活。它对应不同的 javascript 语境会做更加严格的错误检查。严格模式也确保了 javascript 代码更加的健壮，运行的也更加快速。

严格模式会阻止使用在未来很可能被引入的预留关键字。

你应该在你的脚本中启用严格模式，最好是在独立的 立即执行函数 中应用它。避免在你的脚本第一行使用它而导致你的所有脚本都启动了严格模式，这有可能会引发一些第三方类库的问题。 

#### 不推荐
```js
    'use strict';
    (function(){
     
    }());
```
### 推荐
```js
    (function(){
        'use strict';
    }());
```
### 变量声明
对所有的变量声明，我们都应该指定var，如果没有指定var，在严格模式下会报错，并且同一个作用域内的变量应该尽量采用一个var去声明，多个变量用“,”隔开。 
#### 不推荐
```js
    function myFun(){
        x=5;
        y=10;
    }
```
#### 不完全推荐
```js
    function myFun(){
        var x=5;
        var y=10;
    }
```
### 推荐
```js
    function myFun(){
        var x=5,
            y=10;
    }
```
### 使用带类型判断的比较判断
总是使用 === 精确的比较操作符，避免在判断的过程中，由 JavaScript 的强制类型转换所造成的困扰。

如果你使用 === 操作符，那比较的双方必须是同一类型为前提的条件下才会有效。
#### 不推荐
```js
    (function(w){
      'use strict';
     
      w.console.log('0' == 0); // true
      w.console.log('' == false); // true
      w.console.log('1' == true); // true
      w.console.log(null == undefined); // true
     
      var x = {
        valueOf: function() {
          return 'X';
        }
      };
     
      w.console.log(x == 'X');//true
     
    }(window.console.log));
```
#### 推荐
```js
    (function(w){
      'use strict';
     
      w.console.log('0' === 0); // false
      w.console.log('' === false); // false
      w.console.log('1' === true); // false
      w.console.log(null === undefined); // false
     
      var x = {
        valueOf: function() {
          return 'X';
        }
      };
     
      w.console.log(x === 'X');//false
     
    }(window));
```
### 变量赋值时的逻辑操作
逻辑操作符 || 和 && 也可被用来返回布尔值。如果操作对象为非布尔对象，那每个表达式将会被自左向右地做真假判断。基于此操作，最终总有一个表达式被返回回来。这在变量赋值时，是可以用来简化你的代码的。 

#### 不推荐
```js
    if(!x) {
      if(!y) {
        x = 1;
      } else {
        x = y;
      }
    }
```
#### 推荐
```js
    x = x || y || 1;
```
### 分号
总是使用分号，因为隐式的代码嵌套会引发难以察觉的问题。当然我们更要从根本上来杜绝这些问题[1] 。以下几个示例展示了缺少分号的危害：
```js
        // 1.
        MyClass.prototype.myMethod = function() {
          return 42;
        }  //这里没有分号
         
        (function() {
         
        })();
         
         //2.
        var x = {
          'i': 1,
          'j': 2
        }  // 这里没有分号
        //我知道这样的代码你可能永远不会写，但是还是举一个例子
        [ffVersion, ieVersion][isIE]();
         
         // 3.
        var THINGS_TO_EAT = [apples, oysters, sprayOnCheese]  // 这里没有分号
         
        -1 == resultOfOperation() || die();
```
### 错误结果
1.JavaScript 错误 —— 首先返回 42 的那个 function 被第二个function 当中参数传入调用，接着数字 42 也被“调用”而导致出错。 
2.八成你会得到 ‘no such property in undefined’ 的错误提示，因为在真实环境中的调用是这个样子：xffVersion, ieVersion(). 
3.die 总是被调用。因为上一行不加分号的数组减 1 的结果是 NaN，它不等于任何东西（无论 resultOfOperation 是否返回 NaN）。所以最终的结果是 die() 执行完所获得值将赋给 THINGS_TO_EAT.
### 语句块内的函数声明
切勿在语句块内声明函数，在 ECMAScript 5 的严格模式下，这是不合法的。函数声明应该在作用域的顶层。但在语句块内可将函数申明转化为函数表达式赋值给变量。 

#### 不推荐
```js
    if (x) {
      function foo() {}
    }
```
#### 推荐
```js
    if (x) {
      var foo = function() {};
    }
``` 
### 不要使用eval函数
eval() 不但混淆语境还很危险，总会有比这更好、更清晰、更安全的另一种方案来写你的代码，因此尽量不要使用 eval 函数。

### 数组和对象字面量
1.用数组和对象字面量来代替数组和对象构造器。数组构造器很容易让人在它的参数上犯错。 
#### 不推荐
```js
    //数组长度3
    var a1 = new Array(x1, x2, x3);
    //数组长度2
    var a2 = new Array(x1, x2);
     
    //如果x1是一个自然数，那么它的长度将为x1
    //如果x1不是一个自然数，那么它的长度将为1
    var a3 = new Array(x1);
     
    var a4 = new Array();
```
正因如此，如果将代码传参从两个变为一个，那数组很有可能发生意料不到的长度变化。为避免此类怪异状况，请总是采用可读的数组字面量。 
#### 推荐
```js
    var a = [x1, x2, x3];
    var a2 = [x1, x2];
    var a3 = [x1];
    var a4 = [];
```
2.对象构造器不会有类似的问题，但是为了可读性和统一性，我们应该使用对象字面量。
#### 不推荐
```js
    var o = new Object();
 
    var o2 = new Object();
    o2.a = 0;
    o2.b = 1;
    o2.c = 2;
    o2['strange key'] = 3;
```
#### 推荐
```js
    var o = {};
    var o2 = {
      a: 0,
      b: 1,
      c: 2,
      'strange key': 3
    };
```

### 三元条件判断(if的快捷方法)
用三元操作符分配或返回语句。在比较简单的情况下使用，避免在复杂的情况下使用。没人愿意用 10 行三元操作符把自己的脑子绕晕。 
#### 不推荐
```js
    if(x === 10) {
      return 'valid';
    } else {
      return 'invalid';
    }
```
#### 推荐
```js
    return x === 10 ? 'valid' : 'invalid';
``` 
### for循环
使用for循环过程中，数组的长度，使用一个变量来接收，这样有利于代码执行效率得到提高，而不是每走一次循环，都得重新计算数组长度 
#### 不推荐
```js
    for(var i=0;i<arr.length,i++){
     
    }
```
#### 推荐
```js
    for(var i=0,len=arr.length;i<len;i++){
 
    }
```
### 重复的DOM操作
重复的dom操作，使用一个变量来进行接收很有必要，而不是频繁的去操作dom树，这对性能与代码的整洁及易维护性带来不好的影响。
#### 不推荐
```js
    $('.myDiv').find('.span1').text('1');
    $('.myDiv').find('.span2').text('2');
    $('.myDiv').find('.span3').text('3');
    $('.myDiv').find('.span4').text('4');
```
#### 推荐
```js
    var mydiv=$('.myDiv');
    mydiv.find('.span1').text('1');
    mydiv.find('.span2').text('2');
    mydiv.find('.span3').text('3');
    mydiv.find('.span4').text('4');
```
在jquery .end()可使用的情况下应该优先使用.end() 
#### 推荐
```js
      $('.myDiv').find('.span1').text('1')
           .end().find('.span2').text('2')
           .end().find('.span3').text('3')
           .end().find('.span4').text('4');
```
### 注释规范
在描写注释时，推荐格式化且统一的注释风格，在写注释时尽量描述写代码时的思路，而不是代码做了什么。 
#### 不推荐
```js
    //获取订单
    function getOrderByID(id){
        var order;
        //...
        return order;
    }
```
方法的注释应该统一用块级注释。
#### 推荐
```js
    /*
     * 根据订单id获取订单详细数据
     * @param  {[number]} id [订单ID]
     * @return {[order]}    [订单详细信息]
     */
    function getOrderByID(id){
        var order;
        //...
        return order;
    }
```

