## constraintaLayout

### 参考文章
1.https://developer.android.google.cn/training/constraint-layout/#adjust-the-constraint-bias  
2.https://developer.android.google.cn/reference/android/support/constraint/ConstraintLayout#VisibilityBehavior   
3.https://blog.csdn.net/zxt0601/article/details/72736802   
4.https://blog.csdn.net/guolin_blog/article/details/53122387

> 两篇官方文档，非常详细，第一参考资料，google浏览器打开，非常详细。第三篇有demo和中文解释，第四篇为郭神的，一些技巧，但都在官方文档可以找到。所以本文就重点在于记录一些自己学习的时候比较模糊和理解错的地方。

### 学习思路

类比其他布局，linearLayout(权重思想) ，RelativeLayout（参考系思想）,这样理解就很方便了。


1. 版本：最好是1.1之后的，因为之前的有很多属性都没有加，之后的版本才有，这是一个分水岭；
2. 每个视图都有一个水平和垂直的约束，每个约束表示与另一个视图，父布局或不可见指南的连接或对齐。每个约束定义视图沿垂直轴或水平轴的位置;使用该布局时，可以利用IDE去减少我们的工作，参考郭霖文章和google的讲述；
3. 理解layout_goneMarginLeft（right/top等）：当参考系view不见了gone，layout_goneMarginLeft属性才有作用；可以参考自己的demo。
4. 引入角度参考系：利用圆来进行view定位，要记住参考view的中心点作为圆心的；
5. 可以设置guideline 来作为参考，不是非得parent。
6. 引入chain的概念；
7. 可以用view的自身宽高比限定长宽；
8. constraint中android:layout_width和 android:layout_height 值有：
> - 使用特定维度（文字值，例如123dp或Dimension参考）
> - 使用WRAP_CONTENT，这将要求小部件计算自己的大小
> - 使用0dp，相当于“ MATCH_CONSTRAINT”

前两个以与其他布局类似的方式工作。最后一个(0dp)将以匹配所设置的约束的方式调整窗口小部件的大小。如果设置了边距，则在计算中将考虑它们.  好好理解第8点，很重要。如果不明白可以看demo 和第三篇文章。

### 调整视图尺寸

- ![img](https://developer.android.google.cn/studio/images/buttons/layout-width-fixed.png) **Fixed**：具体的尺寸 ==（在使用0dp 时要注意，0dp 在约束条件下其效果会变化）==
- ![img](https://developer.android.google.cn/studio/images/buttons/layout-width-wrap.png)**Wrap Content**：视图仅在需要时扩展以适应其内容。
- ![img](https://developer.android.google.cn/studio/images/buttons/layout-width-match.png) **Match Constraints**：视图会尽可能扩展，以满足每侧的约束条件（在考虑视图的外边距之后）。不过，您可以使用以下属性和值修改该行为（这些属性仅在您将视图宽度设置为“match constraints”时才会生效）
  - layout_constraintWidth_default
    - **spread**：尽可能扩展视图以满足每侧的约束条件。这是默认行为。
    - **wrap**：仅在需要时扩展视图以适应其内容，但如有约束条件限制，视图仍然可以小于其内容。因此，它与使用 **Wrap Content**（上面）之间的区别在于，将宽度设为 **Wrap Content** 会强行使宽度始终与内容宽度完全匹配；而使用 **layout_constraintWidth_default** 设置为 **wrap** 的 **Match Constraints** 时，视图可以小于内容宽度。
  - layout_constraintWidth_min ：最小宽度dp 。
  - **layout_constraintWidth_max** ：

**注意**：您无法将 `match_parent` 用于 `ConstraintLayout` 中的任何视图。请改用“match constraints” (`0dp`)。

> 这句话是官方说的，其实可以用。如果（宽、高）用了match-parent ，则该宽、高就是固定的宽高、不会进行适应。其效果如下
>
> ![image-20200624205735380](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg3od3qu8dj31uc0u04qp.jpg)
>
> 

Guideline

```
  <!-- 百分比的guideline -->
    <androidx.constraintlayout.widget.Guideline
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        app:layout_constraintGuide_percent="0.2" />

    <!-- 距离的guideline-->
    <androidx.constraintlayout.widget.Guideline
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintGuide_begin="30dp" />
```

效果图

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gg3p10s2ppj30lw0keq5h.jpg" alt="image-20200624212034334" style="zoom:67%;" />



这样其他的控件就可以进行已它为参考进行约束。



### chain

链使我们能够对一组在==水平或竖直==方向互相关联的控件的属性进行统一管理。成为链的条件：**一组控件它们通过一个双向的约束关系链接起来。** **链的属性是由一条链的头结点控制的**

![](https://developer.android.google.cn/training/constraint-layout/images/constraint-chain_2x.png)

控件A B 相互依赖约束，形成链。然后这个链的属性放到第一个控件下，这样就可以了。

![链的属性值](https://developer.android.google.cn/training/constraint-layout/images/constraint-chain-styles_2x.png)

链的属性：layout_constraintHorizontal/vitechl_chainStyle

1. **Spread**：视图是均匀分布的（在考虑外边距之后）。这是默认值。
2. **Spread inside**：第一个和最后一个视图固定在链两端的约束边界上，其余视图均匀分布。
3. **Weighted**：当链设置为 **spread** 或 **spread inside** 时，您可以通过将一个或多个视图设置为“match constraints”(`0dp`) 来填充剩余空间。默认情况下，设置为“match constraints”的每个视图之间的空间均匀分布，但您可以使用 `layout_constraintHorizontal_weight` 和 `layout_constraintVertical_weight` 属性为每个视图分配重要性权重。如果您熟悉[线性布局](https://developer.android.google.cn/guide/topics/ui/layout/linear)中的 `layout_weight` 的话，就会知道该样式与它的原理是相同的。因此，权重值最高的视图获得的空间最大；相同权重的视图获得同样大小的空间。
4. **Packed**：视图打包在一起（在考虑外边距之后）

`layout_constraintHorizontal_weight` 等价于 线性布局里面的 比例 。

### 



### Circular positioning

方向定位布局，可以实现其他布局难以实现的效果，比如星球的运动轨迹，[链接]( https://medium.com/devnibbles/constraintlayout-circular-positioning-9489b11cb0e5) ；[圆形菜单](https://blog.csdn.net/xuyonghong1122/article/details/82185347)  。该特性重点有3个属性

- **layout_constraintCircle** ：依赖谁的id
- **layout_constraintCircleRadius** ：the distance to the other widget center 距离
- **layout_constraintCircleAngle** ：角度，从0-360，0 为正上方；

![角度布局-坐标说明](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg3h49glrej316y0hmq8m.jpg)



```java
<Button android:id="@+id/buttonA" ... />
  <Button android:id="@+id/buttonB" ...
      app:layout_constraintCircle="@+id/buttonA"
      app:layout_constraintCircleRadius="100dp"
      app:layout_constraintCircleAngle="45" />
         
```

利用这个角度布局，可以实现那种比较刁专的布局，即view 可以层叠。因为lienarLayout 在同一层级，不能实现view 的重叠，relative可以，但是没有角度，这个布局很好的解决了这个问题。

> 这里有一个问题，就是如果只写circle 依赖，没有其他的依赖，编译器会报错，但实际可以运行。



![只有circle约束时，编译报错](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg3h4f6k4dj30vs0imadj.jpg)



### Ratio 

```java
app:layout_constraintDimensionRatio
```

宽高比 约束。先看看官方文档的介绍：

> You can also define one dimension of a widget as a ratio of the other one. In order to do that, you need to have at least one constrained dimension be set to `0dp` (i.e., `MATCH_CONSTRAINT`), and set the attribute `layout_constraintDimensionRatio` to a given ratio.
>
> The ratio can be expressed either as:
>
> - a float value, representing a ratio between width and height
> - a ratio in the form "width:height"

从这里一定记住 

1. **layout-constraintDimensionRatio = width ： heigt **
2. 至少一个维度是 0dp

如果只有一条边是 0dp ，我们怎么去确定宽、高具体的高度多少？

- 宽 0dp，高度确定h，根据 w/ h = ratio，算出w；
- 高 0dp，宽度确定w，根据 w/ h = ratio，算出h；

由此，只有一条边时，0dp 的那个维度是不确定，由比例和另一条边来确定0dp；

如果两条边都是0dp 呢？

这时候只有一个比例，我们必须要有一个维度的长度，才能算出另一个维度；这时候就需要其他的约束来确定长度，要用约束条件约束该维度的两边，即宽度的约束就要指出 left-toleft和right-toRight ，这时候宽度就已经知道了，然后在通过比例计算高度；此时**比例要指出约束的，添加h或w，比如 “h，2:1”**

```java
<Button
    android:id="@+id/btn6"
    android:layout_width="0dp"
    android:layout_height="0dp"
    android:padding="10dp"
    android:text="宽高比ratio 两个0dp"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    app:layout_constraintTop_toBottomOf="@+id/btn5"
    app:layout_constraintDimensionRatio="h,2:1"
    />
```

或者说两边都是match constraint 0dp 时，比例中要指定被约束的维度 h或w，指定哪个，哪个维度就需要通过另外的约束来间接的确定长度。这里宽度其实是

``` 
 app:layout_constraintRight_toRightOf="parent"    app:layout_constraintLeft_toLeftOf=&quot;parent&quot;
```

条件确定下来了。 



###  app:layout_constrainedWidth/height="true/false"

先看两个view 的布局情况，

![image-20200625112523048](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg4dg31vyaj31z20tue5w.jpg)

![image-20200625112725593](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg4di89eu0j31zk0qy7t7.jpg)

对比上面两个图，发现该属性的作用，为了防止左右的约束失效，可以使用该属性。==一定是左右约束属性都要添加才可以==。

所以该属性的作用就是防止约束失效。



### Barrier

Barrier引用多个小部件作为输入，并创建一条基于指定侧的最极端的控件 作为参考控件；

![image-20200625225251226](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg4xbf1jbbj319v0u0e81.jpg)

 

比如 这里右边的textview 就是以两个button 最右侧的控件作为参考控件。

```
app:barrierDirection="right"
app:constraint_referenced_ids="btn01,btn02"
```

这两个控件属性比较重要，一个是指明方向，一个是barrier 引入参考的控件，然后其他以barrier 为参考的控件就会从里面找出符合条件的控件。



