# 万能的 JS

@[TOC](种一棵树，最好的时候是十年前，其次是现在。)

## JS（JavaScript 轻量级动态脚本语言）

> JavaScript 基于原型编程、多范式的动态脚本语言，并且支持面向对象、命令式、声明式、函数式编程范式

- ECMAScript：**语法规范**，是 js 和 nodejs 要遵守的
- js 操作 dom，事件，发 ajax 请求 == `ECMAScript + webAPI`
- node 处理请求操作文件 == `ECMAScript + nodeAPI`

### 面向对象思想：

- 1.封装：保护成员、数据隐藏——方法、强制访问权限、便于理解
- 2.继承：重用代码、无需修改父类、*多重继承（组合）——JS 不支持、不好、*抽象类
- 3.多态：抽象、简化问题 （条件判断模拟多态）

### 作用域

- 1、全局作用域
  > - var 定义的全局变量不可以删除，没使用 var 定义的全局变量是 window 的属性存在，可删除`abcd=123，delete abcd =>true`
  > - 函数内部没有用 var 定义的全局变量也是 window 的属性存在
- 2、函数作用域
- 3、块状作用域 let&const

  > - let 块级作用域
  > - let 声明的全局变量不能用全局属性来访问。不会把变量定义到 window 上
  > - let 不能重新定义
  > - let 不会进行变量提升
  >
  > - const：常量，不可变的量，指针不能改，内容不可以改

  ```js
  // let的块级作用于
  for (var i = 0; i < 3; i++) {
    setTimeout(function () {
      console.log(i);
    }, 1000);
  } // 3 3 3 用 let 就是 0 1 2

  // let没有预解释
  console.log(a);
  var a = 1; // undefined
  let a = 1; // Cannot access 'a' before initialization 无法在初始化之前访问'a'

  // const不可变的量，指针不能改，内容不可以改
  const a = 1;
  a = 2; // Uncaught TypeError: Assignment to constant variable.
  const b = [1];
  b.push(2);
  console.log(b); // [1,2]
  Object.freeze(b); // =>彻底不能改
  b.push(3); // Uncaught TypeError: Cannot add property 3, object is not extensible
  ```

- 4、动态作用域

### 预解释 （变量提声） 浏览器天生自带

> 当前的作用域中，js 代码执行之前，浏览器首先会默认把所有的带 var 和 function 的进行提前的声明或者定义
>
> - 声明（declare）：var num；告诉浏览器在全局作用域中有一个全局的变量了
> - 定义（defined）：num=12；给变量进行赋值，把数据变量联系在一起

- var 和 function

  > - var 的变量提前声明但不会赋值（不定义），默认的值 undefined，当代码执行的时候才会赋值。
  > - function 提前声明并赋值（定义）

  ```js
    // 预解释只发生在当前的作用域下，例如：开始只对windows下的进行预解释，只有函数执行的时候才会对函数中的进行预解释
    console.log(num); //undefined
    console.log(obj); //undefined
    console.log(sum); //function本身
    var num=12;       //赋值
    function sum(num1,num2){var total=num1+num2;}
    var obj={'name':'zqb',age:30};  //对象开辟一个空间，将属性名和属性值存进去
    *****************************
    console.log(haha);        // 报错haha is not defined
    var a =function(){};      //不会提前赋值，预解释只会看等号左边的变量，并不会看你的值是什么
    function (){             //当代码执行的时候，声明和赋值已经结束了，直接跳过
        function haha(){}    //我们的预解释只发生在当前作用域*************
    }
  ```

- 加 var 和不加 var 的区别：（在全局作用域下）
  > - 1 是否被提前声明 （带 var 的提前声明，不带 var 的不预解释）
  > - 2 var 定义的全局变量不可以删除，没使用 var 定义的全局变量是 window 的属性存在，可删除`abcd=123，delete abcd =>true`
- 函数的运行顺序：1 如果有形参是形参赋值 2 预解释 3 代码逐行执行。。
- 预解释的机制

  > - 1 不论条件是否成立都会预解释（if） chrome 下只声明
  > - 2 预解释的时候带 var 关键字的只看等号左边
  > - 3 全局作用域下的自执行函数不会预解释，预解释只发生在当前作用域
  > - 4return 后面的代码仍然会被预解释，但是 return 出来的值不会被预解释， 即使是一个函数也不会被预解释 ，后面代码不执行，return 后面的是返回值，return 下面预解释但不执行
  > - 5.当 var 和 function 的变量名字一样 不能重复声明了 但是重复赋值

  ```js
  //机制1
  if ("num" in window) {
    //提前声明 num=undefined
    var num = 6; //给 num 赋值
  }
  console.log(num); //6

  //机制2
  var a = function () {};
  sum(); //报错Uncaught ReferenceErrer:sum is not defined

  //机制3
  console.log(b); //b is not defined
  !(function () {
    //自执行函数不会预解释
    var b = 0;
  })();

  //机制4
  function fn() {
    console.log(num); //undefined
    return function () {
      console.log(num);
    }; //return 后面的返回值不进行预解释
    alert(); //代码不执行
    var num = 9; //这个地方还会预解释，永远无法定义，因为代码走不到这里
  }
  var f = fn(); //函数的执行 赋值是执行的结果 没有 return 就是 undefined
  fn();

  //机制5
  var sum = 10;
  function sum() {}
  sum(); //不能执行 Uncaught TypeError: sum is not a function
  //第一步进行预解释 var sum =undefined；sum = 函数体
  //第二步真正的赋值 var sum = 10

  foo(); //2 预解释先赋值
  function foo() {
    console.log(1);
  }
  foo(); //2
  var foo = "haha"; //原来的函数引用地址已经被破坏，并且 foo 赋值了一个字符串，让一个字符串去执行
  foo(); //foo is not a function
  function foo() {
    console.log(2); //预解释的时候还会赋值
  }
  foo(); //报错执行不到这里
  ```

### JS 中内存的分类

- 栈内存：用来提供一个供 JS 代码执行的环境->作用域(全局作用域/私有作用域)，基本数据类型都存在栈内存里
- 堆内存：用来存储引用数据类型的值->对象存储的是属性名和属性值，函数存储的是代码字符串...

### JS 数据类型

- 基本数据类型：String，Number，Boolean，Null，Undefined，symbol。
  > 值类型（不互相干扰）
- 引用数据类型：Object，Array，Function
  > 引用类型（地址，相互改变有影响）

### 检测数据类型

- typeof：优点：能够快速区分基本数据类型 缺点：不能将 Object、Array 和 Null 区分，都返回 object
- instanceof：优点：能够区分 Array、Object 和 Function，适合用于判断自定义的类实例对象 缺点：Number，Boolean，String 基本数据类型不能判断。原理：判断实例对象的属性和构造函数的属性是不是引用的同一个地址

  ```js
  console.log(2 instanceof Number); // false
  console.log([] instanceof Array); // true
  ```

- constructor 构造函数
- Object.prototype.toString.call()：优点：精准判断数据类型 缺点：写法繁琐不容易记，推荐进行封装后使用
  ```js
  var toString = Object.prototype.toString;
  console.log(toString.call(2)); //[object Number]
  ```

### 类型转换

- 强制类型转化：parseInt，parseFloat，toString
- 隐式类型转化：if，逻辑运算，==，+，拼接字符串
- 类型转化：字符串拼接，==，!!

```js
false=='' //true
null==undefined //true
obj.a==null 相当于 obj.a===null||obj.a===undefined
const a = 0
!a //true
!!a //false
!!0 === false //true
!!NaN === false //true
!!null ===false //true
!!undefined === false //true
10&&0 //0
abc||'' //abc
```

```js
// 面试题
var num = 20; //20*=3=60 通过地30行执行fn()把window.num *= 4l了240
var obj = {
  num: 30, //120
  fn: (function (num) {
    //num = 20
    this.num *= 3; //this=>window.num*=3==60;
    num += 20; //20+=20 = 40；//相当于当前作用域内的num变量修改成40了
    var num = 45; //因为和形参是相同的，所以不被预解释,这个var在此处有和没有是一样的效果 num = 45;
    return function () {
      this.num *= 4; //第30行执行的时候，this是window，window.num *=4==240;
      num += 20; //num+=20 =65
      console.log(num); //65
    };
  })(this.num), //this是谁window ==> window.num
};
var fn = obj.fn; //把obj.fn的引用地址告诉了fn
fn(); //65
obj.fn(); // 85  this是obj 所以this.num=30*4=120
console.log(window.num, obj.num); // 240 120
```

### null 和 undefined 区别

- undefined 用法：
  - 变量被声明了，但没有赋值时，就等于 undefined。
  - 调用函数时，应该提供的参数没有提供，该参数等于 undefined。
  - 对象没有赋值的属性，该属性的值为 undefined。
  - 函数没有返回值时，默认返回 undefined。
- null 空指针，用来表示尚未存在的对象，常用来表示函数企图返回一个不存在的对象。用法：
  - 作为函数的参数，表示该函数的参数不是对象。
  - 作为对象原型链的终点。

### this 的指向（取什么值是在函数执行的时候决定的，不是在函数）

- 1、对象的函数属性，点击事件，谁调用 this 就指向谁
- 2、构造函数中的 this 指向当前实例
- 3、函数中 this 指向 window，严格模式 this=>undefined
- 4、call，apply，bind 能改变 this 指向
  `fn.call(thisObj,p1,p2) === fn.apply(thisObj,[p1,p2]);`
- 5、箭头函数的 this 取上级作用域的值

### 闭包

- 本质上说就是在函数内部和函数外部搭建起一座桥梁，使得子函数可以访问父函数中所有的局部变量，但是反之不可以，这只是闭包的作用之一，另一个作用，则是保护变量不受外界污染，使其一直存在内存中，在工作中我们还是少使用闭包的好，因为闭包太消耗内存，不到万不得已的时候尽量不使用。

```js
//  闭包隐藏数据：只提供  API
function createCache() {
  const data = {}; //  闭包中的数据，被隐藏，不被外界访问
  return {
    set: function (key, val) {
      data[key] = val;
    },
    get: function (key) {
      return data[key];
    },
  };
}
const c = createCache();
c.set("a", 100);
console.log(c.get("a"));

// let 块级作用域就解决了
let a;
for (let i = 0; i < 10; i++) {
  a = document.createElement("a");
  a.innerHTML = i + "<br>";
  a.addEventListener("click", function (e) {
    // 如果 i 是全局的点击的时候执行 i 已经变成 10 了
    e.preventDefault();
    alert(i);
  });
  document.body.appendChild(a);
}

//闭包的影响：占用内存不释放，内存在函数执行后依次释放
let a = 0;
function fn1() {
  let a1 = 100;
  function fn2() {
    let a2 = 200;
    function fn3() {
      let a3 = 300;
      return a + a1 + a2 + a3;
    }
    fn3();
  }
  fn2();
}
fn1();
```

### call,apply 和 bind 区别

> - 作用都是将函数绑定到上下文中，用来改变函数中 this 的指向；不同点在于语法的不同。
> - apply 和 call 的区别是：call 接受若干个参数，apply 第二个参数是数组，bind()方法创建一个新的函数, 当被调用时，将其 this 关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列。

```js
sayName.call(callObj, "call param");
sayName.apply(applyObj, ["apply param"]);
let bindFn = sayName.bind(bindObj, "bind param");
bindFn();
```

- call 接受的是若干个参数列表，方法的第一个参数：

  - 1、如果是一个对象类型，那么函数内部的 this 指向该对象
  - 2、如果是 undefined、null，那么函数内部的 this 指向 window
  - 3、如果是数字，this 指向对应的 Number 构造函数的实例：1--> new Number(1)；如果是字符串，this 指向 String 构造函数的实例"abc" --> new String("abc")；如果是布尔值，this 指向 Boolean 构造函数的实例 false --> new Boolean(false)

- apply 参数传数组，call 和 apply 不同的地方：传参的形式不同

  ```js
  function toString(a, b, c) {
    console.log(a + " " + b + " " + c);
  }
  toString.call(null, 1, 3, 5); //"1 3 5"
  toString.apply(null, [1, 3, 5]); //"1 3 5"
  ```

- bind 不立即执行

```js
var obj = {
  age: 18,
  run: function () {
    console.log(this); //this:obj
    var _that = this;
    setTimeout(function () {
      //this 指向 window
      console.log(this.age); // undefined
      console.log(_that.age); //18
    }, 50);
  },
};
obj.run();

//bind 是 es5 中才有的(IE9+)
var obj5 = {
  age: 18,
  run: function () {
    console.log(this); //this:obj5
    setTimeout(
      function () {
        console.log(this.age);
      }.bind(this),
      50
    ); //this:obj5
    //通过执行了 bind 方法，匿名函数本身并没有执行，只是改变了该函数内部的 this 的值，指向 obj5
  },
};
obj5.run();

//bind 基本用法
function speed() {
  console.log(this.seconds);
}
//执行了 bind 方法之后，产生了一个新函数，这个新函数里面的逻辑和原来还是一样的，唯一的不同是 this 指向{ seconds:100 }
var speedBind = speed.bind({ seconds: 100 });
speedBind(); //100

(function eat() {
  console.log(this.seconds);
}.bind({ seconds: 360 })()); //360

//  模拟  bind
myBind = function () {
  const arr = Array.from(arguments); // 参数转为数组
  // const args = Array.prototype.slice.call(arguments);
  const t = args.shift(); // 拿出第一项this
  const self = this; // fn1.bind(...) 中的 fn1
  return function () {
    // 返回一个函数
    return self.apply(t, args);
    //return self.call(t, ...args)
  };
};
```

### ==和===区别：

- ==， 两边值类型不同的时候，要先进行类型转换，再比较
- ===，不做类型转换，类型不同的一定不等。

  > ==类型转换过程：
  >
  > - 如果类型不同，进行类型转换
  > - 判断比较的是否是 null 或者是 undefined, 如果是, 返回 true .
  > - 判断两者类型是否为 string 和 number, 如果是, 将字符串转换成 number
  > - 判断其中一方是否为 boolean, 如果是, 将 boolean 转为 number 再进行判断
  > - 判断其中一方是否为 object 且另一方为 string、number 或者 symbol , 如果是, 将 object 转为原始类型再进行判断
  >
  > 经典面试题：[] == ![] 为什么是 true 转化步骤：
  >
  > - !运算符优先级最高，![]会被转为为 false，因此表达式变成了：[] == false
  > - 根据上面第(4)条规则，如果有一方是 boolean，就把 boolean 转为 number，因此表达式变成了：[] == 0
  > - 根据上面第(5)条规则，把数组转为原始类型，调用数组的 toString()方法，[]转为空字符串，因此表达式变成了：'' == 0
  > - 根据上面第(3)条规则，两边数据类型为 string 和 number，把空字符串转为 0，
  >   因此表达式变成了：0 == 0 两边数据类型相同，0==0 为 true

### 深拷贝和浅拷贝

```js
// 浅拷贝
function simpleClone(obj) {
  var result = {};
  for (var i in obj) {
    result[i] = obj[i];
  }
  return result;
}
// 深拷贝
function deepClone(obj = {}) {
  if (typeof obj !== "object" || obj == null) {
    return obj;
  } // obj  是  null ，或不是对象数组直接返回
  let result; //  初始化返回结果
  obj instanceof Array ? (result = []) : (result = {});
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      //  保证  key  不是原型的属性
      result[key] = deepClone(obj[key]); //  递归调用！！！
    }
  }
  return result; //  返回结果
}
```

### 防抖和节流

> - 正常执行 llllllllllll llllll lllllllllllll
> - 函数防抖&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;l &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;l &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;l
> - 函数节流 l&nbsp;&nbsp;&nbsp;l&nbsp;&nbsp;&nbsp;l&nbsp;&nbsp;&nbsp;l&nbsp;&nbsp;&nbsp;l&nbsp;&nbsp;&nbsp;l&nbsp;&nbsp;&nbsp;l&nbsp;&nbsp;&nbsp;l&nbsp;&nbsp;&nbsp;l

- 防抖 (debounce)顾名思义，防止抖动，以免把一次事件误认为多次

  > - 场景：登录、发短信等按钮避免用户点击太快，导致发送了多次请求，
  >   调整浏览器窗口大小时，resize 次数过于频繁，造成计算过多，此时需要一次到位
  >   文本编辑器实时保存，当无任何更改操作一秒后进行保存
  > - **防抖重在清零 clearTimeout(timer)**

  ```js
  function debounce(f, wait) {
    let timer;
    return (...args) => {
      clearTimeout(timer);
      timer = setTimeout(() => {
        f(...args);
      }, wait);
    };
  }
  function debounce(fn, delay) {
    let timer = null;
    return function () {
      if (timer) clearTimeout(timer);
      timer = setTimeout(() => {
        fn.apply(this, arguments);
      }, delay);
    };
  }
  ```

- 节流 (throttle) 控制水的流量。控制事件发生的频率，如控制为 1s 发生一次。与服务端(server)及网关(gateway)控制的限流 (Rate Limit) 类似。
  > - 场景：scroll 事件，每隔一秒计算一次位置信息等
  >   浏览器播放事件，每个一秒计算一次进度信息等
  >   input 框实时搜索并发送请求展示下拉列表，每隔一秒发送一次请求
  > - **节流重在加锁 timer=timeout**

```js
function throttle(f, wait) {
  let timer;
  return (...args) => {
    if (timer) {
      return;
    }
    timer = setTimeout(() => {
      f(...args);
      timer = null;
    }, wait);
  };
}
function throttle(fn, cycle) {
  let start = Date.now();
  let now;
  let timer;
  return function () {
    now = Date.now();
    clearTimeout(timer);
    if (now - start >= cycle) {
      fn.apply(this, arguments);
      start = now;
    } else {
      timer = setTimeout(() => {
        fn.apply(this, arguments);
      }, cycle);
    }
  };
}
```

- 总结：
  - 防抖：防止抖动，单位时间内事件触发会被重置，避免事件被误伤触发多次。代码实现重在清零 clearTimeout。防抖可以比作等电梯，只要有一个人进来，就需要再等一会儿。业务场景有避免登录按钮多次点击的重复提交。
  - 节流：控制流量，单位时间内事件只能触发一次，与服务器端的限流 (Rate Limit) 类似。代码实现重在开锁关锁 timer=timeout; timer=null。节流可以比作过红绿灯，每等一个红灯时间就可以过一批。

### 数组

- ES5 中数组遍历的方法：

  - for 循环：`break` 直接跳出循环；`continue` 下边的代码不执行，执行下一个循环
  - forEach()、every()、some()、for(let index in obj){}、filter()、fill()

  ```js
  for (var i = 1; i <= 10; i++) {
    if (i == 6) continue;
    console.log(i);
  } // 1234578910

  // forEach不支持 break 和 continue
  arr.forEach((item) => {
    console.log(item);
  });

  // every没有 return true 就只执行一次。
  arr.every((item) => {
    console.log(item);
    return true;
  });

  // for in 支持 continue，索引是字符串，数组也是对象，数组也可以有属性
  for (let index in obj) {
    console.log(index, obj[index]);
  }

  // every():一假即假
  let arr = [12, 3, 4];
  console.log(
    arr.every((item) => {
      return item > 3;
    })
  ); //false

  // some():一真即真
  let arr = [12, 3, 4];
  console.log(
    arr.some((item) => {
      return item == 3;
    })
  ); //true
  ```

- ES6

  - for of、Array.from()、flat(n)拍平 n 层、Array.of()、find()、findIndex()、copyWithin()、includes()、reduce()

    ```js
    //for of 遍历任何数据结构
    for (let item of arr) {
      console.log(item);
    }

    //类数组转化数组：
    // es5:
    [].slice.call(document.querySelectorAll("img")); //nodeList 调用数组的方法
    // es6:
    Array.from(arguments);
    Array.from({ length: 5 }, function () {
      return 1;
    });

    // flat:拍平数组
    [1, [2, 3]].flat(2) //[1,2,3]
    [1, [2, 3, [4, 5]].flat(3) //[1,2,3,4,5]
    [1[2, 3, [4, 5[...]].flat(Infinity)//[1,2,3,4...n]
    // 实现flat
    function flat(arr) {
      const isDeep = arr.some((item) => item instanceof Array); // 查看数组层级
      if (!isDeep) {
        return arr; //已经是一层
      }
      const res = Array.prototype.concat.apply([], arr); //一层数组拍平
      return flat(res); // 递归
    }

    // 去重：
    function unique(arr) {
      const res = [];
      arr.forEach((item) => {
        if (res.indexOf(item) < 0) {
          res.push(item);
        }
      });
      return res;
      //使用 set 去重：
      //const set = new Set(arr)
      //return [...set]
    }

    //生成新数组
    let array = Array(5);
    let array = [];
    //es6:
    Array.of(1, 2, 3, 4, 5);
    let array = Array(5).fill(1); //Array.fill(value,start,end),不限制全部填充替换

    //es5 查找一个元素
    arr.filter(function (item) {
      return item === 6;
    }); //[6] 没有返回[ ]，不高效，都查找一遍
    //es6:
    arr.find(function (item) {
      return item === 6;
    }); // 6 只找到第一个值，没有 undefined

    arr.findIndex(function(item){return item===6})

    arr.copyWithin(target[, start[, end]])
    // target 0 为基底的索引，复制序列到该位置。如果是负数，target 将从末尾开始计算。 如果 target 大于等于 arr.length，将会不发生拷贝。如果 target 在 start 之后，复制的序列将被修改以符合 arr.length。
    // start 0 为基底的索引，开始复制元素的起始位置。如果是负数，start 将从末尾开始计算。如果 start 被忽略，copyWithin 将会从0开始复制。
    // end 0 为基底的索引，开始复制元素的结束位置。copyWithin 将会拷贝到该位置，但不包括 end 这个位置的元素。如果是负数， end 将从末尾开始计算。如果 end 被忽略，copyWithin 方法将会一直复制至数组结尾（默认为 arr.length）。
    [].copyWithin(target,start,end) // 通过自身数据，在指定位置替换数据,返回改变后的数组。
    [1, 2, 3, 4, 5].copyWithin(1, 0, 3) // [1, 1, 2, 3, 5]

    const array1 = [1, 2, 3];
    console.log(array1.includes(2)); // true

    // reduce
    arr.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue])
    // accumulator 累计器累计回调的返回值; 它是上一次调用回调时返回的累积值
    // currentValue 数组中正在处理的元素
    // index 可选 数组中正在处理的当前元素的索引
    // array可选 调用reduce()的数组
    // initialValue可选 作为第一次调用 callback函数时的第一个参数的值。 如果没有提供初始值，则将使用数组中的第一个元素。 在没有初始值的空数组上调用 reduce 将报错。
    var flattened = [[0, 1], [2, 3], [4, 5]].reduce(
      ( acc, cur ) => acc.concat(cur),
      []
    ); // flattened is [0, 1, 2, 3, 4, 5]
    ```

### 数组中常用的方法

- 第一大部分：增删改查
  |方法|功能|参数|返回值|原数组|
  |----|----|----|----|----|
  |push|末尾添加|添加的项|添加后的数组长度|改变|
  |pop|删除最后一项|无|删除的项|改变|
  |unshift|开头添加一项|添加的项|添加后的数组长度|改变|
  |shift|删除数组第一项|无|删除的项|改变|
  |splice(n,m,x)|从索引 n 开始包含 n，删除 m 个元素，用 x 替换原来的|n 是索引,m 是个数,x 新增的替换内容|删除的项|改变|
  |slice(n,m)|从索引 n 开始找到索引 m 不包含 m（查询复制）|n 从索引 n 开始，m 到索引 m|查询数组项|不变|

  - 末尾添加 push：`ary.splice(ary.length,0,x); ary[ary.length]=10;`
  - 末尾删除 pop：`ary.splice(ary.length-1); ary.length--;`
  - 开头添加 unshift：`splice(0,0,x)`
  - 开头删除 shift：`splice(0,1)`
  - ary.splice(n);从索引 n 开始，删除到数组末尾
  - ary.splice(0);清空数组，返回一个新克隆的数组
  - ary.splice();一项都没有删除，返回空数组
  - splice(n,m,x)：先删除，让后用 x 替换，从索引 n 开始 删除 m 个元素用 x 替换原来的，返回删除的内容数组，原有数组改变
  - splice（n，0，x）添加新的内容,从索引 n 开始删除 0 个内容，把新增加的内容放在索引 n 的前面，返回的结果是空数组，原有数组改变
  - slice(n,m)从索引 n 开始找到索引为 m 处，不包含 m，返回新数组，原数组不变
  - slice(n)从索引 n 开始一直找到数组末尾，`n为负数，从右往左复制，m为负数，从右边往左减m个`
  - slice(0) / slice() 把原来的数组克隆一份新数组返回，不修改原数组

    ```js
    let arr = [1, 2, 3, 4, 5, 6];
    console.log(arr.slice(-5, -2)); //[2,3,4]
    ```

- 第二大部分： 数组的截取和拼接
  |方法|功能|参数|返回值|原数组|
  |----|----|----|----|----|
  |concat|拼接数组|要拼接的数组|拼接后的数组|不变|
  |join|拼接数组|要拼接的数组|拼接后的数组|不变|
  |toString|数组转化字符串|无|转化好的字符串|不变|

  ```js
  ary1.concat(ary2); // 把 ary2 和 ary1 进行拼接，ary2 在后面
  // 相当于把 ary1 克隆了一份
  ary1.concat();
  ary1.concat([]);

  //扩展：eval：js 中，把字符串变成表达式执行的方法
  var ary = [1, 2, 3, 4, 5, 6];
  console.log(eval(ary.join("+"))); //21
  ```

- 第三大类：排序
  |方法|功能|参数|返回值|原数组|
  |----|----|----|----|----|
  |reverse|倒序数组|无|倒序后的数组|改变|
  |sort|按一定规则排序|函数|排序好的|改变|

  ```js
  ary.sort(); // 这样只能处理 10 以内的数字进行排序，因为按照 UNICODE 编码的值排序
  ary.sort((a, b) => a - b); // 由小到大排列
  ary.sort((a, b) => b - a); // 由大到小排列
  ```

- 第四大类 ie678 不兼容的

  - indexOf / lastIndexOf (字符串这个两个方法兼容所有浏览器，而数组是不兼容的)，数组中第一次/最后一次出现位置的索引，`没有返回-1`，`有就返回索引值`，`原来数组不变`
  - forEach / map 都是用来遍历数组中的每一项的，forEach 返回值是 undefined，map 返回一个新数组

    ```js
    // map 和 forEach 的语法一样，forEach 返回值是 undefined，map 返回一个新数组
    /*@params arr	可选。当前元素所属的数组对象。thisArg	可选。如果 thisArg 参数有值，则每次 callback 函数被调用时，this 都会指向 thisArg 参数。如果这个参数为空， "undefined" 会传递给 "this" 值*/
    array.forEach(function(currentValue, index, arr), thisArg)
    arr.map(function callback(currentValue, index, array) {
    // Return element for new_array
    }, thisArg)

    ary.forEach(function (item, index) {});
    // 原数组不变 返回的新数组是每一项都乘以 10
    ary.map(function (item, index) {
      return item * 10;
    });
    console.log(ary);
    ```

### Math 数学方法

- Math.abs(); //绝对值
- Math.pow(2,3); //平方 2 底数 3 指数 放大镜
- Math.sqrt(); //开平方 放大镜
- Math.ceil(); //向上去整
- Math.floor(); //向下取整
- Math.round(); //四舍五入 负数需要特殊记忆下 向地板靠拢
- Math.max(); //取最大值 是一组数 不是数组
- Math.min(); //取最小值
- Math.random(); //获取从 0 到 1 之间的随机数 [0,1)
- Math.round(Math.random()\*(m-n)+n); //获取从 n 到 m 的随机整数 （大-小）

### String 字符串方法

- charAt 通过指定的索引值获取字符 ，参数是索引值,返回值就是获取到的字符（验证码）
- charCodeAt 通过索引值获取指定字符的 ASCII 码值
- substr(n,m) 截取：从索引 n 截取 m 个，只有一个参数默认截取到末尾，字符串不变
- subtring(n,m) 复制：索引 n 复制到索引 m 不包含 m，只有一个参数截取到末尾
- slice 跟 substring 是一样的，不同点：slice 支持负数索引,
- toUpperCase 转变成大写
- toLowerCase 转换成小写
- indexOf 字符出现在字符串中的索引位置，如果有返回对应索引，没有返回-1
- lastIndexOf 同 indexOf，最后一次出现的位置的索引 数组不兼容，字符串方法兼容
- split 把字符串按照指定的分隔符拆分成数组
- localCompare；字符串和字符串比较，把两个字符串转化拼音比较字母顺序，值-1,1,0.
- replace(被替换，用谁替换) 替换字符串 后面可以接函数 只替换一次，`/a/g 全局匹配`
  `let str = 'dfgfgdfg'; console.log(str.replace(/df/g, '666')); //666gfg666g`
- match --正则时候讲
- endsWith() 以 xxx 结尾
- startsWith() 以 xxx 开头
- includes() 包含 xxx
- concat 字符串拼接
- padEnd()/padStart() 填充字符串，补白
- trim()
- trimEnd()
- trimStart()
- concat 字符串拼接
  ```js
  let greetList = ["Hello", " ", "Venkat", "!"];
  "".concat(...greetList); // "Hello Venkat!"
  ```

### Date 日期 typeof Date -->”function”

Date 时间： new Date(); 参数：转换成一个时间格式的对象

```js
console.log(new Date()); //2020-04-29T06:39:49.605Z
console.log(date.getFullYear()); //年
console.log(date.getYear()); //年 1900 年开始算
console.log(date1.getMonth()); //月份比实际月份小 1 [0,11]
console.log(date.getDate()); //日期 1-31
console.log(date.getDay()); // [1,2,3,4,5,6,0] 星期日是 0
console.log(date.getHours()); //小时
console.log(date.getMinutes()); //分
console.log(date.getSeconds()); //秒
console.log(date.getMilliseconds()); //ms 毫秒
console.log(date.getTime()); //这个时间对象距离 1970 年 1 月 1 日 0 点 0 分 0 秒
```

- ES9 For await of :异步集合遍历

### 类 Class

```js
// ES5:
let Animal = function (type) {
  this.type = type; //私有属性
  this.eat = function () {}; //私有方法
};
Animal.prototype.eat = function () {}; //公共方法，原型链集成
Animal.walk = function () {}; //es5类的静态方法
let dog = new Animal("dog");
dog.constructor.prototype.eat = function () {}; //修改类的原型方法

// ES6:
class Animal {
  constructor(type) {
    //构造函数里写属性，构造函数外写方法
    this.type = type;
  }
  eat() {} //公有方法，原型上的方法
  // es6属性保护
  get age() {
    return _age;
  } //设置age属性  dog.age age就是一个入口
  set age(val) {
    if (val > 4) {
      // 通过set做拦截
      _age = val;
    }
  }
  static walk() {} //静态方法：拿不到类的实例对象
}
dog.age = 3; // 4  被set拦截没有生效

// 类的实例方法：在原型上或者this.eat私有方法

// es6定义私有方法
class Widget {
  foo(name) {
    setName.call(this, name);
  }
}
function setName(name) {
  return (this.name = name);
}
```

### 原型链

> - 类：prototype 显示原型
> - 实例：**proto** 隐式原型

- 1 创建构造函数/类时

  - 天生自带一个 prototype 的属性。
  - prototype 的值是一个对象类型（装的就是我们公有的属性）`console.log(Person.prototype);//Person {}`
  - prototype 的值也有一个天生自带的属性叫 constructor 构造器，并且这个属性的值是函数(构造函数，类)本身`console.log(Person.prototype.constructor);//[Function: Person]`

  ```js
  //所有的对象都是Object的实例 （Object叫我们的基类）
  console.log(Person.prototype.__proto__); //{}
  console.log(Person.prototype.__proto__ == Object.prototype); //true  Object也是函数
  ```

  > 构造函数,类 ——> prototype(值：对象类型) ——> constructor(值：构造函数，类)本身

- 2 每个实例都是对象，都有**proto**属性，它是这个实例所属类的原型.`console.dir(person.__proto__==Person.prototype);//true`
  > 实例.**proto** ——>类.prototype
- 3 每个引用类型都有天生自带的属性**proto**,所以 prototype 的值也有天生自带一个**proto**的属性。并且这个属性的值也是一个对象类型，一直到我们的基类 Object
  > 实例.**proto** ——>类.prototype.**proto** ——>Object.prototype
- 4 通过类的原型添加的属性和方法都是公有的，每个实例都会自带
- 5 一个实例的方法在运行的时候，如果这个方法是自己私有的，那么就直接用，如果不是私有的，那么通过**proto**去所属类的原型上去查找，如果还没有就通过原型的**proto**一直查到基类的 Object.如果还没有报错，如果有就直接用了。我们把这种通过**proto**查找的机制叫做**原型链**。
  - 实例.**proto** === 原型
  - 原型.constructor === 构造函数
  - 构造函数.prototype === 原型
  - 实例.constructor === 构造函数

```js
var A = function () {};
var a = new A();
console.log(a.__proto__); //A {}（即构造器function A 的原型对象）
console.log(a.__proto__.constructor); //[Function: A]`（function A 本身）
console.log(a.__proto__.__proto__); //Object {}（即构造器function Object 的原型对象）
console.log(a.__proto__.__proto__.__proto__); //null
//Object上有 hasOwnProperty  isPrototypeOf  propertyIsEnumerable

class jQuery {
  constructor(selector) {
    const result = document.querySelectorAll(selector);
    const length = result.length;
    for (let i = 0; i < length; i++) {
      this[i] = result[i];
    }
    this.length = length;
    this.selector = selector;
  }
  get(index) {
    return this[index];
  }
  each(fn) {
    for (let i = 0; i < this.length; i++) {
      const elem = this[i];
      fn(elem);
    }
  }
  on(type, fn) {
    return this.each((elem) => {
      elem.addEventListener(type, fn, false);
    });
  } // 扩展很多 DOM API
}

// 插件
jQuery.prototype.dialog = function (info) {
  alert(info);
};

// “造轮子”
class myJQuery extends jQuery {
  constructor(selector) {
    super(selector);
  } // 扩展自己的方法
  addClass(className) {}
  style(data) {}
}
```

### new 的执行过程方

```js
let newMethod = function (Parent, ...rest) {
  // 1.以构造器的prototype属性为原型，创建新对象；
  let child = Object.create(Parent.prototype);
  // 2.将this和调用参数传给构造器执行
  let result = Parent.apply(child, rest);
  // 3.如果构造器没有手动返回对象，则返回第一步的对象
  return typeof result === "object" ? result : child;
};
```

### 函数声明和函数表达式的区别：

- 函数声明 function fn(){} // 代码执行前会预加载
- 函数表达式 const fn = function(){} // 不会预加载

### new Object()和 Object.create()区别：

- {}等同于 new Object()，原型 Object.prototype
- Object.create(null) 没有原型 Object.create({...}) 可指定原型

```js
const obj2 = new Object(obj1); // obj1===obj2 true  全相等的
const obj2 = Object.create(obj1); // obj1===obj2 false 会把obj1的方法都放到obj2的原型上
```

### 继承

```js
let Dog = function () {
  Animal.call(this, "dog"); // 初始化父类的构造函数并传参，获取父级的私有方法
  // A.apply(this, arguments);
  this.run = function () {}; //子类的私有方法
};
Dog.prototype = Animal.prototype; //引用同一个内存地址

class Dog extends Animal {
  construtor(type) {
    //显示 隐式
    super(type); // 要和父类的 construtor 参数一样，super 要写在 this 上边
    this.age = 2;
  }
}

//构造函数创建类
//function User (name,age){ this.name=name;this.age=age; }
class User {
  constructor(name, age) {
    //私有方法
    this.name = name;
    this.age = age;
  }
  //静态方法：不需要实例化类就能使用的方法
  static getOne() {
    return 111;
  }
  //原型方法
  //User.prototype.changeInfo=function(name,age){
  //this.name=name;
  //this.age=age;
  //}
  changeInfo(name, age) {
    this.name = name;
    this.age = age;
  }
  Info() {
    return this.name + ":" + this.age;
  }
}

//子类继承
class Manger extends User {
  constructor(name, age, password) {
    super(name, age); //先创建父对象的属性，再添加给子类
    this.password = password;
  }
  changePassword() {
    this.password = password; //添加新方法
  }
  Info() {
    var info = super.info();
    return info + "--new";
  }
}
```

### 古代的继承

```js
// 1原型链继承  父(公有+私有)==>子(公有)  无法向父类构造函数传参
B.prototype = new A(); //缺点：一个实例修改方法所有实例的方法都修改了
B.prototype.constructor = B; //由于修改了B的原型导致自带constructor属性丢失

// 2 call 继承   父(私有)==>子(私有)	可以向父类传递参数
function B() {
  A.call(this);
} //把A函数中的this修改成b的当前实例，然后执行A。

// 3拷贝继承  父(公有+私有)==>子(私有)  效率较低，内存占用高，for in不可枚举方法拿不到
//for in 遍历可以遍历实例的私有属性，也可以遍历人为添加的公有属性
function B() {
  var obj = new A(); //A的实例
  for (var key in obj) {
    this[key] = obj[key]; //把父类实例的所有属性和方法同时赋值给b一份
  }
  obj = null;
}

// 4混合继承  原型+call		可以向父类传递参数  红色最完美的继承
B.prototype = new A() || A.prototype || Object.create(A.prototype);
//把A的私有的和公有的都变成B的公有的
B.prototype.constructor = B; //补全constructor属性
function B() {
  A.call(this);
} //call继承 把A的私有属性变成B的私有属性
```

### ES6 发布订阅模式

```js
class EventEmiter {
  constructor() {
    this._events = {}; //维护一个对象
  }
  on(eventName, callback) {
    if (this._events[eventName]) {
      //如果有就放一个新的
      this._events[eventName].push(callback);
    } else {
      //如果没有就创建一个数组
      this._events[eventName] = [callback];
    }
  }
  emit(eventName, ...rest) {
    if (this._events[eventName]) {
      //循环一次执行
      this._events[eventName].forEach((item) => {
        item.apply(this, rest);
      });
    }
  }
  removeListener(eventName, callback) {
    if (this._events[eventName]) {
      //当前数组和传递过来的 callback 相等则移除掉
      this._events[eventName] = this._events[eventName].filter(
        (item) => item !== callback
      );
    }
  }
  once(eventName, callback) {
    function one() {
      //在 one 函数运行原来的函数，只有将 one 清空
      callback.apply(this, arguments);
      this.removeListener(eventName, one); //先绑定 执行后再删除
    }
    this.on(eventName, one); //emit 触发会执行此函数，会给这个函数传递 rest 参数
  }
}
class Man extends EventEmiter {}
let man = new Man();
function findGirl() {
  console.log("找新的女朋友");
}
function saveMoney() {
  console.log("省钱");
  console.log("arguments" + JSON.stringify(arguments));
}
// man.once('失恋', findGirl);
// man.on('失恋', findGirl) //失恋 ，绑定一个函数方法
man.on("失恋", saveMoney); //失恋 ，绑定一个函数方法
// man.removeListener('失恋', saveMoney); //移除一个函数方法
```

### 事件

![avatar](https://github.com/zhanqingbin/myBook/blob/master/images/js%E4%BA%8B%E4%BB%B6%E7%B1%BB.png?raw)

- 捕获和冒泡：事件捕获是为了逐层确定事件的来源,事件冒泡是为了逐级响应事件
- 在 ie 中，document.getElementById("#test").attachEvent(type,listener);
  detachEvent(event,function);

```js
function bindEvent(elem, type, fn) {
  elem.addEventListener(type.fn);
}
bindEvent(btn1, "click", (event) => {
  console.log(event.target); //获取触发的元素
  event.preventDefault(); //阻止默认行为
  alert("clicked");
});
// 阻止事件冒泡：e.stopPropagation()
// 事件代理：利用事件冒泡把事件绑定到父级元素上，通过判断event.target.nodeName==='A'来进行操作
function bindEvent(elem, type, selector, fn) {
  if (fn == null) {
    fn = selector;
    selector = null;
  }
  elem.addEventListener(type, (event) => {
    const target = event.target;
    if (selector) {
      //有代理绑定
      if (target.matches(selector)) {
        fn.call(target.event);
      }
    } else {
      //普通绑定
      fn.call(target, event);
    }
  });
}
```

### 本地对象、内置对象、宿主对象（本全 E，内置不实例，宿主非本如 DOM）

- 本地对象：ECMAScript 提供的对象，包括 Array Object RegExp 等可以 new 实例化的对象和 Math 等不可以实例化的
- 内置对象：Gload，Math 等不可以实例化的(他们也是本地对象，内置对象是本地对象的一个子集)
- 宿主对象：所有的非本地对象，所有的 BOM 和 DOM 对象都是宿主对象，如浏览器自带的 document,window 等对象

### 页面加载

- onready 是页面解析完成之后执行，而 onload 是在页面所有元素加载完成后执行
- DOM 加载到 link 标签 css 文件的加载是与 DOM 的加载并行的,也就是说,css 在加载时 Dom 还在继续加载构建,而过程中遇到的 css 样式或者 img,则会向服务器发送一个请求,待资源返回后,将其添加到 dom 中的相对应位置中;
- 在 script 标签中添加 defer=“ture”，则会让 js 与 DOM 并行加载，待页面加载完成后再执行 js 文件，这样则不存在阻塞；
- 在 scirpt 标签中添加 async=“ture”，这个属性告诉浏览器该 js 文件是异步加载执行的，也就是不依赖于其他 js 和 css，也就是说无法保证 js 文件的加载顺序，但是同样有与 DOM 并行加载的效果；
  同时使用 defer 和 async 属性时，defer 属性会失效；
- 可以将 scirpt 标签放在 body 标签之后，这样就不会出现加载的冲突了。

### ES6

- 默认值

  ```js
  function f(x, y = 2, z = 4) {}
  f(1, undefined, 11); //y传undefined是默认值2

  function f(x, y = 9, z = x + y) {
    console.log(Array.from(arguments)); //拿到传的参数
    console.log(f.length); //直接拿函数的length，能获取到没有默认值的参数的个数
  }
  ```

- Rest parameter 参数

```js
function sum(base, ...nums) {
  let num = 0;
  nums.forEach(function (item) {
    num += item * 1;
  });
  return base * 2 + num;
}
```

- spread 操作 `sum(...data)`

- 箭头函数

- Set 数据结构：不能重复数据，数据是可遍历的对象

  ```js
  let s = new Set([1, 2, 3]);
  s.add(1).add(2);
  s.delete(1);
  s.clear(); //清空
  s.has(1); //true
  s.size;
  s.keys();
  s.values();
  s.entries(); //返回键值对
  s.forEach(function (item) {});
  for (let item of s) {
    console.log(item);
  }

  // set去重数组，牛逼！
  let newArr = [...new Set(arr)];
  ```

- Map 数据结构：key 可以是任意值，可以是对象或者函数

  ```js
  let map = new Map([
    [1, 2],
    [3, 4],
  ]);

  map.set(2, 1);
  map.delete(1); // 删除key
  map.clear();
  map.size;
  map.has(1); // 查索引对应的值
  map.get(1);
  map.keys();
  map.values();
  map.entries();
  map.forEach((value, key) => {});
  for (let [key, value] of map) {
  } //可遍历的对象才能用for of
  ```

- `WeakSet：只能存对象`
- `WeakMap：只能是对象类型的key`
- object

  ```js
  // 可以写变量或表达式
  obj = { x, y, [z]: 6 };
  obj = {x, y, [x + z] };

  const target = {};const source = {b:3,a:4}; // 相同的属性被覆盖，不同的属性被拷贝
  Object.assign(target,source) // es6数据拷贝(浅)引用类型的地址进行替换，不会精确到差异
  Object.is(); // 两个值是否相等
  Object.is(NaN,NaN);// true   NaN===NaN F
  Object.is({},{});// false   {}==={} F
  Object.is(+0,-0);// false   +0===-0 T

  // Object.is() 会在下面这些情况下认为两个值是相同的：
  // 两个值都是 undefined
  // 两个值都是 null
  // 两个值都是 true 或者都是 false
  // 两个值是由相同个数的字符按照相同的顺序组成的字符串
  // 两个值指向同一个对象
  // 两个值都是数字并且
  // 都是正零 +0
  // 都是负零 -0
  // 都是 NaN
  // 都是除零和 NaN 外的其它同一个数字
  ```

- Template:模板字符串

- 解构赋值

  ```js
  let arr = [1, 2];
  let [a, b] = arr;
  let [a, , b] = arr; // 略过某一项用，，
  let [a, , b] = new Set([1, 2, 3]);

  // 解构赋值不仅可以赋值变量，还可以赋值对象的属性
  let user = ({ name: "s", username: "d" };
  [user.name, user.username] = [1, 2];
  for (let [k, v] of Object.entries(user)) {
    console.log(k, v);//[ [ 'name', 1 ], [ 'username', 2 ] ]
  }
  let [user, name, ...last] = arr; //1,2,[3,4,5]

  // 多维数组解构
  let arr = [1,[2,3],4];
  let [a,[b,c],d]=arr;

  // 不完全解析
  let [a,[,c],d]=arr;

  // 变量交换
  let x = 1,y=2;
  [y,x]=[x,y]

  // Set解构赋值
  let [x,y] = new Set([1,2]) // x:1,y:2

  let [x,...y,6] = [1,2,3,4,5,6] // Rest element must be last element
  let [x,...y] = [1,2,3,4,5,6] // y:[2,3,4,5,6]

  // 对象的解构赋值
  let {name} = {name:'Q',age:21}
  console.log(name) // Q

  // 嵌套解构：模式不能打印，只有变量能打印
  let obj = {a:{b:{c:3}}}; // a,b是模式，只有c是变量能打印
  let {a:{b:{c}}} = obj
  console.log(c); // 3

  // 默认值
  let [x=1,y]=[undefined,2] // x:1,y:2

  // 修改变量名
  let {name:myName} = obj
  console.log(myName)

  // 字符串
  let [a,b,c] = 'hello' // a:h   b:e  c:o

  // 参数是数组
  function test([x=1,y=2]=[],z=3){
    console.log(x,y,z)
  }
  test([],80)
  ```

- Promise

```js
function loadScript(src) {
  // pending,undefined
  return new Promise((resolve, reject) => {
    let script = document.createElement("script");
    script.src = src;
    script.onload = () => resolve(src); // fulfilled,result
    script.onerror = (err) => reject(err); // rejected,error
    document.head.append(script);
  });
}
loadScript("./1.js").then(loadScript("./2.js")).then(loadScript("./3.js"));
// .then是promise对象原型上的方法，每次返回一个promise实例，需要传递两个参数，如果没传返回一个空的promise对象，保证可以继续调用.then方法
// promise.then(onFulfilled,onRejected)

loadScript("./1.js")
  .then(
    () => {
      return loadScript("./4.js");
    },
    (err) => {
      console.log(err);
    }
  )
  .then(
    () => {
      loadScript("./3.js");
    },
    (err) => {
      console.log(err);
    }
  );

// promise的静态方法
Promise.resolve(42); // 包裹返回的静态值可以调用then方法
Promise.reject(new Error("ss"));
// catch也是实例的方法 catch异常处理.then().catch(err=>console.log(err))

//异步的并行
const p1 = Promise.resolve(1);
const p2 = Promise.resolve(20);
const p3 = Promise.resolve(3);
Promise.all([p1, p2, p3]).then((value) => {
  console.log(value);
});
Promise.race(); //先到先得
const p1 = () => {
  return new Promise((resolve, reject) => {
    setTimeout(function () {
      resolve(1);
    }, 1000);
  });
};
const p2 = () => {
  return new Promise((resolve, reject) => {
    setTimeout(function () {
      resolve(2);
    }, 3000);
  });
};
Promise.race([p1(), p2()]).then((value) => {
  console.log(value);
});

//Promise代码：
const callAsync = (() => {
  let pending = false;
  let count = 1;
  let textNode = document.createTextNode(count);
  let tasks = [];
  let nextTickHandler = () => {
    pending = false;
    let cbs = tasks.slice(0);
    tasks = [];
    cbs.forEach((cb) => cb());
  };
  let observer = new MutationObserver(nextTickHandler);
  observer.observe(textNode, {
    characterData: true,
  });
  let nextTick = () => {
    count = (count + 1) & 1;
    textNode.data = count;
  };
  return (fn, args, cb, onError) => {
    let func = () => {
      try {
        cb ? cb(fn(args)) : fn(args);
      } catch (err) {
        onError(err);
      }
    };
    tasks.push(func);
    if (pending) return;
    pending = true;
    nextTick();
  };
})();

const PENDING = "pending",
  FULFILLED = "fulfilled",
  REJECTED = "rejected",
  isFunction = (fn) => typeof fn === "function";

class MyPromise {
  constructor(fn) {
    //接收一个excutor执行函数为参数, excutor有两个参数resolve reject
    this._status = PENDING; //初始状态
    this._onFulfilledQueue = []; // 存储fulfilled状态对应的onFulfilled函数
    this._onRejectedQueue = []; // 存储rejected状态对应的onRejected函数
    this._value = null; // fulfilled状态时 返回的信息
    this[Symbol.toStringTag] = "MyPromise";
    try {
      fn(this._resolve.bind(this), this._reject.bind(this)); //立即执行excutor
    } catch (err) {
      this._reject(err);
    }
  }
  _resolve(value) {
    //成功状态下接收的值
    if (this._status !== PENDING) return;
    this._status = FULFILLED;
    const runFulfilled = (value) => {
      let fn;
      while ((fn = this._onFulfilledQueue.shift())) fn(value);
    };
    const runRejected = (error) => {
      let fn;
      while ((fn = this._onRejectedQueue.shift())) fn(error);
    };
    if (value instanceof MyPromise) {
      value.then(
        (res) => {
          this._value = res;
          runFulfilled(res);
        },
        (err) => {
          this._value = err;
          runRejected(err);
        }
      );
    } else {
      this._value = value;
      runFulfilled(value);
    }
  }
  _reject(error) {
    //接收失败的原因
    if (this._status !== PENDING) return;
    this._status = REJECTED;
    this._value = error;
    let fn;
    while ((fn = this._onRejectedQueue.shift())) fn(error);
  }
  then(onFulfilled, onRejected) {
    //onFulfilled接收成功的值，onRejected接收失败的原因
    onFulfilled = isFunction(onFulfilled) ? onFulfilled : (res) => res; //resolve(成功) onFulfilled调用
    onRejected = isFunction(onRejected)
      ? onRejected
      : (err) => {
          throw err;
        }; //reject(失败)
    return new MyPromise((onFulfilledNext, onRejectedNext) => {
      const resolve = (value) => {
        callAsync(
          onFulfilled,
          value,
          (res) => {
            if (res instanceof MyPromise) {
              res.then(onFulfilledNext, onRejectedNext);
            } else {
              onFulfilledNext(res);
            }
          },
          onRejectedNext
        );
      };
      const reject = (error) => {
        callAsync(
          onRejected,
          error,
          (res) => {
            if (res instanceof MyPromise) {
              res.then(onFulfilledNext, onRejectedNext);
            } else {
              onFulfilledNext(res);
            }
          },
          onRejectedNext
        );
      };
      switch (this._status) {
        case PENDING:
          this._onFulfilledQueue.push(resolve);
          this._onRejectedQueue.push(reject);
          break;
        case FULFILLED:
          resolve(this._value);
          break;
        case REJECTED:
          reject(this._value);
          break;
      }
    });
  }
  catch(onRejected) {
    //在链式写法中可以捕获前面then中发送的异常
    return this.then(null, onRejected);
  }
  static resolve(value) {
    return value instanceof MyPromise
      ? value
      : value && isFunction(value.then)
      ? new MyPromise((res) => res()).then(() => new MyPromise(value.then))
      : new MyPromise((res) => res(value));
  }
  static reject(error) {
    return new MyPromise((resolve, reject) => reject(error));
  }
  static all(list) {
    return new MyPromise((resolve, reject) => {
      let values = [],
        count = list.length;
      list.forEach((item, index) => {
        MyPromise.resolve(item).then((res) => {
          values[index] = res;
          --count == 0 && resolve(values);
        }, reject);
      });
    });
  }
  static race(list) {
    return new MyPromise((resolve, reject) => {
      list.forEach((item) => MyPromise.resolve(item).then(resolve, reject));
    });
  }
  static allSettled(list) {
    return new MyPromise((resolve) => {
      let count = list.length,
        values = [];
      list.forEach((item, index) => {
        MyPromise.resolve(item).then(
          (value) => {
            values[index] = {
              status: "fulfilled",
              value,
            };
            --count == 0 && resolve(values);
          },
          (reason) => {
            values[index] = {
              status: "rejected",
              reason,
            };
            --count == 0 && resolve(values);
          }
        );
      });
    });
  }
  finally(cb) {
    return this.then(
      (value) => MyPromise.resolve(cb()).then(() => value),
      (reason) =>
        MyPromise.resolve(cb()).then(() => {
          throw reason;
        })
    );
  }
}
```

- Reflect 反射机制：非反射先指定方法，反射是执行过程中根据条件选调用那个方法

  > 是一个内置的对象，它提供拦截 JavaScript 操作的方法

  ```js
  const obj = { name: "abc", age: 18 };
  // 旧：
  "name" in obj ? true : false;
  delete obj[age];
  Object.keys(obj);

  Reflect.has(obj, "name");
  Reflect.deleteProperty(obj, "age");
  Reflect.ownKeys(obj);
  console.log(Reflect.apply(Math.floor, null, [4.72]));
  console.log(
    Reflect.apply(price > 100 ? Math.floor : Math.ceil, null, [price])
  );
  let d = Reflect.construct(Date, []);
  console.log(d.getTime(), d instanceof Date);
  const student = {};
  const r = Reflect.defineProperty(student, "name", { value: "Mike2" });
  console.log(student, r);
  const obj = { x: 1, y: 2 };
  Reflect.deleteProperty(obj, "x");
  console.log(obj);
  console.log(Reflect.get(obj, "x"));
  console.log(Reflect.get([3, 4], 1)); //4
  console.log(Object.getOwnPropertyDescriptor(obj, "y"));
  let d = new Date();
  console.log(Reflect.getPrototypeOf(d)); //看原型上的方法
  console.log(Reflect.has(obj, "y"));
  Object.freeze(obj); //使obj不可以扩展
  console.log(Reflect.isExtensible(obj)); //是不是可扩展的
  // 自身的属性
  console.log(Reflect.ownKeys(obj));
  console.log(Reflect.ownKeys([1, 2])); //返回索引和length
  Reflect.preventExtensions(obj); //禁止扩展
  Reflect.set(obj, "z", 4);
  Reflect.set(arr, 2, "goose");
  Reflect.setPrototypeOf(arr, String.prototype); //修改原型对象
  ```

- Proxy 代理

  ```js
  // Proxy保护原始信息
  let o = {
    name: 'xiaomi',
    price: 1999
  }
  let d = new Proxy(o, {
    get (target, key) {
      if (key === 'price') {
        return target[key] + 20
      } else {
        return target[key]
      }
    }
    set (target, key, value) {
      return false
    }
  })
  console.log(d.price, d.name)

  // es5拦截处理
  for (let [key] of Object.entries(o)) {
    Object.defineProperty(o, key, {
      writable: false
    })
  }

  // 监听错误
  window.addEventListener('error', (e) => {
    console.log(e.message)
    // report('./')
  }, true)
  ```

- Symbol：不会重复的值

  ```js
  let name1 = Symbol("name");
  let name2 = Symbol("name");
  console.log(name1 === name2); // false

  const name = Symbol();
  const person = {
    [name]: "abc",
    say() {
      console.log(this[name]);
    },
  };
  person[name]; // 'abc'
  Symbol.for(); // 内部接收字符串  Symbol.for(true)===Symbol.for('true')  // √
  ```

- 遍历器 iterator 调用 next()方法才能继续执行
  ```js
  let arr = ["a", "b", "c"];
  let iterator = arr[Symbol.iterator()];
  console.log(iterator.next()); // {value:'a',done:false}
  console.log(iterator.next()); // {value:'b',done:false}
  console.log(iterator.next()); // {value:'c',done:false}
  console.log(iterator.next()); // {value:undefined,done:true}
  console.log(iterator.next()); // {value:undefined,done:true}
  ```
- Generator 生成器 带\*的 fn

  - 带有\*的函数
  - 函数内部使用 yield 暂停执行，遇到 yield 执行就停下来了,yield 后的值作为 next 的 value
  - 调用时用 next()恢复函数执行，找函数中的 yield 或者结尾函数暂停

  ```js
  function * fn(){
    console.log('foo');
    returen 'bar'
  }
  // 生成器执行生成一个iterator
  let it = fn()
  let res = it.next()
  console.log(res)

  // generator中使用yield分段执行：
  function * fn(){
    yield 1;
    yield 2;
    yield 3;
  }
  let it = fn;
  console.log(it.next()) // {value:1,done:false}
  console.log(it.next()) // {value:2,done:false}
  console.log(it.next()) // {value:3,done:false}
  console.log(it.next()) // {value:undefined,done:true}

  for (let v of it){
    console.log(v)
  }  //1 2 3  只会打印done为false的值

  function * loop () {
    for (let i = 0; i < 5; i++) {
      yield console.log(i) //yield关键词
    }
  }
  const l = loop()
  // 场景：抽奖 一个一个抽/自定义遍历器
  l.next() 0
  l.next() 1
  l.next() 2
  l.next() 3
  l.next() 4
  l.next()

  function * gen () {
    let val
    val = yield 1
    console.log(val)
  }
  const l = gen()
  l.next() // 遇到 yield 执行停下来了 后边是 1 所以不打印
  l.next() //undefined 函数已经结束还没找到 yield，yield 表达式没有值返回 undefined

  function * gen () {
    let val
    val = yield * [1, 2, 3] //加\*说明后边是一个可遍历的对象
    console.log(val)
  }
  // next()：返回当前数据的 value 和是否结束{value:1,done:false}
  l.next(10)
  console.log(l.return(100)) //return 提前结束{value:100,done:true}
  l.next(20) // 20 通过 next 传值 修改 yield 的返回值

  function * gen () {
    while (true) {
      try {
        yield 1
      } catch (e) {
        console.log(e.message)
      }
    }
  }
  const g = gen()
  console.log(g.next())
  console.log(g.next())
  console.log(g.next())
  console.log(g.next())
  g.throw(new Error('ss')) //继续执行不会影响类似 continue
  console.log(g.next())
  ```

- Module：模块化设计

  ```js
  // 导出
  export const name = 'hello'
  export let addr = 'Beijing'
  export var list = [1, 2, 3]
  // 导入
  import { name, addr, list } from './name.js'

  // 一起导出
  export default name
  export {
    addr,
    list
  }
  import name, { addr, list } from './name.js'

  // 导入改名：默认导出可以直接改名字，{}导出要用as改名
  import name2, { addr as addr2, list } from './name.js'

  // 函数导出
  const say = (content) => {
    console.log(content)
  }
  const run = () => {
    console.log('i am running')
  }
  //引入函数
  import { say, run } from './say.js'

  export default say
  export {
    run
  }
  //默认导出放到花括号外
  import say { run } from './say.js'

  // 默认导出对象
  export default {
    code:1,
    message:'success'
  }
  import obj form './obj.js'

  // 导出多个对象：
  const data = {
    code: 1,
    message: 'success'
  }
  const des = {
    age: 20,
    addr: 'Beijing'
  }
  export default {
    data,
    des
  }
  import obj from './data.js'
  let {data, des} = obj

  //类的默认导出
  export class Test {
    constructor () {
      this.id = 6
    }
  }
  export class Animal {
    constructor () {
      this.name = 'dog'
    }
  }
  export default class People {
    constructor () {
      this.id = '132'
    }
  }
  import { Test, Animal  }form './test.js'

  // 导入全部模块起一个别名
  import * as Mod from './mod'
  let test = new Mod.Test()
  console.log(test.id)
  let animal = new Mod.Animal()
  console.log(animal.name)
  let people = new Mod.default()
  console.log(people.id)
  ```

- ES8：async await
- ES9：For await of :异步集合遍历、Promise.finally()

  ```js
  function Gen(time) {
    return new Promise((resolve, reject) => {
      setTimeout(function () {
        resolve(time);
      }, time);
    });
  }
  async function test() {
    let arr = [Gen(2000), Gen(100), Gen(3000)]; //都是异步操作
    for (let item of arr) {
      console.log(Date.now(), await item.then(console.log));
    }
  }

  async function test() {
    let arr = [Gen(2000), Gen(100), Gen(3000)];
    for await (let item of arr) {
      console.log(Date.now(), item);
    }
  }
  ```

- ES9 中 Object 的 Rest 和 Spread
  ```js
  const output = {
    ...input, // spread是拷贝的形式做的  不是引用地址，修改原对象不影响现在的
    ...test,
    c: 3,
  };
  ```

### commonjs 和 es6 模块化的区别

> - CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
> - CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。
> - CommonJs 是单个值导出，ES6 Module 可以导出多个
> - CommonJs 是动态语法可以写在判断里，ES6 Module 静态语法只能写在顶层
> - CommonJs 的 this 是当前模块，ES6 Module 的 this 是 undefined

- CommonJS（运行时同步加载的，拷贝模块中的对象，浅拷贝）

  - 模块可以多次加载，但只会在第一次加载，之后会被缓存，引入的是缓存中的值
  - 引入使用：require("path")
  - 1.如果是第三方模块 只需要填入模块名
  - 2.自己定义的模块 需要使用相对路径或者绝对路径
  - 导出模块使用：exports.xxx 或 module.exports.xxx 或 module.exports=xxx 或 this.xxx
  - ！！不管是使用 exports 还是 this  ，都需要用点语法的形式导出，因为 他们两个是 module. - exports 的指针 重新赋值将会切断关联

- Es6 模块（【动态只读引用】，编译时异步加载，有一个独立的模块依赖的解析阶段。ES6 Module 是对模块的引⽤,即 ES6 Module 只存只读，不能改变其值，具体点就是指针指向不能变，类似 const 。）
  - 默认导出    export default 变量或者函数或者对象
  - 默认引入   import name from "相对或绝对路径"
  - 导出的名字和引入的名字可以不一致
  - 按需导出   export 需要声明 变量用 const var let  函数用 function
  - 按需引入   import {变量名或函数名} form "路径"
  - 全部引入 使用   import \* as 自定义 name "路径"
  - 会将默认导出和按需导出 全部引入

### js 宏任务和微任务

> 在挂起任务时，JS 引擎会将所有任务按照类别分到这两个队列中，首先在 macrotask 的队列（这个队列也被叫做 task queue）中取出第一个任务，执行完毕后取出 microtask 队列中的所有任务顺序执行；之后再取 macrotask 任务，周而复始，直至两个队列的任务都取完。

- 同步任务：进入主进程执行
- 异步任务：进入 Event table 注册函数，当指定事情完成时，ET 会将任务移到 Event Queue，主进程任务执行完毕为空，回去 EQ 读取，进入主进程执行，不断重复这个过程，就是 js 的事件循环（Event Loop）

- 宏任务：setTimeout、setInterval、requestAnimationFrame(web)、setImmediate(node)
- 微任务：Promise.then catch finally、MutationObserver(web)、process.nextTick(node)

```js
setTimeout(() => {
  console.log("内层宏事件4"); //执行后 回调一个宏事件
}, 0);
console.log("外层宏事件1");
new Promise((resolve) => {
  console.log("外层宏事件2");
  resolve();
})
  .then(() => {
    console.log("微事件1");
  })
  .then(() => {
    console.log("微事件2");
  });
console.log("外层宏事件3");
// 外层宏事件1 外层宏事件2 外层宏事件3 微事件1 微事件2 内层宏事件4
// 首先浏览器执行js进入第一个宏任务进入主线程, 遇到 setTimeout  分发到宏任务Event Queue中
// 遇到 console.log() 直接执行 输出 外层宏事件1
// 遇到 Promise， new Promise 直接执行 输出 外层宏事件2
// 遇到 console.log() 直接执行 输出 外层宏事件3
// 执行then 被分发到微任务Event Queue中
// 第一轮宏任务执行结束，开始执行微任务 打印 '微事件1' '微事件2'
// 第一轮微任务执行完毕，执行第二轮宏事件，打印setTimeout里面内容'内层宏事件4'

console.log("1"); //主线程直接执行
setTimeout(function () {
  //丢到宏事件队列中
  console.log("2"); //1
  process.nextTick(function () {
    console.log("3"); //3
  });
  new Promise(function (resolve) {
    console.log("4"); //2
    resolve();
  }).then(function () {
    console.log("5"); //4
  });
});
process.nextTick(function () {
  console.log("6"); //微事件1
});
new Promise(function (resolve) {
  console.log("7"); //主线程直接执行
  resolve();
}).then(function () {
  console.log("8"); //微事件2
});
//丢到宏事件队列中
setTimeout(function () {
  console.log("9"); //1
  process.nextTick(function () {
    console.log("10"); //3
  });
  new Promise(function (resolve) {
    console.log("11"); //2
    resolve();
  }).then(function () {
    console.log("12"); //4
  });
});
// 浏览器完整的输出为1，7，6，8，2，4，3，5，9，11，10，12
// 首先浏览器执行js进入第一个宏任务进入主线程, 直接打印console.log('1')
// 遇到 setTimeout  分发到宏任务Event Queue中
// 遇到 process.nextTick 丢到微任务Event Queue中
// 遇到 Promise， new Promise 直接执行 输出 console.log('7');
// 执行then 被分发到微任务Event Queue中
// 第一轮宏任务执行结束，开始执行微任务 打印 6,8
// 第一轮微任务执行完毕，执行第二轮宏事件，执行setTimeout
// 先执行主线程宏任务，在执行微任务，打印'2,4,3,5'
// 在执行第二个setTimeout,同理打印 '9,11,10,12'
// 总结：先执行主进程的：宏任务==》微任务，执行下一队列
```

### 异步和单线程

- js 是单线程，只能做一件事
- 浏览器和 nodejs 已经支持 js 启动进程，如 web worker
- JS 和 DOM 渲染公用一个线程，因为 JS 可修改 DOM 结构（一个进行另一个必须停止）
- 异步：（callback 回调函数）不阻塞代码执行，场景：1、网络请求 2、定时器
- 同步：alert()卡住 阻塞代码执行

### js 操作 DOM

```js
document.getElementById("div1");
doecument.getElemntsdByTagName("div"); //集合
document.getElementsByClassName("div1"); //集合
document.querySelectorAll("p"); //集合
//兼容所有浏览器获取屏幕宽高
let curWidth =
  document.documentElement.clientWidth || document.body.clientWidth;
let curHeight =
  document.documentElement.clientHeight || document.body.clientHeight;
```

- 节点类型
  |名称|nodeType |nodeName |nodeValue（节点值）
  |----|----|----|----|
  |元素 |1 |大写标签名 |null
  |文本 |3 |#text |文本内容
  |注释 |8 |#comment |注释内容
  |document |9 |#document |null

- DOM 中的节点关系属性:

  - childNodes 获取所有子节点,包含元素，文本，换行，空格, 注释等 类数组类型
  - children 获取所有元素子节点（标签元素）低版本浏览器可以拿到注释节点 ie8
  - firstChild 获取第一个子节点
  - fistElementChild 获取第一个元素子节点
  - lastChild 获取所有的子节点中的而最后一个
  - lastElementChild
  - parentNode 获取父亲节点
  - previousSibling 获取哥哥（上一个）节点 （文本，注释，元素）
  - previousElementSibling 哥哥的元素节点
  - nextSibling 获取弟弟节点 （文本，注释，元素）
  - nextElementSibling 弟弟的元素节点
  - HTML 中获取不到的最差也是 null，没见过 undefined。

- property：用 js 属性操作的一种形式，对 js 变量进行修改，修改对象属性，不会体现在 html 结构中 `p1.style.width(className)`设置或者获取页面样式
- attr：设置标签属性，对 dom 节点属性进项修改，修改 html 属性，会体现在 html 中
  ```js
  p1.setAttribute("data-name", "icc");
  p1.getetAttribute("data-name"); //icc
  p1.setAttribute("style", "color:red");
  ```
- property 会比 attr 避免一些重复的 dom 渲染，尽量用 property

  ```js
  // 新建节点：createElement
  const p1 = document.createElement("p");
  p1.innerHtml = "this is p1";
  // 插入和移动节点：appendChild
  div1.appendChild(p1);
  // 获取父元素：parentNode
  console.log(p1.parentNode);
  // 获取子元素列表：childNodes
  console.log(div1.childNodes); //包括文本元素
  // a 你要把谁插入 b 插入谁前面 父元.insertBefore(a,b)
  insertBefore(a, b);
  // 参数是要删除的元素 parentNode.removeChild(oA); 父级删除
  div1.removeChild(child[0]);
  // a 谁来替换 new b 替换谁 old 重点就是一动了父元.replaceChild(a,b)
  replaceChild(a, b);
  // 克隆节点 true：连内容一起拷贝
  cloneNode() / cloneNode(true);
  // 获取 p 标签
  const childP = Array.prototype.slice.call(div.childNodes).filter((child) => {
    if (child.nodeType === 1) {
      return true;
    }
    return false;
  });
  ```

- DOM 性能：耗费 cpu

  - 1、做一个缓存：把 dom 节点付给一个变量
  - 2、将频繁操作的改为一次性操作
    ```js
    // 文档碎片
    // 创建文件片段，此时没有插入 DOM 中
    const frag=document.createDocumentFragment()
    for(let i=0,i<10,i++){
      const li = document.createElement('li')
      li.innerHTML='this is li'
      frag.appendChild(li)
    }
    // 一次性插入
    nodeList.append(frag)
    ```

- BOM
  ```js
  // 如何识别浏览器类型 navigator screen location history
  const ua = navigator.userAgent;
  const isChrome = ua.indexOf("Chrome");
  console.log(screen.width); //屏幕的宽高
  location.href; //网址
  location.protocol; //协议
  location.host; //域名
  location.search; //查询参数
  location.hash;
  location.pathname;
  history.back();
  history.forward();
  ```

### Ajax (Asynchronous JavaScript And XML)

```js
// ajax: XMLHttpRequest
const xhr = new XMLHttpRequset();
xhr.open("GET", "/data/test.json", true); // true是异步的
xhr.onreadystatechange = function () {
  if (xhr.readyState === 4) {
    if (xhr.status === 200) {
      console.log(xhr.responseTest);
    }
  }
};
xhr.send(null);
// post请求：
const postData = {
  userName: "aa",
  password: 123,
};
xhr.send(JSON.stringify(postData));
```

- xhr.readyState：

  - 0 - 未初始化 还没掉用 send()方法
  - 1 - 载入中 已调用 send 方法正在发送请求
  - 2 - 载入完成 send 方法执行完成，已经接收到全部相应内容
  - 3 - 交互 正在解析相应内容
  - 4 - 完成 相应内容解析完成，可以在客户端调用

- 同源策略：浏览器的要求，网页和 server 必须同源，协议、域名、端口都一致。

  - img 可以用于统计打点，可使用第三方统计服务
  - link、script 可以使用 CDN，CDN 一般都是外域
  - script 可以实现 JSONP，也需要 server 端配合，服务端可以拼接任何数据返回

- 解决跨域

  - jsonp 的原理就是利用`<script>`标签没有同源策略限制，通过`<script>`标签 src 属性，发送带有 callback 参数的 GET 请求，服务端将接口返回数据拼凑到 callback 函数中，返回给浏览器，浏览器解析执行，从而前端拿到 callback 函数返回的数据。只能发送 get 一种请求
  - 跨域资源共享（CORS）Access-Control-Allow-Origin：\*
  - 代理 nginx vue 的 proxy
  - postMessage
  - webSocket

  ```js
  // jsonp 只能发送get一种请求
  let script = document.createElement('script');
  script.type = 'text/javascript';
  // 传参一个回调函数名给后端，方便后端返回时执行这个在前端定义的回调函数
  script.src = 'http://localhost:8080/login?user=admin&callback=handleCallback';
  document.head.appendChild(script);
  // 回调执行函数
  function handleCallback(res) {
    console.log(JSON.stringify(res));
  }
  // 服务端返回如下（返回时即执行全局函数）：
  handleCallback({"success": true, "user": "admin"})

  // jsonp
  window.callback = function (data) {
    console.log(data);
  };
  <script src="http://localhost:8080/jsonp.js"></script>;
  //jsonp.js的内容：执行这个函数吧内容带回去
  callback({ name: "abc" });

  // cors 服务端设置可以跨域 CORS请求设置的响应头字段，都以 Access-Control-开头,Access-Control-Allow-Origin：必选

  // #proxy服务器
  `server {
      listen       81;
      server_name  www.domain1.com;

      location / {
          proxy_pass   http://www.domain2.com:8080;  #反向代理
          proxy_cookie_domain www.domain2.com www.domain1.com; #修改cookie里域名
          index  index.html index.htm;

          # 当用webpack-dev-server等中间件代理接口访问nignx时，此时无浏览器参与，故没有同源限制，下面的跨域配置可不启用
          add_header Access-Control-Allow-Origin http://www.domain1.com;  #当前端只跨域不带cookie时，可为*
          add_header Access-Control-Allow-Credentials true;
      }
  }`


  let rdcAjax = function (url, method, type, data) {
    if (!url) {
      return Promise.reject(new Error("url 不能为空！"));
    }
    if (!method) {
      method = "GET";
    }
    if (!type) {
      type = "application/json";
    }
    if (
      type === "application/x-www-form-urlencoded" &&
      typeof data === "object"
    ) {
      let str = "";
      Object.keys(data).forEach((x) => {
        str += `${x}=${data[x]}&`;
      });
      data = str.replace(/&$/, "");
    }
    return new Promise((resolve, reject) => {
      let xhr = new XMLHttpRequest();
      xhr.open(method, url);
      xhr.setRequestHeader("Content-Type", type);
      xhr.setRequestHeader("X-ENT", "rdc");
      xhr.setRequestHeader("access-token", cfg.accessToken);
      xhr.send(data || null);
      xhr.onreadystatechange = function () {
        if (xhr.readyState === this.HEADERS_RECEIVED) {
          console.log(`Content-Type: ${xhr.getResponseHeader("Content-Type")}`);
        } else if (xhr.readyState === 4) {
          if (xhr.status === 200) {
            return resolve(xhr.response);
          } else {
            return reject(xhr.response);
          }
        }
      };
    });
  };
  // jquery中使用ajax
  $.ajax({
    type:'POST',
    contentType:'application/json;charset=UTF-8',
    url:'http://127.0.0.1/admin/list',
    data:JSON.stringify(list),
    jsonpCallback: "handleCallback",  // 自定义回调函数名
    success:function(){},
    error:function(){}
  })
  fetch(url).then()：//返回的Promise不会标记为reject，及时返回的404和500，只有网络故障时候回reject，不会从服务端发送和接收任何cookies，必须设置credentials选项
  //axios:axios.get().then()
  ```

### 存储

- cookie：本身用于浏览器和 server 通讯，借用来做本地存储，可用 document.cookie=”a=100”修改，最大限制 4kb，每次都要发送到服务器，增加请求数据量，
- localStorage：永久存储，H5 专门设计的存储，最大可存 5M，API 简单：setItem getItem，不随 http 请求发送
- sessionStorage：只存在当前会话，浏览器关闭就清空

### linux 命令：

- ssh user@192.168.2.97 => 回车输入密码
- ls 查看文件夹
- ls -a 查看所有-all
- ll 车看列表
- clear
- mkdir file
- rm -rf file 删除文件夹
- cd dist
- mv 1.js 2.js 修改文件名
- mv 1.js ../../product/1.js 移动文件
- cp 1.js 2.js 拷贝
- rm a1.js 删除文件 a1.js
- touch a2.js 创建文件
- vi d.js 编辑文件 i 输入 :wq 写入退出 q!强制退出不保存
- vim 有的话直接打开，没有的话创建打开
- cat package.json 查看
- head package.json 查看头几行
- tail package.json 查看末尾几行
- grep 'babel' package.json 查找关键字

### JS 中的垃圾

> - 1、js 内存管理是自动的
> - 2、对象不被引用时就是垃圾
> - 3、对象不能从根上访问到是垃圾（不可达）

> GC：垃圾回收机制的简写，它可以找到内存中的垃圾并释放和回收空间

- GC 算法：

  - 1、引用计数：设置引用数，判断当前引用数是否为 0

    > 引用计数器 ==> 引用关系改变修改引用数字 ==> 引用数字为 0 立即回收

    - 优点：发现垃圾立即回收，最大限度较少程序暂停
    - 缺点：无法回收循环引用对象，时间开销大
      ```js
      function fn() {
        const obj1 = {};
        const obj2 = {};
        boj1.name = obj2;
        obj2.name = obj1;
      }
      fn();
      ```

  - 2、标记清除
    > 分标记和清除两个阶段，遍历所有对象找标记活动对象，遍历所有对象清除没有标记的对象，回收相应空间
    - 优点：解决对象循环引用操作
    - 缺点：空间碎片化，回收的地址不连续
  - 3、标记整理
    > 标记清除的增强，清除阶段先执行整理，移动对象的位置
    - 缺点：不会立即回收垃圾对象
    - V8 引擎：采用及时编译所以快 内存设限：1.5g/800m
  - 分代回收

    > 内存分新生代、老生代，空间复制、标记清除、标记整理、标记增量

    - 新生代（From 使用空间，To 空闲空间）：存活时间较短的对象，小空间存储（32M/16M）
    - 新生代回收采用复制算法 + 标记整理，活动对象在 from 空间，标记整理后将活动对象拷贝到 to 空间，from 与 to 交换空间完成释放。（复制算法：空间换时间），一轮 GC 还存活的新生代需要晋升到老升代空间，to 空间的使用超过 25%也会晋升
    - 老生代：主要使用标记清除进行 GC，当新生代晋升空间不足在进行标记整理，标记增量进行效率优化。（标记增量和程序是交替执行的，执行一部分，标记一部分）

  - Performance 工具监控内存
  - 监控内存方式：
    - 内存泄漏：内存持续升高，没有下降
    - 内存膨胀：多数设备上存在性能问题
    - 频繁垃圾回收：内存变化图分析
    - 浏览器任务管理器：shift+esc
    - Timeline 时序图记录：Performance
    - 堆快照查找分离 DOM：控制台内存，堆快照
    - 判断是否存在频繁的垃圾回收：Timeline 频繁升降，任务管理去数据频繁增加减小

- 代码优化：
  - 1、慎用全局变量：作用域链顶端，一直存在执行栈到结束，局部作用域出现同名污染全局
  - 2、缓存全局变量：获取 dom 存在变量里
  - 3、通过原型新增方法
  - 4、避开闭包陷阱
  - 5、避免属性访问方法的使用
  - 6、for 循环的优化，选择最优的循环方法
  - 7、文档碎片，克隆优化节点
  - 8、直接用字面量替换 new Object 定义

### JS 中捕获异常：

- 1、手动捕获 try{ }catch(err){ }finally{ }
- 2、自动捕获：

```js
window.onerror = function (message, source, lineNum, colNum, error) {
  // 对于跨域的js，如CDN，不会有详细的报错
  // 对于压缩的js，需要配合sourceMap 反查到未压缩的行和列
};
```

### RAF requestAnimationFrame

> 想要动画流畅，更新频率要 60 帧/s，即 16.67ms 更新一次视图，setTimeout 要手动控制频率，而 RAF 浏览器会自动控制，后台标签或隐藏 iframe 中，RAF 会暂停，而 setTimeout 依然执行

```js
// 3s把宽度从100px变为640px，即增加540px
// 60帧/s，3s 180帧，每次变化3px
const $div1 = $("#div1");
let curWidth = "100px";
const maxWidth = "640px";
function animate() {
  curWidth = curWidth + 3;
  $div1.css("width", curWidth);
  if (curWidth < maxWidth) {
    setTimeout(animate, 16.7); //手动控制时间
    //window.requestAnimationFrame(animate) 浏览器自动控制时间
  }
}
animate();
```

### 浏览器缓存：两个地方用到了缓存，DNS 缓存和页面资源缓存

- 1.DNS 缓存比较简单，就是在浏览器本地把对应的 IP 和域名关联起来
  > 浏览器缓存->系统缓存->本地 host 文件（尽量设置为只读）-> DNS 系统调用请求本地域名服务器 localDNS（LDNS）来解析域名（80%都能找到）->直接请求
- 2.浏览器的资源缓存

  - 强缓存
    > 不请求服务器，直接从缓存中读取，chrome=>Network 可以看到该请求返回 200，并且 Size 显示 from disk cache 或 from memory cache。强缓存可以通过设置两种 HTTP Header 实现：Expires 和 Cache-Control
    - Expires：缓存过期时间，指定资源到期的时间，是服务器端的具体的时间点。
      > Expires=max-age + 请求时间，需要和`Last-modified`结合使用。Expires 是 Web 服务器响应消息头字段，在响应 http 请求时告诉浏览器在过期时间前浏览器可以直接从浏览器缓存取数据，而无需再次请求。
      >
      > Expires 是 HTTP/1 的产物，受限于本地时间，如果修改本地时间，可能会造成缓存失效。
    - Cache-Control：是 HTTP/1.1 的产物。比如设置 Cache-Control:max-age=300，5 分钟内再次加载资源，就会命中强缓存。
      > cache-control 的更多选项：
      >
      > no-cache：表示不使用 Cache-Control 的缓存控制方式做前置验证，而是使用 Etag 或者 Last-Modified 字段来控制缓存。设置了 no-cache 之后，并不是说浏览器就不再缓存数据，只是浏览器在使用缓存数据时，需要先确认一下数据是否还跟服务器保持一致。使用的协商缓存
      >
      > no-store：所有内容都不会被缓存，即不使用强制缓存，也不使用协商缓存
    - 区别：Expires 是 http1.0 的产物，Cache-Control 是 http1.1 的产物，两者同时存在的话，Cache-Control 优先级高于 Expires。
    - 强缓存判断是否缓存的依据来自于是否超出某个时间或者某个时间段，而不关心服务器端文件是否已经更新，这可能会导致加载文件不是服务器端最新的内容，此时我们需要用到协商缓存策略。
  - 协商缓存

    > 就是强缓存失效后，浏览器携带缓存标识向服务器发起请求，由服务器根据缓存标识决定是否使用缓存的过程，主要有以下两种情况：
    >
    > 协商缓存生效，返回 304 和 Not Modified
    >
    > 协商缓存失效，返回 200 和请求结果
    >
    > 协商缓存通过设置两种 HTTP header 实现 Last-Modified 和 ETag

    - Last-Modified（服务端返回）和 If-Modified-Since（请求时候带上）
      > 浏览器在第一次访问资源时，服务器返回资源的同时，在 response header 中添加 Last-Modified 的 header，表示资源在服务器上的最后修改时间，浏览器接收后缓存文件和 header； Last-Modified: Sun, 31 May 2020 05:28:04
      >
      > 浏览器下次请求资源，检测到 Last-Modified 这个 header，添加 If-Modified-Since 这个 header，值就是 Last-Modified 中的值；服务器会根据 If-Modified-Since 中的值与服务器中这个资源的最后修改时间对比，如果没有变化，返回 304 和空的响应体，直接从缓存读取，如果 If-Modified-Since 的时间小于服务器中这个资源的最后修改时间，说明文件有更新，于是返回新的资源文件和 200 状态码
      >
      > Last-Modified 弊端：
      >
      > - 1.本地打开缓存文件，即使没有对文件进行修改，也会造成 Last-Modified 被修改，服务端不能命中缓存导致发送相同的资源
      > - 2.Last-Modified 只能以秒计时，如果在 1s 时间内修改完成文件，那么服务端会认为资源还是命中了，不会返回正确的资源
      >
      > 所以在 HTTP / 1.1 出现了 ETag 和 If-None-Match
    - ETag 和 If-None-Match

      > 流程类似上面。Etag 是服务器响应请求时，返回当前资源文件的一个唯一标识(由服务器生成)，只要资源有变化，Etag 就会重新生成。浏览器在下一次加载资源向服务器发送请求时，会将上一次返回的 Etag 值放到 request header 里的 If-None-Match 里，服务器只需要比较客户端传来的 If-None-Match 跟自己服务器上该资源的 ETag 是否一致，就能很好地判断资源相对客户端而言是否被修改过了。如果服务器发现 ETag 匹配不上，那么直接返回 200，将新的资源（当然也包括了新的 ETag）发给客户端；如果 ETag 是一致的，则直接返回 304，客户端直接使用本地缓存即可。

    - 两者对比：Last-Modified 是 HTTP/1.0 的产物，而 Etag 是 HTTP/1.1 的产物在精确度上，Etag 要优于 Last-Modified。在性能上，Etag 要逊于 Last-Modified，毕竟 Last-Modified 只需要记录时间，而 Etag 需要服务器通过算法来计算出一个 hash 值。在优先级上，服务器校验优先考虑 Etag

  - 总结：强制缓存优先于协商缓存，若强制缓存生效则直接使用缓存，若不生效则进行协商缓存，协商缓存由服务器决定是否使用缓存，若协商缓存失效，那么代表该请求的缓存失效，返回 200，重新返回资源和缓存标识，再存入浏览器缓存中；生效则返回 304，继续使用缓存。
  - 用户行为对浏览器缓存的影响：
    - 1.地址栏输入地址： 查找 disk cache 中是否有匹配。有则使用；没有则发送网络请求。
    - 2.普通刷新 (F5)：因为 TAB 并没有关闭，因此 memory cache 是可用的，会被优先使用，其次是 disk cache。
    - 3.强制刷新 (Ctrl + F5)：浏览器不使用缓存，因此发送的请求头部均带有 Cache-control: no-cache(为了兼容，还带了 Pragma: no-cache),服务器直接返回 200 和最新内容。

### babel 编译原理

- babylon 将 ES6/ES7 代码解析成 AST
- babel-traverse 对 AST 进行遍历转译，得到新的 AST
- 新 AST 通过 babel-generator 转换成 ES5

### 函数柯里化

> 在一个函数中，首先填充几个参数，然后再返回一个新的函数的技术，称为函数的柯里化。通常可用于在不侵入函数的前提下，为函数 预置通用参数，供多次重复调用。

```js
const add = function add(x) {
  return function (y) {
    return x + y;
  };
};
const add1 = add(1);
add1(2) === 3;
add1(20) === 21;
```
