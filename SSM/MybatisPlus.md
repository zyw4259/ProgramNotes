## 1. MyBatisPlus 概览与快速入门

### 1.1 定位与特性

- MyBatisPlus（MP）：基于 MyBatis 的增强工具，目标是**简化开发、提高效率**，是“最好搭档”而非替换
- 常见特性
  - 无侵入：只做增强不做改变
  - 内置通用 CRUD：继承通用 Mapper 即可完成单表增删改查
  - 条件构建器 Wrapper：以编程方式组合查询条件
  - 支持 Lambda：避免字段名写错
  - 支持主键策略、分页插件等

### 1.2 SpringBoot 整合 MP 的最小流程

- 关键步骤
  1. 建库建表（准备测试数据）
  2. 创建 SpringBoot 工程
  3. 引入依赖（MP starter；数据源可选 Druid）
  4. 配置数据源（application.yml）
  5. 创建实体类（与表字段对应）
  6. 创建 Dao 接口：继承 BaseMapper
  7. 启动类配置 Mapper 扫描（@Mapper 或 @MapperScan）
  8. 编写测试类验证 CRUD

- 依赖示例（pom.xml 关键段）
```xml
<dependency>
  <groupId>com.baomidou</groupId>
  <artifactId>mybatis-plus-boot-starter</artifactId>
  <version>3.4.1</version>
</dependency>

<!-- 可选：Druid 数据源 -->
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>druid</artifactId>
  <version>1.1.16</version>
</dependency>
```

- 数据源配置示例（application.yml）
```yml
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mybatisplus_db?serverTimezone=UTC
    username: root
    password: root
```

### 1.3 Dao 扫描与“注入报红”现象

- Dao 写法：继承 BaseMapper 即拥有大量通用方法
```java
@Mapper
public interface UserDao extends BaseMapper<User> {
}
```

- 扫描 Dao 两种方案
  - 方案一：每个 Dao 加 `@Mapper`
  - 方案二：启动类加 `@MapperScan("com.xxx.dao")`（推荐：写一次即可）

- 测试类里注入 Dao 报红的常见原因
  - Dao 是接口，代理对象需要在 SpringBoot 启动、IOC 初始化后才生成
  - 没启动时 IDE 找不到可注入实例，运行不影响

### 1.4 Lombok 简化实体类

- Lombok：通过注解自动生成 getter/setter/toString/构造器等，减少样板代码
- 常见注解
  - `@Data`：组合注解（getter/setter/toString/equals&hashCode）
  - `@NoArgsConstructor`、`@AllArgsConstructor`：无参/全参构造
- 依赖（版本通常可省略，SpringBoot 统一管理）
```xml
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
</dependency>
```

- 实体示例
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
  private Long id;
  private String name;
  private String password;
  private Integer age;
  private String tel;

  // 需要部分构造时，也可以手写（允许与 Lombok 共存）
  public User(String name, String password) {
    this.name = name;
    this.password = password;
  }
}
```

​	

## 2. BaseMapper 标准 CRUD 与分页

### 2.1 CRUD 方法体系

- BaseMapper 提供大量通用方法，典型高频：
  - 新增：`insert(T t)`
  - 删除：`deleteById(Serializable id)`
  - 修改：`updateById(T t)`
  - 查单个：`selectById(Serializable id)`、`selectOne(Wrapper<T> qw)`
  - 查多个：`selectList(Wrapper<T> qw)`、`selectBatchIds(Collection<? extends Serializable> ids)`

- 参数类型为何常用 `Serializable`
  - 常见主键类型（String、Long、Integer 等）都实现了 Serializable
  - 用 Serializable 覆盖面更大，类似用 Object 接收多种类型

### 2.2 新增 insert

- 特点：传入实体对象，返回影响行数（成功 1，失败 0）
```java
@SpringBootTest
class MpTests {
  @Autowired private UserDao userDao;

  @Test
  void testInsert() {
    User user = new User();
    user.setName("黑马程序员");
    user.setPassword("itheima");
    user.setAge(12);
    user.setTel("4006184000");
    userDao.insert(user);
  }
}
```

- 注意：主键值如何生成与“主键策略”相关（后续章节会讲）

### 2.3 删除 deleteById

- 特点：按主键删除，返回影响行数
```java
@Test
void testDeleteById() {
  userDao.deleteById(1401856123725713409L);
}
```

### 2.4 修改 updateById

- 特点
  - 按主键更新：实体对象必须有 id
  - **只会更新实体中“有值”的字段**（null 字段通常不参与更新）
```java
@Test
void testUpdateById() {
  User user = new User();
  user.setId(1L);
  user.setName("Tom888");
  user.setPassword("tom888");
  userDao.updateById(user);
}
```

### 2.5 查询 selectById / selectList

- 按 id 查一条
```java
@Test
void testSelectById() {
  User user = userDao.selectById(2L);
  System.out.println(user);
}
```

- 查全部（不加条件传 null）
```java
@Test
void testSelectAll() {
  List<User> list = userDao.selectList(null);
  System.out.println(list);
}
```

### 2.6 分页查询 selectPage

- 方法原型（核心点）
  - `IPage<T> page`：分页参数（当前页、每页条数）
  - `Wrapper<T> qw`：条件，可为 null
- IPage 常用实现类：`Page<T>`

- 分页测试示例
```java
@Test
void testSelectPage() {
  IPage<User> page = new Page<>(1, 3); // 当前页=1，每页3条
  userDao.selectPage(page, null);

  System.out.println("当前页码：" + page.getCurrent());
  System.out.println("每页条数：" + page.getSize());
  System.out.println("总页数：" + page.getPages());
  System.out.println("总记录数：" + page.getTotal());
  System.out.println("当前页数据：" + page.getRecords());
}
```

- 分页拦截器配置（必须）
```java
@Configuration
public class MybatisPlusConfig {
  @Bean
  public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    interceptor.addInnerInterceptor(new PaginationInnerInterceptor());
    return interceptor;
  }
}
```

### 2.7 SQL 日志输出（调试用）

- 打开 SQL 输出（调试完建议关闭，避免影响性能）
```yml
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

​	

## 3. DQL 条件查询与映射兼容

### 3.1 Wrapper 条件构建的三种常用写法

- 写法一：QueryWrapper（字段名字符串，容易写错）
```java
@Test
void testQueryWrapper() {
  QueryWrapper<User> qw = new QueryWrapper<>();
  qw.lt("age", 18); // age < 18
  List<User> list = userDao.selectList(qw);
  System.out.println(list);
}
```

- 写法二：QueryWrapper + lambda（仍基于 QueryWrapper，但字段用方法引用）
```java
@Test
void testQueryWrapperLambda() {
  QueryWrapper<User> qw = new QueryWrapper<>();
  qw.lambda().lt(User::getAge, 10);
  List<User> list = userDao.selectList(qw);
  System.out.println(list);
}
```

- 写法三：LambdaQueryWrapper（更直接、常用）
```java
@Test
void testLambdaQueryWrapper() {
  LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<>();
  lqw.lt(User::getAge, 10);
  List<User> list = userDao.selectList(lqw);
  System.out.println(list);
}
```

### 3.2 多条件、链式与 or

- 多条件：默认 AND，可链式
```java
@Test
void testMultiConditionAnd() {
  LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<>();
  lqw.lt(User::getAge, 30)
     .gt(User::getAge, 10); // 10 < age < 30
  List<User> list = userDao.selectList(lqw);
  System.out.println(list);
}
```

- OR 条件：`or()` 相当于 SQL 的 OR
```java
@Test
void testConditionOr() {
  LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<>();
  lqw.lt(User::getAge, 10).or().gt(User::getAge, 30);
  List<User> list = userDao.selectList(lqw);
  System.out.println(list);
}
```

### 3.3 条件非空判定（动态查询）

- 典型场景：前端多个可选条件，没填的不能拼进 SQL（避免 `... < null`）
- 常见做法：在 wrapper 方法里用 condition 参数控制是否拼接

- 示例：根据年龄区间（age、age2）动态拼装
```java
@Data
public class UserQuery extends User {
  private Integer age2;
}

@Test
void testNullCondition() {
  UserQuery uq = new UserQuery();
  uq.setAge(10);
  uq.setAge2(30);

  LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<>();
  lqw.lt(uq.getAge2() != null, User::getAge, uq.getAge2());
  lqw.gt(uq.getAge()  != null, User::getAge, uq.getAge());

  List<User> list = userDao.selectList(lqw);
  System.out.println(list);
}
```

### 3.4 查询投影：指定字段、聚合、分组

- 指定字段查询（投影）：不查所有列，只查需要的列
```java
@Test
void testSelectFieldsByLambda() {
  LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<>();
  lqw.select(User::getId, User::getName, User::getAge);
  List<User> list = userDao.selectList(lqw);
  System.out.println(list);
}
```

- 非 lambda 写法：手动写字段名
```java
@Test
void testSelectFieldsByString() {
  QueryWrapper<User> qw = new QueryWrapper<>();
  qw.select("id", "name", "age", "tel");
  List<User> list = userDao.selectList(qw);
  System.out.println(list);
}
```

- 聚合查询：通常用 `selectMaps` 承接（返回 Map）
```java
@Test
void testAggregate() {
  QueryWrapper<User> qw = new QueryWrapper<>();
  // qw.select("count(*) as count");
  // qw.select("max(age) as maxAge");
  // qw.select("min(age) as minAge");
  // qw.select("sum(age) as sumAge");
  qw.select("avg(age) as avgAge");
  List<Map<String, Object>> list = userDao.selectMaps(qw);
  System.out.println(list);
}
```

- 分组查询：`groupBy("tel")` 等
  - 注意：聚合与分组通常无法用 lambda 方式表达时，可用字符串字段名
```java
@Test
void testGroupBy() {
  QueryWrapper<User> qw = new QueryWrapper<>();
  qw.select("count(*) as count", "tel");
  qw.groupBy("tel");
  List<Map<String, Object>> list = userDao.selectMaps(qw);
  System.out.println(list);
}
```

### 3.5 常用条件方法速记

- 等值：`eq`（=）
```java
@Test
void testEqLogin() {
  LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<>();
  lqw.eq(User::getName, "Jerry")
     .eq(User::getPassword, "jerry");
  User user = userDao.selectOne(lqw);
  System.out.println(user);
}
```

- 范围：`lt/le/gt/ge/between`
```java
@Test
void testBetween() {
  LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<>();
  lqw.between(User::getAge, 10, 30);
  List<User> list = userDao.selectList(lqw);
  System.out.println(list);
}
```

- 模糊：`like / likeLeft / likeRight`
```java
@Test
void testLike() {
  LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<>();
  lqw.likeRight(User::getName, "J"); // J%
  List<User> list = userDao.selectList(lqw);
  System.out.println(list);
}
```

- 排序：`orderBy / orderByAsc / orderByDesc`
```java
@Test
void testOrderBy() {
  LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<>();
  lqw.orderBy(true, false, User::getId); // condition=true, isAsc=false => id desc
  List<User> list = userDao.selectList(lqw);
  System.out.println(list);
}
```

### 3.6 映射兼容：字段名/表名不一致与额外字段处理

- 典型问题
  - 表字段名 ≠ 实体属性名：查询封装失败
  - 实体多了表不存在的属性：生成 SQL 会查不存在列，运行报错
  - 默认查询全部字段：可能把敏感字段查出并返回前端
  - 表名 ≠ 类名：默认按“类名首字母小写”映射表名，可能查不到表

- @TableField：解决字段映射、排除字段、控制默认查询
  - `@TableField(value="列名")`：属性 ↔ 列名映射
  - `@TableField(exist=false)`：该属性不对应表字段（不参与 SQL）
  - `@TableField(select=false)`：默认不查询该字段（如 password）

- @TableName：解决表名映射
  - `@TableName("tbl_user")`：类 ↔ 表名映射

- 综合示例
```java
@Data
@TableName("tbl_user")
public class User {
  private Long id;
  private String name;

  @TableField(value = "pwd", select = false)
  private String password;

  private Integer age;
  private String tel;

  @TableField(exist = false)
  private Integer online;
}
```

​		

## 4. DML 编程控制

### 4.1 主键策略控制

#### 4.1.1 @TableId 与 IdType

- 作用：在实体类主键属性上，**指定主键生成策略**
- 注解：`@TableId`
  - `value`：表主键列名（默认不写也可按约定映射）
  - `type`：主键策略（`IdType` 枚举）

- 常见 IdType（课程重点）
  - `NONE`：不设置策略（≈ 需要手动提供）
  - `INPUT`：手工输入 ID（必须自己 setId）
  - `AUTO`：数据库自增（表必须设置主键自增）
  - `ASSIGN_ID`：雪花算法生成（数值/字符串都可兼容，常用 Long）
  - `ASSIGN_UUID`：UUID 生成（一般用 String，32 位）

#### 4.1.2 AUTO 策略（数据库自增）

- 核心点
  - 表主键要开启自增，否则无效
  - 之前用过长 ID 可能导致自增值很大，演示时需清理测试数据并调整自增起始值

```java
@Data
@TableName("tbl_user")
public class User {
  @TableId(type = IdType.AUTO)
  private Long id;

  private String name;

  @TableField(value="pwd", select=false)
  private String password;

  private Integer age;
  private String tel;

  @TableField(exist=false)
  private Integer online;
}
```

#### 4.1.3 INPUT 策略（手工输入 ID）

- 核心点
  - 表不要用自增（否则与手工输入冲突）
  - insert 前必须 setId，否则会因主键为空报错

```java
@Data
@TableName("tbl_user")
public class User {
  @TableId(type = IdType.INPUT)
  private Long id;

  private String name;

  @TableField(value="pwd", select=false)
  private String password;

  private Integer age;
  private String tel;

  @TableField(exist=false)
  private Integer online;
}
```

```java
@Test
void testSave() {
  User user = new User();
  user.setId(666L); // 必须手动设置
  user.setName("黑马程序员");
  user.setPassword("itheima");
  user.setAge(12);
  user.setTel("4006184000");
  userDao.insert(user);
}
```

#### 4.1.4 ASSIGN_ID（雪花算法）与 ASSIGN_UUID

- ASSIGN_ID
  - 不需要手动设置 ID（不设会自动生成）
  - 如果手动 setId，则优先使用自己设置的值
  - 生成结果通常是 Long 类型数字，可排序、性能较好

```java
@Data
@TableName("tbl_user")
public class User {
  @TableId(type = IdType.ASSIGN_ID)
  private Long id;
  private String name;

  @TableField(value="pwd", select=false)
  private String password;

  private Integer age;
  private String tel;

  @TableField(exist=false)
  private Integer online;
}
```

```java
@Test
void testSave() {
  User user = new User();
  user.setName("黑马程序员");
  user.setPassword("itheima");
  user.setAge(12);
  user.setTel("4006184000");
  userDao.insert(user); // 不设置 id
}
```

- ASSIGN_UUID
  - 主键类型一般改成 String
  - 表主键类型应为 varchar，长度要 > 32（避免插入失败）

```java
@Data
@TableName("tbl_user")
public class User {
  @TableId(type = IdType.ASSIGN_UUID)
  private String id;
  private String name;

  @TableField(value="pwd", select=false)
  private String password;

  private Integer age;
  private String tel;

  @TableField(exist=false)
  private Integer online;
}
```

#### 4.1.5 雪花算法结构（理解题/面试题）

- 64bit 整数结构（常考理解点）
  - 1bit：符号位（固定 0）
  - 41bit：时间戳（毫秒级）
  - 10bit：机器 ID（数据中心 5bit + 工作节点 5bit，共 1024 节点）
  - 12bit：序列号（每毫秒 0~4095，共 4096 个）

#### 4.1.6 主键策略对比与选择

- `NONE / INPUT`
  - 需要手动设置，容易主键冲突，实现复杂
- `AUTO`
  - 单库/单机适用；分布式可能冲突
- `ASSIGN_UUID`
  - 分布式可用且唯一；但字符串长、不可排序、占空间、查询性能相对差
- `ASSIGN_ID`
  - 分布式可用；Long 可排序性能好；与服务器时间有关（系统时间异常可能带来风险）
- 结论：按业务选择（日志、自增；订单、规则 ID；分布式建议全局唯一）

#### 4.1.7 全局简化配置（减少重复注解）

- 统一主键策略（所有模型类默认一致）
```yml
mybatis-plus:
  global-config:
    db-config:
      id-type: assign_id
```

- 表名前缀（比如表都以 `tbl_` 开头）
  - MP 默认表名：模型类名首字母小写
  - 配置前缀后：`tbl_` + `user` => `tbl_user`

```yml
mybatis-plus:
  global-config:
    db-config:
      table-prefix: tbl_
```

### 4.2 多记录操作（批量）

- 批量删除（按 ID 集合）
```java
// int deleteBatchIds(Collection<? extends Serializable> idList);

@Test
void testDeleteBatch() {
  List<Long> list = new ArrayList<>();
  list.add(1402551342481838081L);
  list.add(1402553134049501186L);
  list.add(1402553619611430913L);
  userDao.deleteBatchIds(list);
}
```

- 批量查询（按 ID 集合）
```java
// List<T> selectBatchIds(Collection<? extends Serializable> idList);

@Test
void testGetByIds() {
  List<Long> list = new ArrayList<>();
  list.add(1L);
  list.add(3L);
  list.add(4L);
  userDao.selectBatchIds(list);
}
```

### 4.3 逻辑删除

#### 4.3.1 物理删除 vs 逻辑删除

- 物理删除：`delete`，数据从库中移除
- 逻辑删除：保留数据，通过状态字段区分（如 deleted：0 正常 / 1 删除），执行本质是 `update`
- 典型业务原因：主外键/历史统计/垃圾数据等问题

#### 4.3.2 实现步骤（@TableLogic）

- 步骤1：表添加删除标记列（例如 `deleted`，默认 0）
- 步骤2：实体类加属性 + 标记逻辑删除字段

```java
@Data
public class User {
  // 省略其它字段...

  @TableLogic(value="0", delval="1")
  // value：未删除值；delval：已删除值
  private Integer deleted;
}
```

- 步骤3：执行删除（会转成 update）
```java
@Test
void testDelete() {
  userDao.deleteById(1L);
}
```

#### 4.3.3 对查询的影响与“查全部”方案

- MP 会在查询时自动追加“未删除”条件（已删除数据不会被查出）
- 如果需要把已删除也查出来：自定义 SQL（绕开逻辑删除条件）

```java
@Mapper
public interface UserDao extends BaseMapper<User> {
  @Select("select * from tbl_user")
  List<User> selectAll(); // 包含已删除数据
}
```

#### 4.3.4 逻辑删除全局配置（优化每表都写注解）

```yml
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: deleted
      logic-not-delete-value: 0
      logic-delete-value: 1
```

- 逻辑删除本质 SQL（理解点）
```sql
UPDATE tbl_user SET deleted=1 WHERE id=? AND deleted=0
```

#### 4.3.5 知识点速记：@TableLogic

- 名称：`@TableLogic`
- 类型：属性注解
- 位置：逻辑删除字段属性上
- 作用：标识逻辑删除字段（删除走 update，查询自动过滤）

### 4.4 乐观锁

#### 4.4.1 解决问题与核心思想

- 目标：更新一条记录时，希望该记录**没被别人改过**
- 做法：表加 `version` 字段，更新时带上旧 version 作为条件
  - 成功：version + 1
  - 失败：where 条件不成立，更新行数为 0（说明被并发修改）

#### 4.4.2 实现步骤（@Version + 拦截器）

- 步骤1：表添加 `version` 列（默认 1）
- 步骤2：实体类增加字段并标注 `@Version`

```java
@Data
public class User {
  // 省略其它字段...

  @Version
  private Integer version;
}
```

- 步骤3：配置乐观锁拦截器（MP 新拦截器体系）

```java
@Configuration
public class MpConfig {
  @Bean
  public MybatisPlusInterceptor mpInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
    return interceptor;
  }
}
```

#### 4.4.3 更新时必须携带 version（做题/排错点）

- 只 new 一个对象不带 version，version 不会自动更新（因为缺少条件）
- 正确做法：先查再改再更新（携带 version）

```java
@Test
void testUpdate() {
  // 1) 先查出要改的那条（带 version）
  User user = userDao.selectById(3L);

  // 2) 修改属性
  user.setName("Jock888");

  // 3) updateById（where 会带 version）
  userDao.updateById(user);
}
```

#### 4.4.4 并发模拟（理解题）

```java
@Test
void testUpdateConcurrent() {
  User user1 = userDao.selectById(3L); // version=3
  User user2 = userDao.selectById(3L); // version=3

  user2.setName("Jock aaa");
  userDao.updateById(user2); // 成功：version => 4

  user1.setName("Jock bbb");
  userDao.updateById(user1); // 失败：where version=3 不成立
}
```

​	

## 5. 快速开发

### 5.1 代码生成器原理

- 生成器的本质：**用模板 + 数据库元信息 + 配置**，生成重复度高的代码
- 需要的三类信息
  - 模板：MP 提供（也可自定义，但成本高）
  - 数据库配置：连接库，读取表/字段信息
  - 开发者配置：包名、输出路径、作者、ID 策略、要生成哪些表、表前缀等

### 5.2 代码生成器实现（AutoGenerator）

#### 5.2.1 关键依赖

- MyBatisPlus 生成器依赖 + 模板引擎（Velocity）

```xml
<!-- 代码生成器 -->
<dependency>
  <groupId>com.baomidou</groupId>
  <artifactId>mybatis-plus-generator</artifactId>
  <version>3.4.1</version>
</dependency>

<!-- velocity 模板引擎 -->
<dependency>
  <groupId>org.apache.velocity</groupId>
  <artifactId>velocity-engine-core</artifactId>
  <version>2.3</version>
</dependency>
```

#### 5.2.2 生成器核心代码骨架（配置项理解）

```java
public class CodeGenerator {
  public static void main(String[] args) {
    AutoGenerator autoGenerator = new AutoGenerator();

    // 1) 数据源配置
    DataSourceConfig dataSource = new DataSourceConfig();
    dataSource.setDriverName("com.mysql.cj.jdbc.Driver");
    dataSource.setUrl("jdbc:mysql://localhost:3306/mybatisplus_db?serverTimezone=UTC");
    dataSource.setUsername("root");
    dataSource.setPassword("root");
    autoGenerator.setDataSource(dataSource);

    // 2) 全局配置
    GlobalConfig globalConfig = new GlobalConfig();
    globalConfig.setOutputDir(System.getProperty("user.dir") + "/mybatisplus_04_generator/src/main/java");
    globalConfig.setOpen(false);
    globalConfig.setAuthor("黑马程序员");
    globalConfig.setFileOverride(true);
    globalConfig.setMapperName("%sDao");
    globalConfig.setIdType(IdType.ASSIGN_ID);
    autoGenerator.setGlobalConfig(globalConfig);

    // 3) 包名配置（示例）
    PackageConfig packageInfo = new PackageConfig();
    packageInfo.setParent("com.aaa");     // 生成代码的父包
    packageInfo.setEntity("domain");      // 实体包名
    packageInfo.setMapper("dao");         // mapper 包名
    autoGenerator.setPackageInfo(packageInfo);

    // 4) 策略配置
    StrategyConfig strategyConfig = new StrategyConfig();
    strategyConfig.setInclude("tbl_user");              // 参与生成的表（可变参数）
    strategyConfig.setTablePrefix("tbl_");              // 表前缀
    strategyConfig.setRestControllerStyle(true);        // REST 风格 controller
    strategyConfig.setVersionFieldName("version");      // 乐观锁字段
    strategyConfig.setLogicDeleteFieldName("deleted");  // 逻辑删除字段
    strategyConfig.setEntityLombokModel(true);          // Lombok 实体
    autoGenerator.setStrategy(strategyConfig);

    // 5) 执行生成
    autoGenerator.execute();
  }
}
```

- 运行后一般会生成：`controller / service / mapper / entity` 等结构代码

### 5.3 MP 的 Service 层 CRUD

#### 5.3.1 IService 与 ServiceImpl

- 传统写法：自己定义接口、实现类、注入 Dao、写 findAll 等方法（重复度高）
- MP 提供
  - `IService<T>`：通用 Service 接口
  - `ServiceImpl<M, T>`：通用实现（M 通常是 Mapper/Dao）

#### 5.3.2 改造方式（核心写法）

```java
// 接口：继承 IService，获得通用 CRUD
public interface IUserService extends IService<User> {
}
```

```java
// 实现：继承 ServiceImpl 并实现自己的接口
@Service
public class UserServiceImpl extends ServiceImpl<UserDao, User> implements IUserService {
}
```

#### 5.3.3 常用调用示例

```java
@SpringBootTest
class Mybatisplus04GeneratorApplicationTests {

  @Autowired
  private IUserService userService;

  @Test
  void testFindAll() {
    List<User> list = userService.list(); // 通用查询
    System.out.println(list);
  }
}
```

- 说明：Service 层可用的方法很多（如 save、removeById、updateById、page 等），方法名可能随版本有差异，但参数/返回值整体一致

（第 2 部分结束）

