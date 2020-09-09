1、内高度、内宽度： 内边距 + 内容框

```
element.clientWidth
element.clientHeight
```

2、外高度，外宽度： 边框 + 内边距 + 内容框

```
element.offsetWidth
element.offsetHeight
```

3、上边框、左边框

```
element.clientTop
element.clientLeft
```

4、元素的大小及其相对于视口的位置

```
element.getBoundingClientRect()
//x\y:元素的左上角和父元素左上角的距离
//width/height:边框 + 内边距 + 内容框
//top:元素的上边界和父元素上边界的距离
//left:元素的左边界和父元素左边界的距离
//right:元素的右边界和父元素的左边界的距离
//bottom:元素的下边界和父元素上边界的距离
```

5、上边偏移量，左边的偏移量

```
element.offsetTop
element.offsetLest
```

6、可视区域的大小

```
document.documentElement.clientWidth
document.documentElement.clientHeight
```

7、页面的实际大小

```
document.documentElement.scrollWidth
document.documentElement.scrollHeight
```

8、窗口左上角 与 屏幕左上角的 距离

```
window.screenX
window.screenY
```

9、屏幕宽高

```
window.screen.width
window.screen.height
```

10、屏幕可用宽高（去除任务栏）

```
window.screen.availWidth
window.screen.availHeight
```

11、窗口的内高度、内宽度（文档显示区域+滚动条）

```
window.innerWidth
window.innerHeight
```

12、窗口的外高度、外宽度

```
window.outerWidth
window.outerHeiht
```

