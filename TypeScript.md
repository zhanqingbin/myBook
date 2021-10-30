# TypeScript 常见用法详解（Vue3.0 前奏）

@[TOC](种一棵树，最好的时候是十年前，其次是现在。)

## TypeScript 简介

1. TypeScript 是微软开发的一款开源编程语言
2. TypeScript 是 JavaScript 的超级，遵循 Es6、Es5 规范

## TypeScript 使用

- 安装 TypeScript

```
npm install -g typescript
```

- 使用 `tec` 命令编译 `.ts` 扩展名的文件

```
tsc test.ts
```

## TypeScript 中的类型注解

- 为了使编写代码规范，利于维护，增加了类型校验

#### 布尔类型（boolean）

```javascript
let flag: boolean = true;
flag = 123; // 错误
flag = false; // 正确
```

#### 数字类型（number）

```javascript
let num: number = 123;
num = "str"; // 错误
num = 456; // 正确
```

#### 字符串类型(string)

```javascript
let str: string = "this is ts";
str = true; // 错误
str = "haha"; // 正确
```

#### 数组类型（array）

```javascript
let arr: number[] = [11, 22, 33];
let arr2: Array<number> = [11, 22, 33];
let arr3: any[] = ["1314", 22, true];
```

#### 元组类型（tuple）

- 定长的数组，属于数组的一种

```javascript
let arr: [number, string] = [123, "this is ts"]; // 定义数组内部的数据类型
```

#### 枚举类型（enum）

- 处理非数值的数据，尽量用自然语言中含义清楚的单词来表示它的每一个值
  > enum 枚举名{ 标识符[=整型常数], 标识符[=整型常数], ... 标识符[=整型常数], } ;

```javascript
enum Flag {success=1,error=2};
let s:Flag=Flag.success;
console.log(s); // 1
```

#### 任意类型（any）

```javascript
let oDiv: any = document.getElementById("div");
oDiv.style.color = "red";
```

#### null 和 undefined

- 定义没有赋值就是 undefined

```javascript
let num: number | undefined;
let num: number | null | undefined; // 一个元素可能是 number类型 可能是null 可能是undefined
```

#### void 类型

- 表示没有任何类型，一般用于定义方法的时候方法没有返回值。

```javascript
function run(): void {
  console.log("run");
}
run();
```

#### never 类型

- 包括 null 和 undefined 的子类型，代表从不会出现的值。

```javascript
let a:undefined; a=undefined; // a只能等于undefined
let b:null;	b=null; // b只能等于null
/ /声明never的变量只能被never类型所赋值
function throwError(msg:string):never{  throw new Error(msg);}
```

## TypeScript 中的函数

```javascript
function run(): string {
  return "run";
}
// ts中定义方法传参
function getInfo(name: string, age: number): string {
  return `${name} --- ${age}`;
}
// 没有返回值的方法,使用void类型
function run(): void {
  console.log("run");
}
// 可选参数加?
// 注意:可选参数必须配置到参数的最后面
function getInfo(name: string, age?: number): string {
  return `${name} --- ${age}`;
}
// 默认参数
function getInfo(name: string, age: number = 20): string {
  return `${name} --- ${age}`;
}
// 剩余参数：三点运算符 接受新参传过来的值
function sum(a: number, b: number, ...result: number[]): number {}
sum(1, 2, 3, 4, 5, 6);
// ts函数重载
// java中方法的重载：函数名相同，参数不同。
// typescript中的重载：通过为同一个函数提供多个函数类型定义来试下多种功能的目的。
// es5中出现同名方法，下面的会替换上面的方法
function getInfo(str: any): any {
  return typeof str === "string" ? "我叫：" + str : "我的年龄是" + str;
}
```

## TypeScript 中的类

> 类：图纸（Array、汽车设计图、模子）
> 实例：产品 （arr、汽车、盆）

#### ts 中类的定义 class

```javascript
class Person {
  name: string; // 属性 前面省略了public关键词
  constructor(n: string) {
    //构造器 实例化类的时候触发的方法
    this.name = n;
  }
  run(): void {
    //方法 不返回值
    alert(this.name);
  }
}
let p = new Person("张三");
p.run();
```

#### ts 中实现继承 extends

```javascript
class Web extends Person {
  age: number;
  constructor(name: string, age: number) {
    super(name); // 2、初始化父类的构造函数，把李四传到父类的方法中执行
    this.age = age;
  }
}
let w = new Web("李四"); // 1、把参数李四传入到Web的constructor中
```

`父类的方法和子类的方法一致，先执行子类的方法，子类重写父类。`

#### ts 三种修饰符

- public：公有 $\color{green}{当前类}$、$\color{green}{子类}$、$\color{green}{类外部}$ 能用
- protected：保护类型 $\color{green}{当前类}$、$\color{green}{子类}$ 能用、$\color{red}{类外部}$ 不能用
- private ：私有 $\color{green}{当前类}$ 能用、$\color{red}{子类}$、$\color{red}{类外部}$ 不能用
- readonly ：只读 1.初始赋值 2.构造器中初始化修改 其他地方不能改
- static ：静态成员 不需要实例化就能用 如：Array.from(), Object.keys(), Math.max()

#### ts 访问器 get set

```javascript
class Person{
  private _a=0;
  public get a(){return this._a;}
  public set a(val){
    if(val>0){this._a=val;}else{throw new Error('a must be > 0');}
  }
}
let p=new Person(); console.log(p.a);
```

#### ts 多态

> 多态：父类定义一个方法不去实现，让继承它的子类去实现 每一个子类有不同的表现

```javascript
//多态属于继承,代码在运行时具体决定哪个函数
class Animal {
  eat() {
    console.log("吃的方法");
  }
}
class Dog extends Animal {
  eat() {
    return this.name + "吃粮食";
  }
}
class Cat extends Animal {
  eat() {
    return this.name + "吃猫粮";
  }
}
```

#### ts 中的抽象类

> - 提供其他类继承的基类，不能直接被实例化。就是一个标准，用`abstract`关键字定义抽象类和抽象方法，抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。abstract 抽象方法只能放在抽象类里面
> - 抽象类和抽象方法用来定义标准

```javascript
abstract class Animal{
	abstract eat():any;  //抽象方法不包含具体实现必须在派生类中实现。
}
// let a=new Animal() 错误的写法，抽象类不能够被实例
class Dog extends Animal{ // 抽象类的子类必须实现抽象类里面的抽象方法
	eat(){console.log(this.name+'吃粮食')}
}
class Cat extends Animal{// 抽象类的子类必须实现抽象类里面的抽象方法
    eat(){console.log(this.name+'吃老鼠')}
}
```

## TypeScript 中的接口

> - 在面向对象的编程中，接口是一种规范的定义，它定义了行为和动作的规范。不关心这些类的内部状态数据，也不关心这些类里方法的实现细节，只规定这批类里必须提供某些方法，提供这些方法的类就可以满足实际需要。 typescrip 中的接口类似于 java，同时还增加了更灵活的接口类型，包括属性、函数、可索引和类等。
> - 接口：行为和动作的规范，对批量方法进行约束

#### 对象接口

```javascript
interface FullName {
  firstName: string; // 注意;结束
  secondName: string;
}
function printName(name: FullName) {
  // 必须传入对象 firstName secondName
  console.log(name.firstName + "--" + name.secondName);
}
// 参数的顺序可以不一样，传入的参数必须包含 firstName secondName
let obj = { age: 20, firstName: "张", secondName: "三" };
printName(obj);
// 可选属性?
interface FullName {
  firstName: string;
  secondName?: string;
}
```

#### 函数类型接口

```javascript
interface encrypt {
  (key: string, value: string): string;
}
let md5: encrypt = function (key: string, value: string): string {
  return key + value;
};
console.log(md5("name", "zhangsan"));
// 可索引接口 对数组的约束
interface UserArr {
  [index: number]: string;
}
let arr: UserArr = ["aaa", "bbb"]; // √
let arr: UserArr = [123, "bbb"]; // 错误
// 可索引接口 对对象的约束
interface UserObj {
  [index: string]: string;
}
let arr: UserObj = { name: "张三" };
```

#### 接口的继承 implements

```javascript
interface Animal{   //类接口有属性和方法
    name:string;
    eat(str:string):void;
}
class Dog implements Animal{ // 类集成接口使用implements （实现）
    name:string;
    constructor(name:string){this.name=name;}
    eat(){console.log(this.name+'吃粮食')}
}

// 构造器的interface
interface BoxConstructorInterface{new (a:string);} //构造器接口
interface BoxInterface{show(a:number):void;} //类的接口
const Box:BoxConstructorInterface=class implements BoxInterface {
  private a:string;
  constructor(a:string){this.a=a;}
  show(a:number){alert(this.a+a);}
}

// 接口继承接口：
interface Animal{eat():void;}
interface Person extends Animal{work():void;}
class Web implements Person{
  public name:string;
  constructor(name:string){this.name=name;}
  eat(){console.log(this.name+'喜欢吃馒头')}
  work(){console.log(this.name+'写代码');}
}

// 接口继承类：使用这个接口也必须先继承这个类
class Box {private width:number;}
interface BoxInter extends Box{user:string;}
class A extends Box implements BoxInter{user:string;}
```

## TypeScript 中的泛型

> - 考虑可重用性，不仅能够支持当前的数据类型，也能支持未来的数据类型。
> - 可以支持不特定的数据类型，把类型作为一个特殊的参数传进去
> - T 表示泛型，具体什么类型是调用这个方法的时候决定的

```javascript
function getData<T>(value:T):T{return value;}
getData<number>(123);
getData<string>('1314');
getData<number>('1212'); // 错误
function getData<T>(value:T):any{return '2145214214';}
getData<number>(123); // 参数必须是number

// 类的泛型
class MinClas<T>{
    public list:T[]=[];
    add(value:T):void{
        this.list.push(value);
    }
    min():T{
        let minNum=this.list[0];
        for(let i=0;i<this.list.length;i++){
            if(minNum>this.list[i]){
                minNum=this.list[i];
            }
        }
        return minNum;
    }
}
let m1=new MinClas<number>(); //实例化类 并且制定了类的T代表的类型是number
m1.add(11);  m1.add(3);  m1.add(2);	alert(m1.min())
let m2=new MinClas<string>(); //实例化类 并且制定了类的T代表的类型是string
m2.add('c');  m2.add('a');  m2.add('v'); alert(m2.min())

// 1、泛型接口
interface ConfigFn{<T>(value:T):T;}
let getData:ConfigFn=function<T>(value:T):T{
  return value;
}
// 2、泛型接口
interface ConfigFn<T>{(value:T):T;}
function getData<T>(value:T):T{	return value;}
let myGetData:ConfigFn<string>=getData;
myGetData('20');  /*正确*/   myGetData(20)  //错误
// 3、泛型的extends -- 限定范围
class Box<T extends Date>{
  private a:T;
  constructor(a:T){this.a=a;}
  show(){console.log(this.a);}}
let b=new Box(new Date());
```

## 总结

> - 定义类型由两种方式：`接口（interface）` 和类型别名 `（type alias）`
>   interface 只能定义对象类型，type 声明的方式可以定义组合类型，交叉类型和原始类型
>   1、interface 方式可以实现接口的 extends/implements，而 type 不行
>   2、interface 可以实现接口的 merge，但是 type 不行
>   1.type 适合：变量、参数、函数。声明类型 变量、函数 √ class ×
>   type Fn=(a:number,b:number)=>number;
>   2.interface：适合细节：class、constructor
>   3.abstract：适合主干，定义标准：一对一，层级 4.泛型：比较灵活
