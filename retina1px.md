#method-1: text-shadow
.border-top-1px(@color: '#333') {
box-shadow: 0px 1px 1px -1px @color inset;
-webkit-box-shadow: 0px 1px 1px -1px @color inset;
}

.border-bottom-1px(@color: '#333') {
box-shadow: 0 1px 1px -1px @color;
-webkit-box-shadow: 0 1px 1px -1px @color;
}

.border-left-1px(@color: '#333') {
box-shadow: 1px 0px 1px -1px @color inset;
-webkit-box-shadow: 1px 0px 1px -1px @color inset;
}

－优点－
代码量少
可以满足所有场景
－缺点－
边框有阴影，颜色变浅

#method-2: position
.border-top-1px(@color: '#333') {
content: '';
position: absolute;
top: 0;
left: 0;
background: @color;
width: 100%;
height: 1px;
-webkit-transform: scaleY(0.5);
transform: scaleY(0.5);
-webkit-transform-origin: 0 0;
transform-origin: 0 0;
}

.border-bottom-1px(@color: '#333') {
content: '';
position: absolute;
left: 0;
bottom: 0;
background: @color;
width: 100%;
height: 1px;
-webkit-transform: scaleY(0.5);
transform: scaleY(0.5);
-webkit-transform-origin: 0 0;
transform-origin: 0 0;
}

.border-left-1px(@color: '#333') {
content: '';
position: absolute;
top: 0;
left: 0;
background: @color;
width: 1px;
height: 100%;
-webkit-transform: scaleX(0.5);
transform: scaleX(0.5);
-webkit-transform-origin: 0 0;
transform-origin: 0 0;
}

－优点－
所有场景都能满足
支持圆角 ( 伪类和本体类都需要加 border-radius)
－缺点－
对于已经使用伪类的元素 ( 例如 clearfix) ，可能需要多层嵌套


