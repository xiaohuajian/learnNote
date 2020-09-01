# 作用域函数

link ：https://www.kotlincn.net/docs/reference/scope-functions.html

let 、 apply

let 操作符的作用

**场景一:** 最常用的场景就是使用let函数处理需要针对一个可null的对象统一做判空处理。

**场景二:** 用一个缩写 it 代替当前对象使用；

![image-20200628101425457](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg7s94zpjoj311y08utfb.jpg)

apply ：在某个对象A应用apply的大括号领域内，可以省略当前对象，直接调用该对象A的方法；

```
recyclerView = findViewById<RecyclerView>(R.id.my_recycler_view).apply {
	setHasFixedSize(true)
	layoutManager = viewManager
	adapter = viewAdapter
}
```

