# 一、标准CSS盒子模型与IE盒子模型
**盒子模型的概念**：具备内容、填充、边框、边界这些属性的均可以看作盒子模型，无论是不是块级元素

**标准盒子模型**：元素的width或height=content的width或height；
**IE盒子模型**：元素的width或height=content的width或height+padding*2+border*2；

**注意：这里所说的元素的width或height是指我们设置的元素的宽和高的大小！**
`box-sizing: border-box;`
# 二、float，浮动原理，清除/闭合浮动
### 1.什么是浮动？
浮动原理：使当前元素脱离普通流，相当于浮动起来一样，浮动的框可以左右移动，直至它的外边缘遇到包含框或者另一个浮动框的边缘。
### 2.浮动的影响
- 因为浮动框不在文档普通流中，所以在布局的时候，文档中的普通流元素就会表现得和浮动框不存在一样， 当浮动框高度超出了包含框的时候，也就会出现包含框不会自动伸高来闭合浮动元素；

- 浮动元素脱离了普通文档流，使得包含框的高度发生了变化，包含框内部由于不存在其他普通元素，就会表现出高度为0，发生高度塌陷，在实际的布局中，这不是我们所希望的，因此我们需要闭合浮动元素，使得其包含框表现出正常的高度。

### 3.清除浮动的方法
**清除浮动**：使用clear元素清除外面浮动，解决外面浮动对自己的影响
**闭合浮动**：当前块级中，其子元素使用了浮动，会给当前块内部和块外部的布局带来影响，所以将当前块中的浮动闭合，能将影响最大化清除。

BFC（Block formatting context）译为“块级格式化上下文”。它是一个独立的渲染区域，只有Block-level box参与，它规定了内部的Block-level如何布局，并且与这个区域外部毫不相干。

**哪些元素会生成BFC**
　　（1）根元素

　　（2）float属性不为none

　　（3）position为absolute或fixed

　　（4）display为inline-block，table-cell，table-caption,flex,inline-flex

　　（5）overflow不为visiable
  
**BFC布局规则**

　　（1）内部的box会在垂直方向，一个接一个地放置

　　（2）Box垂直方向的距离有margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠

　　（3）每个元素的margin box 的左边，会包含块border box的左边相接触（对于从左往右的格式化，否则相反），即使存在浮动也是如此

　　（4）BFC的区域不会与float box 重叠

　　（5）BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此

　　（6）计算BFC的高度时，浮动元素也参与计算

**BFC的作用**
（1）自适应两栏布局
（2）清除内部浮动
（3）防止垂直margin重叠

# 4、flex布局使用场景
（一）骰子布局参考：http://www.ruanyifeng.com/blog/2015/07/flex-examples.html
（二）网格布局
![](https://img-blog.csdnimg.cn/20190305150419499.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjU1NDMxMQ==,size_16,color_FFFFFF,t_70)
（三）圣杯布局
![](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071323.png)
(四)流式布局（常用）
（五）悬挂式布局
![](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071325.png)

