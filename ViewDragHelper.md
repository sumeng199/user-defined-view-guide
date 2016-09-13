# ViewDragHelper
---
在项目中很多场景需要用户手指拖动其内部的某个View，此时就需要在 onInterceptTouchEvent() 和 onTouchEvent() 这两个方法中写不少逻辑了，比如处理：拖拽移动，越界，多手指的按下，加速度检测等等。

ViewDragHelper可以极大的帮我们简化类似的处理，它提供了一系列用于处理用户拖拽子View的辅助方法和与其相关的状态记录。比较常见的：QQ侧滑菜单，Navigation Drawer的边缘滑动，都可以由它实现。

## 使用示例
```python
public class MyLinearLayout extends LinearLayout {
    private ViewDragHelper mViewDragHelper;

    public MyLinearLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
        initViewDragHelper();
    }

    //初始化ViewDragHelper
    private void initViewDragHelper() {
        mViewDragHelper = ViewDragHelper.create(this, 1.0f, new ViewDragHelper.Callback() {
            @Override
            public boolean tryCaptureView(View child, int pointerId) {
                return true;
            }

            //处理水平方向的越界
            @Override
            public int clampViewPositionHorizontal(View child, int left, int dx) {
                int fixedLeft;
                View parent = (View) child.getParent();
                int leftBound = parent.getPaddingLeft();
                int rightBound = parent.getWidth() - child.getWidth() - parent.getPaddingRight();

                if (left < leftBound) {
                    fixedLeft = leftBound;
                } else if (left > rightBound) {
                    fixedLeft = rightBound;
                } else {
                    fixedLeft = left;
                }
                return fixedLeft;
            }

            //处理垂直方向的越界
            @Override
            public int clampViewPositionVertical(View child, int top, int dy) {
                int fixedTop;
                View parent = (View) child.getParent();
                int topBound = getPaddingTop();
                int bottomBound = getHeight() - child.getHeight() - parent.getPaddingBottom();
                if (top < topBound) {
                    fixedTop = topBound;
                } else if (top > bottomBound) {
                    fixedTop = bottomBound;
                } else {
                    fixedTop = top;
                }
                return fixedTop;
            }

            //监听拖动状态的改变
            @Override
            public void onViewDragStateChanged(int state) {
                super.onViewDragStateChanged(state);
                switch (state) {
                    case ViewDragHelper.STATE_DRAGGING:
                        System.out.println("STATE_DRAGGING");
                        break;
                    case ViewDragHelper.STATE_IDLE:
                        System.out.println("STATE_IDLE");
                        break;
                    case ViewDragHelper.STATE_SETTLING:
                        System.out.println("STATE_SETTLING");
                        break;
                }
            }

            //捕获View
            @Override
            public void onViewCaptured(View capturedChild, int activePointerId) {
                super.onViewCaptured(capturedChild, activePointerId);
                System.out.println("ViewCaptured");
            }

            //释放View
            @Override
            public void onViewReleased(View releasedChild, float xvel, float yvel) {
                super.onViewReleased(releasedChild, xvel, yvel);
                System.out.println("ViewReleased");
            }
        });
    }

    //将事件拦截交给ViewDragHelper处理
    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        return mViewDragHelper.shouldInterceptTouchEvent(ev);
    }


    //将Touch事件交给ViewDragHelper处理
    @Override
    public boolean onTouchEvent(MotionEvent ev) {
        mViewDragHelper.processTouchEvent(ev);
        return true;
    }
}
```
从这个例子可以看出来ViewDragHelper是作用在ViewGroup上的(比如LinearLayout)而不是直接作用到某个被拖拽的子View。其实这也不难理解，因为子View在布局中的位置是其所在的ViewGroup决定的。

## 所做的主要操作
> * 接管了ViewGroup的事件拦截
> * 接管了ViewGroup的Touch事件
> * 处理了拖拽子View时的边界越界
> * 监听拖拽子View时的状态变化

除了这些常见的操作，ViewDragHelper还可以实现：抽屉拉伸，拖拽结束松手后子View自动返回到原位等复杂操作。
