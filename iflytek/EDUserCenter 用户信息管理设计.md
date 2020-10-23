## 1 背景

由于App支持多产品模块接入，各产品模块需要共享统一的登录后用户基本信息，以便进行业务处理。因此在基础架构提供一种机制，一方面便于各上层产品 模块共享用户数据，另一方面可以适应不同的App集成。

## 2  设计方案

新增一个UserCenterModule 库让FrameworkLib 库依赖；EDUserCenter对外提供以下能力：

- 对业务提供获取通用User 信息，tgt、userId

- 对业务方提供getDeviceId 的能力；

- 当用户登录、登出、切换用户，通知业务方

- 提供获取createToken 方法

  

## 3  详细设计

对于上述列举的三种能力，第一个能力是在调用CommonLogin 接口后，获取到User信息后保存到EDUserCenter 类，同时通知业务方登录成功；借助LoginSDK，UserCenterModule实现createToken 、getDeviceId方法，供业务方调用。

## 4 EDUserCenter 类设计

![image-20201020091731090](https://tva1.sinaimg.cn/large/007S8ZIlgy1gjvj9185h0j30se0jv40f.jpg)



## 5 时序图

![image-20201020091702990](https://tva1.sinaimg.cn/large/007S8ZIlgy1gjvj8lmrrhj30o60imta6.jpg)

