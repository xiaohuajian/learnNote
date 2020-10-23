## LayoutParams

当我们在代码中动态添加view时，需要设置params，这时候需要明白一些知识点。

只有viewGroup才有这个属性，普通view 没有这个属性。这是因为view的测量需要父view的Layout 和子view 的大小才能确定最终的大小；运用这个`ayoutparam` 有几种情况（对某个viewGroup （如ReletiveLayout、FrameLayout）添加一个子Textview；

1. **这个子view之前是没有存在xml，纯粹是靠代码进行添加**，可以先构造一个对应viewGroup的layoutparams，然后根据需求设置params，设置好了，最后给子view 。(**(这里的LayoutParam 一定要是)**)

   ```
    //对该viewGroup 构造一个layoutParam ，这个时候宽高的设置需要根据我们期望子view采用什么宽高而定
   LinearLayout.LayoutParams layoutParams = new LinearLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.WRAP_CONTENT);
    // 设置其他的属性
   layoutParams.topMargin = Dp2PxUtil.dip2px(mContext, 10);
   //把params 赋给 子view
   view.setLayoutParams(layoutParams);
   ```

   

2. **这个子view 已经存在xml，我们想动态改变布局参数**，比如之前在列表中，我们根据不同的条件，做出不同的布局。RetiveLayout 中，距离顶部距离marginTop从20dp，改为30dp；这时候我们没有必要new 一个LayoutParams 了，只需要通过view.getLayoutParams 即可。这样的好处是只需改变我们想要改的约束设置，其他的约束条件不变。如果new LayoutParams的话，就需要把xml其他的约束在用代码写一遍。

   ```
   (divideView.layoutParams as? ConstraintLayout.LayoutParams)?.let {
               it.topToBottom = R.id.cl_content
               it.leftToLeft = R.id.cl_content
           }
   ```


总结一下：LayoutParam

- 凭空在某个viewGroup添加一个view，这个view 对应的LayoutParam一定要是该viewGroup，比如说LinearLayout 的viewGroup中添加一个view，给该view设置LayoutParams 时就要用LinearLayoutParam, 这样最好，也可以用其他的LayoutParam 但这样不怎么好；同时，凭空添加还包括inflator 一个布局，这种情况和new 一个view本质一样的，layoutParam一样处理。

- 如果修改某个view的LayoutParams 就可以按照第二点处理；

另外总结一下RelativeLayout的使用：

```
RelativeLayout.LayoutParams layoutParams = (RelativeLayout.LayoutParams) tv.getLayoutParams();
        layoutParams.width = RelativeLayout.LayoutParams.MATCH_PARENT;
        layoutParams.topMargin = dip2px(this, 40);
        layoutParams.addRule(RelativeLayout.ALIGN_PARENT_BOTTOM, RelativeLayout.TRUE);
        layoutParams.addRule(RelativeLayout.ALIGN_LEFT, R.id.ref1);
        // 就这么几个了，没有其他了
```

LinearLayoutParam的：

![image-20200928155439190](https://tva1.sinaimg.cn/large/007S8ZIlly1gj6f3imiiuj30mb0at40z.jpg)

其实，两个LayoutParam 都没有多少方法。

### getWidth、getMeasureWidth 区别

**getMeasuredWidth()获取的是view原始的大小，也就是这个view在XML文件中配置或者是代码中设置的大小。getWidth（）获取的是这个view最终显示的大小，这个大小有可能等于原始的大小也有可能不等于原始大小。**

getMeasureWidth 是在onMeasure 方法

```
public final int getMeasuredWidth() {
 2         return mMeasuredWidth & MEASURED_SIZE_MASK;
 3     }
 4 
 5   protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
 6         setMeasuredDimension(getDefaultSize(getSuggestedMinimumWidth(), widthMeasureSpec),
 7                 getDefaultSize(getSuggestedMinimumHeight(), heightMeasureSpec));
 8     }
    protected final void setMeasuredDimension(int measuredWidth, int measuredHeight) {
11         boolean optical = isLayoutModeOptical(this);
12         if (optical != isLayoutModeOptical(mParent)) {
13             Insets insets = getOpticalInsets();
14             int opticalWidth  = insets.left + insets.right;
15             int opticalHeight = insets.top  + insets.bottom;
16 
17             measuredWidth  += optical ? opticalWidth  : -opticalWidth;
18             measuredHeight += optical ? opticalHeight : -opticalHeight;
19         }
20         setMeasuredDimensionRaw(measuredWidth, measuredHeight);
21     }
22 
23   private void setMeasuredDimensionRaw(int measuredWidth, int measuredHeight) {
24         mMeasuredWidth = measuredWidth;
25         mMeasuredHeight = measuredHeight;
26 
27         mPrivateFlags |= PFLAG_MEASURED_DIMENSION_SET;
28     }

```

getMeasuredWidth（）获取的是mMeasuredWidth的这个值。这个值是一个8位的十六进制的数字，高两位表示的是这个measure阶段的Mode的值，具体可以查看MeasureSpec的原理。这里mMeasuredWidth & MEASURED_SIZE_MASK表示的是测量阶段结束之后，view真实的值。而且这个值会在调用了setMeasuredDimensionRaw（）函数之后会被设置。所以getMeasuredWidth（）的值是measure阶段结束之后得到的view的原始的值。

**getWidth** 

```
public final int getWidth() {  
      return mRight - mLeft;  
      // mRight、mLeft的值赋值是在layout过程中的setFrame（）->分析1
  }  

  /**
  * 分析1：setFrame（）
  * 作用：根据传入的4个位置值，设置View本身的四个顶点位置
  * 即：最终确定View本身的位置
  */ 
  protected boolean setFrame(int left, int top, int right, int bottom) {
        ...
    // 特别注意：就是这里赋值mRight、mLeft的
    // 通过以下赋值语句记录下了视图的位置信息，即确定View的四个顶点
    // 从而确定了视图的位置
    mLeft = left;
    mTop = top;
    mRight = right;
    mBottom = bottom;

    // setFrame（）的参数left、right是从在layout（）调用时传入的 -> 分析2

    }

}   
```

mRight和mLeft是在Layout 阶段设置的，是view 左右的边界坐标值的差，表示view 的实际显示大小。

#### 4.1 不相等情况

- 问：上面提到，一般情况下，二者获取的宽 / 高是相等的。那么，“非一般” 情况是什么？（即二者不相等）
- 答：人为设置：通过重写View的 layout（）强行设置

```java
@Override
public void layout( int l , int t, int r , int b){
   // 改变传入的顶点位置参数
   super.layout(l，t，r+100，b+100)；
}
```

- 效果：在任何情况下，getWidth() / getHeight()获得的宽/高 总比 getMeasuredWidth() / getMeasuredHeight()获取的宽/高大100px
- 即：View的最终宽/高 总比 测量宽/高 大100px

