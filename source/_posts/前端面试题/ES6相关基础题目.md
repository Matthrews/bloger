---
title: ES6 相关基础题目

tag: 前端面试

author: matthew

date: 20210803

---


# ES6 相关基础题目

1. 考察 let, const, var

   ```javascript
   (function() {
       var c = d = 3;
       console.log(e);  // Uncaught ReferenceError: Cannot access 'e' before initialization
       let e = 5;
   })()
   console.log(c);  // 报错 not defined
   console.log(d);  // 3
   console.log(e);  // 报错 not defined
   ```

   解析：`var` 声明会被提升到块级作用域顶部，`let` 和 `const` 会产生块级作用域，初始化之前访问会进入 `TDZ` 报错。
   `var c = d = 3;`等效于`d = 3; var c = d;`，所以let和var在函数销毁时不复存在，而`d=3`是存在的

2. 考察运算符优先级

   ```javascript
   var a = { n: 1 };
   var b = a;
   a.x = a = { n: 2 };
   console.log(a.n, b.n);  //  2 1
   console.log(a.x, b.x);  //  undefined { n: 2 }
   ```

   解析：`var b = a;`，此时a和b指向同一对象， .访问符优先级比=符号优先级高，此时`b = { n: 1, x: undefined }`， 在计算=，赋值从右向左，所以此时`a = { n: 2 }, b = { n: 2, x: { n: 2 }}`

3. 变量提升优先级

   ```javascript
   console.log(func);
   var func;
   function func(a) {
   	console.log(a);
   	var a = 3;
   	function a() {}
   }
   func(2);
   // 打印结果如下：
// function c(a) {
   //     console.log(a);
   //     var a = 3;
   //     function a() {
   //     }
   // }
   
   // function a() { }
   ```
   
   解析：变量提升优先级：函数声明》arguments》变量声明
   
4. 变量提升

   ```javascript
   var c = 1;
   function c(c) {
   	console.log(c);
   	var c = 3;
   }
   console.log(c);  // 1
   c(2);  // Uncaught TypeError: c is not a function
   
   // 上述代码等效于
   function c(c) {
   	console.log(c);
   	var c = 3;
   }
   var c;
   c = 1;
   console.log(c);
   c(2);
   ```

   解析：函数声明优先提升，当console.log(c); 执行的时候c已经被赋值为1，再执行c(2)就会抛出TypeError，因为c已经不是函数了

5. 变量提升

   ```javascript
   var name = "A";
   (function name() {
   	if (typeof name === 'undefined') {
   		var name = 'B';
   		console.log(name);
   	} else {
   	console.log(name);
   	}
   })();
   console.log(name)
   // 输出 B A
   // 如果将`var name = 'B';`改为`name = 'B';`, 则输出 name函数 和 A
   ```

6. 变量提升

   ```javascript
   var a = 10;
   function test() {
   	a = 100;
   	console.log(a);  // 100
   	console.log(this.a);  // 10
   	var a;
   	console.log(a);  // 100
   }
   test(); 
   ```

7. 块级作用域

   ```javascript
   foo();  // foo is not a function
   var a = true;
   if (a) {
   	var foo = function () { console.log('a'); }
   } else {
   	var foo = function () { console.log('b'); }
   }
   
   bar();  // bar is not defined
   var b = true;
   if (b) {
   	bar = function () { console.log('a'); }
   } else {
   	bar = function () { console.log('b'); }
   }
   
   far();  // far is not a function
   var c = true;
   if (c) {
   	function far() { console.log('a'); }
   } else {
   	function far() { console.log('b'); }
   }
   ```

8. this 指向

   ```
   var v = 1;
   var obj = {
   	v: 2,
   	del: function () {
   		console.log(this);
   		this.v *= 2;
   		console.log(this.v);
   		console.log(v);
   	},
   };
   obj.del();  // obj  4  1 
   
   var name = 'A';
   var obj = {
   	name: 'B',
   	getNameFuc: function () {
   		// console.log(this);  // obj
   		return function () {
   			return this.name;
   		}
   	}
   }
   console.log(obj.getNameFuc()());  // 相当于闭包  this指向window，所以输出A
   
   // 如何输出B？
   var name = 'A';
   var obj = {
   	name: 'B',
   	getNameFuc: function () {
   		var that = this;
   		return function () {
   			return that.name;
   		}
   	}
   }
   console.log(obj.getNameFuc()());  // 输出B,备份了this
   
   // 如何输出B？
   console.log(obj.getNameFuc().call(obj));
   console.log(obj.getNameFuc().call(obj)); 
   console.log(obj.getNameFuc().bind(obj)());
   ```

9. 其他

   ```javascript
   (function () {
   	var a = b = 5;
   })();
   console.log(b);  // 5
   console.log(a);  // Uncaught ReferenceError: a is not defined
   
   
   var c = 6;
   setTimeout(function () {
   	c = 666;
   }, 0);
   console.log(c);  // 6
   console.log(c);  // 6
   console.log(c);  // 6
   
   
   var a;
   var b = 'undefined';
   var c = 1;
   var d = 1;
   console.log(typeof a);  // 'undefined'
   console.log(typeof b);  // 'string'
   console.log(typeof c);  // 'string'
   console.log(typeof d);  // 'string'
   console.log(typeof e);  // 'undefined'
   // typeof一个未定义的变量时,不会抛出错误,会返回'undefined'
   
   
   var o = () => { };
   var p = ['undefined'];
   var q = {};
   var r = null;
   var s = NaN;
   console.log(typeof o);  // 'function'
   console.log(typeof p);  // 'object'
   console.log(typeof q);  // 'object'
   console.log(typeof e);  // 'object'
   console.log(typeof s);  // 'number'
   // typeof无法区分Array/Date/ReExp和Object
   // typeof null === "object"
   // typeof NaN === "number"
   
   
   var x = 1;
   if (function f() { }) {
       x += typeof f;
   }
   console.log(x);  // '1function'  ❌
   // 正确答案：'1undefined'
   // function f(){}当做if条件判断,其隐式转换后为true
   // 但是在()中的函数不会声明提升,因此f函数在外部是不存在的
   
   
   function foo1() {
       return { bar: 'hello' };
   }
   function foo2() {
       return
       { bar: 'hello' };
   }
   console.log(foo1());  // { bar: "hello" }
   console.log(foo2());  // undefined
   // foo2与foo1的区别在于foo2函数的return后面换行了
   
   
   console.log(Array(3));  //  [empty × 3]
   console.log(Array(2, 3));  // [2, 3]
   
   
   console.log(false.toString());  // 'false'
   console.log([1, 2, 3].toString()); // '1, 2, 3'
   console.log(1.toString());  // Uncaught SyntaxError: Invalid or unexpected token
   // 执行时变为(1.)toString()，少了一个点
   // 正确的应该是: 1..toString();1 .toString();(1).toString();
   ```

   解析：`var a = b = 5;`等同`var a = b; b = 5`，JS执行代码是单线程的，同步任务优先执行

10. 闭包

    ```javascript
    function func() {
    	var a = 6;
    	function func2() {
    		a++;  // 闭包，因此此处a访问的是func下的a
    		console.log(a);
    	}
    	return func2;
    };
    
    var f = func();
    f();  // 7
    f(); // 8
    ```

    

11. 函数作用域

    ```javascript
    var x = 10;
    function fn() {
        console.log(x);
    }
    function show(f) {
        var x = 20;
        f();
    }
    show(fn) // 10
    ```

    解析：JavaScript采用的是词法作用域,它规定了函数。访问变量时,查找变量是从函数声明的位置向外层作用域中查找,而不是从调用函数的位置开始向上查找。
    因此fn函数内部访问的x是全局作用域中的x,而不是show函数作用域中的x

12. 数据类型转换

    ```javascript
    var a = 666;
    console.log(++a);  // 667
    consoNle.log(a++);  // 667
    console.log(a);  // 668
    // 使用这类运算符时要注意:
    // 1）这里的++、–-不能用作于常量。比如 1++; // 抛出错误
    // 2）如果a不是数字类型, 会首先通过Number(a), 将a转换为数字。再执行++等运算
    
    
    var a = 10, b = 20, c = 30;
    ++a;  // a = 11
    a++;  // a = 11
    e = ++a + (++b) + (c++) + a++;  // a = 13
    console.log(e);  // 13 + 21 + 30 + 13
    
    
    var str = "123abc";
    console.log(typeof str++);  // number
    // 如果变量不是数字类型,会首先用Number()转换为数字。因此typeof str++相当于typeof Number(str)++
    // 由于后置的++是先取值后计算,因此相当于typeof Number('123abc')。即typeof NaN,所以输出'number。
    
    
    console.log(1 + "2" + "2");  // '122'
    console.log(1 + +"2" + "2"); // '32'
    console.log(1 + -"1" + "2"); // '02'
    console.log(+"1" + "1" + "2");  // '112'
    console.log("A" - "B" + "2"); // 'NaN2'
    console.log("A" - "B" + 2);  // NaN
    // 1. +a, 会把a转换为数字。-a会把a转换成数字的负值(如果能转换为数字的话, 否则为NaN)
    // 2. 字符串与任何值相加都是字符串拼接。
    
    
    console.log(Boolean(false)); // false
    console.log(Boolean('0'));  // true
    console.log(Boolean(''));  // false
    console.log(Boolean(NaN));  // false
    // 1. undefined, null, false, "", +0, -0, NaN 前述7个值会转为false;
    // 2. 除此之外所有对象都将转为true
    
    
    let user = {
      name: "John",
      money: 1000,
    
      [Symbol.toPrimitive](hint) {
        alert(`hint: ${hint}`);
        return hint == "string" ? `{name: "${this.name}"}` : this.money;
      }
    };
    
    // 转换演示：
    alert(user); // hint: string -> {name: "John"}
    alert(+user); // hint: number -> 1000
    alert(user + 500); // hint: default -> 1500
    
    
    let user = {
      name: "John",
      money: 1000,
    
      // 对于 hint="string"
      toString() {
        return `{name: "${this.name}"}`;
      },
    
      // 对于 hint="number" 或 "default"
      valueOf() {
        return this.money;
      }
    
    };
    
    alert(user); // toString -> {name: "John"}
    alert(+user); // valueOf -> 1000
    alert(user + 500); // valueOf -> 1500
    // 转换算法是：
    //    调用 obj[Symbol.toPrimitive](hint) 如果这个方法存在，
    //    否则，如果 hint 是 "string"
    //    尝试 obj.toString() 和 obj.valueOf()，无论哪个存在。
    //    否则，如果 hint 是 "number" 或者 "default"
    //    尝试 obj.valueOf() 和 obj.toString()，无论哪个存在。
    // 参考：https://zh.javascript.info/object-toprimitive
    
    // 如下情况hint为string
    // 输出
    alert(obj);
    // 将对象作为属性键
    anotherObj[obj] = 123;
    
    // 如下情况hint为number
    // 显式转换
    let num = Number(obj);
    // 数学运算（除了二元加法）
    let n = +obj; // 一元加法
    let delta = date1 - date2;
    // 小于/大于的比较
    let greater = user1 > user2;
    
    // 如下情况hint为default
    // 二元加法使用默认 hint
    let total = obj1 + obj2;
    // obj == number 使用默认 hint
    if (user == 1) { ... };
    
    
    var a = [3];
    var b = [2];
    var c = [];
    console.log(a - b - c);   // 1
    ```

13. 集合枚举

    ```javascript
    Object.prototype.bar = 1;
    var foo = { moo: 2 };
    for (var i in foo) {
    	console.log(i);  // 'moo'  'bar'
    }
    ```

    解析：

    1. for/in循环：遍历可枚举的自有属性和继承属性

    2. Object.keys(): 返回对象可枚举自有属性数组

    ​    3. Object.getOwnPropertyNames():返回对象所有自有属性数组

    ​    4. Object对象的propertyIsEnumerable()方法可以判断此对象是否包含某个属性，并且这个属性是否可枚举。

    5. 集合属性枚举影响以下三个函数的结果：for/in, Object.keys(), JSON.stringify

14. 值传递

    ```javascript
    var obj = { n: 1 };
    var arr = [1, 2, 3];
    function func(a, b) {
    	b = [];
    	console.log("b", b);
    	a.n = 2;
    	a = { b: 1 };
    	console.log("a", a);
    }
    func(obj, arr); // [] { b: 1 }
    console.log(obj); // { n: 2 }
    console.log(arr); // [1, 2, 3]
    
    function changeAgeAndReference(person) {
        person.age = 25;
        person = {
            name: "John",
            age: 50,
        };
    
        return person;
    }
    var personObj1 = {
        name: "Alex",
        age: 30,
    };
    var personObj2 = changeAgeAndReference(personObj1);
    console.log(personObj1); // -> {name: 'Alex', age: 25}
    console.log(personObj2); // -> {name: 'John', age: 50}
    ```

    解析：函数传递参数时,如果是基本类型为值传递,如果是引用类型,为引用地址的值传递。其实都是值传递

15. 原型相关

    ```
    console.log(Object instanceof Function);  // true
    console.log(Function instanceof Object);  // true
    console.log(Function instanceof Function);  // true
    console.log(Number instanceof Number);  // false
    console.log(String instanceof String);  // false|
    
    function f() {
        return f;  // 注意此处返回f本身
    }
    console.log(new f() instanceof f);  // false
    
    
    function A() { }
    A.prototype.n = 1;
    var b = new A();
    A.prototype = {
    	n: 2,
    	m: 3
    }
    var c = new A();
    console.log(b.n, b.m);  // 1 undefined
    console.log(c.n, c.m);  // 2 3
    
    
    var F = function () {};
    var O = {};
    Object.prototype.a = function() {
    	console.log('a');
    }
    
    Function.prototype.b = function() {
    	console.log('b');
    }
    
    var f = new F();
    F.a();  // a
    F.b();  // b
    O.a();  // a
    O.b();  // Uncaught TypeError: O.b is not a function
    // F的原型链：F => F.__proto__ => Function.prototype => Function.prototype.__proto__ => Object.prototype => null
    // O的原型链：O => O.__proto__  => Object.prototype => null
    
    
    function Person() {  // 1
        getAge = function () {
            console.log(10);
        }
    	return this;
    }
    
    Person.getAge = function () {  // 2
    	console.log(20);
    }
    
    Person.prototype.getAge = function () {  // 3
    	console.log(30);
    }
    
    var getAge = function () {  // 4
    	console.log(40);
    }
    
    function getAge() {  // 5
    	console.log(50);
    }
    
    Person.getAge();  // 20 执行Person函数上的getAge方法
    getAge();  // 40 执行全局中的getAge方法
    Person().getAge();  // 10 由于Person()单独执行所以,作用域中的this绑定为window, 相当于window.getAge()
    // 但是Person执行时,内部修改了全局的getAge方法
    new Person.getAge(); // 20 此时相当于实例化Person.getAge这个函数
    getAge();  // 10 由于在Person().getAge()执行时把全局getAge改变了
    new Person().getAge();  // 30  new Person().getAge();此时调用的是Person原型上的getAge方法
    ```