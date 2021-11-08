# H5 是 HTML 的第 5 个版本

@[TOC](阴阳二气充盈，可随时化身金刚)

## HTML（Hyper Text Markup Language 超文本标记语言）

### HTML 版本：

HTML4/4.01(SGML)：SGML 是 XML 的超级，随便写，浏览器背锅进行大量容错
XHTML(XML) 最严格
HTML5 `<!DOCTYPE html >`

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
//dtd：规定文档是什么类型的
<html xmlns="http://www.w3.org/1999/xhtml">
  //xml的命名空间，允许有哪些元素和属性
</html>
```

### head 区的元素：不会在页面留下直接的内容

> mate/title/style/link/script/base

```html
<meta charset="utf-8" />
<meta http-equiv="X-UA-Compatible" content="IE=edge" />
<meta
  name="viewport"
  content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no"
/>
<!-- viewport-fit=cover 适配iPhone X -->
<!-- <meta
      name="viewport"
      content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no,viewport-fit=cover"
    /> -->
<base href="/" />
```

### body 区域的元素

- div/section 区域块/article 文章/nav 导航/aside 不重要内容：广告/header/footer/p/address 文档或文章的作者/拥有者的联系信息/area 图像映射指得是带有可点击区域的图像/blockquote 长文本引用/aption 元素定义表格标题/del 定义文档中已被删除的文本/audio/canvas/video
- span/em/strong/a/i(icon)
- table/thead/tbody/tr/td
- ul/ol/li/dl/dt/dd
- form/input/select/textarea/button（日期，时间，搜素，表单验证，placeholder，autofocus）
- a[href target]/img[src alt]/table td[clospan 列 rowspan 行]/form[action method - enctype]
- input[type value]/button[type]/select>option[value]/label[for]
- type 等于 submit 和 reset 出现在 form 内部才有意义。

```html
<body>
  <div id="container">
    <header id="page-header">
      定义文档的页眉（介绍信息） IE9及以上
      <nav id="main-navigation">
        定义导航链接的部分 IE9及以上
        <ul>
          <li class="current"><a href="#">首页</a></li>
        </ul>
      </nav>
    </header>
    <main>
      规定文档的主要内容
      <article id="page-content">
        规定独立的自包含内容 IE9及以上
        <section>
          定义文档中的节:比如章节、页眉、页脚或文档中的其他部分 IE9及以上
          <hgroup>
            对网页或区段（section）的标题进行组合 IE9及以上
            <h1>这是一个用HTML5做的网页</h1>
            <h2>HTML5+CSS3网页</h2>
          </hgroup>
          <p></p>
          <h2></h2>
          <ul>
            <li>uli</li>
          </ul>
          <ol>
            <li>oli</li>
          </ol>
        </section>
        <aside>
          定义其所处内容之外的内容 IE9及以上
          <h2>这就是aside部分的内容</h2>
          <p></p>
        </aside>
        <details>
          详细内容IE9及以上
          <summary>HTML 5</summary>
          归纳 IE9及以上 This document teaches you everything you have to learn
          about HTML 5.
        </details>
      </article>
    </main>
  </div>
  <footer>
    标签定义 section 或 document 的页脚 IE9及以上 Copyright Dave Woods 2009
  </footer>
</body>
```

### HTML 分类：

- 块级 block：独占一行，可以设置宽高
- 行内 inline：多元素在一行
- inline-block：select 可以和 span 在一行还可以设置宽高
  > 块级元素可以包含行内元素，不一定能包含块级元素（p 不能包含 div，div 会被浏览器拿到外边），行内一般不能包含块级，但 a 标签可以，因为 a 是一个 transparent 透明元素
  >
  > 去除默认样式：reset.css 一会会去除默认样式，normalize.css 修正使默认样式统一

### Canvas：图表，游戏，滤镜

```js
<script>
window.onload=function (){
  let oC=document.getElementById('c1');
  let gd=oC.getContext('2d'); //图形上下文——接口：所有绘图方法、属性
  //路径操作——类似PS的选区
  gd.moveTo(470, 81); 		//起点
  gd.lineTo(778, 236);		//移动到
  gd.lineTo(532, 411);
  gd.lineTo(312, 259);
  //gd.lineTo(470, 81);
  gd.closePath();			//闭合
  //gd.lineWidth=20;		//线宽
  //gd.strokeStyle='green';	//线颜色
  //gd.stroke();
  gd.fillStyle='yellow';		//填充颜色
  gd.fill();
};
</script>
  // 圆：
   let oC=document.getElementById('c1');
   let gd=oC.getContext('2d');
   let cx=200,cy=200,r=100;
   gd.beginPath();
   gd.moveTo(cx+r, cy);
   gd.arc(cx, cy, r, 0, Math.PI*2, true);
  gd.stroke();
  // 矩形运动：
   let oC=document.getElementById('c1');
   let gd=oC.getContext('2d');
   let x=100;
   setInterval(function (){
     gd.clearRect(0, 0, oC.width, oC.height);
     x+=5;
     gd.strokeRect(x, 100, 200, 150);
    gd.stroke();
   }, 1000/60);
```

### 1、doctype 的意义是什么

> 让浏览器以标准模式渲染，让浏览器知道元素的合法性，浏览器有自己的渲染模式，典型的是 ie 盒子模型

### 2、HTML5 的变化

> 新的语义化，表单增强，新 API（离线、音视频、图形、实时通讯、本地存储、设备能力）

- 语义化更好的内容标签（header,nav,footer,aside,article,section）
- 音频、视频 API(audio,video)
- 画布(Canvas) API
- 本地离线存储 localStorage sessionStorage
- 表单控件，calendar、date、time、email、url、search
- 新的技术 websocket, Geolocation 地理
  > Geolocation：
  > navigator.geolocation.getCurrentPosition(function (res){},function(err){})

### 3、em 和 i 的区别

> em 标签语义化，表强调；i 是纯样式标签，表斜体。H5 一般不推荐 i，i 一般做图标

### 4、语义化的意义

> 开发者容易理解，机器更容易理解，有利于 SEO

### 5、那些元素可以自闭合

> input img mate link br hr 不用加/

### 6、HTML 和 DOM 的关系

> HTML 是死的，DOM 是 HTML 解析来的，是活的，JS 操作是 DOM

### 7、property 和 attribute 的区别

- property：用 js 属性操作的一种形式，对 js 变量进行修改，修改对象属性，不会体现在 html 结构中 `p1.style.width（className）`设置或者获取页面样式
- attr：设置标签属性，对 dom 节点属性进项修改，修改 html 属性，会体现在 html 中
  ```js
  p1.setAttribute("data-name", "icc");
  p1.getetAttribute("data-name"); //icc
  p1.setAttribute("style", "color:red");
  ```
  > property 会比 attr 避免一些重复的 dom 渲染，尽量用 property

### 8、form 的作用

> 直接提交表单、使用 submit/reset 按钮、便于浏览器保存表单、第三方库可以整体提取和验证

### 9、Cookies，sessionStorage 和 localStorage 的区别

- sessionStorage：非持久化的本地存储，当会话结束后数据销毁。
- localStorage：持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。
- cookie：大小是受限只有 4kb，WebStorage 有 5M，并且每次请求一个新的页面的时候 Cookie 都会被发送过去，这样无形中浪费了带宽，另外 cookie 还需要指定作用域，不可以跨域调用，还有次数限制
  > WebStorage 拥有 setItem,getItem,removeItem,clear 等方法，不像 cookie 需要自己封装 setCookie，getCookie。但是 Cookie 也是不可以或缺的：Cookie 的作用是与服务器进行交互，作为 HTTP 规范的一部分而存在 WebStorage 仅仅是为了在本地“存储”数据而生。
