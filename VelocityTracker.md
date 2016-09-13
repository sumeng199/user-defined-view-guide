# VelocityTracker
---
速度追踪，用于跟踪触摸屏事件（比如，Flinging及其他Gestures手势事件等）的速率。

## 常用套路
### 1. 开始速度追踪
```python
private void startVelocityTracker(MotionEvent event) {  
    if (mVelocityTracker == null) {  
         mVelocityTracker = VelocityTracker.obtain();  
     }  
     mVelocityTracker.addMovement(event);  
}  
```
这里我们初始化VelocityTracker, 并且把要追踪的MotionEvent注册到VelocityTracker的监听中。

### 2. 获取追踪到的速度
```python
private int getScrollVelocity() {  
     // 设置VelocityTracker单位.1000表示1秒时间内运动的像素  
     mVelocityTracker.computeCurrentVelocity(1000);  
     // 获取在1秒内X方向所滑动像素值  
     int xVelocity = (int) mVelocityTracker.getXVelocity();  
     return Math.abs(xVelocity);  
}
```
同理可以获取1秒内Y方向所滑动像素值

### 3. 解除速度追踪
```python
private void stopVelocityTracker() {  
      if (mVelocityTracker != null) {  
          mVelocityTracker.recycle();  
          mVelocityTracker = null;  
      }  
}  
```
