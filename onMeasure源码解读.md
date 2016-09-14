# [measure](http://blog.csdn.net/lfdfhl/article/details/51347818)
---
自定义View三大步骤之首。许多一知半解的问题
> * 为什么父 View影响到了子View 的 MeasureSpec的生成?
> * 为什么我们自定义一个View在布局时将其宽或者高指定为wrap_content但是其实际是match_parent的效果？
> * 子View的specMode和specSize的生成依据又是什么？

## MeasureSpec 基础知识
系统显示一个View，首先需要通过测量(measure)该View来知晓其长和宽从而确定显示该View时需要多大的空间。在测量的过程中MeasureSpec贯穿全程，发挥着不可或缺的作用。
> * MeasureSpec封装了父布局传递给子View的布局要求
> * MeasureSpec可以表示宽和高
> * MeasureSpec由 size 和 mode组成

MeasureSpec通常翻译为 **测量规格**,它是一个 32位的int数据.
其中高2位代表SpecMode即某种测量模式，低30位为SpecSize代表在该模式下的规格大小.

可以通过如下方式分别获取这两个值：
```python
int specSize = MeasureSpec.getSize(measureSpec);
int specMode = MeasureSpec.getMode(measureSpec);
```
也可以通过这两个值生成新的MeasureSpec
```python
int measureSpec = MeasureSpec.makeMeasureSpec(size, mode);
```
### SpecMode三种模式

#### 1. MeasureSpec.EXACTLY
> 父容器已经检测出子View 所需要的精确大小，在该模式下，View的测量大小即为SpecSize

#### 2. MeasureSpec.AT_MOST
> 父容器未能检测出子View所需要的精确大小，但是指定了一个可用大小即 specSize，该模式下，View的测量大小不能超过SpecSize

#### 3. MeasureSpec.UNSPECIFIED
> 父容器不对子View的大小做限制，这种模式一般用作Android系统内部，或者ListView 和 ScrollView 等滑动控件。不做讨论。

### 具体使用

#### 1. 使用 MeasureSpec.EXACTLY的情况
> * 当子View的LayoutParams的宽(高)采用具体的值(如100px)时且父容器的MeasureSpec为MeasureSpec.EXACTLY或者MeasureSpec.AT_MOST或者MeasureSpec.UNSPECIFIED时：系统返回给该子View的specMode就为MeasureSpec.EXACTLY，系统返回给该子View的specSize就为子View自己指定的大小(childSize)。

> * 当子View的LayoutParams的宽(高)采用match_parent时并且父容器的MeasureSpec为MeasureSpec.EXACTLY时：系统返回给该子View的specMode就为 MeasureSpec.EXACTLY，系统返回给该子View的specSize就为该父容器剩余空间的大小(parentLeftSize)。

#### 2. 使用 MeasureSpec.AT_MOST 的情况
> * 当子View的LayoutParams的宽(高)采用match_parent并且父容器的MeasureSpec为MeasureSpec.AT_MOST时：系统返回给该子View的specMode就为MeasureSpec.AT_MOST，系统返回给该子View的specSize就为该父容器剩余空间的大小(parentLeftSize)

> * 当子View的LayoutParams的宽(高)采用wrap_content时并且父容器的MeasureSpec为MeasureSpec.EXACTLY时：系统返回给该子View的specMode就为 MeasureSpec.AT_MOST，系统返回给该子View的specSize就为该父容器剩余空间的大小(parentLeftSize)
> * 当子View的LayoutParams的宽(高)采用wrap_content时并且父容器的MeasureSpec为MeasureSpec.AT_MOST时:系统返回给该子View的specMode就为MeasureSpec.AT_MOST，系统返回给该子View的specSize就为该父容器剩余空间的大小(parentLeftSize)

### measureChildWithMargins( )和measureChild( )有什么相同点和区别呢？
> * measureChildWithMargins( )和measureChild( )均用来测量子View的大小
> * 两者在调用getChildMeasureSpec( )均要计算父View已占空间
> * 在measureChild( )计算父View所占空间为mPaddingLeft + mPaddingRight，即父容器左右两侧的padding值之和
> * measureChildWithMargins( )计算父View所占空间为mPaddingLeft + mPaddingRight + lp.leftMargin + lp.rightMargin+ widthUsed。此处，除了父容器左右两侧的padding值之和还包括了子View左右的margin值之和( lp.leftMargin + lp.rightMargin)，因为这两部分也是不能用来摆放子View的应算作父View已经占用的空间。这点从方法名measureChildWithMargins也可以看出来它是考虑了子View的margin所占空间的。

### 开篇问题解决
- 1， 子View在XML布局文件中对于大小的设置采用wrap_content，那么不管父View的specMode是MeasureSpec.AT_MOST还是MeasureSpec.EXACTLY对于子View而言系统给它设置的specMode都是MeasureSpec.AT_MOST，并且其大小都是parentLeftSize即父View目前剩余的可用空间。这时wrap_content就失去了原本的意义，变成了match_parent一样了.

所以自定义View在重写onMeasure()的过程中应该手动处理View的宽或高为wrap_content的情况。

#### 处理方法
> * 如果在xml布局中View的宽和高均用wrap_content.那么需要设置
View的宽和高为mWidth和mHeight.
> * 如果在xml布局中View的宽或高其中一个为wrap_content,那么就将该值设置为默认的宽或高,另外的一个值采用系统测量的specSize即可.

```python
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    super.onMeasure(widthMeasureSpec , heightMeasureSpec);  
    int widthSpecMode = MeasureSpec.getMode(widthMeasureSpec);  
    int widthSpceSize = MeasureSpec.getSize(widthMeasureSpec);  
    int heightSpecMode=MeasureSpec.getMode(heightMeasureSpec);  
    int heightSpceSize=MeasureSpec.getSize(heightMeasureSpec);  

    if(widthSpecMode==MeasureSpec.AT_MOST&&heightSpecMode==MeasureSpec.AT_MOST){  
        setMeasuredDimension(mWidth, mHeight);  
    }else if(widthSpecMode==MeasureSpec.AT_MOST){  
        setMeasuredDimension(mWidth, heightSpceSize);  
    }else if(heightSpecMode==MeasureSpec.AT_MOST){  
        setMeasuredDimension(widthSpceSize, mHeight);  
    }  
 }  
```
