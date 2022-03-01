# HTML/CSS基础

---

## Html

### 1.标签语义化

* 代码结构更加清晰
* 见名知意，没有基础的人也能知道这部分代码是干嘛的
* 方便团队开发维护，代码可读性更强
* 有利于SEO优化，爬虫依赖于标签来确定上下文关系

### 2.meta标签

* meta标签提供关于html文档的元数据，不会显示在页面，但是对于机器是可读的，告诉浏览器怎么解析页面，告诉搜索引擎关键字（SEO优化）
* meta作用：告诉机器浏览器该如何解析该页面，描述这个页面的主要内容，可以设置服务器发送到浏览器的http头部内容
* charset="utf-8"设置页面使用的字符编码
* viewport 设置视口，移动端的适配

### 3.css与javascript引入位置

* script标签的引入一般放在body最后，这样避免脚本过大，加载时间长，导致页面长时间空白，这是因为渲染进程与js进程是互斥的，脚本会阻塞页面的渲染，脚本之间的加载是同步进行的，按引入顺序执行，但是以下两个属性会影响脚本执行与页面渲染的顺序
  * defer：不会阻塞渲染，这样即使放在header内部，也不会阻塞页面加载，不过js会先于document加载完成，并且也不会影响脚本之间的执行顺序，按照引入顺序执行
  * async：与defer一样，都是解决阻塞渲染，但它是在document加载完成后才执行，并且它的执行顺序是按照谁先加载完成执行谁，所以对于文件顺序有要求，存在前后依赖的不要使用它

### 4.HTML 的块级元素，行内元素，行内块元素有哪些?区别是什么?

* 块级元素：div，h1-h6，p，ul，ol，dl，li，hr，dt，dd，form，table
  * 块元素独占一行
  * 宽高生效
  * 默认宽和父元素一样，内容撑开高度
  * margin，padding全部生效

* 行内元素：em，i，del，small，strong，ins，span，a
  * 宽高不生效
  * 左右margin生效上下不生效
  * 在一行排列
  * 大小靠内容撑开
  * padding都生效

* 行内块元素：img，input(表单元素，除去form)
  * 在一行排列
  * 宽高生效
  * margin，padding生效

## CSS

### 1.盒模型，什么是标准 CSS 盒模型?

* margin padding border content
  * 标准盒模型：盒子宽度 = width + padding + border + margin
  * 怪异盒模型：盒子宽度 = width（包含padding+border） + margin

### 2.CSS3新特性

* border-radius圆角
* border-image 边框图片
  * border-image: url() top right bottom left
  * border-width: top right bottom left
* box-shadow盒子阴影: x,y,size,opcity
* text-shadow文字阴影
* linear-gradient 线性渐变
  * background: linear-gradient(to position , color,color,...,color)
* radial-gradient 径向渐变
  * background: radial-gradient(shap size at position , color,color,...,color)
* 2D/3D转换 transform：rotate(旋转) scale(缩放) translate(位移)
* @media媒体查询，根据屏幕宽度，设置，用来解决移动端适配,根据屏幕大小使相应的css生效
* flex布局(弹性盒子)

### 3.实现元素隐藏

* display：none 不占位，源码可见
* opacity: 0 占位，源码可见，透明度0
* visibility: hidden 占位，源码可见
* position: top:-9999px,left:-9999px 利用定位将元素移出视窗

### 4.实现元素水平居中/垂直居中

* 水平居中
  * 行内元素：text-align：center
  * 块元素: margin: 0 auto
  * position: left: 50%; transform: translate(-50%)
* 垂直居中
  * height = line-height
  * verticle-align: middle
  * position: top: 50%; transform: translate(0,-50%)

### 5.定位元素的水平垂直居中

* 宽高固定 position: top:0,left:0,right:0,bottom:0,margin:auto
* 宽高固定 position：top: 50%, left: 50%, margin-left: -width/2px,margin-top: -height/2px
* dispaly: flex; justify-content: center,align-items: center(极力推荐)
* position: left: 50%,top: 50%; transform: translate(-50%,-50%)

### 6.Position

* static 默认
* relative 相对定位，不脱标，相对于自身位置进行偏离，不影响元素本身特性，z-index提升层级
* absolute 绝对定位，脱标，相对于已有定位的父元素进行偏离，都没有就相对于body进行偏离
* fixed 固定定位，脱标，相对于视窗进行偏离

### 7.清除浮动

* 给父元素设置高度
* 使用overflow：hidden
* 使用额外标签法：在子元素末尾添加一个标签，给标签添加一个样式，样式定义clear:both闭合浮动
* 伪元素清除浮动
```css
.clearfix:after{
 content: '',
 height: 0,
 display: block,
 clear: both,
 visibility: hidden
}
.clearfix{
 *zoom: 1
}
```
* 双伪元素清除浮动
```css
.clearfix:before,
.clearfix::after {
 content: '',
 display: table
}
.clearfix:after:{
 clear: both
}
.clearfix{
 *zoom: 1
}
```

### 8.外边距合并，外边距塌陷

* 两个块元素垂直排列给上加下margin，给下加上margin，会产生外边距合并，这时候会取最大的那个显示
* 嵌套块元素，给子元素设置上margin，父元素会跟着掉下来，这叫外边距塌陷
  * 给父盒子设置上边框（不推荐）
  * 用overflow：hidden
  * 加内边距（不用margin）

### 9.重排和重绘

* 部分渲染树（或者整个渲染树）需要重新分析并且节点尺寸需要重新计算。这被称为重排。注意这里至少会有一次重排-初始化页面布局。
* 由于节点的几何属性发生改变或者由于样式发生改变，例如改变元素背景色时，屏幕上的部分内容需要更新。这样的更新被称为重绘。
* 什么时候会触发重排重绘
  * 添加、删除、更新 DOM 节点
  * display: none 隐藏一个 DOM 节点-触发重排和重绘
  * 通过 visibility: hidden 隐藏一个 DOM 节点-只触发重绘，因为没有几何变化
  * 移动或者给页面中的 DOM 节点添加动画
  * 添加一个样式表，调整样式属性
  * 用户行为，例如调整窗口大小，改变字号，或者滚动

### 10.css 选择器有哪些?选择器的优先级?

* id选择器
* 类选择器
* 属性选择器
* 伪类选择器
* 标签选择器
* 伪元素选择器
* 通配符选择器
* 优先级：内联样式 > ID选择器(100)> 类选择器(10) = 属性选择器 = 伪类选择器 > 元素选择器(1) = 关系选择器 = 伪元素选择器 > 通配符选择器(0)
* !important
* 后代选择器选全部
* 子代选择器只选亲孩子

