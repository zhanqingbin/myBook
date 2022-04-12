# JS 数据类型

### JS 中内存的分类

- 栈内存：用来提供一个供 JS 代码执行的环境->作用域(全局作用域/私有作用域)，基本数据类型都存在栈内存里
- 堆内存：用来存储引用数据类型的值->对象存储的是属性名和属性值，函数存储的是代码字符串...

### JS 数据类型

- 基本数据类型：String，Number，Boolean，Null，Undefined，Symbol。
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

### 类型转换（强制类型转化、隐式类型转化）

- 强制类型转化：`parseInt，parseFloat，toString，Boolean()，Number()，!!`
- 隐式类型转化：
  - 函数类：`isNaN()` 会对参数进行隐式的 Number()转换，如果转换不成功，则会返回 true
  - 条件类：if 隐式的调用 Boolean()函数
  - 逻辑运算：
    - 1： `- \* / %` 会隐式的调用 Number()，如果转换不成功，整个表达式返回 NaN
    - 2： `+` 数字会相加，字符串会隐式的调用 toSting()方法，然后`拼接字符串`
    - 3：如果都是 boolean 相加，进行 Number()转换，false 为 0 true 为 1，再相加`console.log(false+true) // 1`。
    - 4：==

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

### !和!!的区别：

- ！将变量转换成 boolean 类型，除了 null、undefined 和""空字符串取反都为 true，其余都为 false`!null=true / !undefined=true / !''=true / ![]=false / !11=false`。
- ！！是将表达式强制转化为 bool 值的运算，运算结果为 true 或 false

  ```js
  let a
  // a有内容才执行的代码
  if(a!=null&&typeof(a)!=undefined&&a!=''){}
  // 可以写成
  if(!!a){}
  // 因为
  !!(NaN || undefined || null || 0 || ' ')=false;
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
