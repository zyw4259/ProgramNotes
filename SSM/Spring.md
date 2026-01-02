
## 1. Spring 相关概念

### 1.1 初识 Spring

#### 1.1.1 Spring 家族
- Spring 是生态圈，不是单一技术，可用于 Web / 微服务 / 分布式等开发。
- 重点关注
  - Spring Framework：最早、最核心、其他 Spring 技术的基础
  - Spring Boot：在“简化开发”基础上进一步提升开发效率
  - Spring Cloud：微服务与分布式相关能力

#### 1.1.2 Spring 发展史
- 早期 JavaEE 受 EJB 思想影响，后来出现更高效的替代方案并落地为 Spring 1.0
- 版本演进（理解趋势即可）
  - Spring 1.0：纯 XML 配置
  - Spring 2.0：引入注解（配置 + 注解）
  - Spring 3.0：支持纯注解开发（效率提升明显）
  - Spring 4.0：随 JDK 升级调整部分 API
  - Spring 5.x：全面支持 JDK8（课程版本常见为 5 系）

### 1.2 Spring 系统架构

#### 1.2.1 架构分层理解
- Core Container：核心容器（最基础，其他模块依赖它）
- AOP / Aspects：面向切面编程及其实现
- Data Access / Data Integration：数据访问与数据集成（可整合 MyBatis 等）
- Transactions：事务管理（AOP 的具体应用，后期重点）
- Web：Web 层（通常在 SpringMVC 学）
- Test：测试支持（整合 JUnit 等）

#### 1.2.2 学习路线
- IOC / DI
- AOP
- 事务管理（AOP 应用）
- 整合 MyBatis（IOC 应用）

### 1.3 核心概念

#### 1.3.1 现有代码痛点
- 业务层调用数据层通常要在业务层 `new` 数据层对象
- 数据层实现类变更会导致业务层代码改动、重新编译打包部署
- 结果：耦合度高

#### 1.3.2 IOC、IOC 容器、Bean、DI
- IOC 控制反转
  - 核心：对象创建控制权从“程序内部主动 new”转为“外部提供”
  - Spring 对 IOC 思想的实现：提供 IOC 容器充当“外部”
- IOC 容器做什么
  - 负责对象创建、初始化等
  - 容器中被创建/管理的对象统称 Bean
- DI 依赖注入
  - 仅有 bean 还不够：service 依赖 dao，需要建立关系
  - DI：在容器中建立 bean 与 bean 之间的依赖关系

​	


## 2. 入门案例

### 2.1 IOC 入门

#### 2.1.1 实现思路
- IOC 容器管理项目中用到的类对象（如 Service、Dao）
- 用配置文件告诉容器要管理哪些对象
- 通过 Spring 提供的接口获取 IOC 容器
- 通过容器方法获取 bean 并调用

#### 2.1.2 关键代码
- Maven 依赖（示例）
```xml
<dependencies>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.10.RELEASE</version>
  </dependency>
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
  </dependency>
</dependencies>
```

- 接口与实现（示例）
```java
public interface BookDao {
    void save();
}
public class BookDaoImpl implements BookDao {
    public void save() {
        System.out.println("book dao save ...");
    }
}

public interface BookService {
    void save();
}
public class BookServiceImpl implements BookService {
    private BookDao bookDao = new BookDaoImpl();
    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
}
```

- XML 配置 bean（applicationContext.xml）
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">

  <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
  <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl"/>

</beans>
```

- 获取容器与获取 bean
```java
public class App {
    public static void main(String[] args) {
        ApplicationContext ctx =
            new ClassPathXmlApplicationContext("applicationContext.xml");

        BookService bookService = (BookService) ctx.getBean("bookService");
        bookService.save();
    }
}
```

### 2.2 DI 入门

#### 2.2.1 实现思路
- 依赖注入必须建立在 IOC 管理 Bean 之上
- 删除业务层中 `new DaoImpl()` 的写法
- 在 Service 中提供 setter，让容器能把 Dao 注入进来
- 通过 XML 描述 Service 与 Dao 的依赖关系

#### 2.2.2 关键代码
- Service 去掉 new + 提供 setter
```java
public class BookServiceImpl implements BookService {
    private BookDao bookDao;

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }

    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
}
```

- XML 注入（property + ref）
```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>

<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
  <property name="bookDao" ref="bookDao"/>
</bean>
```
- 理解两个 bookDao
  - `name="bookDao"`：对应 `setBookDao(...)` 的属性名
  - `ref="bookDao"`：引用容器中 `id="bookDao"` 的那个 Bean

​		

## 3. IOC 相关内容

### 3.1 Bean 基础配置

#### 3.1.1 id 与 class
- `<bean id="" class=""/>`
  - id：bean 名称（同一个配置文件中必须唯一）
  - class：具体实现类全限定名（不能写接口，因为接口无法实例化）

#### 3.1.2 name 别名
- name：为 bean 指定别名，可配置多个，逗号/分号/空格分隔
```xml
<bean id="bookService" name="service service4 bookEbi"
      class="com.itheima.service.impl.BookServiceImpl">
  <property name="bookDao" ref="bookDao"/>
</bean>

<bean id="bookDao" name="dao"
      class="com.itheima.dao.impl.BookDaoImpl"/>
```
- 获取 bean：`getBean("id")` 或 `getBean("任意别名")` 都可以
- 常见错误
  - ref 指向的 bean 必须存在，否则报错
  - 通过 id/name 都获取不到会抛 `NoSuchBeanDefinitionException`

#### 3.1.3 scope 作用范围
- 默认：singleton（单例）
- prototype：非单例（每次获取创建新对象）
- 验证方式：同一 bean 获取两次打印地址
```java
public class AppForScope {
    public static void main(String[] args) {
        ApplicationContext ctx =
            new ClassPathXmlApplicationContext("applicationContext.xml");

        BookDao bookDao1 = (BookDao) ctx.getBean("bookDao");
        BookDao bookDao2 = (BookDao) ctx.getBean("bookDao");
        System.out.println(bookDao1);
        System.out.println(bookDao2);
    }
}
```
```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl" scope="singleton"/>
<!-- 或 -->
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl" scope="prototype"/>
```
- 为什么默认单例
  - 避免频繁创建与销毁、提高复用与性能
- 单例是否线程安全
  - 有状态对象（有成员变量存数据）：多线程共享可能不安全
  - 无状态对象（不存共享数据）：通常不会有线程安全问题
- 适合交给容器管理
  - 表现层、业务层、数据层、工具类对象
- 不适合交给容器管理
  - 封装实例的域对象（易引发线程安全问题）

​	

### 3.2 Bean 实例化

#### 3.2.1 构造方法实例化
- Spring 创建 bean 走构造方法，本质依赖反射
- 通常要求无参构造
  - 若只提供有参构造且无默认构造，会报 “No default constructor found / NoSuchMethodException: <init>()”
- 结论：构造方法实例化是最常用方式

​	

#### 3.2.2 静态工厂实例化

- 适用：兼容早期老系统的创建方式（了解即可）
- 配置方式：`class + factory-method`
```java
public class OrderDaoFactory {
    public static OrderDao getOrderDao() {
        return new OrderDaoImpl();
    }
}
```
```xml
<bean id="orderDao"
      class="com.itheima.factory.OrderDaoFactory"
      factory-method="getOrderDao"/>
```
- 意义：工厂方法中除了 new，还可以加入必要业务操作（如初始化、校验等）

​	

#### 3.2.3 实例工厂实例化

- 配置方式：先把工厂对象交给容器，再由工厂方法创建目标 bean
```java
public class UserDaoFactory {
    public UserDao getUserDao() {
        return new UserDaoImpl();
    }
}
```
```xml
<bean id="userFactory" class="com.itheima.factory.UserDaoFactory"/>
<bean id="userDao" factory-bean="userFactory" factory-method="getUserDao"/>
```

​	

#### 3.2.4 FactoryBean

- 目的：简化“实例工厂”复杂配置（整合其他框架时常用）
- 关键点
  - `getObject()`：创建并返回对象
  - `getObjectType()`：返回创建对象的类型
  - `isSingleton()`：是否单例（默认 true，可重写为 false）
```java
public class UserDaoFactoryBean implements FactoryBean<UserDao> {
    public UserDao getObject() {
        return new UserDaoImpl();
    }
    public Class<?> getObjectType() {
        return UserDao.class;
    }
    // 默认单例；需要非单例时再重写
    // public boolean isSingleton() { return false; }
}
```
```xml
<bean id="userDao" class="com.itheima.factory.UserDaoFactoryBean"/>
```

​		

### 3.3 Bean 生命周期

#### 3.3.1 生命周期理解
- 生命周期：从创建到消亡的完整过程
- bean 生命周期：bean 从创建到销毁的整体过程
- 生命周期控制：在“创建后、销毁前”做一些初始化/释放资源的事

#### 3.3.2 init 与 destroy 方法
- 在类里写方法（方法名可自定义）
```java
public class BookDaoImpl implements BookDao {
    public void save() {
        System.out.println("book dao save ...");
    }
    public void init() {
        System.out.println("init...");
    }
    public void destory() {
        System.out.println("destory...");
    }
}
```
- XML 指定
```xml
<bean id="bookDao"
      class="com.itheima.dao.impl.BookDaoImpl"
      init-method="init"
      destroy-method="destory"/>
```

#### 3.3.3 关闭容器触发 destroy
- `ApplicationContext` 没有 `close()`，需要使用 `ClassPathXmlApplicationContext`
```java
ClassPathXmlApplicationContext ctx =
    new ClassPathXmlApplicationContext("applicationContext.xml");
ctx.close();
```
- 不调用 close：main 结束 JVM 退出，容器来不及销毁，destroy 不执行

#### 3.3.4 注册钩子关闭容器
- `registerShutdownHook()`：JVM 退出前回调关闭容器
- 对比
  - `close()`：调用时立即关闭
  - `registerShutdownHook()`：JVM 退出前关闭
```java
ClassPathXmlApplicationContext ctx =
    new ClassPathXmlApplicationContext("applicationContext.xml");
ctx.registerShutdownHook();
```

#### 3.3.5 接口方式控制生命周期
- Spring 提供接口：减少配置 `init-method/destroy-method`（了解即可）
  - `InitializingBean.afterPropertiesSet()`：属性注入完成后执行
  - `DisposableBean.destroy()`：销毁前执行
- 执行顺序要点
  - 先执行属性注入（set）
  - 再执行初始化方法（afterPropertiesSet）

​	

## 4. DI 依赖注入

### 4.1 注入类型与入口
- 注入类型
  - 引用类型：Dao、Service 等对象
  - 简单类型：基本类型、String
- 常见注入入口
  - Setter 注入：`<property name="" .../>`
  - 构造器注入：`<constructor-arg .../>`
- 自动装配
  - 容器按规则自动查找并注入依赖（主要用于引用类型）
- 集合注入
  - 数组、List、Set、Map、Properties（可存简单类型或引用类型）

​	

### 4.2 Setter 注入

#### 4.2.1 引用类型注入
- 要点
  - 在类中定义引用类型属性
  - 提供对应 setter 方法
  - XML 用 `<property name="" ref=""/>` 注入容器中的其它 bean

- 示例：为 BookServiceImpl 注入 bookDao、userDao
```java
public class BookServiceImpl implements BookService{
    private BookDao bookDao;
    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
        userDao.save();
    }
}
```
```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
<bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"/>

<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
  <property name="bookDao" ref="bookDao"/>
  <property name="userDao" ref="userDao"/>
</bean>
```

#### 4.2.2 简单类型注入
- 要点
  - 简单类型没有对应 bean，不能用 ref
  - XML 用 `<property name="" value=""/>`
  - Spring 会做类型转换；无法转换会报错（如 int 写成 abc）

- 示例：为 BookDaoImpl 注入 databaseName、connectionNum
```java
public class BookDaoImpl implements BookDao {
    private String databaseName;
    private int connectionNum;

    public void setConnectionNum(int connectionNum) {
        this.connectionNum = connectionNum;
    }
    public void setDatabaseName(String databaseName) {
        this.databaseName = databaseName;
    }
}
```
```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
  <property name="databaseName" value="mysql"/>
  <property name="connectionNum" value="10"/>
</bean>
```

​	

### 4.3 构造器注入

#### 4.3.1 引用类型注入
- 要点
  - 提供带依赖参数的构造方法
  - XML 用 `<constructor-arg ... ref="..."/>`
  - 一般不依赖 setter（强制依赖更适合构造器注入）

- 示例：为 BookServiceImpl 构造注入 bookDao
```java
public class BookServiceImpl implements BookService{
    private BookDao bookDao;

    public BookServiceImpl(BookDao bookDao) {
        this.bookDao = bookDao;
    }

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
}
```
```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>

<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
  <constructor-arg name="bookDao" ref="bookDao"/>
</bean>
```

#### 4.3.2 多参数构造注入
- 示例：构造注入 bookDao、userDao
```java
public class BookServiceImpl implements BookService{
    private BookDao bookDao;
    private UserDao userDao;

    public BookServiceImpl(BookDao bookDao, UserDao userDao) {
        this.bookDao = bookDao;
        this.userDao = userDao;
    }
}
```
```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
<bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"/>

<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
  <constructor-arg name="bookDao" ref="bookDao"/>
  <constructor-arg name="userDao" ref="userDao"/>
</bean>
```

#### 4.3.3 简单类型构造注入与解耦写法
- 示例：为 BookDaoImpl 构造注入 databaseName、connectionNum
```java
public class BookDaoImpl implements BookDao {
    private String databaseName;
    private int connectionNum;

    public BookDaoImpl(String databaseName, int connectionNum) {
        this.databaseName = databaseName;
        this.connectionNum = connectionNum;
    }

    public void save() {
        System.out.println("book dao save ..."+databaseName+","+connectionNum);
    }
}
```
- 按 name 注入（与形参名耦合）
```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
  <constructor-arg name="databaseName" value="mysql"/>
  <constructor-arg name="connectionNum" value="666"/>
</bean>
```
- 按 type 注入（降低对形参名依赖）
```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
  <constructor-arg type="int" value="10"/>
  <constructor-arg type="java.lang.String" value="mysql"/>
</bean>
```
- 按 index 注入（index 从 0 开始）
```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
  <constructor-arg index="1" value="100"/>
  <constructor-arg index="0" value="mysql"/>
</bean>
```

​	

### 4.4 自动装配（autowire）

#### 4.4.1 概念与方式
- 自动装配：容器根据规则自动查找并注入依赖
- 常见方式
  - byType：按类型
  - byName：按名称
  - constructor：按构造方法
  - no：不启用

#### 4.4.2 byType 自动装配
- 做法
  - 删除 `<property>` 手动注入配置
  - 在需要注入的 bean 上加 `autowire="byType"`
```xml
<bean class="com.itheima.dao.impl.BookDaoImpl"/>
<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl"
      autowire="byType"/>
```
- 注意
  - 依赖属性仍需要 setter（容器要能注入进去）
  - 被注入对象必须是容器中的 bean
  - 同类型候选 bean 多个会报错（类型不唯一）

#### 4.4.3 byName 自动装配
```xml
<bean class="com.itheima.dao.impl.BookDaoImpl"/>
<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl"
      autowire="byName"/>
```
- 规则
  - 以属性名（对应 setter）作为名称去容器里找同名 bean（通常匹配 bean 的 id/name）
  - 找不到：注入为 null
- 特点
  - 与变量/属性命名耦合更强，一般不推荐

#### 4.4.4 自动装配限制
- 主要用于引用类型，不能对简单类型生效
- 若同时存在手动注入（property/constructor-arg），一般以手动配置为准

​	

### 4.5 集合注入

#### 4.5.1 支持类型与规则
- 支持：数组、List、Set、Map、Properties
- 集合元素
  - 简单类型：用 `<value>`
  - 引用类型：用 `<ref>`

#### 4.5.2 数组 / List / Set
```xml
<!-- 数组 -->
<property name="array">
  <array>
    <value>100</value>
    <value>200</value>
    <value>300</value>
  </array>
</property>

<!-- List -->
<property name="list">
  <list>
    <value>itcast</value>
    <value>itheima</value>
    <value>boxuegu</value>
    <value>chuanzhihui</value>
  </list>
</property>

<!-- Set -->
<property name="set">
  <set>
    <value>itcast</value>
    <value>itheima</value>
    <value>boxuegu</value>
    <value>boxuegu</value>
  </set>
</property>
```

#### 4.5.3 Map / Properties
```xml
<!-- Map -->
<property name="map">
  <map>
    <entry key="country" value="china"/>
    <entry key="province" value="henan"/>
    <entry key="city" value="kaifeng"/>
  </map>
</property>

<!-- Properties -->
<property name="properties">
  <props>
    <prop key="country">china</prop>
    <prop key="province">henan</prop>
    <prop key="city">kaifeng</prop>
  </props>
</property>
```

​		

## 5. 第三方Bean配置管理

### 5.1 数据源对象交给IOC管理

#### 5.1.1 基本思路

  - 引入第三方依赖（如 Druid / C3P0）
  - 在 XML 中把第三方类配置成 `<bean>`，交给 IOC 容器管理
  - 用 `<property>` 注入数据库连接四要素（驱动、url、用户名、密码）
  - 从容器中 `getBean` 获取对象验证

#### 5.1.2 Druid 数据源管理示例

```xml
<!-- applicationContext.xml -->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
  <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
  <property name="url" value="jdbc:mysql://localhost:3306/spring_db"/>
  <property name="username" value="root"/>
  <property name="password" value="root"/>
</bean>
```

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
DataSource ds = (DataSource) ctx.getBean("dataSource");
System.out.println(ds);
```

#### 5.1.3 C3P0 数据源管理要点

  - C3P0 的核心类：`com.mchange.v2.c3p0.ComboPooledDataSource`
  - 属性通过 setter 注入，前提是类中存在对应 setter
  - 四要素属性名与 Druid 不同（注意对照类属性）

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
  <property name="driverClass" value="com.mysql.jdbc.Driver"/>
  <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/spring_db"/>
  <property name="user" value="root"/>
  <property name="password" value="root"/>
  <property name="maxPoolSize" value="1000"/>
</bean>
```

  - 若报 `ClassNotFoundException: com.mysql.jdbc.Driver`：缺少 MySQL 驱动依赖，需要导入 `mysql-connector-java`
  - 差异现象：
    - C3P0 初始化会尝试加载驱动，缺驱动会立刻报错
    - Druid 可能启动不报错，但调用 `getConnection()` 时仍会因缺驱动报错

​	

### 5.2 XML 加载 properties 并注入

#### 5.2.1 加载并使用 ${key}

  - 将常量（如数据库四要素）抽取到 `*.properties`，便于维护
  - 关键点：开启 `context` 命名空间 + `property-placeholder` + `${key}`

```properties
# jdbc.properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://127.0.0.1:3306/spring_db
jdbc.username=root
jdbc.password=root
```

```xml
<!-- applicationContext.xml -->
<beans
  xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:context="http://www.springframework.org/schema/context"
  xsi:schemaLocation="
    http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">

  <context:property-placeholder location="jdbc.properties"/>

  <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${jdbc.driver}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
  </bean>
</beans>
```

#### 5.2.2 读取单个属性注入示例

  - `BookDaoImpl` 提供 `name` 属性与 setter
  - XML 用 `${key}` 注入后，在方法中打印验证

```java
public class BookDaoImpl implements BookDao {
  private String name;
  public void setName(String name) { this.name = name; }
  public void save() { System.out.println("book dao save ..." + name); }
}
```

```xml
<context:property-placeholder location="jdbc.properties"/>
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
  <property name="name" value="${jdbc.driver}"/>
</bean>
```

#### 5.2.3 注意事项

  - key 写成 `username` 可能“注入成系统用户名”
    - 原因：`<context:property-placeholder/>` 会加载系统环境变量，并可能优先使用
    - 解决：
      - 避免使用 `username` 作为 key
      - 或设置不加载系统属性：

```xml
<context:property-placeholder location="jdbc.properties" system-properties-mode="NEVER"/>
```

#### 5.2.4 多个 properties 文件加载方式

```xml
<!-- 方式一：逐个写（文件多时麻烦） -->
<context:property-placeholder location="jdbc.properties,jdbc2.properties" system-properties-mode="NEVER"/>

<!-- 方式二：*.properties（不标准写法，不推荐） -->
<context:property-placeholder location="*.properties" system-properties-mode="NEVER"/>

<!-- 方式三：classpath:（仅当前项目根路径） -->
<context:property-placeholder location="classpath:*.properties" system-properties-mode="NEVER"/>

<!-- 方式四：classpath*:（包含当前项目及依赖项目的根路径） -->
<context:property-placeholder location="classpath*:*.properties" system-properties-mode="NEVER"/>
```

​	

## 6. 核心容器

### 6.1 容器创建方式

#### 6.1.1 类路径加载（常用）

```java
ApplicationContext ctx =
  new ClassPathXmlApplicationContext("applicationContext.xml");
```

#### 6.1.2 文件系统路径加载（了解）

```java
ApplicationContext ctx =
  new FileSystemXmlApplicationContext("D:\\xxx\\applicationContext.xml");
```

  - 从项目路径/文件系统查找，路径变动会导致代码修改，耦合高，不推荐

### 6.2 Bean 获取方式

  - 方式一：按名称（需要强转）

```java
BookDao dao = (BookDao) ctx.getBean("bookDao");
```

  - 方式二：按名称 + 类型（避免强转）

```java
BookDao dao = ctx.getBean("bookDao", BookDao.class);
```

  - 方式三：按类型（容器中该类型只能有一个）

```java
BookDao dao = ctx.getBean(BookDao.class);
```

### 6.3 容器层次结构要点

  - `BeanFactory` 是 IoC 容器顶层接口
  - `ApplicationContext` 是常用的核心容器接口，在其基础上扩展了更多能力

### 6.4 BeanFactory 与 ApplicationContext 的区别

  - `BeanFactory`：延迟加载（用到 `getBean` 才创建对象）
  - `ApplicationContext`：立即加载（创建容器时就创建 bean）

```java
public class BookDaoImpl implements BookDao {
  public BookDaoImpl() { System.out.println("constructor"); }
  public void save() { System.out.println("book dao save ..."); }
}
```

  - 使用 BeanFactory 示例（了解）：

```java
Resource res = new ClassPathResource("applicationContext.xml");
BeanFactory bf = new XmlBeanFactory(res);
BookDao dao = bf.getBean(BookDao.class);
dao.save();
```

  - ApplicationContext 也可配置延迟加载：

```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl" lazy-init="true"/>
```

​	

## 7. IOC/DI 注解开发

### 7.1 注解定义 Bean（组件扫描）

#### 7.1.1 @Component 与派生注解

  - `@Component`：把类交给 Spring 管理（可指定 beanId）
  - 不能加在接口上（接口无法实例化）
  - 默认 beanId：类名首字母小写（未显式指定时）

```java
@Component("bookDao")
public class BookDaoImpl implements BookDao { ... }
```

  - 派生注解（作用等价，便于分层语义）：
    - `@Controller` 表现层
    - `@Service` 业务层
    - `@Repository` 持久层

#### 7.1.2 XML 开启包扫描

```xml
<context:component-scan base-package="com.itheima"/>
```

  - base-package 越精确扫描越快；一般扫描到 groupId 根包即可

​	

### 7.2 纯注解开发（配置类替代 XML）

#### 7.2.1 核心注解

  - `@Configuration`：声明配置类（替代 applicationContext.xml）
  - `@ComponentScan`：替代 `<context:component-scan/>`

```java
@Configuration
@ComponentScan("com.itheima")
public class SpringConfig { }
```

#### 7.2.2 容器启动方式

```java
ApplicationContext ctx =
  new AnnotationConfigApplicationContext(SpringConfig.class);
```

​	

### 7.3 Bean 作用范围

#### 7.3.1 @Scope

  - 默认：`singleton`（单例）
  - 可选：`prototype`（非单例）

```java
@Repository
@Scope("prototype")
public class BookDaoImpl implements BookDao { ... }
```

​	

### 7.4 Bean 生命周期

#### 7.4.1 初始化与销毁注解

  - `@PostConstruct`：初始化方法
  - `@PreDestroy`：销毁方法（需要容器关闭才触发）

```java
@Repository
public class BookDaoImpl implements BookDao {

  @PostConstruct
  public void init() { System.out.println("init ..."); }

  @PreDestroy
  public void destroy() { System.out.println("destroy ..."); }
}
```

```java
AnnotationConfigApplicationContext ctx =
  new AnnotationConfigApplicationContext(SpringConfig.class);
BookDao dao = ctx.getBean(BookDao.class);
ctx.close(); // 触发 @PreDestroy
```

  - 可能需要依赖（用于注解 API）：

```xml
<dependency>
  <groupId>javax.annotation</groupId>
  <artifactId>javax.annotation-api</artifactId>
  <version>1.3.2</version>
</dependency>
```

​	

### 7.5 注解依赖注入

#### 7.5.1 引用类型注入 @Autowired

  - 默认按类型自动装配
  - 可写在属性上（常用），也可写在 setter/方法形参（了解）
  - 原理要点：基于反射（可对 private 字段“暴力反射”设值），因此可不写 setter

```java
@Service
public class BookServiceImpl implements BookService {
  @Autowired
  private BookDao bookDao;

  public void save() {
    System.out.println("book service save ...");
    bookDao.save();
  }
}
```

  - 多个同类型 bean 时：
    - `@Autowired` 先按类型
    - 若同类型多个，再用“属性名”和“bean 名称”匹配
    - 若仍无法确定，需要 `@Qualifier` 指定名称

#### 7.5.2 按名称指定 @Qualifier

  - 不能单独使用，必须配合 `@Autowired`

```java
@Autowired
@Qualifier("bookDao1")
private BookDao bookDao;
```

#### 7.5.3 简单类型注入 @Value

  - 注入基本类型/字符串（注意类型匹配）

```java
@Repository
public class BookDaoImpl implements BookDao {
  @Value("itheima")
  private String name;

  public void save() {
    System.out.println("book dao save ..." + name);
  }
}
```

#### 7.5.4 读取 properties：@PropertySource + @Value

  - 在配置类上加载 properties
  - 用 `@Value("${key}")` 取值

```properties
# jdbc.properties
name=itheima
```

```java
@Configuration
@PropertySource("classpath:jdbc.properties")
public class SpringConfig { }
```

```java
@Repository
public class BookDaoImpl implements BookDao {
  @Value("${name}")
  private String name;
}
```

  - @PropertySource 常见写法：
    - `@PropertySource({"jdbc.properties","xxx.properties"})`
    - `@PropertySource({"classpath:jdbc.properties"})`
  - 注意：@PropertySource 不支持通配符 `*`（写通配符会导致运行报错）

​	

## 8. 注解管理第三方 Bean

### 8.1 @Bean 定义第三方 Bean

  - 适用场景：第三方类在 jar 包中，无法在类上加 `@Component` 等注解
  - @Bean：将“方法返回值”交给 Spring 管理为 bean

```java
@Configuration
public class SpringConfig {

  @Bean
  public DataSource dataSource() {
    DruidDataSource ds = new DruidDataSource();
    ds.setDriverClassName("com.mysql.jdbc.Driver");
    ds.setUrl("jdbc:mysql://localhost:3306/spring_db");
    ds.setUsername("root");
    ds.setPassword("root");
    return ds;
  }
}
```

  - 注意点：
    - 不要用 `DataSource ds = new DruidDataSource()` 去设置属性（DataSource 接口没有这些 setter）
    - 需要用具体实现类创建并设值，再返回（返回类型可声明为 DataSource）

​	

### 8.2 拆分与引入外部配置类

#### 8.2.1 包扫描引入（不推荐）

  - SpringConfig 扫描到 `JdbcConfig`（JdbcConfig 需要在扫描路径下，并标识为配置类）

```java
@Configuration
@ComponentScan("com.itheima.config")
public class SpringConfig { }
```

```java
@Configuration
public class JdbcConfig {
  @Bean
  public DataSource dataSource(){ ... }
}
```

  - 缺点：不直观，不容易看出到底引入了哪些配置类

#### 8.2.2 @Import 引入（推荐）

  - 在主配置类上显式引入其他配置类
  - 参数是数组，可一次引入多个（避免写多个 @Import）

```java
@Configuration
@Import({JdbcConfig.class, Xxx.class})
public class SpringConfig { }
```

​	

### 8.3 向第三方 Bean 注入数据

#### 8.3.1 注入简单类型（@Value）

  - 在配置类中定义属性，用 @Value 注入
  - 在 @Bean 方法中使用这些属性完成 set 注入

```java
public class JdbcConfig {

  @Value("com.mysql.jdbc.Driver")
  private String driver;

  @Value("jdbc:mysql://localhost:3306/spring_db")
  private String url;

  @Value("root")
  private String userName;

  @Value("root")
  private String password;

  @Bean
  public DataSource dataSource() {
    DruidDataSource ds = new DruidDataSource();
    ds.setDriverClassName(driver);
    ds.setUrl(url);
    ds.setUsername(userName);
    ds.setPassword(password);
    return ds;
  }
}
```

  - 扩展：将四要素写进 `jdbc.properties` 后
    - `@PropertySource` 加载配置文件
    - `@Value("${jdbc.driver}")` 读取键值注入

#### 8.3.2 注入引用类型（方法形参自动装配）

  - 在 `@Bean` 方法上通过“形参”接收依赖，容器会按类型自动装配

```java
@Bean
public DataSource dataSource(BookDao bookDao) {
  System.out.println(bookDao); // 验证已注入
  DruidDataSource ds = new DruidDataSource();
  // ...设置四要素
  return ds;
}
```

  - 前提：`BookDao` 必须已被 Spring 管理（例如通过组件扫描或其他方式注入到容器中）

​		

## 9. Spring整合MyBatis

### 9.1 MyBatis基础环境回顾

#### 9.1.1 核心对象与流程

  - `SqlSessionFactoryBuilder` 读取 MyBatis 核心配置，构建 `SqlSessionFactory`
  - `SqlSessionFactory` 创建 `SqlSession`
  - `SqlSession` 获取 Mapper 代理并执行数据库操作
  - 资源用完要关闭 `SqlSession`

```java
// 1. 创建 SqlSessionFactoryBuilder
SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
// 2. 加载配置文件
InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
// 3. 构建 SqlSessionFactory
SqlSessionFactory factory = builder.build(in);
// 4. 获取 SqlSession
SqlSession sqlSession = factory.openSession();
// 5. 获取 Mapper 并执行
AccountDao accountDao = sqlSession.getMapper(AccountDao.class);
Account ac = accountDao.findById(1);
System.out.println(ac);
// 6. 释放资源
sqlSession.close();
```

#### 9.1.2 jdbc.properties 与 useSSL

  - 数据库连接四要素建议外置到 `jdbc.properties`
  - `useSSL=false` 用于关闭 MySQL 的 SSL 连接提示

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/spring_db?useSSL=false
jdbc.username=root
jdbc.password=root
```

#### 9.1.3 MyBatis核心配置文件要点

  - `<properties resource="jdbc.properties"/>` 读取外部配置
  - `<typeAliases><package .../></typeAliases>` 配置别名扫描
  - `<mappers><package .../></mappers>` 配置 Mapper 扫描包

```xml
<configuration>
  <properties resource="jdbc.properties"></properties>

  <typeAliases>
    <package name="com.itheima.domain"/>
  </typeAliases>

  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <!-- 省略：driver/url/username/password -->
      </dataSource>
    </environment>
  </environments>

  <mappers>
    <package name="com.itheima.dao"></package>
  </mappers>
</configuration>
```

​	

### 9.2 整合思路

  - 目标1：让 Spring 管理 `SqlSessionFactory`
    - `SqlSession` 由 `SqlSessionFactory` 创建，所以只需要把 `SqlSessionFactory` 管好
  - 目标2：让 Spring 管理 Mapper 扫描
    - Mapper 接口与映射的加载时机与 `SqlSessionFactory` 不完全一致，需要单独扫描并注册代理

​	

### 9.3 整合步骤

#### 9.3.1 引入整合依赖

  - `spring-jdbc`：Spring 操作数据库相关支持
  - `mybatis-spring`：Spring 与 MyBatis 整合包

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-jdbc</artifactId>
  <version>5.2.10.RELEASE</version>
</dependency>

<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis-spring</artifactId>
  <version>1.3.0</version>
</dependency>
```

#### 9.3.2 主配置类 SpringConfig

  - `@Configuration` 声明配置类
  - `@ComponentScan` 扫描业务层等组件

```java
@Configuration
@ComponentScan("com.itheima")
public class SpringConfig { }
```

#### 9.3.3 数据源配置类 JdbcConfig

  - `@PropertySource` 读取 `jdbc.properties`
  - `@Value` 注入四要素
  - `@Bean` 生成并交给容器管理 `DataSource`

```java
public class JdbcConfig {
  @Value("${jdbc.driver}")
  private String driver;
  @Value("${jdbc.url}")
  private String url;
  @Value("${jdbc.username}")
  private String userName;
  @Value("${jdbc.password}")
  private String password;

  @Bean
  public DataSource dataSource(){
    DruidDataSource ds = new DruidDataSource();
    ds.setDriverClassName(driver);
    ds.setUrl(url);
    ds.setUsername(userName);
    ds.setPassword(password);
    return ds;
  }
}
```

#### 9.3.4 MyBatis配置类 MybatisConfig

  - 关键类1：`SqlSessionFactoryBean`
    - 封装 `SqlSessionFactory` 的创建（FactoryBean 思想）
    - 需要 `DataSource`，可通过方法形参自动装配注入
  - 关键类2：`MapperScannerConfigurer`
    - 扫描 Dao 接口并创建代理对象放入 IOC
    - 核心属性：`basePackage`

```java
public class MybatisConfig {

  @Bean
  public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){
    SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
    ssfb.setTypeAliasesPackage("com.itheima.domain");
    ssfb.setDataSource(dataSource);
    return ssfb;
  }

  @Bean
  public MapperScannerConfigurer mapperScannerConfigurer(){
    MapperScannerConfigurer msc = new MapperScannerConfigurer();
    msc.setBasePackage("com.itheima.dao");
    return msc;
  }
}
```

#### 9.3.5 引入配置类并读取 properties

  - 主配置类统一管理引入关系
  - 同时在主配置类上加载 properties

```java
@Configuration
@ComponentScan("com.itheima")
@PropertySource("classpath:jdbc.properties")
@Import({JdbcConfig.class, MybatisConfig.class})
public class SpringConfig { }
```

#### 9.3.6 运行验证 App2

  - 从 IOC 获取 `AccountService`，调用方法验证整合成功

```java
public class App2 {
  public static void main(String[] args) {
    ApplicationContext ctx =
      new AnnotationConfigApplicationContext(SpringConfig.class);

    AccountService accountService = ctx.getBean(AccountService.class);

    Account ac = accountService.findById(1);
    System.out.println(ac);
  }
}
```

  - 整合完成后最核心的两个类
    - `SqlSessionFactoryBean`
    - `MapperScannerConfigurer`

​		

## 10. Spring整合JUnit

### 10.1 整合目的

  - 单元测试不再手动创建容器或写 main 方法
  - 让测试类在 Spring 环境中运行，直接 `@Autowired` 注入要测的 bean

### 10.2 整合步骤

#### 10.2.1 引入依赖

```xml
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
  <scope>test</scope>
</dependency>

<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-test</artifactId>
  <version>5.2.10.RELEASE</version>
</dependency>
```

#### 10.2.2 编写测试类

  - 固定写法：
    - `@RunWith(SpringJUnit4ClassRunner.class)` 指定运行器
    - `@ContextConfiguration(...)` 指定 Spring 配置来源
  - 测试哪个 bean，就 `@Autowired` 注入哪个

```java
@RunWith(SpringJUnit4ClassRunner.class)
// 加载配置类
@ContextConfiguration(classes = {SpringConfiguration.class})
// 加载配置文件（需要时再用）
// @ContextConfiguration(locations={"classpath:applicationContext.xml"})
public class AccountServiceTest {

  @Autowired
  private AccountService accountService;

  @Test
  public void testFindById(){
    System.out.println(accountService.findById(1));
  }

  @Test
  public void testFindAll(){
    System.out.println(accountService.findAll());
  }
}
```

  - 测试配置类：`@ContextConfiguration(classes = 配置类.class)`
  - 测试配置文件：`@ContextConfiguration(locations = {"classpath:xxx.xml"})`

​	

### 10.3 关键注解速记

#### 10.3.1 @RunWith

  - 位置：测试类上方
  - 作用：设置 JUnit 运行器
  - 常用：`SpringJUnit4ClassRunner`

#### 10.3.2 @ContextConfiguration

  - 位置：测试类上方
  - 作用：设置 JUnit 加载的 Spring 核心配置
  - 参数：
    - `classes`：配置类（可数组）
    - `locations`：配置文件（可数组）

​	

## 11. AOP简介

### 11.1 AOP是什么

* AOP（Aspect Oriented Programming）：面向切面编程，一种编程范式，用来指导如何组织程序结构。
* 核心思想：**在不改原有代码的前提下，对方法进行增强**（无侵入式）。

### 11.2 AOP作用

* 把“与业务无关但到处都要用”的共性功能抽取出来（如：日志、权限、事务、性能监控），统一织入到指定方法上。
* 典型收益：减少重复代码、降低耦合、让业务代码更干净。

### 11.3 AOP核心概念

* **连接点（JoinPoint）**：程序执行过程中的位置；在 SpringAOP 中通常理解为“方法的执行”。
* **切入点（Pointcut）**：被选中、需要增强的方法（连接点的子集）。
* **通知（Advice）**：在切入点处要执行的增强逻辑（共性功能方法）。
* **通知类**：承载通知方法的类。
* **切面（Aspect）**：描述“通知 + 切入点”的对应关系（哪个通知加到哪些方法、加在什么位置）。

​	

### 11.4 AOP入门案例

#### 11.4.1 需求与思路

* 需求：在不修改原方法的情况下，在方法执行前打印当前系统时间。
* 思路（典型 AOP 开发流程）：
  1. 导入依赖（Spring + AspectJ Weaver）
  2. 准备原始业务（连接点：接口与实现类的方法）
  3. 抽取共性功能（通知类 + 通知方法）
  4. 定义切入点（描述哪些方法需要增强）
  5. 绑定通知与切入点（形成切面）
  6. 开启 AOP 注解支持并运行验证

#### 11.4.2 关键依赖

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.10.RELEASE</version>
  </dependency>

  <!-- Spring 整合 AspectJ 的实现需要 -->
  <dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
  </dependency>
</dependencies>
```

#### 11.4.3 基础代码结构示例

**1）连接点（原始业务）**

```java
public interface BookDao {
    void update();
}
```

```java
@Repository
public class BookDaoImpl implements BookDao {
    public void update() {
        System.out.println("book dao update ...");
    }
}
```

**2）切面（通知 + 切入点）**

```java
@Component
@Aspect
public class MyAdvice {

    @Pointcut("execution(void com.itheima.dao.BookDao.update())")
    private void pt(){}

    @Before("pt()")
    public void before(){
        System.out.println(System.currentTimeMillis());
    }
}
```

**3）开启 AOP（配置类）**

```java
@Configuration
@ComponentScan("com.itheima")
@EnableAspectJAutoProxy
public class SpringConfig {
}
```

**4）运行验证**

```java
public class App {
    public static void main(String[] args) {
        ApplicationContext ctx =
            new AnnotationConfigApplicationContext(SpringConfig.class);
        BookDao bookDao = ctx.getBean(BookDao.class);
        bookDao.update();
    }
}
```

#### 11.4.4 常用注解速记

* `@EnableAspectJAutoProxy`：开启注解 AOP 功能（写在配置类上）。
* `@Aspect`：标记当前类为切面类。
* `@Pointcut`：定义切入点表达式（通常用一个“无参无返回值的空方法”承载）。
* `@Before`：前置通知（切入点方法执行之前执行）。

​		

### 11.5 AOP工作流程

#### 11.5.1 容器启动到方法执行的流程

1. **Spring 容器启动**：加载需要被管理的 bean（目标类、通知类等），此时对象可能还未创建完成。
2. **读取切面配置**：扫描切面，解析切入点表达式。
3. **初始化 bean 并判定是否需要增强**  
   * 若类的方法 **匹配任意切入点**：创建**目标对象**并为其创建**代理对象**（后续从容器取到的是代理）。
   * 若不匹配：直接创建并存入原始对象（后续从容器取到的是原对象）。
4. **获取 bean 并调用方法**  
   * 取到原始对象：直接执行原方法  
   * 取到代理对象：由代理控制“原方法 + 通知”的执行顺序与组合

#### 11.5.2 关键结论

* 只有“命中切入点”的目标类，容器中才会放入它的**代理对象**。
* SpringAOP 本质：**代理模式实现增强**（对外表现为：你调用的是代理的方法）。

​	

## 12. AOP配置管理

### 12.1 切入点表达式

#### 12.1.1 标准语法结构（理解即可）

* `execution(访问修饰符 返回值 包名.类/接口名.方法名(参数) 异常名)`
* 常用动作关键字：`execution(...)`（表示匹配方法执行）

示例（精准匹配某个方法）：

```java
@Pointcut("execution(void com.itheima.dao.BookDao.update())")
private void pt(){}
```

#### 12.1.2 常见通配符

* `*`：
  * 返回值任意：`execution(* com.xxx... )`
  * 类名/方法名任意：`execution(void com.xxx.*.update())`
* `..`：
  * 参数任意个：`execution(* com.xxx.BookDao.findName(..))`
  * 多级包匹配（不建议滥用，匹配范围大、效率低）：`com..service..`
* `*` 匹配单层包：`com.*.*.*.update()`（只匹配固定层级）

#### 12.1.3 书写技巧（更贴近工程实践）

* **优先匹配接口而不是实现类**（降低耦合）。
* 面向接口开发时访问修饰符常用 `public`（很多时候可省略）。
* 增删改：尽量用精准返回值提升匹配速度；查询：可用 `*` 简化。
* 包名：尽量少用 `..`，常用 `*` 做单层包匹配或直接精准写包名。
* 类/接口名：模块相关可用 `*Service`、`*Dao` 等模式匹配。
* 方法名：动词尽量精准（如 `getBy*`），名词类可适度通配。
* 参数匹配较复杂，按业务灵活调整；通常不使用“异常”作为匹配条件。

​	

### 12.2 通知类型

#### 12.2.1 五种通知类型与执行位置

* **前置通知 `@Before`**：方法执行前
* **后置通知 `@After`**：方法执行后（无论是否抛异常都会执行）
* **环绕通知 `@Around`（重点）**：包裹方法执行前后（可替代其他通知）
* **返回后通知 `@AfterReturning`**：方法正常返回后
* **异常后通知 `@AfterThrowing`**：方法抛异常后

#### 12.2.2 环绕通知的关键点

* 环绕通知必须接收 `ProceedingJoinPoint`，并通过 `pjp.proceed()` 调用原始方法。
* `proceed()` 会抛 `Throwable`，所以通知方法一般 `throws Throwable`。
* **原始方法有返回值时**：环绕通知必须把返回值返回出去，否则可能出现返回值不匹配问题。

示例（含返回值的环绕通知写法）：

```java
@Around("pt()")
public Object around(ProceedingJoinPoint pjp) throws Throwable {
    System.out.println("around before advice ...");

    Object ret = pjp.proceed();  // 调用原始方法

    System.out.println("around after advice ...");
    return ret;                  // 关键：把返回值带回去
}
```

​	

### 12.3 通知中获取数据

### 12.3.1 获取参数

**非环绕通知获取参数：**

```java
@Before("pt()")
public void before(JoinPoint jp){
    Object[] args = jp.getArgs();
    System.out.println(Arrays.toString(args));
}
```

* `getArgs()` 返回数组：因为参数个数不固定，用数组更通用。

**环绕通知获取与修改参数：**

```java
@Around("pt()")
public Object around(ProceedingJoinPoint pjp) throws Throwable {
    Object[] args = pjp.getArgs();
    // ...可以读取/修改 args
    return pjp.proceed(args); // 用新参数执行原方法
}
```

### 12.3.2 获取返回值

```java
@AfterReturning(value = "pt()", returning = "ret")
public void afterReturning(Object ret) {
    System.out.println("afterReturning advice ..." + ret);
}
```

* 只有方法**正常返回**才会进入返回后通知。

### 12.3.3 获取异常

```java
@AfterThrowing(value = "pt()", throwing = "t")
public void afterThrowing(Throwable t) {
    System.out.println("afterThrowing advice ..." + t);
}
```

* 只有方法**抛出异常**才会进入异常后通知。

​	

## 13. Spring事务基础

### 13.1 事务与目标

* 事务作用：在数据层保障一系列数据库操作**同成功同失败**，保证数据一致性。
* 为什么一般加在业务层：一次完整业务往往包含多次数据层操作，需要统一提交/回滚。

### 13.2 Spring事务管理器

* Spring 事务管理核心接口：`PlatformTransactionManager`
* 常用实现：`DataSourceTransactionManager`
  * 基于 JDBC 事务
  * MyBatis 底层同样使用 JDBC，因此常用于 Spring + MyBatis 的事务管理

### 13.3 注解事务的启用与基本用法

* 在需要事务的方法上添加 `@Transactional`（通常是业务层方法）
* 在配置类上开启注解式事务支持：`@EnableTransactionManagement`
* 配置事务管理器（与数据源绑定）

```java
@Configuration
@EnableTransactionManagement
public class SpringConfig {
}
```

```java
@Bean
public PlatformTransactionManager transactionManager(DataSource dataSource){
    DataSourceTransactionManager tx = new DataSourceTransactionManager();
    tx.setDataSource(dataSource);
    return tx;
}
```

```java
@Service
public class XxxServiceImpl implements XxxService {

    @Transactional
    public void doBusiness() {
        // 多个数据库写操作：统一提交/回滚
    }
}
```

### 13.4 事务角色

* 事务管理员：发起事务的一方（通常是**开启事务的业务方法**）
* 事务协调员：加入事务的一方（通常是**数据层方法**，也可能是其他业务方法）
* 结果：管理员开启的事务会把相关操作纳入同一个事务边界，确保一致性。

​		

## 14. 事务属性与传播

### 14.1 @Transactional 常用属性

* `readOnly`
  * `true`：只读事务（常用于查询）
  * `false`：读写事务（增删改必须为 false）
* `timeout`：超时时间（秒），超时自动回滚（`-1` 表示不设置）
* `rollbackFor` / `rollbackForClassName`：遇到指定异常回滚
* `noRollbackFor` / `noRollbackForClassName`：遇到指定异常不回滚
* `isolation`：隔离级别
* `propagation`：传播行为

### 14.2 回滚规则与 rollbackFor

* Spring 默认回滚范围：
  * **Error**
  * **RuntimeException** 及其子类
* 对受检异常（非运行时异常）也要回滚：使用 `rollbackFor`

```java
public interface XxxService {
    void doBusiness() throws IOException;
}

@Service
public class XxxServiceImpl implements XxxService {

    @Transactional(rollbackFor = {IOException.class})
    public void doBusiness() throws IOException {
        // 写操作...
        // throw new IOException();
    }
}
```

### 14.3 isolation 隔离级别（常见）

* `DEFAULT`：采用数据库默认隔离级别
* `READ_UNCOMMITTED`：读未提交
* `READ_COMMITTED`：读已提交
* `REPEATABLE_READ`：重复读取
* `SERIALIZABLE`：串行化

### 14.4 传播行为与 REQUIRES_NEW（典型：日志独立提交）

* 传播行为：当“方法 A（已有事务）”调用“方法 B（也声明事务）”时，B 如何处理现有事务。
* 典型需求：主业务失败要回滚，但“日志/审计记录”必须保留。
* 解决：让日志方法使用**新事务**，与主业务事务隔离（`REQUIRES_NEW`）。

```java
@Service
public class LogServiceImpl implements LogService {

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void saveLog(String info) {
        // 写入日志表：无论外部事务是否回滚，都应独立提交
    }
}
```

### 14.5 propagation 常见取值（理解即可）

* `REQUIRED`：默认值；有事务加入，无事务新建
* `REQUIRES_NEW`：无论是否存在事务，都新建事务（原事务挂起）
* `SUPPORTS`：有事务加入，无事务以非事务方式执行
* `NOT_SUPPORTED`：以非事务方式执行；存在事务则挂起
* `MANDATORY`：必须存在事务，否则抛异常
* `NEVER`：必须不存在事务，否则抛异常
* `NESTED`：嵌套事务（依赖底层数据库支持）