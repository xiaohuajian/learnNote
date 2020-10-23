### 常用的androidx 库 对照比对

|                Android                 |                 Androidx                 |
| :------------------------------------: | :--------------------------------------: |
| android.support.v7.widget.RecyclerView | androidx.recyclerview:recyclerview:1.1.0 |
|    com.android.support:cardview-v7     |        androidx.cardview:cardview        |
|       com.android.support:design       |   com.google.android.material:material   |
|    android.arch.lifecycle:livedata     |  androidx.lifecycle:lifecycle-livedata   |
|    android.arch.lifecycle:viewmodel    |  androidx.lifecycle:lifecycle-viewmodel  |
|                                        |                                          |

更多的可以[参考](https://developer.android.google.cn/topic/libraries/support-library/downloads/androidx-class-mapping.csv) 官方网站. 

### 一键迁移流程

如果要使用as的一键迁移功能,需要project中gradle tool 版本为3.2.0 以上, 工程的sdk 编译版本为28以上. 如果之前的项目不是28版本的, 那么切到28之后, 需要注意由于sdk的改变进行适配,比如说之前是26 , 换到28 ,可能有些系统 api 不能使用了,或则说28的版本特性比如说http 无法进行网络请求,必须进行添加配置才行;

如果手动修改,需要添加一些配置:

1 在gradle.properties 中添加:

```
android.useAndroidX=true
android.enableJetifier=true
```

2 接下来就是替换库 及xml 中使用的控件;



