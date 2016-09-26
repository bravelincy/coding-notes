# flex布局
> flex为css提供了更加灵活的布局方式，中文称之为“伸缩布局”。这种布局发生在父容器与子容器之间，通过它我们可以非常容易地设置父子容器彼此之间复杂的布局关系。

## 快速导航
- [display](#display)
- [flex-direction](#flex-direction)
- [flex-wrap](#flex-wrap)
- [justify-content](#justify-content)
- [align-items](#align-items)
- [align-self](#align-self)
- [align-content](#align-content)
- [order](#order)
- [flex](#flex)

## 基本用法
一个伸缩盒模型有两条方向垂直的轴，默认地，最左边是主轴（main-axis）的起点，最右边是主轴的终点，两点之间的距离是主轴的长度。同理，与之方向垂直的轴就是侧轴（cross-axis）了。

### display
如同`block`和`inline-block`，flex也有这么两个版本

- flex：起始和结束位置换行
- inine-flex：不换行

### flex-direction
设置主轴的方向，它会影响子元素的排列方式

- row（默认值）：从左到后
- row-reverse：从右到左
- column：从上到下
- column-reverse：从下到上

### flex-wrap
默认子元素不会换行，此属性设置换行

- nowrap（默认值）：不换行
- wrap：换行，从上到下或从左到右，取决于`flex-direction`
- wrap-reverse：换行，方向相反

### justify-content
子元素在主轴上的对齐方式

- flex-start（默认值）：与主轴的起点对齐
- flex-end：与主轴的终点对齐
- center：与主轴中心点对齐
- space-between：第一个与最后一个元素分别对齐主轴起点和终点，每个元素的距离平均分配
- space-around：每个元素前后的空间均匀分配

### align-items
设置单行内容子元素在侧轴上的对齐方式

- flex-start（默认值）：与侧轴的起点对齐
- flex-end：与侧轴的终点对齐
- center：与侧轴的中心点对齐
- strech：将子元素撑满至侧轴的长度
- baseline：与基线对齐

### align-self（用于子元素）
单独设置子元素自身在侧轴上的分布方式

### align-content
设置多行内容子元素在侧轴上的对齐方式

- flex-start（默认值）：与侧轴的起点对齐
- flex-end：与侧轴的终点对齐
- center：与侧轴的中心点对齐
- space-between：第一行与最后一行分别对齐侧轴起点和终点，每行的距离平均分配
- space-around：每行之间的空间均匀分配
- strech：将每行元素撑满至侧轴长度

### order（用于子元素）
设置子元素排列顺序

## 进阶
### flex（用于子元素）
这是一个简写属性，它由三部分组成：
``` css
flex: <flex-grow> <flex-shrink> <flex-basis>
```

所以也可以分开设置：
``` css
flex-grow: 1;
flex-shrink: 2;
flex-basis: 300px;
```
- flex-grow（默认值1）：瓜分父容器剩余空间的占比
- flex-shrink（默认值1）：补偿父容器不足空间的占比
- flex-basis（默认值0）：参与瓜分或补偿前自身的空间大小


## 参考文档
- [深入解析 CSS Flexbox](http://www.oxxostudio.tw/articles/201501/css-flexbox.html)
- [深入理解css3中的flex-grow、flex-shrink、flex-basis](http://zhoon.github.io/css3/2014/08/23/flex.html)


