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