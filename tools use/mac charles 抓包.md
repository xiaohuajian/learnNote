Charles 工具很强大，一般的来说主要有这个几个功能，现在来说一下：

- 抓包（http和https）
- 模拟复杂网络环境（若网、设置最高/低网速）
- 接口映射 （可以通过map remote/local 劫持真正的网络请求实现接口模拟）

# 安装

先说安装吧，要使用首先安装；以Mac为例；首先这个软件是要收费的，也可以破解。下载地址 www.charlesproxy.com/download/ ，破解办法就是 下载一个charles.jar 进行替换。打开应用程序 -> 选择Charles -> 显示包内容然后点击Contents->Java->将我们下载下来的charles.jar替换这里的charles.jar；重启Charles，就不会再提示30天使用期限了。

![pic](https://tva1.sinaimg.cn/large/007S8ZIlgy1gilcdd7y3nj313e0u0djz.jpg)

> 注意：破解jar文件必须于charles的版本对应，否则，可能无法正常打开。

# 抓包

这个功能是最重要的，也是最复杂的。当然了，也很强大，很方便。其实，这里复杂就复杂在它的配置，但如果你对网络通信很了解的话，就能明白其中的含义，然后就能很好的使用，否则你知运用，不会原理，不能发挥它最大功力。

## 配置

### 安装电脑证书

![img](https://tva1.sinaimg.cn/large/007S8ZIlgy1gilciy5ie8j30e107w41w.jpg)

刚刚安装的证书是不被信任的，将其改成系统信任方式：右键选中Charles证书文件，将信任设置改为“始终信任”模式即可；

![pic](https://tva1.sinaimg.cn/large/007S8ZIlgy1gilcovf90sj322s0jkke4.jpg)



### 手机上安装代理证书

**1.打开Help菜单上的SSL Proxying选项，选择对应的栏目，根据手机系统，选择对应的选项**

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gilct97i8qj31ri0qab29.jpg)



**2.手机端登录 网址，下载证书** 

==如果手机端也安装了相应的证书，就可以抓https 包，只抓http 这个证书可以不安装==

![截屏2020-09-10 上午10.39.04](https://tva1.sinaimg.cn/large/007S8ZIlgy1gildc1s3g0j31mc0rg46w.jpg)

下载好了，还要安装，android 和 iPhone 安装方式不太一样。



假设安装好了。

### 设置charles

![截屏2020-09-10 上午11.01.52](https://tva1.sinaimg.cn/large/007S8ZIlgy1gildj6st78j31gg0sohaw.jpg)

![截屏2020-09-10 上午11.29.01](https://tva1.sinaimg.cn/large/007S8ZIlgy1gileb3bpdnj319b0u0k9e.jpg)

这个443 端口添加也是为了https 抓包

![截屏2020-09-10 上午11.19.28](https://tva1.sinaimg.cn/large/007S8ZIlgy1gile1773ywj31980u0qja.jpg)

### 网络设置

然后手机和电脑处于同一网段（即ip地址的前三位都是一样的），比如说处于同一个wifi 下，同一局域网内；或者手机使用电脑发出来的wifi。**因为抓包的本质其实是电脑拦截你的请求，替你发送，拿到结果在转发给你，相当于手机和服务器之间多了一个charles 代理。** 假设处于同一网段了，开始设置代理ip；

先找到电脑ip 地址；打开 charles ——help——local ip ，可以看到ip 地址；在打开 charles——proxy——proxy setting，看代理的端口，如果没有可以设置为 8888。

![截屏2020-09-10 上午11.10.13](https://tva1.sinaimg.cn/large/007S8ZIlgy1gildqrd65vj315k0nqdif.jpg)

![截屏2020-09-10 上午11.01.52](https://tva1.sinaimg.cn/large/007S8ZIlgy1gildps7kr8j31gg0sohaw.jpg)

### 配置手机代理

以Android为例，把刚刚获取的ip 和 端口填写到你手机连接的wifi 配置中，

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gildv54iuoj30u01hcmzj.jpg" alt="pic" style="zoom:67%;" />



## 开始抓包

如果上面的步骤都做完了，就可以进行抓包了，然后就可以过滤host 来显示自己想要的主机名。

![截屏2020-09-10 上午11.24.29](https://tva1.sinaimg.cn/large/007S8ZIlly1gile7qhe9aj31bt0u0no4.jpg)

![截屏2020-09-10 上午11.25.57](https://tva1.sinaimg.cn/large/007S8ZIlly1gile7l5gzoj31e90u01kx.jpg)



# 模拟网络 **Throttle Settings** 

throttle 意思风门、喉咙，就是关卡的意思。我们在这里进行多个设置。具体

# 映射

应用场景：移动端app在开发新功能时，需要服务端提供接口，但是服务端没有开发完，这时候我们可通过charles 模拟接口返回数据，具体做法就是 map local /remote

我们可以本地先写一个文本，把数据放入，然后点击map local ，把自己想要的接口ip、port、path 填入，然后把映射的路径指向刚刚建立的本地文本，这样就可以获取到数据了。

另外，当有一些bug需要的环境比较难搭建，我们就可以通过这个功能直接获取想要返回的数据，不要通过搭建真实环境返回数据。