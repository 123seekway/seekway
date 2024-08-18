# 第一章 创建简单的映射器代理工厂
作者：小傅哥
博客：https://bugstack.cn(opens new window)
原文：https://mp.weixin.qq.com/s/G3fZES2FvNQK8JLnd9Hx9w

> 只有神知道的世界，男主最终还是选择了现实世界。

![争论的脸](https://github.com/user-attachments/assets/52ba99ec-7107-4e08-9348-7c423132ab82)
### 一、业务
任务：
1. 尝鲜代理invoke(方法，形参)
2. 熟悉工厂模式的调用。

实现：
1. 创建代理工厂，目的是实现调用注入，为要实现的代理对象方法提供物料，这里为接口.class，IUserDao.class。
``` java
public class MapperProxyFactory<T> {

    private final Class<T> mapperInterface;

    //无参构造，只要调用它就可以实现注入
    public MapperProxyFactory(Class<T> mapperInterface) {
        this.mapperInterface = mapperInterface;
    }
```
2. 要启动代理对象，一是接口类加载器，二是class数组，三是接口和方法的构造器对象
``` java
(T) Proxy.newProxyInstance(接口类加载器，class数组，接口和方法)
```