### Listview 、RecycleView 缓存机制

recycleVIew 四级缓存，其缓存管理类是 recycler ，来看看这个类：

```
public final class Recycler {
    final ArrayList<ViewHolder> mAttachedScrap = new ArrayList<>();
    ArrayList<ViewHolder> mChangedScrap = null;

    final ArrayList<ViewHolder> mCachedViews = new ArrayList<ViewHolder>();

    RecycledViewPool mRecyclerPool;

    private ViewCacheExtension mViewCacheExtension;

    static final int DEFAULT_CACHE_SIZE = 2;
```

**Scrap** 

意思是废弃，包含两个mAttachedScrap 和 mChangedScrap ，主要是快速重用屏幕上可见的列表项ItemView，而不需要重新createView和bindView；

**mCachedViews**

滑动时刚被移出屏幕的viewholer的缓存；默认大小为2，这个大小可以设置。

**mViewCacheExtension**

自定义的缓存，一般使用较少; 使用此缓存需要实现其他的很多东西，不太方便。

```
public abstract static class ViewCacheExtension {

    /**
     * Returns a View that can be binded to the given Adapter position.
     * <p>
     * This method should <b>not</b> create a new View. Instead, it is expected to return
     * an already created View that can be re-used for the given type and position.
     * If the View is marked as ignored, it should first call
     * {@link LayoutManager#stopIgnoringView(View)} before returning the View.
     * <p>
     * RecyclerView will re-bind the returned View to the position if necessary.
     *
     * @param recycler The Recycler that can be used to bind the View
     * @param position The adapter position
     * @param type     The type of the View, defined by adapter
     * @return A View that is bound to the given position or NULL if there is no View to re-use
     * @see LayoutManager#ignoreView(View)
     */
    public abstract View getViewForPositionAndType(Recycler recycler, int position, int type);
}
```

**mRecyclerPool**

用于存放无效的viewholder。这个不同于可扩展extention 缓存，这个内部类，google 写好了其他的逻辑，一般的，pool 一般和 cache 配合使用。

我们还要注意一个方法：tryGetViewHolderForPositionByDeadline ()  这个方法就是获取view 的核心方法，会按照下面的逻辑去获取；

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gha75zhzy8j30fb0sxq7g.jpg" alt="image-20200731154147705"  />

**对比ListView**

```
class RecycleBin {
  
    /**
     * Views that were on screen at the start of layout. This array is populated at the start of
     * layout, and at the end of layout all view in mActiveViews are moved to mScrapViews.
     * Views in mActiveViews represent a contiguous range of Views, with position of the first
     * view store in mFirstActivePosition.
     */
    private View[] mActiveViews = new View[0];

    /**
     * Unsorted views that can be used by the adapter as a convert view.
     */
    private ArrayList<View>[] mScrapViews;
```

这是AbsListVIew 的一个内部类，mActiviews 相当于 mScrapVIew ，负责屏幕内的view 缓存； mScrapVIew 相当于recycleview的pool 和cache ，负责屏幕外的itemView 重用。

参考文章：

[连接1](https://mp.weixin.qq.com/s?__biz=MzA3NTYzODYzMg==&mid=2653578065&idx=2&sn=25e64a8bb7b5934cf0ce2e49549a80d6&chksm=84b3b156b3c43840061c28869671da915a25cf3be54891f040a3532e1bb17f9d32e244b79e3f&scene=4#wechat_redirect)

[连接2](https://mp.weixin.qq.com/s/Qey-3JDdKYG04mo9WeBZ2g)



### setContentView 说起

主要的逻辑在phoneWindow 类里面

![截屏2020-07-31 下午8.10.43](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghaeyjquz6j310a0u011q.jpg)

一个Activity启动后，

1. 首先实例化PhoneWindow对象，调用setContentView时，首先执行installDecor()，
2. 通过generateDecor()实例化一个DecorView对象，在Decorview构造方法中，将PhoneWindow和DecorView进行了关联绑定，
3. 通过generateLayout(mDecor)加载系统布局到DecorView上，并将ID为content的FrameLayout赋值给mContentParent，再把contentParent 绑定到decorVIew 这样就形成了顶层是Decorview，我们的view 布局就是系统布局的id 为content的view。

### 布局加载原理 LayoutInflator

![image-20200805154845398](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghfzgqcevej31t60i67bm.jpg)

布局加载流程主要分为**加载解析xml文件**和**填充View树**两部分；



其实这个原理 只要追踪源码可以发现，流程很清晰，不复杂。

// 第一步：加载布局

mLayoutInflater.inflate——

// 第二步就是得到xml解析器XmlResourceParse 在用这个解析器解析layout

XmlResourceParser parser = res.getLayout(resource) ——loadXmlResourceParser(id, "layout") 

// 第三步：填充view树，反射方式创建view；

—— createViewFromTag (root, name, inflaterContext, attrs) 【先解析跟标签，判断是不是merge标签，是的话特殊处理，在去判断是不是include 标签】—— createView（）—— 最后反射创建view 

[参考文章](https://www.jianshu.com/p/8ca35e86d476)

