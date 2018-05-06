# 前端基础 - CSS

## 盒子模型
margin - border - padding - content

## DIV+CSS布局下，不同分辨率的适应
- 一般的解决方案：采用定长定宽的布局。规划好页面的各部分元素大小。精确到px。然后整体居中，两侧留白。这样分辨率不一样，只会影响留白部分的大小。居中部分在各分辨率下显示效果一致；
- 另一种解决方案比较少用。就是采用流式布局。页面的各部分都是按照百分比去设置的。这样分辨率不一样的情况下。效果都是一致的。但是比较难以掌控。编码难度大，测试困难。因此较少采用；
- 另外，针对不同移动设备下，不同分辨率通常采用服务端判断设备类型，然后加载相应css去实现不同设备访问自适应；
- 采用rem布局，页面距离等比缩放

```CSS
.abc{
  height:300px;
  border:1px solid #000;
  margin:0 auto
}

/* css注释：设置了浏览器宽度不小于1201px时 abc 显示1200px宽度 */
@media screen and (min-width: 1201px) {
  .abc {width: 1200px}
}

/* 设置了浏览器宽度不大于1200px时 abc 显示900px宽度 */
@media screen and (max-width: 1200px) {
  .abc {width: 900px}
}
```

## 块级元素和行内元素的区别
- 行内元素会在一条直线上排列，都是同一行的，水平方向排列
- 块级元素各占据一行，垂直方向排列。块级元素从新行开始结束接着一个断行
- 块级元素可以包含行内元素和块级元素。行内元素不能包含块级元素
- 行内元素与块级元素属性的不同，主要是盒模型属性上
- 行内元素设置width无效，height无效(可以设置line-height)，margin上下无效，padding上下无效；但左右margin和padding都有效

## 特指度
> 特指度是一个css选择器度重要程度；分为id（100）、class（10）和element（1）。

- 规则一，包含ID的选择器胜过包含Class的选择器，包含Class的选择器胜过包含元素的选择器；
- 规则二，不同选择器的特指度比较时，不区分加载的顺序（相同选择器在层叠时，后加载的覆盖前加载的）；
- 规则三，设置的样式高于继承的样式，不用考虑特指度；
- 规则四，!important优先级最高，* 优先级最低。

## 垂直水平居中
```CSS
.Absolute-Center {
  margin: auto;  
  position: absolute;  
  top: 0; left: 0; bottom: 0; right: 0;  
}
```
```CSS
.parent {
  display: table;
}
.child {
  display: table-cell;
  vertical-align: middle;
}
```
```CSS
.parent {
  position: relative;
}
.chiled {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

## z-index计算规则
- 普遍规则
  - position:static 不会影响节点的遮盖关系；
  - W3C 对 z-index 属性的描述中提到 在 z-index 属性仅在节点的 position 属性为 relative, absolute 或者 fixed 时生效；
  - 设置了 position 而没有 z-index 的节点虽然不参与层级树的比较, 但还会在 DOM 中与兄弟节点进行层级比较；
- 从父规则
  - 如果 A, B 节点都定义了 position:relative, A 节点的 z-index 比 B 节点大, 那么 A 的子节点必定覆盖在 B 的子节点前面;
  - 如果所有节点都定义了 position:relative, A 节点的 z-index 和 B 节点一样大, 但因为顺序规则, B 节点覆盖在 A 节点前面. 就算 A 的子节点 z-index 值比 B 的子节点大, B 的子节点还是会覆盖在 A 的子节点前面;

## 页面优化的基本方法
- 压缩css、js文件
- 合并js、css文件，减少http请求
- 外部js、css文件放在最底下
- 减少dom操作，尽可能用变量替代不必要的dom操作
- 图片优化
- 遵守web标准的一些规定，css放到head中，js文件放到body之前或者之后
- 资源cnd化

## 页面引入css的方法
- 外联（.css文件）
- 内联（style=""）
- 内嵌（<style></style>）

> !important > 内联 > 外联 > 内嵌

## 回流reflow与重绘repaint
- 当render tree中的一部分(或全部)因为元素的规模尺寸，布局，隐藏等改变而需要重新构建。这就称为回流(reflow)。每个页面至少需要一次回流，就是在页面第一次加载的时候。在回流的时候，浏览器会使渲染树中受到影响的部分失效，并重新构造这部分渲染树，完成回流后，浏览器会重新绘制受影响的部分到屏幕中，该过程成为重绘。
- 当render tree中的一些元素需要更新属性，而这些属性只是影响元素的外观，风格，而不会影响布局的，比如background-color。则就叫称为重绘。
- 注意：回流必将引起重绘，而重绘不一定会引起回流。

## 标准盒子模型和IE盒子模型
- 标准 W3C 盒子模型的范围包括 margin、border、padding、content，并且 content 部分不包含其他部分。
- IE 盒子模型的范围也包括 margin、border、padding、content，和标准 W3C 盒子模型不同的是：IE 盒子模型的 content 部分包含了 border 和 pading。
