## 1. SpringMVC基础与工程搭建

### 1.1 定位与交互方式

- SpringMVC：基于 Java 的 MVC Web 框架，常用于接收请求、参数绑定、调用业务层并返回数据
- 同步（传统）
  - controller 组装 Model + View → 视图解析 → 返回页面
- 异步（主流前后端分离）
  - 后端专注返回数据（常见 JSON），前端负责渲染页面

### 1.2 核心组件与流程

- DispatcherServlet：前端控制器，统一接收与分发请求
- 流程要点
  - 请求进入 DispatcherServlet
  - 根据映射匹配处理器方法
  - 调用 Controller 方法
  - 返回结果
    - 返回页面：走视图解析
    - 返回数据：直接写回响应（常用 JSON）

### 1.3 入门搭建要点

- Maven Web（war）
- 关键依赖
  - `spring-webmvc`
  - `javax.servlet-api` 必须 `provided`（避免与 Tomcat 内置 jar 冲突）

```xml
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>javax.servlet-api</artifactId>
  <version>3.1.0</version>
  <scope>provided</scope>
</dependency>
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webmvc</artifactId>
  <version>5.2.10.RELEASE</version>
</dependency>
```

- Web3.0 初始化（替代 web.xml）
  - 继承 `AbstractAnnotationConfigDispatcherServletInitializer`
  - 三个核心点
    - 加载 Spring 容器（Root）
    - 加载 SpringMVC 容器（Servlet）
    - 配置拦截路径（常用 `/`）

```java
public class ServletContainersInitConfig
        extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMvcConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}
```

- 控制器与返回数据
  - `@Controller`：声明控制器
  - `@RequestMapping`：配置路径
  - `@ResponseBody`：返回值直接作为响应内容（不走视图）

```java
@Controller
public class UserController {

    @RequestMapping("/save")
    @ResponseBody
    public String save() {
        System.out.println("user save .");
        return "{'info':'springmvc'}";
    }
}
```

### 1.4 双容器分层与扫描控制

- 容器职责
  - Spring（Root 容器）：service/dao 等业务 bean
  - SpringMVC（Servlet 容器）：controller 与 MVC 相关组件
- 扫描控制：Spring 扫描业务包时排除 `@Controller`，避免把 controller 放进 Root 容器

```java
@Configuration
@ComponentScan(
    value = "com.itheima",
    excludeFilters = @ComponentScan.Filter(
        type = FilterType.ANNOTATION,
        classes = Controller.class
    )
)
public class SpringConfig {
}
```

- 易错点（理解即可）
  - SpringConfig 扫到 SpringMvcConfig（`@Configuration` 也是 bean）
  - SpringMvcConfig 的 `@ComponentScan` 可能把 controller 又扫回来
- 处理思路
  - 分包分层：Root 扫业务包，MVC 扫 controller/config 包，避免互相覆盖
  - 或缩小/精确 SpringMvcConfig 的扫描范围
  
  

## 2. 请求处理与参数绑定

### 2.1 接口测试工具

- Postman：无需写页面即可发送请求（GET/POST），常用于接口调试与回归

### 2.2 请求映射

- `@RequestMapping`：可写在类上/方法上
  - 类上：模块前缀（推荐，用于区分模块）
  - 方法上：具体功能路径

```java
@Controller
@RequestMapping("/user")
public class UserController {

    @RequestMapping("/save")
    @ResponseBody
    public String save() {
        return "{'module':'user save'}";
    }
}
```

### 2.3 参数类型与绑定

#### 2.3.1 普通参数

- 参数名一致即可自动绑定
- 不一致用 `@RequestParam("请求参数名")`
- `@RequestParam` 常用属性
  - `required`
  - `defaultValue`

```java
@RequestMapping("/commonParam")
@ResponseBody
public String commonParam(String name, Integer age) {
    System.out.println("name=" + name + ", age=" + age);
    return "{'module':'common param'}";
}

@RequestMapping("/commonParam2")
@ResponseBody
public String commonParam2(@RequestParam("name") String username) {
    return "{'module':'common param2'}";
}
```

#### 2.3.2 POJO 与嵌套 POJO

- POJO：请求参数名与属性名一致即可封装

```java
@RequestMapping("/pojoParam")
@ResponseBody
public String pojoParam(User user) {
    System.out.println(user);
    return "{'module':'pojo param'}";
}
```

- 嵌套对象：用“对象名.属性名”传参

```text
address.province=beijing&address.city=beijing
```

#### 2.3.3 数组与集合

- 数组/集合：同名参数重复出现即可绑定
- List 常配合 `@RequestParam`

```java
@RequestMapping("/listParam")
@ResponseBody
public String listParam(@RequestParam List<String> likes) {
    System.out.println(likes);
    return "{'module':'list param'}";
}
```

### 2.4 JSON 参数

- 使用场景：`Content-Type: application/json`
- 必做步骤
  - 导入 jackson 依赖
  - 配置类加 `@EnableWebMvc`（开启注解驱动与消息转换）
  - 形参加 `@RequestBody`（从请求体读取 JSON）
  - Postman 用 raw → JSON 发送
- `@RequestBody` 注意
  - 读取请求体 JSON
  - 同一方法通常只写一次

#### 2.4.1 JSON 数组 → List

```java
@RequestMapping("/listParamForJson")
@ResponseBody
public String listParamForJson(@RequestBody List<String> likes) {
    System.out.println(likes);
    return "{'module':'list common for json param'}";
}
```

#### 2.4.2 JSON 对象 → POJO

```json
{
  "name": "itcast",
  "age": 15
}
```

```java
@RequestMapping("/pojoParamForJson")
@ResponseBody
public String pojoParamForJson(@RequestBody User user) {
    System.out.println(user);
    return "{'module':'pojo for json param'}";
}
```

#### 2.4.3 JSON 对象数组 → List<POJO>

```json
[
  {"name":"itcast","age":15},
  {"name":"itheima","age":12}
]
```

```java
@RequestMapping("/listPojoParamForJson")
@ResponseBody
public String listPojoParamForJson(@RequestBody List<User> list) {
    System.out.println(list);
    return "{'module':'list pojo for json param'}";
}
```

#### 2.4.4 @RequestBody 与 @RequestParam

- `@RequestParam`：URL/表单参数（常见 `application/x-www-form-urlencoded`）
- `@RequestBody`：请求体 JSON（`application/json`）

  

### 2.5 乱码处理

- 配置 `CharacterEncodingFilter` 强制 UTF-8

```java
@Override
protected Filter[] getServletFilters() {
    CharacterEncodingFilter filter = new CharacterEncodingFilter();
    filter.setEncoding("UTF-8");
    filter.setForceEncoding(true);
    return new Filter[]{filter};
}
```

​	

### 2.6 静态资源放行

- 当拦截路径为 `/`：静态资源也会被 SpringMVC 拦截 → 404
- 解决：资源映射放行（并确保该配置类被 SpringMVC 扫描到）

```java
@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {
    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
        registry.addResourceHandler("/js/**").addResourceLocations("/js/");
        registry.addResourceHandler("/css/**").addResourceLocations("/css/");
        registry.addResourceHandler("/plugins/**").addResourceLocations("/plugins/");
    }
}
```

```java
@Configuration
@ComponentScan({"com.itheima.controller","com.itheima.config"})
@EnableWebMvc
public class SpringMvcConfig {
}
```

​	

### 2.7 日期时间参数转换

- `@DateTimeFormat`：指定日期字符串格式，辅助 String → Date 转换

```java
@RequestMapping("/dataParam")
@ResponseBody
public String dataParam(
        Date date,
        @DateTimeFormat(pattern="yyyy-MM-dd") Date date1,
        @DateTimeFormat(pattern="yyyy/MM/dd HH:mm:ss") Date date2) {
    System.out.println("date ==> " + date);
    System.out.println("date1 ==> " + date1);
    System.out.println("date2 ==> " + date2);
    return "{'module':'data param'}";
}
```

- 类型转换相关接口（理解即可）
  - `Converter`：String → Integer / Date 等
  - `HttpMessageConverter`：对象 ↔ JSON
  
  

### 2.8 类型转换原理

- 两类“转换器”
  - `Converter`：处理常见类型转换（如 `String -> Integer`、`String -> Date`）
  - `HttpMessageConverter`：处理对象与 JSON 的互转（`@RequestBody/@ResponseBody` 生效的关键）
- 经验
  - SpringMVC 配置类基本标配 `@EnableWebMvc`（开启注解驱动与消息转换能力）
  - 日期字符串格式不一致时，用 `@DateTimeFormat` 明确指定

```java
public interface Converter<S, T> {
    T convert(S source);
}
```

```java
@RequestMapping("/dataParam")
@ResponseBody
public String dataParam(
        Date date,
        @DateTimeFormat(pattern="yyyy-MM-dd") Date date1,
        @DateTimeFormat(pattern="yyyy/MM/dd HH:mm:ss") Date date2) {
    System.out.println("date ==> " + date);
    System.out.println("date1 ==> " + date1);
    System.out.println("date2 ==> " + date2);
    return "{'module':'data param'}";
}
```

​	

### 2.9 响应处理

- 响应页面
  - 返回值为页面名/视图名
  - 不要加 `@ResponseBody`（否则当文本写回）
- 响应数据
  - 文本：`String + @ResponseBody`
  - JSON：`Object/Collection + @ResponseBody`（并确保 jackson 依赖 + `@EnableWebMvc`）

```java
// 响应页面：不要加 @ResponseBody
@RequestMapping("/toJumpPage")
public String toJumpPage() {
    System.out.println("jump page");
    return "page.jsp";
}
```

```java
// 响应文本：必须加 @ResponseBody
@RequestMapping("/toText")
@ResponseBody
public String toText() {
    System.out.println("text");
    return "response text";
}
```

```java
// 响应 JSON：返回 POJO
@RequestMapping("/toJsonPOJO")
@ResponseBody
public User toJsonPOJO() {
    User user = new User();
    user.setName("itcast");
    user.setAge(15);
    return user;
}
```

```java
// 响应 JSON：返回集合
@RequestMapping("/toJsonList")
@ResponseBody
public List<User> toJsonList() {
    User user1 = new User();
    user1.setName("传智播客");
    user1.setAge(15);

    User user2 = new User();
    user2.setName("黑马程序员");
    user2.setAge(12);

    List<User> list = new ArrayList<>();
    list.add(user1);
    list.add(user2);
    return list;
}
```

- `@ResponseBody` 用法
  - 方法上：仅该方法返回值写回响应体
  - 类上：该类所有方法默认写回响应体（常用 `@RestController` 替代）
  
  

## 3. REST与静态资源

### 3.1 REST约定

- 资源用路径表示，动作用请求方式表示
  - 查询：GET
  - 新增：POST
  - 修改：PUT
  - 删除：DELETE
- 常配套
  - `@PathVariable`：从路径取参数（如 `/books/{id}`）
  - `@RequestBody`：从请求体取 JSON

### 3.2 常用注解简化

- 方法映射简化
  - `@GetMapping / @PostMapping / @PutMapping / @DeleteMapping`
- 控制器简化
  - `@RestController = @Controller + @ResponseBody`

```java
@RestController
@RequestMapping("/books")
public class BookController {

    @PostMapping
    public String save(@RequestBody Book book) {
        System.out.println("book save." + book);
        return "{'module':'book save'}";
    }

    @PutMapping
    public String update(@RequestBody Book book) {
        System.out.println("book update." + book);
        return "{'module':'book update'}";
    }

    @GetMapping("/{id}")
    public String getById(@PathVariable Integer id) {
        System.out.println("book getById." + id);
        return "{'module':'book getById'}";
    }

    @GetMapping
    public String getAll() {
        System.out.println("book getAll.");
        return "{'module':'book getAll'}";
    }
}
```

### 3.3 静态资源访问

- 问题来源
  - `DispatcherServlet` 映射为 `/` 时，静态资源也会被 SpringMVC 拦截
  - 未配置放行 → 静态页面/JS/CSS 等 404
- 解决方式
  - 配置资源处理器映射（并确保该配置类被 SpringMVC 扫描到）

```java
@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {
    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
        registry.addResourceHandler("/js/**").addResourceLocations("/js/");
        registry.addResourceHandler("/css/**").addResourceLocations("/css/");
        registry.addResourceHandler("/plugins/**").addResourceLocations("/plugins/");
    }
}
```

​	

## 4. 拦截器

### 4.1 概念与作用

- 拦截器（Interceptor）：SpringMVC 提供的一种“请求增强”机制，在请求到达 Controller 前后插入自定义逻辑，用于：
  - 登录校验 / 权限校验
  - 统一日志、性能统计
  - 参数预处理、统一编码处理等
- 静态资源与动态资源（理解拦截器作用范围）
  - 静态资源（html/js/css/图片等）：通常由 Tomcat 直接处理
  - 动态资源（Controller 处理的请求）：会进入 SpringMVC 流程，拦截器主要作用于这一类请求
- 与过滤器（Filter）的区别（核心）
  - Filter：Servlet 规范的一部分，作用范围更底层，能拦截更广（进入 Web 容器的请求）
  - Interceptor：SpringMVC 体系内的增强，只对进入 SpringMVC 的请求生效，更贴近业务层

### 4.2 入门开发与配置

- 开发步骤（核心 3 步）
  1) 创建拦截器类：实现 `HandlerInterceptor` 接口（常用只重写 `preHandle`）
  2) 配置拦截器：在 SpringMVC 配置中注册拦截器并指定拦截路径
  3) 运行测试：访问接口观察拦截器是否执行

- 拦截器类示例（前置处理）

```java
@Component
public class ProjectInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request,
                             HttpServletResponse response,
                             Object handler) throws Exception {
        System.out.println("preHandle...");
        return true; // true 放行，false 拦截
    }
}
```

- 注册拦截器（在 SpringMVC 配置类中）

```java
@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {

    @Autowired
    private ProjectInterceptor projectInterceptor;

    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(projectInterceptor)
                .addPathPatterns("/books", "/books/*"); // 按需配置
    }
}
```

- 关键结论
  - `preHandle()` 返回 `true`：放行，继续执行 Controller 方法
  - `preHandle()` 返回 `false`：拦截，后续流程不再执行（包含 Controller 与后置方法）

### 4.3 拦截路径匹配规则

- 常见“拦截器没生效”的原因：路径不匹配
  - 例：只配置了 `"/books"`，访问 `"/books/100"` 不会匹配，因此不拦截
- 处理方式：补充匹配规则
  - 常见写法：
    - `"/books"`：只匹配该精确路径
    - `"/books/*"`：匹配一级路径（如 `/books/100`）
    - `"/books/**"`：匹配多级路径（如 `/books/a/b`，视框架匹配规则而定）

### 4.4 三个方法与参数含义

- 4.4.1 前置处理：`preHandle`
  - 执行时机：Controller 方法执行前
  - 返回值：控制是否放行（最常用）
  - 参数含义：
    - `request`：请求对象（可读请求头、参数等，如 `Content-Type`）
    - `response`：响应对象
    - `handler`：被调用的处理器对象（本质上对应要执行的方法信息）

```java
public boolean preHandle(HttpServletRequest request,
                         HttpServletResponse response,
                         Object handler) throws Exception {
    System.out.println("preHandle");
    return true;
}
```

- 4.4.2 后置处理：`postHandle`
  - 执行时机：Controller 方法执行后、视图渲染前
  - 特点：如果 Controller 被拦截（preHandle 返回 false），则不会执行
  - `ModelAndView`：用于页面渲染数据调整；如果项目主要返回 JSON，该参数使用率较低

- 4.4.3 完成处理：`afterCompletion`
  - 执行时机：整个流程结束后执行（类似“收尾”）
  - 特点：无论 Controller 是否执行，都可能进入（取决于是否进入过拦截器链）
  - `Exception ex`：若执行过程中出现异常，可在此做额外处理（但通常项目已有全局异常处理器时使用率较低）

### 4.5 拦截器链

- 当配置多个拦截器时，会形成“拦截器链”
- 运行顺序（掌握规律）
  - `preHandle`：按配置顺序执行（先进先出）
  - `postHandle`：按配置顺序的反向执行（后进先出，且可能不执行）
  - `afterCompletion`：按配置顺序的反向执行（后进先出，且可能不执行）
- 中断规则
  - 任意拦截器 `preHandle` 返回 `false`：
    - 后续拦截器不再执行
    - 仅对“已经执行过 preHandle 的拦截器”执行 `afterCompletion`（用于收尾）
- 记忆法（够用版）
  - 看成“入栈/出栈”：
    - 进入时（preHandle）顺序执行
    - 返回时（postHandle/afterCompletion）逆序执行
    - 一旦中途拦截，后面的都停止
