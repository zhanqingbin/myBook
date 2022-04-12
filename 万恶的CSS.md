# 万恶的 CSS（一）

@[TOC](阻碍我的，都将毁灭。)

## CSS（Cascading Style Sheet 层叠样式表）

> 是一种用来表现 HTML（标准通用标记语言的一个应用）或 XML（标准通用标记语言的一个子集）等文件样式的计算机语言

## CSS 选择器

- 选择器：用于匹配 HTML 元素，浏览器解析选择器是从右向左的，然后验证上一级，为了加快效率
- 伪元素选择器：::before 不出现在 html 和 dom 树中，但是是真是存在的元素，可以显示内容设置样式。单冒号兼容老旧浏览器的写法
- 属性选择器：input[type=radio]{}
- 伪类选择器：:hover{} 是元素的一个状态，元素是存在页面中可以显示的元素
- 组合选择器：[type=checkbox]+label{}
- 否定选择器：not(.div){}
- 选择器权重：id：100，类 属性 伪类 10，元素 为元素 1，其他 0 （不能进位）
- !important 优先级最高 元素属性（行内样式）优先级高 权重相同后写生效
- 多平台字体的适配：fallback 机制：找不到设置的往下找，屏显效果好的字体放前面

```css
font-family: "PingFang SC",   "Microsoft Yahei",  monospace;
```

- iconfont 的原理：使用 @font-face 定义一个字体 family，每一个字都有对应的 unicode。比如我们在 web 上输入我跟输入&#x6211;是一样的。浏览器会自动帮你找到对应的图形去渲染。

```css
@font-face  {
  font-family: "IF";
  src: url("./IndieFlower.ttf");
}
.custom-font {
  font-family: IF;
}
```

- line-height 垂直居中
- vertical-align 调整跟基线对齐的位置
  > 图片的 3px 问题：原因图片是 inline 对齐元素根据文字的基线 baseline 对齐，可以用
  > vertical-align:bottom 或者 display:block

```css
background: url(./test_bg.png)  no-repeat; // 背景图
background-position: -17px -5px; // 雪碧图
background-size: 261px 113px; // 移动的适配  三倍屏做一个三倍尺寸的图/3
background-size: cover; // 把背景图像扩展至足够大，以使背景图像完全覆盖背景区域，某些部分也许无法显示在背景定位区域中。
background-size: contain; // 把图像图像扩展至最大尺寸，最大边为主
// 边框设置背景图
border: 30px solid transparent;
border-image: url(./border.png)  30 round;
// 边框做三角形：原理是边框连接处使用斜切的方式
border-bottom: 30px solid red;
border-left: 30px solid transparent;
border-right: 30px solid transparent;
```

- base64:图片体积变大三分之一，传输性能有优势减少 http 请求，适用小图标
- 边框：solid 实线 dashed 虚线 dotted 点
- 文字换行：

```css
overflow-wrap: normal; // 单词保存完整，超长的不换行，break-word长单词折行
word-break: normal; // break-all打断所有的，不把单词当成整体，keep-all所有单词一个整体
white-space: normal; // nowrap不换行
```

- **装饰性属性**：粗体：font-weight 斜体：font-style:itatic 下划线：text-decoration 指针 cursor
- **CSS Hack**：CSS Hack 大致有 3 种表现形式，CSS 属性前缀法、选择器前缀法以及 IE 条件注释法（即 HTML 头部引用 if IE），主要用于区分不同浏览器。难理解难维护易失效 \9
  代替方案：特性检测，针对性加 class
  > - 属性前缀法(即类内部 Hack)：例如 IE6 能识别下划线"_"和星号" * "，IE7 能识别星号" * "，但不能识别下划线"_"，IE6~IE10 都认识"\9"，但 firefox 前述三个都不能认识。
  > - 选择器前缀法(即选择器 Hack)：例如 IE6 能识别*html .class{}，IE7 能识别*+html .class{}或者\*:first-child+html .class{}。
  > - IE 条件注释法(即 HTML 条件注释 Hack)：针对所有 IE(注：IE10+已经不再支持条件注释)：

```css
<!--[if IE]>IE浏览器显示的内容 <![endif]-->，针对IE6及以下版本： <!--[if lt IE 6]>只在IE6-显示的内容 <![endif]-->。这类Hack不仅对CSS生效，对写在判断语句里面的所有代码都会生效。
“-″减号是IE6专有的hack
“\9″ IE6/IE7/IE8/IE9/IE10都生效
“\0″ IE8/IE9/IE10都生效，是IE8/9/10的hack
“\9\0″ 只对IE9/IE10生效，是IE9/10的hack
```

- **美化 checkbox：** label[for]和 id 隐藏原生 input :checked+label

```css
.checkbox input{display: none;}
.checkbox input + label{background:url(./checkbox1.png) left center no-repeat;background-size:20px 20px;padding-left:20px; }
.checkbox input:checked + label{background-image:url(./checkbox2.png);}
```

## CSS 布局

> 早期 table 为主，后来以技巧布局为主，现在 flexbox/grid，响应式

### Flex 布局

> Flex 是 Flexible Box 的缩写，意为"弹性布局",任何一个容器都可以指定为 Flex 布局，行内元素也可以使用 Flex 布局，设为 Flex 布局以后，子元素的 float、clear 和 vertical-align 属性将失效
>
> 采用 Flex 布局的元素，称为容器（flex container）。它的所有子元素为容器成员，称为项目（flex item）。

- 容器的属性
  > - flex-direction:决定主轴的方向 row | row-reverse | column | column-reverse
  > - flex-wrap:如果一条轴线排不下，如何换行 nowrap | wrap | wrap-reverse
  > - flex-flow:flex-direction 属性和 flex-wrap 属性的简写形式，默认值为 row nowrap
  > - justify-content:定义了项目在主轴上的对齐方式 flex-start | flex-end | center | space-between | space-around
  > - align-items:定义项目在交叉轴上如何对齐 flex-start | flex-end | center | baseline(项目的第一行文字的基线对齐) | stretch(如果项目未设置高度或设为 auto，将占满整个容器的高度)
  > - align-content:定义了多根轴线的对齐方式 flex-start | flex-end | center | space-between | space-around | stretch
- 项目的属性
  > - order:定义项目的排列顺序。数值越小，排列越靠前，默认为 0
  > - flex-grow:定义项目的放大比例，默认为 0，即如果存在剩余空间，也不放大
  >   
  >   > 如果所有项目的 flex-grow 属性都为 1，则它们将等分剩余空间。如果一个项目的 flex-grow 属性为 2，其他项目都为 1，则前者占据的剩余空间将比其他项多一倍。
  > - flex-shrink:定义了项目的缩小比例，默认为 1，即如果空间不足，该项目将缩小。
  >   
  >   > 如果所有项目的 flex-shrink 属性都为 1，当空间不足时，都将等比例缩小。如果一个项目的 flex-shrink 属性为 0，其他项目都为 1，则空间不足时，前者不缩小。
  > - flex-basis:定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为 auto，即项目的本来大小,它可以设为跟 width 或 height 属性一样的值（比如 350px），则项目将占据固定空间
  > - flex:是 flex-grow, flex-shrink 和 flex-basis 的简写，默认值为 0 1 auto
  > - align-self:允许单个项目有与其他项目不一样的对齐方式，可覆盖 align-items 属性。默认值为 auto，表示继承父元素的 align-items 属性，如果没有父元素，则等同于 stretch。 auto | flex-start | flex-end | center | baseline | stretch

### float 布局：脱离文档流，但不脱离文本流，形成块（BFC）

- BFC(Block fomatting context) ：块级格式化上下文
  
  > 是一个独立的渲染区域，让处于 BFC 内部的元素与外部的元素相互隔离，使内外元素的定位不会相互影响。

> **触发条件:**
>
> - IE 下为 Layout，可通过 zoom:1 触发
> - position: absolute/fixed 值不是 static 或者 relative
> - display 的值是 inline-block、table-cell、flex、table-caption 或者 inline-flex
> - float 的值不是 none
> - overflow 的值不是 visible
>
> **规则:**
>
> - 属于同一个 BFC 的两个相邻 Box 垂直排列
> - 属于同一个 BFC 的两个相邻 Box 的 margin 会发生重叠
> - BFC 中子元素的 margin box 的左边， 与包含块 (BFC) border box 的左边相接触 (子元素 absolute 除外)
> - BFC 的区域不会与 float 的元素区域重叠
> - 计算 BFC 的高度时，浮动子元素也参与计算
> - 文字层不会被浮动层覆盖，环绕于周围
>
> **应用:**
>
> - 阻止 margin 重叠:属于同一个 BFC 的两个相邻的 Box 会发生 margin 重叠，所以我们可以设置，两个不同的 BFC，也就是我们可以让把第二个 p 用 div 包起来，然后激活它使其成为一个 BFC
> - 自适应两栏布局
>   可以阻止元素被浮动元素覆
> - 可以包含浮动元素 —— 清除内部浮动(清除浮动的原理是两个 div 都位于同一个 BFC 区域之中)

```
// 防止父级高度塌陷：overflow/设置宽高/清除浮动
.container2::after{content: 'aaa'; clear:both;display: block;visibility: hidden;height:0;}
// 左中右布局：
.left{/* float:left; *//* height:100%; */width:200px;position: absolute;height:200px;}
.right{background:blue;float:right; width:200px;height:100%;}
.middle{ margin-left:200px;margin-right:200px;}
```

> - inlineblock 布局（使用定宽）：标签间空白间隙，可以父级字体设 0，左右把字体设置回来
> - 实现两栏三栏式布局：1、表格布局 2、float+margin 3、inline-block 4、flexbox

### 定位 position

> - static 默认值。元素出现在正常的普通流中
> - relative 生成相对定位的元素，相对于其在普通流中的位置进行偏移。这个属性是没有脱离文档流的，所以元素本身所占的位置会保留。
> - fixed （老 IE 不支持）生成绝对定位的元素，通常相对于浏览器窗口或 iframe 进行定位。
> - inherit 继承父元素的 position 属性，但需要注意的是 IE8 以及往前的版本都不支持 inherit 属性。
> - absolute 生成绝对定位的元素， 相对于最近一级的 不是 static 的父元素来进行定位,如果没有找到的话，最终是根据 body 进行定位。
> - absolute 定位的基准是相对于最近一级的不是默认值 static 的父元素(可以是 absolute/relative/fixed 等)来进行定位，fixed 相对 viewport

### 效果属性：

```css
box-shadow: 5px 5px 10px 0 rgba(0,0,0,.2);//x偏移，y偏移，模糊区域，扩展值，rgba颜色
text-shadow: 0 0 1px rgba(128,128,128,.2);//x偏移，y偏移，模糊区域，rgba颜色
border-radius: 50%;
background:url(./panda.jpg);
background-size: contain;//全部显示缺的地方空白，cover覆盖容器
background-repeat: no-repeat;
background-position: center center;
/* clip-path: inset(100px 50px); */ 对容器进行裁剪，几何图形，自定义路径
/* clip-path: circle(50px at 100px 100px); */
/* clip-path: polygon(50% 0%, 100% 50%, 50% 100%, 0% 50%, 10% 10%, 40px 10px); */
clip-path: url(#clipPath);
/* background-size: cover; */
transition:clip-path .4s;
// 不占空间的边框：box-shadow，outline
```

### transform：3D 变幻

```css
// 实现3D效果
perspective: 500px; //透视的角度
transform-style: preserve-3d;
transform: translateZ(-100px);
transition: transform .4s;

transform: translateZ(-100px)  rotateX(90deg)  rotateY(90deg);
// translate(x,y) 位移
// scale(x,y) 缩放
// rotate(angle) 旋转
// skew(x-angle,y-angle) 倾斜
// perspective(n) 为 3D 转换元素定义透视视图
```

## CSS 动画 （原理：视觉暂留作用，画面逐渐变化）

> - 1、transition（过度）补间动画，有开头结尾，中间是补出来的，从一个图形变另一个图形
> - 2、keyframe 关键帧动画，也是补间动画，但可以设置很多关键帧
> - 3、逐帧动画：画面间没法计算，直接跳到下一帧,任然使用关键帧动画，但是把关键帧的补间去掉 animation-timing-function: steps(1);@keyframes run{0%{background-position}12.5%{ ...}
> - html5 动画，js 动画，CSS3 动画,通常采用的时间间隔就是 1/60,也就是 16.7ms。

```
transition: all 1s;//需要元素状态变化 如hover
transition-timing-function: ease-in-out/linear;
transition-timing-function: cubic-bezier(0.465, -0.460, 0.525, 1.435);//贝塞尔曲线
transition-delay: 1s;
animation: run 1s linear;//不需要元素状态变化，进度可控
animation-direction: reverse;//动画过程反着来
animation-fill-mode: forwards;//停止在终点，backwards停在开始的状态
animation-iteration-count: infinite;
animation-play-state: paused;
@keyframes run{  0%{width: 100px;}  50%{width: 800px;} 100%{width: 100px;}  }
```

> - **CSS3 动画性能：** 性能不坏，部分情况下优于 JS，但 JS 可以做的更好，慎用高危属性 box-shadow

## CSS 预处理器

> 基于 CSS 的另一种语言，通过工具编译成 CSS，添加了 CSS 不具备的特性，提升了 CSS 文件的组织
>
> > 嵌套、变量和计算、Extend 和 Mixin 代码重用、循环 Less（node.js） Sass（Ruby 有 node）
>
> > import CSS 模块化：在浏览器动态引用样式表，性能不好，不会直接用。可以在预处理使用，less/sass 中会将@import 的文件编译成一个文件减少 http 请求数

```css
// 嵌套：&就是当前元素
.content{
  font-size: 14px;
  &:hover{
    background:red;
  }
}

// 变量：可计算less@,sass$
@fontSize: 12px;
@bgColor: red;
.content{
  font-size: @fontSize + 2px;
}

// mixin代码复用像函数可以传值
.block(@fontSize){
  font-size: @fontSize; 
  border: 1px solid #ccc;
}
.nav{.block(12px);}//less中直接不需声明直接调用

// sass中minin
@mixin block($fontSize){...}
@include block($fontSize);
.nav:extend(.block){ color: #333;};//把公共的class抽离出来，编译后体积小，里面覆盖样式

// sass中extend
.nav{
  @extend .block;
  color: #333;
}

// less中的循环
.gen-col(@n) when (@n > 0){
    .gen-col(@n - 1);
    .col-@{n}{width: 1000px/12*@n;}

.gen-col(12);

// sass中的判断if+递归
@mixin gen-col($n){
  @if $n > 0 {
    @include gen-col($n - 1);
    .col-#{$n}{width: 1000px/12*$n;
  }
}
@include gen-col(12);

// sass中的for循环
@for $i from 1 through 12 {
  .col-#{$i} {
    width: 1000px/12*$i;
  }
}
```

### Bootstrap 的响应式原理

> 通过 media query 设置不同分辨率的 class

### Vue style 中的 scoped 实现原理：

> - 1、通过添加随机属性控制作用域范围
>   `<div data-dsfsfs> .dic[data-dsfsfs]{}`
> - 2、添加随机选择器`class='dfghjkkjhgfd' .dfghjkkjhgfd{}`

## 移动端适配

> 所谓移动端适配，就是 WebApp 在不同尺寸的屏幕上等比显示
>
> viewport、rem、@media query 设计上：隐藏 折行 自适应

- **viewport 适配**
  
  > 原理：通过设置 initial-scale , 将所有设备布局视口的宽度调整为设计图的宽度

```
//获取meta节点
const metaNode = document.querySelector('meta[name=viewport]');
//定义设计稿宽度为375
const designWidth = 375;
//计算当前屏幕的宽度与设计稿比例
const scale = document.documentElement.clientWidth/designWidth;
//通过设置meta元素中content的initial-scale值达到移动端适配
meta.content="initial-scale="+scale+",minimum-scale="+scale+",maximum-scale="+scale+",user-scalable=no";
```

- **借助 media 实现 rem 适配**
  > - rem：CSS 的长度单位， 根元素字体大小的倍数，只有根元素字体大小有关
  > - 长度单位都是用 rem 设置
  > - 当屏幕尺寸改变时，只需要修改 html 元素的 font-size 即可实现等比适配

```
// 对屏幕大小划分了html不同的font-size
@media screen and (min-width: 320px) {html{font-size:50px;}}
@media screen and (min-width: 480px) {html{font-size:75px;}}
@media screen and (min-width: 720px) {html{font-size:112.5px;}}
@media screen and (min-width: 960px) {html{font-size:150px;}}
```

- **JS 配合修改配合 rem 适配**

```
// 设计稿宽度
const designWidth = 375;
// 在屏幕宽度375px，的时候，设置根元素字体大小 100px
const remPx = 100;
//计算当前屏幕的宽度与设计稿比例
const scale = window.innerWidth / designWidth
// 根据屏幕宽度 动态计算根元素的 字体大小
document.documentElement.style.fontSize = scale*remPx + 'px';
```

`<meta name="viewport" content="width=device-width, initial-scale=1.0">`

> 设备像素比 = 设备像素 / css 像素（垂直方向或水平方向）。可以通过 JS 来获取：window.devicePixelRatio

### 1px 问题

这里的 1 像素指的是 CSS 像素，在写 1px 的时候，dpr 为 2 时，映射的像素为 2px；dpr3 时就映射成 3px。这样的话，1px 渲染出来就样子就会有些粗。

```
利用媒体查询 + css3的transform
利用媒体查询区分二倍屏和三倍屏，利用transform:scale(xx)对高度进行缩放。

/*二倍屏*/
@media only screen and (-webkit-min-device-pixel-ratio: 2.0) {
    .my-border::after {
        transform: scaleY(0.5);
        webkit-transform: scaleY(0.5);
    }
}
/*三倍屏*/
@media only screen and (-webkit-min-device-pixel-ratio: 3.0) {
    .my-border::after {
        transform: scaleY(0.33);
        webkit-transform: scaleY(0.33);
    }
}

```

### 1、盒子模型：margin border padding content

- 标准 w3c 盒子模型 box-sizing: content-box：width = content
- IE 盒子模型 box-sizing: border-box：宽度包括 border 和 padding，width = ct+padding+border

### 2、css3 新特性

- word-wrap 文字换行 word-wrap:break-word;长单词换行到下一行
- text-overflow 超过指定容器的边界时如何显示 text-overflow:ellipsis;/ɪˈlɪpsɪs/省略
- text-decoration 文字渲染 text-decoration:none/overline/underline/blink 闪烁/line-through 穿过
- text-shadow 文字阴影 text-shadow: 5px 5px 5px #FF0000;*水平阴影，*垂直，模糊距离，颜色
- gradient 渐变效果 linear-gradient(to left top, blue, red);
- transition 过渡效果
- transition-duration：过渡的持续时间
- transform 拉伸，压缩，旋转，偏移等变换
- animation 动画

### 3、div 水平居中

- 行内元素：text-align: center;
- 块级元素：margin: 0 auto;
- flex 布局：display: flex; justify-content: center;
- 绝对定位定宽：position: absolute; width: 宽度; left: 50%; margin-left: -0.5\*宽度
- 绝对定位不定宽：position: absolute; left: 50%; transform: translate(-50%, 0);

### 4、div 垂直居中：

- 行内元素：height: 高度;line-height: 高度;
- 块级元素：vertical-align: middle;
- flex 布局：display: flex; align-items: center;
- 绝对定位定宽：position: absolute; top: 50%; height: 高度; margin-top: -0.5 高度;
- 绝对定位不定宽：position: absolute; top: 50%; transform: translate( 0, -50%);

### 5、定位

- position：static 默认值，没有定位，元素出现在正常的流中；
- position：fixed  固定定位 是相对于浏览器窗口来进行定位；
- position：relative 相对定位   相对于本身来进行定位，它原本所占的空间仍保留，不脱离文档流
- position：absolute 绝对定位 相对于不是 static 的第一个父元素进行定位
- absolute 的”根元素“是可以设置的，相对于最近的已定位的祖先元素，如果无已定位祖先元素, 以 body 元素为偏移参照基准，fixed 的“根元素”固定为浏览器窗口

### 6、让元素消失

- display: none; **不占据空间，不响应事件**，所以动态改变此属性时**会引起重排**。
- visibility: hidden;
  > - 元素会被**隐藏**，但是**不会消失**，依然**占据空间**
  > - **会被子类继承**，子类也可以通过显示的设置 visibility: visible;来反隐藏
  > - 不会触发该元素已经绑定的事件，动态修改此属性会引起重绘
  > - transition 无效。(会延迟 n 秒后 hidden，但不会渐变)
- opacity:0
  > - 只是改变透明度为 1,依然占据空间
  > - 会被子元素继承,且子元素无法反隐藏
  > - 依然能触发已经绑定的事件
  > - transition 有效
- rgba
  > - background：rgba(R, G, B, 0)，只是背景颜色透明，元素透明，依然占据空间
  > - background：rgba(R, G, B, 0)不会被子元素继承
  > - 依然能触发已经绑定的事件
  > - transition 有效
- z-index: -1
  > - 在元素当前 dom 脱离文档流(position:absolute)的前提下，设置 z-index 才起作用
  > - 设置 z-index：-1 本质是改变当前 dom 的层叠上下文，使器置于其他元素之下，达到被隐藏的目的
  > - 部分重排，不影响其他图层布局
  > - 被其他元素遮挡部分，无法响应事件，即使上层元素设置了 pointer-events:none;也无法点击到(注：这个属性会被继承的)

### 7、清除浮动：主要是为了解决，父元素因为子级元素浮动引起的内部高度为 0 的问题

- 在浮动元素后面添加  clear:both  的空 div 元素
- 给父元素添加  overflow:hidden  或者 auto 样式，触发 BFC
- 使用伪元素，也是在元素末尾添加一个点并带有 clear: both 属性的元素实现的。

```css
.clearfix:after {
  content: "";
  height: 0;
  clear: both;
  display: block;
  visibility: hidden;
}
.clearfix {
  *zoom: 1; /*ie6清除浮动的方式 *号只有IE6-IE7执行，其他浏览器不执行*/
}
```

### 8、calc 函数

- calc 函数是 css3 新增的功能，可以使用 calc()计算 border、margin、pading、font-size 和 width 等属性设置动态值。
- width: calc( 100% / (100px \* 2) );
- 需要注意的是，运算符前后都需要保留一个空格，例如：width: calc(100% - 10px);
- calc()函数支持 "+", "-", "\*", "/" 运算;
- 对于不支持 calc() 的浏览器，整个属性值表达式将被忽略。

### 9、圣环布局

> 为了中间 div 内容不被遮挡，将中间 div 设置了左右 padding-left 和 padding-right 后，将左右两个 div 用相对布局 position: relative 并分别配合 right 和 left 属性，以便左右两栏 div 移动后不遮挡中间 div。

```html
<div id="container">
  <div id="center" class="column">center</div>
  <div id="left" class="column">left</div>
  <div id="right" class="column">right</div>
</div>
```

```css
#container {
  padding-left: 200px;
  padding-right: 150px;
}
#container .column {
  float: left;
}
#center {
  width: 100%;
}
#left {
  width: 200px;
  margin-left: -100%;
  position: relative;
  right: 200px;
}
#right {
  width: 150px;
  margin-right: -150px;
}
```

### 10、双飞翼布局

> 为了中间 div 内容不被遮挡，直接在中间 div 内部创建子 div 用于放置内容，在该子 div 里用 margin-left 和 margin-right 为左右两栏 div 留出位置。

```html
<div id="container" class="column">
  <div id="center">center</div>
</div>
<div id="left" class="column">left</div>
<div id="right" class="column">right</div>
```

```css
#container {
  width: 100%;
}
.column {
  float: left;
}
#center {
  margin-left: 200px;
  margin-right: 150px;
}
#left {
  width: 200px;
  margin-left: -100%;
}
#right {
  width: 150px;
  margin-left: -150px;
}
```

### 11、伪类和伪元素

> - 伪类存在的意义是为了通过选择器找到那些不存在 DOM 树中的信息以及不能被常规 CSS 选择器获取到的信息。比如 a 标签的`:focus,:hover,:link,visited` 等。要获取第一个子元素，我们无法用常规的 CSS 选择器获取，但可以通过 :first-child 来获取到。
> - 伪元素用于创建一些不在文档树中的元素，并为其添加样式。比如说，我们可以通过:before 来在一个元素前增加一些文本，并为这些文本添加样式。虽然用户可以看到这些文本，但是这些文本实际上不在文档树中。常见的伪元素有：`::before，::after，::first-line，::first-letter，::selection、::placeholder` 等。
> - 伪类与伪元素的区别在于：有没有创建一个文档树之外的元素。
>   CSS2 中的伪元素使用 1 个冒号，在 CSS3 中，为了区分伪类和伪元素，规定伪元素使用 2 个冒号。

### 12、CSS 画圆半圆扇形三角梯形

```css
div {
  margin: 50px;
  width: 100px;
  height: 100px;
  background: red;
}
/* 半圆 */
.half-circle {
  height: 50px;
  border-radius: 50px 50px 0 0;
}
/* 扇形 */
.sector {
  border-radius: 100px 0 0;
}
/* 三角 */
.triangle {
  width: 0px;
  height: 0px;
  background: none;
  border: 50px solid red;
  border-color: red transparent transparent transparent;
}
/* 梯形 */
.ladder {
  width: 50px;
  height: 0px;
  background: none;
  border: 50px solid red;
  border-color: red transparent transparent transparent;
}
```

### 13、这些浏览器的内核分别是什么?

- IE:  Trident 内核
- Firefox：Gecko 内核
- Safari: webkit 内核
- Opera: Presto 内核，13 年之后，Opera 宣布加入谷歌阵营，弃用了 Presto。
- Chrome: Blink(基于 webkit，Google 与 Opera Software 共同开发)

### 14、简述一下 src 与 href 的区别。

- src:替换当前元素
  
  > src 是 source 的缩写，指向外部资源的位置，指向的内容会嵌入到文档中当前标签所在位置；在请求 src 资源时会将其指向的资源下载并应用到文档内，例如 js 脚本，img 图片和 frame 等元素。当浏览器解析到该元素时，会`暂停其他资源的下载和处理`，直到将该资源加载、编译、执行完毕，图片和框架等元素也如此，类似于将所指向资源嵌入当前标签内。这也是为什么将 js 脚本放在底部而不是头部。
- href:在当前文档和引用资源之间确立联系。
  > href 是 Hypertext Reference 的缩写，指向网络资源所在位置，建立和当前元素（锚点）或当前文档（链接）之间的链接，`<link href=”common.css” rel=”stylesheet”/>`
  > 浏览器会识别该文档为 css 文件，就会`并行下载资源`并且不会停止对当前文档的处理。这也是为什么建议使用 link 方式来加载 css，而不是使用@import 方式。

### link 与 @import 的区别

> link 功能较多，可以定义 RSS，定义 Rel 等作用，而@import 只能用于加载 css
当解析到 link 时，页面会同步加载所引的 css，而@import 所引用的 css 会等到页面加载完才被加载

### em和rem
- em：相对于当前font-size大小，如果当前没有，就看父级的font-size
- rem：相对于根元素html的font-size大小