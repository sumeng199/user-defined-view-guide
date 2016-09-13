# ViewConfiguration
----
ViewConfiguration提供了一些自定义控件用到的标准常量，如尺寸大小，滑动距离，敏感度等等。可以利用ViewConfiguration的静态方法获取一个实例：
```python
ViewConfiguration viewConfiguration = ViewConfiguration.get(context);
```
#### 几个对象方法
```python
// 获取touchSlop。该值表示系统所能识别出的被认为是滑动的最小距离
int touchSlop = viewConfiguration.getScaledTouchSlop();

// 获取Fling速度的最小值和最大值
int minimumVelocity = viewConfiguration.getScaledMinimunFlingVelocity();
int maximumVelocity = viewConfiguration.getScaledMaximumFlingVelocity();

// 判断是否有物理键
boolean isHavePermanentMenuKey = viewConfiguration.hasPermanentMenuKey();
```
#### 有用的静态方法
```python
// 双击间隔时间，在该时间内是双击，否则是单机
int doubleTapTimeOut = ViewConfiguration.getDoubleTapTimeout();

// 按住状态转变为长按状态需要的时间
int longPressTimeout = ViewConfiguration.getLongPressTimeOut();

//重复按键的时间
int keyRepeatTimeout = ViewConfiguration.getKeyRepeatTimeout();
```
