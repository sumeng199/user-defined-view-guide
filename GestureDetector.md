# GestureDetector
---
Android系统给我们提供的一个手势处理的监听工具类。代替我们自己在onTouchEvent()中做的手势处理。利用该类可以简化许多操作，轻松实现一些常用的功能。

## 使用步骤
### 1. 实现OnGestureListener
```python
private class GestureListenerImpl implements GestureDetector.OnGestureListener {
        //触摸屏幕时均会调用该方法
        @Override
        public boolean onDown(MotionEvent e) {
            System.out.println("---> 手势中的onDown方法");
            return false;
        }

        //手指在屏幕上拖动时会调用该方法
        @Override
        public boolean onFling(MotionEvent e1,MotionEvent e2, float velocityX,float velocityY) {
            System.out.println("---> 手势中的onFling方法");
            return false;
        }

        //手指长按屏幕时均会调用该方法
        @Override
        public void onLongPress(MotionEvent e) {
            System.out.println("---> 手势中的onLongPress方法");
        }

        //手指在屏幕上滚动时会调用该方法
        @Override
        public boolean onScroll(MotionEvent e1,MotionEvent e2, float distanceX,float distanceY) {
            System.out.println("---> 手势中的onScroll方法");
            return false;
        }

        //手指在屏幕上按下,且未移动和松开时调用该方法
        @Override
        public void onShowPress(MotionEvent e) {
            System.out.println("---> 手势中的onShowPress方法");
        }

        //轻击屏幕时调用该方法
        @Override
        public boolean onSingleTapUp(MotionEvent e) {
            System.out.println("---> 手势中的onSingleTapUp方法");
            return false;
        }
    }
```
### 2. 生成GestureDetector对象
```python
GestureDetector gestureDetector = new GestureDetector(context, new GestureListenerImpl()
);
```
这里的GestureListenerImpl就是GestureListener监听器的实现

### 3. 将Touch事件交给GestureDetector处理
比如将 **Activity** 的Touch事件交给GestureDetector处理
```python
@Override  
public boolean onTouchEvent(MotionEvent event) {  
     return mGestureDetector.onTouchEvent(event);  
}
```
比如将 **View** 的Touch事件交给GestureDetector处理
```python
mButton=(Button) findViewById(R.id.button);  
mButton.setOnTouchListener(new OnTouchListener() {            
   @Override  
   public boolean onTouch(View arg0, MotionEvent event) {  
          return mGestureDetector.onTouchEvent(event);  
      }  
});  
```
