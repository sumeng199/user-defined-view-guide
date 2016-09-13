# Scroller
---
Scroller常见的滚动控制类

## 常见重要问题
### 1. scrollTo() 和 scrollBy() 的关系
scrollBy()的源码
```python
public void scrollBy(int x, int y) {   
       scrollTo(mScrollX + x, mScrollY + y);   
}
```
### 2. scroll 的本质
scrollTo( )和scrollBy( )移动的只是View的内容，而且View的背景是不移动的。

### 3. scrollTo( )和scrollBy( )方法的坐标说明
比如我们对于一个TextView调用scrollTo(0,25) ；那么该TextView中的content(比如显示的文字:Hello)会怎么移动呢?
向下移动25个单位？不！恰好相反！！这是为什么呢?
因为调用该方法会导致视图重绘，即会调用
```python
public void invalidate(int l, int t, int r, int b)
```
此处l,t, r, b 四个参数表示View原来的坐标。
在该方法中最终会调用：
```python
tmpr.set(l - scrollX, t - scrollY, r - scrollX, b - scrollY);
p.invalidateChild(this, tmpr);
```
其中tmpr是一个Rect，this是原来的View；通过这两行代码就把View在一个Rect中重绘。
请注意第一行代码:
原来的l和r均减去了scrollX
原来的t和b均减去了scrollY
就是说scrollX如果是正值,那么重绘后的View的宽度反而减少了;反之同理
就是说scrollY如果是正值,那么重绘后的View的高度反而减少了;反之同理
所以，TextView调用scrollTo(0,25)和我们的理解相反

scrollBy(int x,int y)方法与上类似,不再多说了.
