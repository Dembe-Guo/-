# 耦合
* 【耦合的强度取决于模块间接口的复杂性、调用模块方式以及通过界面从传送数据的多少。模块间的耦合度是指模块间的依赖关系，包括控制关系、调用关系 、数据传递关系。模块间的联系越多，其耦合性越强，同时表明其独立性越差。】
## 解耦
* 核心技术：控制反转（IoC）
    * 对象在建立时，由一个容器（调控系统）内所有对象的外界实体将其所依赖的对象的引用传递给它。也可以说，依赖对象的引用被注入到引用对象中，所以控制反转的含义是：关于一个对象如何获取它所依赖的对象的引用，这个责任的反转。
---
# Spring 
* spring 框架实例化Bean 的三种方式
    * 构造方法实例化
    * 静态工厂实例化
    * 实例工厂实例化
* Bean的作用域
    * 1.singleton 默认的作用域，在spring容器中只有一个Bean实例。
    * 2.prototype spring每次获取prototype定义的Bean，容器都会创建一个新的Bean实例。
    * 3.request  不同的HTTP请求返回不同的Bean实例。
    * 4.session  在一个HTTP session 中容器将返回同一个Bean实例。
    * 5.webSocket 为每个webSocket对象创建一个Bean 实例。
        * 3 4 5 作用域仅在web Spring 中使用。
* 基于XML配置的装配
    * 构造方法注入： 使用<bean> 的子元素<constructor-arg>来定义构造方法的参数
    * setter方法注入 ： Bean的实现类需要提供一个默认无参数的构造方法，并为每个需要注入的属性提供对应的setter方法，使用<bean>的子元素<property>。
* 基于注解的装配
    * @Component  泛化，表示一个组件对象（Bean)可以作用在任何层次上。
    * @Repository 注解数据访问层（Dao)bean;
    * @Service 注解逻辑组件类（service）bean;
    * @Controller 注解控制组件类（Controller）bean;
    * @Autowired 该注解可以对类成员变量，方法，及构造方法进行标注，完成自动装配工作。可以消除setter和getter方法。
    * @Resource该注解和@Autowired功能一样，该注解是按照名称来装配注入，和@Qualifier注解一起使用。
    * @Qualifier
## Aop
* 切入点：通过切入点可以确定哪些连接点需要被处理。
* 通知： 切面的具体实现
* 织入：将切面代码插入到目标对象上，从而生成代理对象的过程。
    * 编译期织入；
    * 类装载期织入；
    * 动态代理织入；
* JDK动态代理
    * 必须提供接口才能使用。
    * 创建代理的方法，实现InvocationHandler接口，建立代理对象和真实对象的代理关系，并返回代理对象
        * 1.类加载器
        ```java
        ClassLoader cld = 代理类.class.getClassLoader();
        ```
        * 2.被代理对象实现的所有接口
        ```java
        Class[] clazz = 代理对象.getClass().getInterfaces();
        ```
        * 3.使用代理类进行增强，返回代理后的对象
        ```java
        return Proxy.newProxyInstance(cld,class,this);
        ```
        * 4.覆写invoke方法
        ```java
        /** 代理的逻辑方法，所有动态代理类的方法都交给该方法处理  
          * proxy 是被代理对象  
          * method 是将要执行的方法  
          * args 是执行方法是需要的参数  
          * return 指返回代理结果
         */
        @Override
        public Object invoke (Object proxy,Method method,Object[] ags)throws Throwable{

        }
        ```
* CGLIB 动态代理
    * 创建代理类CglibDynamicProxy 实现MethodInterceptor接口
    ```java
    public CglibDynamicProxy implements MethodInterceptor{
        /**
        * 创建代理的方法，生成CGLIB代理对象
        * target 是目标对象，需要增强对象
        * 返回目标对象的CGLIB代理对象
        */
        public Object createProxy (Object target){
            //创建一个动态类对象，及增强类对象
            Enhancer enhancer = new Enhancer();
            //确定需要增强的类，设置其父类
            enhancer.setSuperclass(target.getclass());
            //确定代理逻辑对象为当前对象，要求当前对象实现MethodInterceptor的方法
            enhancer.setCallback(this);
            //返回创建的代理对象
            return enhancer.create();
        }
        /**
        * intercept  方法会在程序执行目标方法时被调用
        * proxy 是CGLIB根据指定父类生成的代理对象
        * method 是拦截方法
        * args 是拦截方法的参数数组
        * methodProxy 是方法的代理对象，用于执行父类的方法
        * 返回代理结果
        */
        @Override
        public Object intercept (Object proxy,Method method ,Object[] args, MethodProxy methodproxy) throws Throwable{
            ...
        }
    }
    ```
    
