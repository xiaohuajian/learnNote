# CoordinatorLayout配合AppBarLayout、CollapsingToolbarLayout

实现那种滑动之后会隐藏、变化的布局，我们可以通过CoordinatorLayout 配合 AppBarLayout、CollapsingToolbarLayout实现；其实现思路和注意点有：

实现代码：

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/app_bar"
        android:layout_width="match_parent"
        android:layout_height="@dimen/app_bar_height"
        android:fitsSystemWindows="true">
        <com.google.android.material.appbar.CollapsingToolbarLayout
            android:id="@+id/toolbar_layout"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:contentScrim="@color/red"
            app:layout_scrollFlags="scroll|exitUntilCollapsed|snap">

            <ImageView
                android:scaleType="centerCrop"
                android:src="@drawable/student_bg_paper_requirement_person"
                android:layout_width="match_parent"
                app:layout_collapseMode="parallax"
                android:layout_height="300dp" />

            <androidx.appcompat.widget.Toolbar
                android:id="@+id/toolbar"
                app:layout_collapseMode="pin"
                android:layout_width="match_parent"
                android:minHeight="?attr/actionBarSize"
                android:layout_height="?attr/actionBarSize"/>
            <!-- 这里也可以不用 toolbar ，可以自己定义类似toobar的view或者只用一个textview-->
            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:text="我是textview"
                android:padding="10dp"
                android:textSize="16sp"
                android:minHeight="?attr/actionBarSize"
                />

        </com.google.android.material.appbar.CollapsingToolbarLayout>

    </com.google.android.material.appbar.AppBarLayout>

   <!-- 这是下面的展示部分-->
    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"

        app:layout_behavior="@string/appbar_scrolling_view_behavior"
        android:layout_height="wrap_content">
        <TextView
            android:text="@string/test"
            android:textSize="20sp"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />

    </androidx.core.widget.NestedScrollView>


</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

使用注意点：

**CoordinatorLayout**：

- 是一个FrameLayout

**AppBarLayout**：

- 是一个vertical的LinearLayout，AppBarLayout下方的滑动控件，比如RecyclerView，NestedScrollView（与AppBarLayout同属于CoordinatorLayout的子View,并列的关系，）,必须严格地通过在xml中指出其滑动Behavior来与AppBarLayout进行绑定。**通常这样：`app:layout_behavior="@string/appbar_scrolling_view_behavior"` 这个属性一定不能忘记设置，否则无效**

**CollapsingToolbarLayout**:

- **是AppBarLayout的子View，不然不起作用**。它应通过 `setScrollFlags(int)`  或者xmL中的`app:layout_scrollFlags`来提供他们的Behavior。**一般的我们设置：app:layout_scrollFlags="scroll|exitUntilCollapsed“ 注意，这几个标志是多选的，不是只能选择一个，假如说只选择了scroll ，就不会折叠**

  > 具体的`app:layout_scrollFlags`有这么几个： 
  >
  > 1. scroll: 将此布局和滚动时间关联。这个标识要设置在其他标识之前，没有这个标识则布局不会滚动且其他标识设置无效。
  > 2. enterAlways:任何向下滚动操作都会使此布局可见。这个标识通常被称为“快速返回”模式。
  > 3. enterAlwaysCollapsed：假设你定义了一个最小高度（minHeight）同时enterAlways也定义了，那么view将在到达这个最小高度的时候开始显示，并且从这个时候开始慢慢展开，当滚动到顶部的时候展开完。
  > 4. exitUntilCollapsed：当你定义了一个minHeight，此布局将在滚动到达这个最小高度的时候折叠。
  > 5. snap:当一个滚动事件结束，如果视图是部分可见的，那么它将被滚动到收缩或展开。例如，如果视图只有底部25%显示，它将折叠。相反，如果它的底部75%可见，那么它将完全展开。

- 内部的子View一般都要加上属性：app:layout_collapseMode=""，常用的是默认值 off，parallax，pin。parallax是视差滚动，用在imageView, pin是固定，用在toolbar。

  > 1. off：这个是默认属性，布局将正常显示，没有折叠的行为。
  > 2. pin：CollapsingToolbarLayout折叠后，此布局将固定在顶部。
  > 3. parallax：CollapsingToolbarLayout折叠时，此布局也会有视差折叠效果。用在滚动的，比如上面的imageView

- 用`setContentScrim(drawable/color)`来设置内容纱布，就是当折叠到只剩下Toolbar的时候，用图片或者颜色来设置toolbar的背景。这个也可以在xml设置；

**Toolbar**: 

​	这个控件其实不一定需要，因为它就是充当了一个title的作用，换成其他的view 也可以的。



另外，我们可以通过androidStudio 的模版的acitivity 创建一个scrollActivity 来创建相关的Demo，并好好看一下。可以参考自己的demo—— CoordinatorLayoutDemo 工程 （Mac 电脑下的）