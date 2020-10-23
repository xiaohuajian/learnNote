工程中使用遇到的问题

---

#### 下载太慢

有时候，下载太慢gradle，我们可以直接拷贝源码到相应的目录。对于windows而言，其gradle的存放目录为：

<img src="https://i.loli.net/2020/09/03/Ptbw8QBGHZIMuV4.png" alt="base64" style="zoom:67%;" />

<img src="https://i.loli.net/2020/09/03/twYOdv7por6mCI4.png" alt="base65" style="zoom:67%;" />

从这里要明白，下载的为**gradle-版本号-all.zip 压缩包**，然后自动会解压并生成lck 和 ok文件。那么，我们可以直接去  [官网](https://services.gradle.org/distributions/) 下载相应的

![base66](https://i.loli.net/2020/09/03/qYASdGa4g6Dh85p.png)

图中 红色的标注。

但要注意的是：看一下那个目录结构，发现有一层目录是乱码结构，这样的原因是因为加密设计的，如果我们自己直接下载放入相应的目录，没有这层目录恐怕不行，那么如何生成这个目录，很简单，**只要我们下载过，这个gradle会自己生成，只要有这个目录了，我们就可以从别人那里拷贝，在放入目录中**。

#### resourcePrefix

这个东西可以限制字符串资源的命名前缀，不按此前缀命名会有lint提示报错，但是可以编译通过。

![image-20200927180401530](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj5d7u4sa0j313i094gn1.jpg)



#### aar 传递依赖

Q：如果application A 依赖module B，B中依赖了一个aar C，假如说A想用C中的类，如何实现呢？

我们知道A——B——C，A、B、C都是module，A依赖B，B依赖C，A要用C中的内容，可以通过api 实现；假如说C是aar，这时候仅仅通过api 无法办到，还要在

```
最外层工程
allprojects {
    repositories {
        google()
        jcenter()
        // 添加依赖
        flatDir{
            dirs  project(':B').file('libs')
        }
    }
    
}

# 再在B中依赖C aar 
在android 括号或者最外层添加 aar 的存放处lib文件
repositories {
    flatDir {
        dirs 'libs'
    }
}

然后dependencies 中添加
api (name:'aar 名字', ext: 'aar')

```

我们观察 

```
flatDir{ dirs  project(':B').file('libs')} 和 flatDir {dirs 'libs'}        
```

发现后者是相比前者 少了project