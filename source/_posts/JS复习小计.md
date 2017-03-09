---
title: JS复习小计
date: 2017-03-09 16:41:24
categories:
tags:
---
---

* js中存在变量提升,即先解析代码，获取**所有**被声明的变量，然后再一行一行地运行。这与python等脚本语言不同.
```javaScript
//可以成功运行,tips:变量提升只对var命令声明的变量有效，如果一个变量不是用var命令声明的，就不会发生变量提升。
console.log(a);
var a = 1;
```

---

* 区块不构成单独的作用域(ECMAScript6则相反)

  ```javaScript
  {
    var a = 1;
  }
  a // 1

  ```

---

* switch-case使用的是严格比较符(===)   

---
* 支持与java一样的break/continue label.

  ```javaScript
  top:
    for (var i = 0; i < 3; i++){
      for (var j = 0; j < 3; j++){
        if (i === 1 && j === 1) break top;
        console.log('i=' + i + ', j=' + j);
      }
    }
  ```
---
*  6种基本类型number,string,boolean,object(real object,array,function),undefined,null.可以看出函数是作为第一等公民存在的.

---
*  结合上诉六种类型使用typeof判断一个变量的类型.

  ```javaScript
  //number --> "number";string --> "string";boolean --> "boolean"
  //function --> "function",undefined --> "undefined",除此这五种都返回"object"
  //以上几个类型中特殊的是null,type null === "object",但null本质上是特殊值.

  var v;
  //使用严格比较符,该语句为true
  if (typeof v === "undefined") {
    // ...
  }
  ```
---
* null表示空值,是一个设计上的缺陷,一般使用undefined(未定义)即可.

---
* 以下六个值被视为false(自动转换为false),其他值均为true,包括空数组[]/空对象{}都是true.

  ```javaScript
      undefined;null;false;0;NaN;""或''（空字符串)
  ```
---
* number汇总

  ```javaScript
  js里只有64位float一种类型(IEEE 754),在某些需要整形的场合会强转成int.

  1. 由于采用IEEE 754表示,所以浮点数比较时可能出错.
     eg: 0.1+0.2 === 0.3 //false

  2. IEEE 754中第一位表示整体值的正负,接下11位表示指数部分,最后52位表示小数部分.(IEEE小数部分自带一个1,即实际是1.XXXXX..XXXX,实际有53位)
  整体表示为:(-1)^符号位 * 1.xx...xx * 2^指数位

  3. 指数位表示了数值范围,所以实际最大的数值范围为,(2^1024,2^-1023)

  4. 小数位指示了数值精度,所以实际最大的精度为2^53-1 --> 9007199254740991,即只有[-(2^53-1),2^53-1]范围内的整数可以精确表示!!!不在该范围内的值(除了科学计数法变大)一定存在舍入,这也是jquery.parseJSON转换一个大的id时容易出错的原因.
  // 多出的三个有效数字，将无法保存
  9007199254740992111
  // 9007199254740992000除以正零得到+Infinity，除以负零得到-Infinity

  5. 0b前导表示二进制

  6. 除以正零得到+Infinity，除以负零得到-Infinity

  7. NaN表示非数字,如0/0 --> NaN 5 - 'x' --> NaN;
  它的类型是number,不是一种独立类型.NaN不等于任何值，包括它本身.
  isNaN(x),用于判断x是不是NaN,如果x不是number,则调用Number(x)进行类型转换.NaN和任何值比较返回false;

  8. Infinity表示无穷大,-Infinity表示负无穷.isFinite判断一个数是不是非无穷大,isFinite(1) --> true,isFinite(Infinite) --> false,isFinite(NaN) --> false;
  ```
---
* 数值函数

  ```javaScript
  1. parseInt(x),将字符串x,按字符逐个解析成int,直到遇到非数字.
  若第一个数字非数字(头部空格会自动去掉),返回NaN.
  若传入其他值,先转换成string,再使用规则解析.
  parseInt("1.2") // 1
  parseInt(1e5)// 100000,string(1e5) --> '100000'
  parseInt("1e10")// 1
  parseInt('a') // NaN
  parseInt('   21') // 21

  parseInt(),可以传入第二个数表示进制,此时前面的字符串不应该有前导.
  字符串以0X开头,按16进制解析,parseInt("0x10") --> 16
  字符串以0开头,按照10进制解析,parseInt("010") --> 10

  2.parseFloat,将字符串解析成float,可以解析.与e符号,也会自动取出前导空白字符.
  parseFloat("1.2e5") // 120000,可以解析了
  如果不是字符串,也会先进行类型转换.
  parseFloat([])//NaN,无法转换,返回NaN
  parseFloat("")//NaN
  与Number的区别如下
  parseFloat(true)  // NaN
  Number(true) // 1

  parseFloat(null) // NaN
  Number(null) // 0

  parseFloat('') // NaN
  Number('') // 0

  parseFloat('123.45#') // 123.45
  Number('123.45#') // NaN
  ```
---
* 字符串汇总
  ```JavaScript
  1. 使用"abx"[2]来获得某个字符.

  2. JS内部使用UTF16编码,"s".length返回码元个数,对于辅助平面不特殊处理.

  3. JavaScript引擎不支持辅助平面解析,所以涉及到辅助平面字符的字符串处理函数会出错.
  以下是正确提取支持辅助平面字符的函数
  function getSymbols(string) {
    var length = string.length;
    var index = -1;
    var output = [];
    var character;
    var charCode;
    while (++index < length) {
      character = string.charAt(index);
      charCode = character.charCodeAt(0);
      if (charCode >= 0xD800 && charCode <= 0xDBFF) {
        output.push(character + string.charAt(++index));
      } else {
        output.push(character);
      }
    }
    return output;
  }

  4. btoa将base64转为ASCII.

  5. atob将ASCII转为base64.

  6. 以上两个函数只支持ASCII转换,其他字符应该先转换成ASCII,如
  var s = "你好";
  var en = encodeURIComponent(s);
  atob(en);
  ```
---
* 对象小结

  ```javaScript
  1. 创建方式
  var o1 = {
    p: 123,
    m: function () { ... }
    };
  var o2 = new Object();
  var o3 = Object.create(null);

  2. 对象的键值是字符串,所以是否加引号没有影响,但是如果其名字不符合变量
  的命名规范,则一定要加引号.

  3. 支持动态创建属性
  var oo = {}
  oo.p1 = 9;
  oo.m = function(){}

  4. JavaScript规定，如果行首是大括号，一律解释为语句（即代码块）。如果要解释为表达式（即对象），必须在大括号前加上圆括号。
  eg:
  eval('{foo: 1}') // 123
  eval('({foo: 1})') // {foo: 123}

  5. 属性访问
  点操作符,只支持非数字类型的访问,o.p;
  []操作符,会将参数解释为string,如果传进去参数为number,其也会先转换成
  string,而不作为下标index进行访问.eg: o[1] = "s" ;//o --> {"1":"s"}

  6. 访问不存在的属性会返回undefined.
  // 检查a变量是否被声明
  if (a) {...} // 报错
  //由于在浏览器环境下,所有的全局变量都是,window对象的属性.
  if (window.a) {...} // 不报错
  if (window['a']) {...} // 不报错

  7. Object.keys(o),返回对象o的所有键值数组

  8. delete操作符用于删除某个属性,其用于返回true,除了delete一个不能删除的属性.

  9. in 操作符用于判断一个对象是不是有某个键值
  eg: "p" in o
  10. for .. in用于遍历一个对象的所有属性

  11. with语句,with内部的属性必须是之前就创建的属性,否则会创建一个全局对象;一般不建议使用.
  with(o){
  p1 = 1;
  p2 = 2;
  }
  等价于
  o.p1 = 1;
  o.p2 = 2;
  ```
---
* 数组

  ```javaScript
  1. 数组是特殊的对象,其键值由以0开始,依次递增的整数组成(可能有其他键).
  只支持[]访问.

  2. 数组中可以放入任意数据.

  3. 数组的大小可以自适应,但最大为2^32-1

  4. length属性可写,当length设置为0时,则清空当前数组.

  5. for ... in, in操作都是对数组的键而言,而不是对于数组的值.for .. in
  操作数组时,会遍历除了下标index之外的其他键.
  ```
---
* 函数

  ```javaScript
  1.如果同一个函数被多次声明，后面的声明就会覆盖前面的声明。
  JavaScript引擎将函数名视同变量名，所以采用function命令声明函数时，整个函数会像变量声明一样，被提升到代码头部。
  function f() {
    console.log(1);
  }
  f() // 2

  function f() {
    console.log(2);
  }
  f() // 2

  2. 如果同时采用function命令和赋值语句声明同一个函数，最后总是采用赋值语句的定义。
  var f = function() {
    console.log(1);
  }
  f() // 1

  function f() {
    console.log(2);
  }
  f() // 1

  3. 不能在条件语句中声明函数
  //不允许
  if (foo) {
    function x() {}
  }
  //要达到在条件语句中定义函数的目的，只有使用函数表达式。
  if(foo){
     var x = function(){};
  }

  4. 函数的length属性返回期望的形参个数.

  5. toString()方法直接返回函数代码.

  6. 函数内部可以调用arguments对象,调用arguments.length获得实参个数,
  调用argument[0]获得第一个参数值...,arguments对象不是数组!!!

  ```
---
* 变量作用域

  ```javaScript
  Javascript只有两种作用域：全局作用域与局部作用域.
  1. 在函数外部声明的变量为全局变量,可以被函数内部调用.
  2. 在函数内部声明的变量为局部变量,不可以在函数外部访问.
  3. 局部变量会覆盖同名的全局变量.
  4. 函数内也存在着变量提升.
  5. 不使用var声明的变量,都属于全局变量.
  ```
---
* 函数闭包与立即调用的函数表达式

  ```javaScript
  1. 从一个函数（外部函数）中返回另一个/一组函数（内部函数），称之为闭包。其中所有的内部函数共享外部函数的变
  量，其中一个内部函数改变外部函数变量x，则其他函数读取也会发现x变更。
  //1.1 一个最简单的例子，只返回一个内部函数。
  function createIncrementor(start) {
    return function () {
      return start++;
    };
  }

  var inc = createIncrementor(5);
  inc() // 5
  inc() // 6
  inc() // 7

  //1.2 返回一组函数（包装成对象）
  function x(initVal) {
      //模块模式
      return {
          changeVal: function (newVal) {
              initVal = newVal;
          },
          showVal: function () {
              console.log(initVal);
          }
      };
  }
  var xobj = x(10);
  xobj.showVal(); // 10
  xobj.changeVal(20);
  xobj.showVal(); // 20

  //1.3 一个错误的例子，打印从（0，i-1）的每个值
  function loop(i) {
      var x = 0;
      //一个函数对象数组，本意是每个函数对象打印一个值，但由于共享x,所以最后实际调用都打印了同一个值。
      var arr = [];
      for (;x < i;++x){
          arr.push(function () {
             console.log(x);
          });
      }
      return arr;
  }
  //loop中的for循环结束后，x为100
  var arf = loop(100);
  //都打印100
  for(var i = 0;i < arf.length;++i){
      arf[i]();
  }

  //修改
  function loop(i) {
      var x = 0;
      var arr = [];
      for (;x < i;++x){
          //使用IIFE将下标x固定，返回一个新函数用于打印。
          arr.push(function (e) {
              return function () {
                  console.log(e);
              }
          }(x));
      }
      return arr;
  }

  //1.4 利用函数+闭包实现封装
  function people(name,age){
      //该方法只有闭包可以访问，实现了封装
      var innerPlus = function () {
          return name+age;
      };
      //该变量只有闭包可以访问
      var word = "love";
      return function () {
          console.log(word);
          return innerPlus();
      }
  }
  var p = people("go",12);
  console.log(p());

  //1.5 实现记忆（缓存）功能
  var hash = function (){
      //用于记忆以及计算过的值。
      var caled = {};
      var cal = function (s) {
          console.log("invoked");
          var res = s+"hash";
          caled[s] = res;
          return res;
      }
      return function fib(s){
          return caled[s] || cal(s);
      };
  }();
  //只调用一次进行实际计算。
  console.log(hash("1212"));
  console.log(hash("1212"));

  2. 立即调用的函数表达式(IIFE),主要是为了避免全局变量污染.
  function关键字可以解释为语句或者表达式,JS规定,当function出现在行首
  解释为语句定义,不能调用IIFE.
  // 语句
  function f() {}
  // 表达式
  var f = function f() {}

  //IIFE,必须要求function解释为表达式.
  //加(),js将其解释为表达式
  (function(x){console.log(x);})(2);
  or
  (function(x){console.log(x);}(2));
  or
  !function(x){console.log(x);}(2);
  or
  var i = function(x){console.log(x);}(2);
  即只要不让function出现在开头就可以调用IIFE.
  相反的
  function(){}(); --> 会提示语法错误.

  ```
---
* 运算符

  ```javaScript
  1.+,运算规则如下:
  (1) 如果运算子是对象,则先转换成基本类型(string/number/boolean),具体规
  则是先调用valueOf,如果返回值不是基本类型,则再调用toString().
  Date类型直接调用toString().
  (2) 两个运算子都转换为基本类型后,如果其中一个是string,则进行字符串拼接.
  (3) 两个运算子都转为number(false -> 0,true -> 1),进行加法.

  2.%,mod运算,其运算结果的正负由第一个运算子决定.
  -1 % 2 // -1
  1 % -2 // 1

  3.+,单目运算符.可以将任何值转换成number,同Number函数功能.
  +true //1
  +[] //0
  +{} //NaN

  3. 比较运算符,比较规则如下
  (1) 如果运算子是对象,则先转换成基本类型(string/number/boolean),具体规
  则是先调用valueOf,如果返回值不是基本类型,则再调用对象的toString().
  (2) 两个运算子都转换为基本类型后,如果都是string,则按照unicode码点比较.
  (3) 否则将其他基本类型转为number(Number()),再比较,string可能会被转为NaN.

  4.严格相等运算符
  a === b
  (1) 如果a与b的类型不一致,则直接返回false;
  (2) 如果a,b为object,则直接比较对象地址(是否引用同一对象).
  (3) 除了NaN === NaN// false,其他特殊值严格相等

  5.相等运算符
  a == b
  (1) 如果a,b类型相同,则其结果和===一样.
  (2) 如果a,b类型不同,则会进行类型转换再比较,转换规则如下:
     a.原始类型的数据会同一使用Number转换成number比较
     b.对象与原始类型比较时,会将对象转换为相应的原始类型再比较.
     eg: [1] == "1"//String([1]) == "1"
         [1,2] == 1 //Number([1,2]) == "2;
  (3)undefined/null与其他值比较时返回false,但是null == undefined//true
  tips: 由于相等运算符进行类型转换,所以可能出现违反直觉的结果,所以只建议使
  用===;

  6. ! 取反操作符有类型转换(转为boolean)作用,除了以下6个值取反后为true,其
  余值取反后均为true.(六个值:0,false,null,undefined,NaN,""或'')
  常用!!x 取代 Boolean(x)

  7. void运算符,作用时执行一个表达式,并不返回值.
  //void的IIFE
  eg: void function(x){console.log(x)}(1)
  //用于在超级链接中插入代码，目的是返回undefined可以防止网页跳转。
  //在不刷新当前页的情况下,打开新窗口
  <a href="javascript:void window.open('http://www.baidu.com')">click</a>
  //以下链接,如果f()不返回false,则会跳转当前页面.
  <a href="http://example.com" onclick="f();">文字</a>
  //使用以下void形式替代
  <a href="javascript:void(f());">文字</a>
  //提交表单,页面不跳转
  <a href="javascript: void(document.form.submit())">
  文字</a>

  8. , 运算符 取最后一个值作为整体的返回值
  var x = (i++,2)
  x //2

  9. 除了=和?:的结合顺序是右结合,其他运算符都是左结合.

  var x = y = z = 1; //var x = (var y = (var z = 1));
  a ? b : c ? d : e ? f : g; // a ? b : (c ? d : (e ? f : g));
  ```
---
*  类型转换

  ```javaScript
  1. Number转换
  基本类型转number
  // 数值：转换后还是原来的值
  Number(324) // 324

  // 字符串：如果可以被解析为数值，则转换为相应的数值
  Number('324') // 324
  Number('12e2') //1200

  // 字符串：如果不可以被解析为数值，返回NaN
  Number('324abc') // NaN

  // 空字符串转为0
  Number('') // 0

  // 布尔值：true 转成1，false 转成0
  Number(true) // 1
  Number(false) // 0

  // undefined：转成 NaN
  Number(undefined) // NaN

  // null：转成0
  Number(null) // 0

  对象转number,规则如下

  （1）调用对象自身的valueOf方法。如果返回原始类型的值，则直接对
  该值使用Number函数，不再进行后续步骤。
  （2）如果valueOf方法返回的还是对象，则改为调用对象自身的
  toString方法。如果返回原始类型的值，则对该值使用Number函数，不再进行后续步骤。
  （3）如果toString方法返回的是对象，就报错。
  o -> valueOf -> toString(除了string转换，其他都是按照该流程)

  2. String
  基本类型
  String(false) --> "false"

  对象转为string
  (1)先调用对象自身的toString方法。如果返回原始类型的值，则对该
  值使用String函数，不再进行以下步骤。
  (2)如果toString方法返回的是对象，再调用valueOf方法。如果返回
  原始类型的值，则对该值使用String函数，不再进行以下步骤。
  (3)如果valueOf方法返回的是对象，就报错。

  3. Boolean(x)与！！x一致，只有六个值返回false。
  4. 自动转换点
  （1）if/while/do 会自动转换成boolean
  （2）+ ，若运算子存在string，则会主动转为string,否则都转为number
  (3) 比较操作 ,如果两个运算子不全是string，则转为number。
  （4） 其他运算会转成number '123' * []//0
  ```
---
* 错误处理

  ```javaScript
  1.Error对象,基本属性有name(名称)，message(信息),stack(错误堆栈);

  2.原生错误类型,7个：
  SyntaxError //解析代码错误
  ReferenceError //引用一个不存在的变量
  RangeError //数组长度为负数，Number对象的方法参数超出范围，以及函数堆栈超过最大值.
  TypeError //类型错误
  URIError //URI函数参数错误时抛出
  EvalError // eval()方法错误
  Error //顶级错误

  3.throw语句可以抛出任意数据，函数将在throw处结束。
  throw 1;
  throw "error";
  throw new Error("you make a mistake");

  4. try{..}catch(e){..}finally{..}
  由于可以throw非Error实例的数据，所以错误信息e，不一定第一个error对象。
  ```
---
*  编码风格

  ```javaScript
  1.区块风格，大括号应该紧跟关键字，原因是js会自动加';',可能导致错误
  block {
    // ...
  }
  错误示例
  return
  {
    key: value
  };

  // 相当于底下代码,实际返回了undefined。
  return;
  {
    key: value
  };

  2.函数定义结尾没有';'，但函数表达式一定要加';'.
  function f() {
  } // 没有分号
  //一定要有;
  var f = function f() {
  };

  3.建议自己手动添加';'
  （1）js自动添加分号，只在下一行开头与当前行末尾连在一起无法解释
  时，才会自动添加分号，所以由js添加分号容易出错。
  （2）自动加分号的规则
  如果一行的行首是++/--时，会在++/--前添加分号。
  a
  ++
  b
  --
  c
  //等价于
  a;
  ++b;
  --c;

  如果continue、break、return和throw这四个语句后面，直接跟换行符，则会自动添加分号。
  这意味着
  return
  {a:1}; //不会返回预期结果，所以语句块的大括号要紧跟关键字。

  4. 尽量减少使用全局变量，如果使用尽量全大写。
  5. 由于[函数内]变量提升，所以尽量把变量声明语句放到代码块头部。
  6. 建议使用Object.create()替代new一个对象。
  7. 不建议使用with语句。
  8. switch-case改写为对象结构
  function doAction(action) {
    var actions = {
      'hack': function () {
        return 'hack';
      },
      'slash': function () {
        return 'slash';
      },
      'run': function () {
        return 'run';
      }
    };

    if (typeof actions[action] !== 'function') {
      throw new Error('Invalid action.');
    }

    return actions[action]();
  }
  ```

* 类库
 * Object类库

  ```javaScript
  Object.keys(o):返回o中所有非继承且可枚举的属性数组.(for .. in即遍历该属性数组)

  Object.getOwnPropertyNames(o):返回o中所有非继承的属性数组.

  ```

* 面向对象编程

  ```javaScript
  - JS使用构造函数来创建对象.
  构造函数与普通函数没有本质上的区别,只是在使用new调用时会返回相应的对象.
  function Vehicle(){
  	this.price = 100;
  	//如果x是一个对象,则会将x赋给v;
  	//如果x为基本类型,则返回this对象.
  	//没有return语句,默认返回this.
  	return x;
  }
  var v = new Vehicle();

  - new的调用过程
  先创建一个空对象{}并将this指向该对象，将空对象的__proto__指向构造函数（对象）的prototype属性。

  - this关键字!!!
  this的语义是函数的上下文运行环境(调用者),具体如下.
  (1) 在全局环境下,指的是顶级对象window.
  	this === window; //true
  	void function(){
  	     console.log(this);//window
  	}();
  (2) 使用new创建时,会指向一个空对象,初始化后并返回.
  (3) 当函数f作为某个对象x的属性时,f里的this指向x.
      tips:在obj.prototype.m中定义的方法,其调用时obj.m(),实际调用者是
      obj,所以this指向obj.
  	var x = {
  	a:1,
  	m:function(){
  		console.log(this.a);  
  	}};
  	x.m();//1
  如果将某个对象的方法赋值给另一个对象，会改变this的指向。
  	var o1 = new Object();
  	o1.m = 1;
  	o1.f = function (){ console.log(this.m);};

  	o1.f() // 1

  	var o2 = new Object();
  	o2.m = 2;
  	o2.f = o1.f
  	o2.f() // 2
  (4) 出现在html元素的js调用中时,即指的的是元素本身.
  	<input type="text" onclick="show(this);">

  - this关键字的使用注意点
    (1) 避免使用嵌套this,可以考虑中间变量实现.
  	var o = {
  	  f1: function() {
  	    console.log(this);
  	    var f2 = function() {
  	      console.log(this);
  	    }();
  	  }
  	}
  	o.f1()
  	// Object
  	// Window

  	var o = {
  	  f1: function() {
  	    console.log(this);
  	    var that = this;
  	    var f2 = function() {
  	      console.log(that);
  	    }();
  	  }
  	}

  	o.f1()
  	// Object
  	// Object

   (2) 不要在回调函数中使用this.

  - 固定this(给this指定具体对象)
  (1) call(obj,arg1,..,)方法,使this指向obj并调用方法,arg*为参数.
  	var o = {};

  	var f = function (){
  	  return this;
  	};
  	f.call(o) === o // true

  (2) apply(obj,[arg1,arg2..]),与call相同,只不过参数用数组传递.
  (3) bind(obj,arg1),返回一个新函数将其this指向obj,并将参数固定到新函数上.

  	var add = function (x,y) {
  	  return x*this.m + y*this.n;
  	}

  	var obj = {
  	  m: 2,
  	  n: 2
  	};
  	//固定了第一个参数5,并返回新函数.
  	var newAdd = add.bind(obj, 5);
  	//不再需要传入第一个参数
  	newAdd(5);

  由于每次bind时都会返回新函数,所以注册Event时,所使用的函数不应该调用bind,否则无法删除.
  正确的做法:
  	var listener = o.m.bind(o);
  	element.addEventListener('click', listener);
  	//  ...
  	element.removeEventListener('click', listener);
  	//错误的做法
  	element.addEventListener('click', o.m.bind(o));

  ```
* 继承与原型链
 * 构造函数也是对象（Function）
 * 构造函数X的prototype属性，称为原型对象。所有由X创建的对象x,都拥有同一个原型prototype.
 * 原型对象里有一个属性constructor,它表示声明拥有该原型对象的类（构造函数）。X.prototype.constructor --> 指向X
 * 每个对象都有构造函数（Function对象），而每个构造函数都有prototype（为对象），这样就形成了一条原型链，x -> X.prototype -> class(X.prototype).prototype --> Object.prototype

  ```javaScript
  function Animal(livingArea){
  	this.livingArea = livingArea;
  	//每个Animal对象都会创建一个该函数的实例，所以不建议，建议把方法放到prototype中。
  	this.x = function(){};
  }
  //所有Animal实例共用该函数对象
  Animal.prototype.showLivingArea = function(){
  	//该this最终会指向正确的对象。
  	console.log(this.livingArea);
  };
  Animal a1 = new Animal("China");
  Animal a2 = new Animal("Japan");
  a1.x === a2.x --> 返回false
  a1.showLivingArea === a2.showLivingArea --> 返回true

  //继承实现
  function People(name,area){
  	Aniaml.call(this,area);
  	this.name = name;
  }
  //可以不传参数
  People.prototype = new Animal();
  //constructor指向该原型对象所在的构造函数，即一个构造函数的原型的constructor指向该构造函数本身。
  //这保证了一个具体对象在调用p.constructor时的正确性
  People.prototype.constructor = People;

  var p = new People("g",12);
  p.constructor //将使用原型链查询到People.

  ```
* Some skill

  ```javaScript
  1.巧用 || 与 &&
  1.1 val1 || val2，当!!val1 === true时，直接返回val1,否则返回val2.
  eg:obj.value || "1";    //当obj.value不存在时,返回"1"
  1.2 val1 && val2,当!!val1 === true时，返回val2.否则返回val1.
  eg：obj.x || obj.x.y;   //只有在obj.x不为undefined的情况下，才计算obj.x.y。

  2.将this应用到内部函数中。
  js中，如果一个函数f,它没有被绑定到一个对象的属性上。那么，该函数中的this关键字指向一个全局对象。
  这导致了内部函数绑定错误，不能访问外部方法指向的对象属性。可以通过以下方式解决：
  num.abs = function(){
  	//这里this指向num对象
  	var that = this;
  	var innerHelper = function(){
  		return that.value < 0 ? -that.value : that.value;
  	}
  	return innerHelper();
  }

  3.提供一个方法，用于给所有函数对象添加方法。
  Function.method = function(name,f){
  	Function.prototype[name] = f;//Function.prototype是所有函数对象的原型
  }
  //Add method "say" to all function object.
  Function.method('say',function(){console.log('here');});
  (function(){}).say();//Will print the word here.

  4.漂亮的继承写法
  Function.method('inherit',function(Father){
  	//this会指向构造函数
  	this.prototype = new Father();
  	return this;
  });
  var Cat = (function(){}).inherit(Animal);

  5.纯粹的基于原型的继承（将所有属性与方法都放到原型中）
  Object.beget = function(atom){
  	var F = function(){};
  	F.prototype = atom;
  	return new F();
  }
  //所有使用beget(obj)创建的对象，都共享obj的属性和方法。
  ```
