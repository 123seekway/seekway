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
3. 启发式学习，以目的性为主的实现

目的：为代理方法提供物料——接口和实现类，使其调用接口时被代理对象接管，能更好的管理和使用。
1. 要启动代理对象，一是接口类加载器，二是class数组[接口]，三是接口和方法的构造器对象，且类要实现InvocationHandler, Serializable，这里单独创建个类实现。返回值为接口本身，调用方法时即被代理。
``` java
(T) Proxy.newProxyInstance(接口类加载器，class数组，接口和方法)
```
2. 创建代理工厂，把需要的接口对象注入无参构造器。
``` java
public class MapperProxyFactory<T> {

    private final Class<T> mapperInterface;

    //无参构造，只要调用它就可以实现注入
    public MapperProxyFactory(Class<T> mapperInterface) {
        this.mapperInterface = mapperInterface;
    }
```
3. 代理类的第三个形参为需继承InvocationHandler的类，创建此类且利用构造方法注入。
``` java
public class MapperProxy<T> implements InvocationHandler, Serializable {

    //Map<"cn.bugstack.mybatis.test.dao.IUserDao.queryUserName", "模拟执行，查询用户姓名">
    private Map<String, String> sqlSession;
    //接口对象
    private final Class<T> mapperInterface;
    //构造器
    public MapperProxy(Map<String, String> sqlSession, Class<T> mapperInterface) {
        this.sqlSession = sqlSession;
        this.mapperInterface = mapperInterface;
    }
```
5. invoke(方法，形参)
### 第三章 实现映射器的注册和使用
目的：把包下的类扫描放到map中
扫描操作：包下有类和接口，做分类处理
这节设计巧妙的地方，用扫描出的类去调用另一个实现类，再调其方法。
有趣的发现：接口可以作为其实现类值的停靠站。
线路一：接口注入，使用了注册映射器代理工厂，从包扫描到的接口。
问题-：好好的为何要实现SqlSession，主要是提供一个接口，调用方便。
接口要有对应的实现类。
关键还是看接口。
1.注入接口 2.方法调用时即被代理，包括形参，后面为自定义
SqlSessionFactory接口业务流程为
生产：
class可以强转成接口