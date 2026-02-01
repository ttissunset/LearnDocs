### 1. 伪类和伪元素

#### 1.1 伪类

伪类的操作对象是文档树中**已有的元素**，使用**冒号 `:` **作为标识，常用于**需要基于元素状态来改变样式的场景**

##### 1.1.1 常见伪类：

- `:hover` ： 鼠标悬停状态
- `:active`：鼠标点击(按下不放)时激活
- `:focus`：获得焦点时
- `:first-child`：第一个子元素
- `:last-child`：最后一个子元素
- `:nth-child()` ：指定索引的子元素(从头到尾)
- `:nth-last-child()` ：指定索引的子元素(从尾到头)
- `:checked`：勾选状态的表单元素
- `:disabled`：禁用状态的表单元素
- `:not()`：不匹配给定选择器
- `:visited`：已访问的 a 标签
- `:link`：未访问的 a 标签

#### 1.2 伪元素

伪元素用于**创建**了一个**文档树外**的元素，这些元素实际上并不存在于HTML文档中，使用**双冒号 `::` **作为标识，常用于**在元素内容前后添加额外内容或样式**

##### 1.2.1 常见伪元素：

- `::before`：在元素内容前插入一个**虚拟元素**

- `::after`：在元素内容后插入一个**虚拟元素**
- `::first-line`：选择元素的第一行文本
- `::first-letter`：选择元素的第一个字母或第一个字符
- `::selection`：选择用户选中的文本
- `::placeholder`：选择表单元素的占位符文本

#### 1.3 总结

| 对比维度         | 伪类（Pseudo-class）       | 伪元素（Pseudo-element）      |
| ---------------- | -------------------------- | ----------------------------- |
| 是否创建新元素   | ❌ 不创建新元素             | ✅ 创建虚拟元素                |
| 是否影响文档结构 | ❌ 不改变 DOM 结构          | ❌ 不改变 DOM 结构（虚拟元素） |
| 表示符号         | 单冒号 `:`                 | 双冒号 `::`                   |
| 主要用途         | 描述元素的某种状态或位置   | 操作元素的某些特定部分        |
| 示例             | `a:hover`, `p:first-child` | `p::before`, `p::first-line`  |



### 2. 准盒模型 vs IE盒模型(怪异盒模型)

#### 2.1 标准盒模型： 

- **总宽度/总高度 = width/height + border + padding + margin**
- 通过设置 `box-sizing：content-box`，也是 `box-sizing` 的默认值

![img](https://static.ecool.fun//article/bb71712f-4d36-49cc-a180-b08f4a536b9e.png)

#### 2.2 IE盒模型：

- **总宽度/总高度 = width/height + margin** ( `padding` 和 `border` 被包含在 `width/height` 中)
- 通过设置 `box-sizing：border-box`

![img](https://static.ecool.fun//article/39712bfa-735c-404d-9cfb-78a9873b0e38.png)



### 3. 回流和重绘

#### 3.1 回流 Reflow

##### 3.1.1 简介

回流也称为**==布局重排==**，是指当元素的尺寸、位置或其他影响其布局的属性发生变化时，浏览器需要重新计算元素的布局，并==**重新构建布局树**==的过程

**==回流一定会引起重绘，但重绘不一定会导致回流!!!!!==****

**回流的成本通常比重绘大得多，因此我们应该尽量减少回流操作，以降低页面性能消耗**

##### 3.1.2 常见的回流操作：

- 更改元素的**尺寸**，如设置 `width、height、padding、border、margin`

- **添加或删除可见的DOM元素**
- 修改元素的内容，如文本变化、数量，图片数量、大小、字体大小
- 改变视口，如窗口缩放、滚动等
- 页面初始渲染。
- 浏览器窗口大小改变。
- 激活 CSS 伪类，如 `:hover`
- 查询某些属性或调用某些方法，如 `offsetWidth`、`scrollTop`

##### 3.1.3 如何优化回流

**核心：==减少回流发生的频率，控制节流的范围==**

- **样式集中处理**：通过创建一个**样式数组**，通过**更改一个元素的 `class`** 来实现样式的切换，从而**减少操作次数**
- **脱离文档流**：使用**绝对定位**或**固定定位**可以将元素脱离文档流，从而**减少回流的影响范围**
- **DOM离线操作**：使用文档片段 `DocumentFragment`来**批量更新DOM**，**避免频繁的回流**

#### 3.2 重绘 Repaint

##### 3.2.1 简介

重绘是指当**元素的外观发生变化**时，浏览器需要重新绘制这些元素。**==根据分层信息重新生成绘制指令==**

##### 3.2.2 常见的重绘操作

- 元素的颜色、背景色、边框等样式属性发生变化
- 元素的可见性发生变化，如 `display: none` 变为 `display: block`

> 解析：`display: none` 的元素不会在页面中占据空间也不会参与布局计算。当将其改为 `display: block` 时，元素会**重新出现在页面中并占据空间**，这使得浏览器需要**重新计算该元素以及可能受其影响的其他元素的布局信息**，确定它们在页面上的位置和大小，这个重新计算布局的过程就是回流。在完成回流确定好元素新的布局信息后，浏览器需要**根据新的样式和布局将元素绘制到屏幕上更新元素的外观**，这个过程就是重绘



### 4. position 定位 

#### 1. 相对定位 relative

- **相对==自身原有位置==**进行偏移，通过 `top`、`right`、`bottom`、`left` 设置偏移量
- **==不脱离标准流==**，元素在文档流中的原始位置会被保留，==**其他元素仍以原始位置布局**==
- **不影响子元素的定位**：若子元素设置 `absolute`，子元素会**相对于该元素的==原始位置==**定位
- 作用：
  - 微调元素
  - 作为绝对定位的参考（**==子绝父相==**）


```css
.box {
  position: relative;
  top: 20px; /* 向下偏移 20px */
  left: 10px; /* 向右偏移 10px */
}
```

#### 2. 绝对定位 absolute

- **==脱离标准流，元素不再占据原位置==**，其他元素会忽略它的存在

- 相对于**==最近的已定位祖先元素==**，定位为 `relative、absolute、fixed、sticky`的元素

- 若祖先元素均未定位，则相对于浏览器根元素 `<html>` 进行定位

  - **如果是 top 则会以页面的左上角为参考点**
  - **如果是 bottom 则会以==浏览器首屏==的左下角作为参考**

  ![image-20250806205518085](https://gitee.com/phenylaminopropionic-acid/photo/raw/master/image-20250806205518085.png)

- 通过 `top、right、bottom、left` 设置相对于基准元素的偏移量。

- ==绝对定位会使元素具有**行内块**的特性，**可以设置宽高**==

```css
<div class="parent"> 
  <div class="child"> 
    子元素相对于父元素定位
  </div>
</div>

<style>
.parent {
  position: relative; /* 父元素设置相对定位，作为子元素的定位基准 */
  width: 200px;
  height: 200px;
  border: 1px solid #000;
}
.child {
  position: absolute;
  top: 50px;
  left: 50px;
}
</style>
```

#### 3. 固定定位 fixed

- **==脱离标准流==**，可以看做**特殊的绝对定位**，**==始终相对于浏览器视口==**定位，**不随页面滚动而移动**
- `top`、`right`、`bottom`、`left` 以视口为基准，常用于实现导航栏、返回顶部按钮等固定位置的元素。
- ==固定定位会使元素具有**行内块**的特性，**可以设置宽高==**

```css
.fixed-navbar {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  background: #333;
  color: white;
}
```

#### 4. 粘性定位 sticky

- **混合定位模式**，元素在**==正常文档流中时表现为相对定位==**，当滚动到**指定阈值**时表现为**固定定位**
  - 父元素的 `overflow` 必须为 `bisible`
  - 父元素的高度不能低于 `sticty` 元素的定位
  - 没有已定位的父元素，则相对于 `viewport` 视口

- **需要==结合 `top`、`right`、`bottom`、`left` 中的至少一个属性==来触发粘性效果（例如 `top: 0` 表示滚动到顶部时固定）**
- **兼容性**：IE 浏览器不支持，现代浏览器 `Chrome、Firefox` 等需添加 `-webkit-` 前缀

```css
.sticky-header {
  position: sticky;
  top: 0; /* 滚动到顶部时固定 */
  background: #f5f5f5;
  padding: 10px;
}
```

#### 5. 静态定位 static

- **默认值**，元素按照**文档标准流**正常布局进行排列，`top`、`right`、`bottom`、`left` 等定位属性**无效**。**块级元素独占一行，内嵌元素共享一行**。
- 元素的位置由 HTML 文档中的顺序决定，**不受其他定位模式影响**

```css
.box {
  position: relative;
  top: 20px; /* 向下偏移 20px */
  left: 10px; /* 向右偏移 10px */
}
```

#### 6. z-index

**`z-index`** 用于控制定位元素 `position `值为 `relative`、`absolute`、`fixed` 或 `sticky`的**层叠顺序**，决定元素在页面三维空间中（垂直于屏幕方向）的显示顺序，**数值越大**的元素会覆盖在数值较小的元素**上方**

##### 6.1 层叠上下文

- **DOM文档根元素天生就构成了一个层叠上下文**，它的子元素默认都在这个层叠上下文中
- **层叠上下文处理元素层级关系时，只在==本上下文==中按照层叠大小进行排序**
- **兄弟上下文是相互独立的**
- **属性值小于1的时候，也会形成层叠上下文**，它的渲染层级相当于定位元素 **`z-index：0`**的渲染层级
- `flex` 元素中的 `z-index` **只对子元素有效**，对 `flex` 元素本身不生效   

```javascript
<div id="div1">div1 position relative <br> z-index: 2</div>
<div id="div2">
  div2 position absolute <br> z-index: 1
  <div id="div2_1">div2_1 position relative <br> z-index: 5</div>
  <div id="div2_2">div2_2 position absolute <br> z-index: 4</div>
</div>
<div id="div3">div3 position static <br> z-index: 3</div>
```

![img](https://pic2.zhimg.com/v2-2c4a66d96745c5d73e21dfd1464c8971_1440w.jpg)

#### 7.总结

![image-20250710214358054](https://gitee.com/phenylaminopropionic-acid/photo/raw/master/image-20250710214358054.png)



### 5. CSS单位体系

#### 5.1 绝对单位 -- 不随用户的浏览器或设备而改变

- **像素 px**
- **点 pt ：印刷行业标准，1pt = 1/72 英寸**
- **派卡 pc**
- **英寸 in**
- **厘米 cm / 毫米 mm**

#### 5.2 相对单位

- **百分比 %**：相对于**父元素的宽度或高度**

```javascript
/* 使div的宽度为父元素宽度的80% */
.container {
  width: 80%;
}
```



- **em**：相对于**父元素**的字体大小

```javascript
.parent {
	font-size: 16px;
}
 
.child {
    /* 相对于父元素字体大小的20倍，即高度是320px */
	height: 20em; 
	background-color: #00f;
}
```

- **rem**：相对于**根元素（`html`）**的字体大小

```javascript
/* 设置根元素的字体大小为18px */
html {
	font-size: 18px;
}

.root {
	/* 相对于根元素字体大小的20倍，即高度是360px */
	height: 20rem; 
	background-color: #00f;
}
```

- **视窗单位 `vm/vh`**：相对于可视窗口的大小，分别取**视口宽度/高度的 1%** 作为标准

```javascript
.box {
	width: 100vw; /* 100% of the viewport width，相当于是可视窗口的100% */
	height: 50vh; /* 50% of the viewport height，可视窗口的50% */
}
```

#### 5.3 视窗单位衍生新单位

##### 原因：在移动端，==100vh 不总是等于一屏幕的高度==，原因有以下两点：

1. 很多浏览器，在计算 100vh 的高度的时候，会把**地址栏**等相关控件的高度计算在内
2. 同时，很多时候，由于会弹出**软键盘**等操作，在弹出的过程中，`100vh` 的计算值并不会实时发生变化

![img](https://user-images.githubusercontent.com/8554143/217008049-eeb01b85-7536-4efd-b8e8-a1cebfb2b917.png)

因此，CSS 新增了以下三种新的视窗单位：

- **大视口单位 `lvh/lvw`**：任何动态扩展和缩回的 UA 界面都没有展开时
- **小视口单位 `svh/svw`**：任何动态扩展和缩回的 UA 界面都展开时

![img](https://user-images.githubusercontent.com/8554143/215314173-40207539-e88a-43ff-8538-e41e794d6b55.png)

- **动态视口单位 `dvh/dvw`**：根据**视口的实时变化自动调整大小**，以适应不同的滚动位置和浏览器UI状态



### 6. Css 选择器及权重

#### 6.1 Css 选择器

- **标签选择器**：以元素标签开头，如 `div、span`
- **类选择器** `.`：给元素设置 **class 类名**后使用
- **id选择器** `#`：给元素设置**唯一 id** 后使用

- **通配符选择器 `*`** ：选择**所有的元素**
- **后代选择器**：如 `div p{ ... }` 选中 `div` 下面的所有 `p` 标签，包括**儿子和孙子**

- **子元素选择器 `>`**：选择所有**1亲儿子元素**
- **相邻兄弟选择器 `+`**：选择**严格相邻的兄弟**元素

```javascript
p + span{
	color: red;
}

<p>松子</p>
<span>果子</span>
<h3>hhh</h3>
<span>我不是相邻的  没有被选中</span>
```

- **通用兄弟选择器 `~`**：选择**所有兄弟元素**
- **属性选择器 `[属性名]`**：

```javascript
/* 存在属性 */
[target] { background: yellow; }

/* 精确匹配 */
[type="text"] { border: 1px solid #ccc; }

/* 包含指定值 */
[class~="logo"] { width: 100px; }

/* 前缀匹配 */
[href^="https"] { color: green; }

/* 后缀匹配 */
[src$=".jpg"] { border: 2px solid #999; }

/* 子串匹配 */
[alt*="logo"] { opacity: 0.8; }
```

- **伪类选择器 `：`、 伪元素选择器 `::`**：见知识点 1

#### 6.2 选择器权重

##### 6.2.1 基本权重

- **内联样式**：1000（在元素style属性中直接写的样式）
- **ID选择器**：0100
- **类/属性/伪类选择器**：0010
- **元素/伪元素选择器**：0001
- **通配符/子选择器/相邻选择器**：0000

##### 6.2.2 特殊规则

- **`!important`**：优先级**最高**，**可以覆盖任何其他权重的样式**，但应尽量避免使用

```css
p {
    color: red !important
}
```

- **相同权重**：后定义的样式会覆盖前面的样式

```css
p { 
    color: red; 
}
/* 最终生效，覆盖了上面的样式 */
p { 
    color: blue; 
} 
```

- **继承的样式**：权重最低，不如任何直接指定的样式

##### 6.2.3 权重计算

**权重越大的样式才会被显示**

```javascript
#list {} 1000
div 1
.container 100

.container #list::before 100 + 10 + 1 = 111
.container #list a::before 100 + 10 +1 + 1 = 112
.container .from input[type='text'] 10 + 10 + 1 + 10 = 031
.container ul a:hover 10 + 1 + 1 + 10 = 022
112 > 111 > 031 > 032
```



### 7. flex 布局

#### 7.1 容器属性

- `flex-direction`：定义了在那个方向上排列项目
- `flex-wrap`：定义了项目在轴线上的排列是否换行
- `justify-content` ：定义了项目在主轴上的对齐方式
- `align-items`：定义了项目在交叉轴上的对齐方式
- `align-content`：定义了多根**主轴线**在垂直方向上的对齐方式

#### 7.2 项目属性

| **项目属性**  | **默认值** | **说明**                                                     |
| ------------- | ---------- | ------------------------------------------------------------ |
| `order`       | 0          | 定义了项目的排列顺序。                                       |
| `flex-grow`   | 0          | 扩大因子，定义了项目的放大比例                               |
| `flex-shrink` | 1          | 收缩因子，定义了项目的缩小比例                               |
| `flex-basic`  | auto       | flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。 |

#### 7.3 flex 复合属性

 `flex` 属性是一个复合属性，是由： `flex-grow、flex-shrink、flex-basis`三个属性的缩写

- **flex-grow**：定义项目的**放大比例** ；
  - 默认为0，即即使存在剩余空间也不会放大；
  - 所有项目的flex-grow为1：等分剩余空间（自动放大占位）；
  - flex-grow为n项目，占据的空间（放大的比例）是flex-grow为1的n倍
- **flex-basis**：定义在分配多余空间之前，项目占据的主轴空间，默认值为 auto
- **flex-shrink：定义项目缩小比例**，取值0/1
  - 主要用于制作移动端横向滚动区域



### 8. Gird布局





### 9. 居中方式

#### 9.1 水平居中

- **通过设置 `margin：0 auto`**
  - 原理：当设置 `margin-left：auto` 时盒子会跑到最右边；同理设置 `margin-right：auto` 时盒子会跑到最左边。因此，我们设置左右 `margin` 均为 `auto` 时盒子就会水平居中
- 通过**弹性盒子**给父盒子设置 `display:flex` 和 `justify-content:center` 

#### 9.2 垂直居中

- 通过设置`verticle-align：middle`

  > 注意：使用 `verticle-align` 属性有两个前提
  >
  > 1. 当前元素是行内块元素，即 `displsy：inline-block`
  > 2. 当前元素需要**以最高的兄弟元素作为参照物**，垂直于兄弟元素的中心点（给兄弟元素设置 `height：100%`）

- 通过使用伪元素 `::before` 替代兄弟盒子

```css
box::before{
    content:'';
    display:inline-block;
    vertical-align:middle;
    height:100%
}
```

- 通过**绝对定位**

```css
.container{
    positiopn:relative;
    height:300px;
    width:500px
}

.box{
    position:absolute;
    width:100px;
    height:100px;
    left:50%;
    top:50%;
	transform:translateX(-50%) translateY(-50%)
    /* 或者： left:calc(50% - 50px)*/
}
```

- 通过**弹性盒子**给父盒子设置 `display:flex` 和 `align-items:center` 



### 10. 隐藏元素的方式

#### 10.1 完全隐藏：元素从渲染树上消失，不占据空间

- 设置 `display:none` 
- 在元素上添加 `hidden` 属性：如 `<div hidden>`

#### 10.2 视觉上隐藏：屏幕上不可见，但占据空间

- 设置 `opcity：0`
- 设置 `visibility：hidden`
- 使用**绝对定位**设置 `top、left` 等**移出可视区**
- 使用 `margin` 将元素**移出可视区**
- 设置**宽高为 0** 加上 `overflow：hidden`

#### 10.2 语义上隐藏：读屏软件不可见，但占据空间

- 使用 CSS3 新增属性 `aria-hidden：true` 



### 11. 浮动 float

#### 11.1 特性：

- **脱离标准流**
- **浮动元素会相互贴靠**，除非宽度不够
- 如果浮动元素**没有设置宽度**的情况下爱，会**收缩自适应宽度**

#### 11.2 ==清除浮动的方法==

- 给**父元素设置大于浮动子元素的高度**

- 使用 **clear：both** 属性清除左右两侧浮动

  - 注意：`clear` 回导致 `margin` 属性失效

- 隔墙法：在==多个浮动元素中间==添加一个空的 `<div style:"clear:both" />` 的盒子

- 内墙法：在==某个浮动元素内部==添加 一个空的 `<div style:"clear:both" />` 的盒子

- **使用 `overflow: hidden`** 使父元素生成 BFC

- ==**伪类清除法**==：

  ```javascript
  xxx::after{
      content:"";
      dispalay:block;
      height:0;
      clear:both;
      visibility:hidden
  }
  ```

  

#### 11.3 作用

- 实现图文环绕效果
- 实现两列、三列布局

### 动画与过渡



### 11. CSS 渲染性能优化



### 12. `<img>`是什么元素

`<img />` 标签没有独占一行，所以是**行内元素**，`<img />` 是特殊的**替换元素**

#### 引申：不可替换元素和替换元素

**不可替换元素**：HTML的大多数元素是不可替换元素，即其内容直接表现给用户端

```html
<h1>我是标题</h1>
```

**可替换元素**：浏览器**根据元素的标签和属性**，来决定元素的具体显示内容

- **替换元素一般有内在尺寸，所以具有 `width` 和 `height`**
- **可替换元素的性质同设置了`display:inline-block `的元素一致**

- `<input>` 标签的 `type` 属性来决定是显示输入框，还是单选按钮等
- `<img>` 标签的 `src` 属性的值来读取图片信息并显示出来



###  13. Css 超出省略怎么写，三行超出省略怎么写

```javascript
// 超出省略
.text {
    /* 超出部分不换行 */
	white-space: nowrap;   
	/* 超出部分进行隐藏 */
	overflow: hidden;   
    /* 超出部分用 ... 代替 */
	text-overflow: ellipsis 
}

// 超出三行省略
.textOVerThree {
    display: -webkit-box;
    white-space: normal !important;
    overflow: hidden;
    text-overflow: ellipsis;
    word-wrap: break-word;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical
}
```



### 13. 宽度自适应，高度保持等比缩放

- ##### 使用宽度百分比和vw

```javascript
<div class="adapt">
	<img src="http://ashuai.work/static/img/avantar.png">
</div>

<style>
	.adapt {
	    width: 36%;
	    height: 36vw;
	    background-color: pink;
	}

	img {
	    width: 100%;
	    height: 100%;
	}
</style> 
```

- ##### 宽度百分比加高度被 `padding-top` 百分比撑开

```javascript
<div class="adapt">
	<img src="http://ashuai.work/static/img/avantar.png">
</div>

<style>
	.adapt {
	    width: 36%;
	    height: 0;
	    padding-top: 36%;
	    background: pink;
	    position: relative;
	}

	img {
	    width: 100%;
	    position: absolute;
	    top: 0;
	    left: 0;
	}
</style>
```

- ##### 宽度百分比搭配伪元素

```javascript
<div class="adapt">
	<img src="http://ashuai.work/static/img/avantar.png">
</div>

<style>
	.adapt {
	    width: 36%;
	    overflow: hidden;
	    background: pink;
	    position: relative;
	}

	.adapt::after {
	    content: '';
	    display: block;
	    margin-top: 100%;
	}

	img {
	    width: 100%;
	    position: absolute;
	    top: 0;
	    left: 0;
	}
</style>
```



### 14. 为什么 transform 的效率高

==因为 `transform` **既不会影响布局也不会影响绘制指令**，**只会影响渲染流程的最后一个 `draw` 阶段**==所以无论**==主线程多忙碌也不不受影响==**

- 使用 top left 定位是**直接改变元素真实位置**的
- 用 transform: translateY(-5px) 只是**改变了视觉位置**，**元素本身位置还是在 0px**
-  **transform 不改动 css 布局**，并且**可以利用GPU加速**，不阻塞主线程，在渲染效率上更高
- **Left/Top每次改变都会触发==重排和重绘==**，影响整个文档流

![image-20250806230128209](https://gitee.com/phenylaminopropionic-acid/photo/raw/master/image-20250806230128209.png)



### 15. 上下固定，中间滚动布局如何实现

- 使用 `Flex` 布局

  - 将整个页面分为三个部分：上（header）、中（main）、下（footer）。

  - 设置嘴最外层容器为 `flex` 布局，方向为 column，并设置高度为100vh。

  - 设置header和footer的高度为固定值，中间main部分设置flex:1，并设置overflow:auto。

```javascript
<div>
   <header />
   <main />
   <footer /> 
</div>

<style>
div{
    display:flex;
    flex-direction:column;
  	height:100vh
} 
header{
    width:20vh;
}
main{
    flex:1;
    overflow: auto;
}  
footer{
    width:20vh;
}  
</ style>
```

- 使用绝对定位（或固定定位）

  - 设置header为固定高度，绝对定位（或固定定位）在顶部。

  - 设置footer为固定高度，绝对定位（或固定定位）在底部。

  - 中间main部分设置定位为absolute，并设置top为header高度，bottom为footer高度，然后设置overflow:auto。



### 16. 移动端适配 1px 的问题

### 

### 17.Css 如何实现一个半圆和三角形

#### 17.1 三角形

==**原理：边框实际上并不是一个直线，每条边框都是一个梯形，当元素的宽度和高度都为 0 时，边框会相互挤压，形成四个三角形。通过设置其中三个边框为透明，只保留一个边框的颜色，就可以得到一个三角形**。==

实现步骤：

- **设置元素尺寸为0**：将元素的 `width` 和高度 `height` 设为 0
- **设置边框**：为元素设置边框，但**只给其中一个方向的边框设置颜色，其余三个方向的边框设置为透明**。
- **调整边框宽度**：通过调整**有颜色的边框的宽度**可以控制三角形的大小。

```html
<div id="app"></div>

#app{
  width:0;
  height:0;
  border-top: 50px solid yellowgreen;
  border-bottom: 50px solid deeppink;
  border-left: 50px solid bisque;
  border-right: 50px solid chocolate;
}
```

![image-20250713115145176](https://gitee.com/phenylaminopropionic-acid/photo/raw/master/image-20250713115145176.png)



#### 17.2 半圆 -- 利用 border-radius 属性

`border-radius` 属性其实是`border-top-left-radius、border-top-right-radius、border-bottom-right-radius、border-bottom-left-radius`四个属性的简写模式

`border-radius`同样可以省略部分值，省略时同样是采用**对角线相等**的原则

![img](https://i-blog.csdnimg.cn/blog_migrate/3c19a9ab458efc576b47b0e047e3b476.png)

```javascript
.semi-circle{
  width:100px;
  height:50px;
  background-color:red;
  border-radius:50px 50px 0 0; //设置外边框圆角 上半圆
}
 
.semi-circle2{
  width:100px;
  height:50px;
  background-color:red;
  border-radius:  0 0 50px 50px; //设置下半圆
}

.semi-circle3{
  width:100px;
  height:50px;
  background-color:red;
  border-radius: 50px  0 0  50px; //设置左半圆
}

.semi-circle4{
  width:100px;
  height:50px;
  background-color:red;
  border-radius: 0 50px  50px  0 ; //设置右半圆
}
```

### 18. 垂直居中九宫格解决方案
