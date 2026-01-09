## 1. 分模块开发

### 1.1 分模块开发思路

- 分模块开发：把一个项目按“功能/层”拆成多个可独立维护、可复用的 Maven 模块（如 domain、dao、service/web 等）。
- 核心原则：**先做模块设计，再编码实现**；不是“先把单体做完再硬拆”。拆分方式可按功能拆，也可按模块/层拆。
- 模块复用方式：被依赖的模块需要先发布（本地仓库 install；团队协作则发布到私服）。

### 1.2 分模块开发实现流程

#### 1.2.1 环境准备

- 将既有的整合项目（示例：maven_02_ssm）导入 IDEA，确保能正常编译/运行后再拆分。

#### 1.2.2 抽取 domain 层

- 新建 jar 模块：`maven_03_pojo`
- 在新模块中新建包：`com.itheima.domain`，把原项目中的 `Book` 等 domain 类复制进来
- 原项目删除 domain 包后，会导致引用 `Book` 的类报错（缺少依赖）
- 在原项目 `maven_02_ssm` 中添加对 `maven_03_pojo` 的依赖：

```xml
<dependencies>
  <dependency>
    <groupId>com.itheima</groupId>
    <artifactId>maven_03_pojo</artifactId>
    <version>1.0-SNAPSHOT</version>
  </dependency>
</dependencies>
```

- 若本地仓库还没有该模块，需要先在 `maven_03_pojo` 执行安装到本地仓库：
  - 命令：`mvn install`（常用：`mvn clean install`）
  - 安装位置取决于本机 Maven 本地仓库配置（`settings.xml` 的 `localRepository`）

#### 1.2.3 抽取 Dao 层

- 新建 jar 模块：`maven_04_dao`
- 在新模块中新建包：`com.itheima.dao`，把原项目中的 `BookDao` 等 Dao 相关代码复制进来
- Dao 模块常见报错：Dao 接口里引用的 domain 类找不到  
  - 解决：在 `maven_04_dao` 的 `pom.xml` 中依赖 `maven_03_pojo`（domain 模块）
- 原项目要使用 Dao 模块：在 `maven_02_ssm` 中依赖 `maven_04_dao`
- 若提示找不到 `maven_04_dao`：同样需要先在 `maven_04_dao` 执行 `mvn install` 安装到本地仓库

> 实战中建议的安装顺序：先 install `maven_03_pojo` → 再 install `maven_04_dao` → 最后编译/运行 `maven_02_ssm`。

#### 1.2.4 运行测试与小结

- 抽取完成后，回到 Web/主模块运行并回归测试（CRUD 等功能应保持可用）。
- 分模块开发的固定三步：
  1) 创建 Maven 模块  
  2) 编写并迁移模块代码  
  3) install / deploy 使模块可被其他工程依赖（本地仓库 / 私服）
  
  

## 2. 依赖管理

### 2.1 依赖传递与冲突

- 依赖：当前项目运行所需的 jar，一个项目可配置多个依赖。
- 依赖传递：A 依赖 B，B 依赖 C，则 A 会**间接**获得 C（除非被阻断）。
- 在 IDEA 的 Maven Dependencies 中：
  - 前面**没有箭头**：通常是当前工程**直接依赖**
  - 前面**有箭头**：通常表示这是通过别的依赖“带进来的”**间接依赖**（可展开查看来源）
- 冲突的来源：同一个坐标（或同一组件）可能被多条路径引入不同版本，Maven 需要做“版本选择”。

常见冲突选择规则（按文档表述整理）：
- 特殊优先：同级配置相同资源的不同版本时，**后配置覆盖先配置**
- 路径优先：同一资源经不同路径传递时，**层级越浅优先级越高**（路径更短更优先）
- 声明优先：当资源在**相同层级**被依赖时，**配置顺序靠前覆盖靠后**

### 2.2 可选依赖与排除依赖

目标场景：A 依赖 B，B 依赖 C（C 会传递到 A）；现在希望 **A 不要用到 C**。

#### 2.2.1 可选依赖（optional）

- 定义：在 B 上把对 C 的依赖标记为可选，相当于对外“隐藏”C（不透明），使其不再传递给 A
- 示例：在 `maven_04_dao` 引入 `maven_03_pojo` 时设置 optional：

```xml
<dependency>
  <groupId>com.itheima</groupId>
  <artifactId>maven_03_pojo</artifactId>
  <version>1.0-SNAPSHOT</version>
  <optional>true</optional>
</dependency>
```

- 现象：如果 A（如 `maven_02_ssm`）不再显式依赖 C，那么使用 C 中类会直接编译报错（如 `Book` 找不到）。

#### 2.2.2 排除依赖（exclusions）

- 定义：A 明知 B 会带来 C，但在 A 里主动把 C 排除掉（主动断开）；被排除的资源通常**不需要写 version**
- 示例：在 `maven_02_ssm` 依赖 `maven_04_dao` 时排除 `maven_03_pojo`：

```xml
<dependency>
  <groupId>com.itheima</groupId>
  <artifactId>maven_04_dao</artifactId>
  <version>1.0-SNAPSHOT</version>
  <exclusions>
    <exclusion>
      <groupId>com.itheima</groupId>
      <artifactId>maven_03_pojo</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

- `exclusions` 带 s：可以一次排除多个传递依赖（如 log4j、mybatis 等）：

```xml
<exclusions>
  <exclusion>
    <groupId>com.itheima</groupId>
    <artifactId>maven_03_pojo</artifactId>
  </exclusion>
  <exclusion>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
  </exclusion>
  <exclusion>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
  </exclusion>
</exclusions>
```

- 可选 vs 排除（记忆法）：
  - **可选依赖**：在 B 做手脚，A “不知道 C 存在”
  - **排除依赖**：在 A 做手脚，A “知道 C 存在但主动不要”
  
  

## 3. 聚合和继承

### 3.1 聚合

- 目标：多模块项目下，**用一个入口工程统一构建/管理**各子模块，避免手动逐个编译、安装。
- 关键点：
  - 聚合工程打包方式必须是 `pom`
  - 在聚合工程 `pom.xml` 用 `<modules>` 列出需要聚合的模块
- 示例（聚合 `maven_02_ssm / maven_03_pojo / maven_04_dao`）：

```xml
<packaging>pom</packaging>

<modules>
  <module>../maven_02_ssm</module>
  <module>../maven_03_pojo</module>
  <module>../maven_04_dao</module>
</modules>
```

- 效果：对聚合工程执行 `compile / test / install` 等构建操作时，被聚合的模块会被统一执行对应构建。

### 3.2 继承

- 背景问题：多模块下会出现大量**重复依赖配置**、以及**版本不一致**带来的维护成本和冲突风险。
- 继承作用（对应文档总结）：
  - 简化配置：把公共依赖上提到父工程，子工程无需重复写
  - 减少版本冲突：父工程统一管理版本，子工程按需引用

实现要点：
- 父工程：
  - 打包方式为 `pom`
  - 可直接在 `<dependencies>` 放“所有子模块都需要的公共依赖”
  - 对“部分模块才需要的依赖”，建议使用 `<dependencyManagement>` 统一管版本（不强制引入）
- 子工程：
  - 在 `pom.xml` 声明 `<parent>` 指向父工程

父子继承示意（子工程声明 parent，relativePath 指向父 pom 路径）：

```xml
<parent>
  <groupId>com.itheima</groupId>
  <artifactId>maven_01_parent</artifactId>
  <version>1.0-RELEASE</version>
  <relativePath>../maven_01_parent/pom.xml</relativePath>
</parent>
```

#### 3.2.1 dependencyManagement 的用法与规则

- 父工程用 `<dependencyManagement>`：只做**版本管理**，不真正把 jar 引入到子工程
- 子工程如果要用该依赖：
  - 只写 `groupId + artifactId (+ scope)`，**不写 version**
  - 版本由父工程统一提供，避免各处散落版本号导致冲突
- 示例：父工程统一管理 junit 版本：

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

子工程按需引入（不写 version）：

```xml
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <scope>test</scope>
</dependency>
```

### 3.3 聚合与继承对比、IDEA 快速构建

#### 3.3.1 聚合与继承的区别

- 聚合：用于**快速构建项目、统一管理模块构建**
- 继承：用于**快速配置依赖、统一管理版本**
- 相同点：
  - 两者的工程打包方式通常都是 `pom`
  - 都是“设计型模块”（本身不放业务代码），可在同一个 pom 中同时做聚合 + 继承
- 不同点：
  - 聚合：在“当前工程”配置 `<modules>`，聚合工程能感知到被聚合的模块
  - 继承：在“子工程”配置 `<parent>`，父工程无法主动感知有哪些子工程继承自己

#### 3.3.2 IDEA 构建聚合与继承工程

- IDEA 可一键生成“既是聚合工程又是父工程”的结构：
  1) 创建一个 Maven 项目（可删除 `src`），作为聚合工程 + 父工程
  2) 创建子模块：自动加入聚合关系，并自动生成 `parent` 继承配置
- 好处：减少手写 `<modules>`、`<parent>` 的出错概率


  

## 4. 属性与版本管理

### 4.1 Maven 属性

#### 4.1.1 为什么需要属性

- 痛点：即使在父工程用 `dependencyManagement` 统一了依赖管理，但像 Spring 这种“一个技术栈对应多组依赖”的场景，仍然要在多个依赖里分别写版本号。
- 风险：
  - 升级版本要改很多处，容易漏改
  - 漏改后可能出现依赖版本不一致导致的问题
- 思路：像“变量”一样，把版本抽成一个统一的属性，所有地方引用它；只改一处即可全局生效。

#### 4.1.2 properties 定义与引用

- 步骤1：在父工程定义属性（统一维护版本）
- 步骤2：在依赖的 `version` 中引用属性

```xml
<properties>
  <spring.version>5.2.10.RELEASE</spring.version>
  <junit.version>4.12</junit.version>
  <mybatis-spring.version>1.3.0</mybatis-spring.version>
</properties>
```

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-core</artifactId>
  <version>${spring.version}</version>
</dependency>
```

- 结论：后续只需要改 `<properties>` 里的版本值，子项目里所有引用都会同步更新。


### 4.2 配置文件加载属性

- 目标：不仅管理 `pom.xml` 里的版本号，还想把项目配置文件（如 `jdbc.properties`）中的配置也交给 Maven 统一管理。
- 实现思路：把配置值定义为 Maven 属性 → 在 `jdbc.properties` 中使用 `${}` 引用 → 开启资源过滤（filtering）让 Maven 在打包时替换占位符。

#### 4.2.1 实现步骤

- 步骤1：父工程定义属性（示例：数据库 URL）

```xml
<properties>
  <jdbc.url>jdbc:mysql://127.1.1.1:3306/ssm_db</jdbc.url>
</properties>
```

- 步骤2：在 `jdbc.properties` 中引用 Maven 属性

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=${jdbc.url}
jdbc.username=root
```

- 步骤3：设置 Maven 过滤资源目录范围（指定资源目录 + 开启 filtering）
  - 说明：路径前加 `../` 的原因：资源目录相对于父工程 `pom.xml` 不在同级，需要向上回退一层再定位。

```xml
<build>
  <resources>
    <!--设置资源目录-->
    <resource>
      <directory>../maven_02_ssm/src/main/resources</directory>
      <!--设置能够解析${}，默认是false -->
      <filtering>true</filtering>
    </resource>
  </resources>
</build>
```

- 步骤4：打包验证（对对应模块执行打包，检查打包产物中配置值是否已替换为 Maven 中配置的值）

#### 4.2.2 系统属性补充

- Maven 还可以读取系统/运行时属性。
- 查看方式：命令行执行 `mvn help:system`
- 使用方式：`${key}`，其中 `key` 为等号左边的名称，例如 `${java.runtime.name}`


### 4.3 版本管理

- 常见版本标识：
  - **SNAPSHOT（快照版本）**
    - 开发过程中临时输出的版本
    - 会随着开发进展不断更新
  - **RELEASE（发布版本）**
    - 开发到阶段性里程碑后发布的稳定版本
    - 发布后内容应保持稳定，即使后续继续开发也不改变该发布版本的构件内容
- 其他常见发布阶段（了解即可）：
  - alpha：内测版（不稳定，bug 多）
  - beta：公测版（比 alpha 稳定，但仍可能不稳定）
  - 纯数字版：常规稳定发布版本号
  
  

## 5. 多环境与测试

### 5.1 多环境开发

- 场景：
  - 开发环境、测试环境、生产环境的配置不同（尤其是数据库 URL）
  - 希望能在不改代码的情况下切换不同环境配置
- Maven 支持：通过 `profiles` 定义多环境配置，并在构建时切换。

#### 5.1.1 profiles 定义与默认激活

- 在父工程配置多个环境，并指定默认环境（`activeByDefault`）

```xml
<profiles>
  <!--开发环境-->
  <profile>
    <id>env_dep</id>
    <properties>
      <jdbc.url>jdbc:mysql://127.1.1.1:3306/ssm_db</jdbc.url>
    </properties>
    <!--设定是否为默认启动环境-->
    <activation>
      <activeByDefault>true</activeByDefault>
    </activation>
  </profile>

  <!--生产环境-->
  <profile>
    <id>env_pro</id>
    <properties>
      <jdbc.url>jdbc:mysql://127.2.2.2:3306/ssm_db</jdbc.url>
    </properties>
  </profile>

  <!--测试环境-->
  <profile>
    <id>env_test</id>
    <properties>
      <jdbc.url>jdbc:mysql://127.3.3.3:3306/ssm_db</jdbc.url>
    </properties>
  </profile>
</profiles>
```

#### 5.1.2 构建时切换环境

- 使用方式：`mvn 指令 -P 环境定义ID`
  - `-P` 后面跟 profile 的 `id`（例如 `env_test` / `env_pro`）
- 总结：多环境切换两步
  1) 父工程定义多环境（profiles）
  2) 构建过程选择环境（-P）


### 5.2 跳过测试

- 为什么要跳过：
  - `install` 等构建会按生命周期执行，包含 `test`，重复执行测试会耗时
  - 部分模块未完成导致测试失败，但仍想先打包/安装已完成部分
- 实现方式有多种：

#### 5.2.1 方式一：IDEA 直接切换跳过测试

- IDEA 提供按钮：`Toggle 'Skip Tests' Mode`
  - 点击后测试会出现“画横线”表示已关闭
  - 再点一次恢复
- 特点：简单但“比较暴力”，会跳过所有测试。

#### 5.2.2 方式二：配置插件精细控制（Surefire）

- 在父工程 `pom.xml` 添加测试插件配置：

```xml
<build>
  <plugins>
    <plugin>
      <artifactId>maven-surefire-plugin</artifactId>
      <version>2.12.4</version>
      <configuration>
        <skipTests>false</skipTests>
        <!--排除掉不参与测试的内容-->
        <excludes>
          <exclude>**/BookServiceTest.java</exclude>
        </excludes>
      </configuration>
    </plugin>
  </plugins>
</build>
```

- 参数含义：
  - `skipTests`
    - true：跳过所有测试
    - false：不跳过测试
  - `excludes`
    - 指定哪些测试类不参与测试（通常配合 `skipTests=false` 使用）
  - `includes`
    - 指定哪些测试类要参与测试（通常配合 `skipTests=true` 使用）

#### 5.2.3 方式三：命令行跳过测试

- 命令格式：`mvn 指令 -D skipTests`
- 注意事项：
  - 构建指令必须包含测试生命周期才有效（例如只执行 `compile` 不经过 `test`，则无效果）
  - 命令行需要在 `pom.xml` 所在目录下执行
  
  

## 6. 私服

### 6.1 私服简介

- 团队开发问题：
  - 自研模块安装在个人本地仓库只能自己用
  - 其他同事需要使用时如果靠拷贝 jar，会造成管理混乱、易出错
  - 中央仓库不允许私人上传自研 jar 包
- 解决方案：搭建公司内部的“私服”
- 概念：
  - 私服：公司内部搭建的、用于存储 Maven 资源的服务器
  - 远程仓库：Maven 官方/社区维护的公共仓库（如中央仓库）
- 作用：解决团队内部资源共享与资源同步
- 常见私服产品：Nexus（Sonatype）

### 6.2 私服安装与基础配置（Nexus）

- 安装流程（示例为 Windows）：
  1) 下载并解压到空目录
  2) 启动：进入 `nexus-xxx\bin` 执行

```bash
nexus.exe /run nexus
```

  3) 浏览器访问（示例地址）

```text
http://localhost:8081
```

  4) 首次登录后重置密码（示例设置为 admin），并配置是否允许匿名访问
- 配置文件位置：
  - `etc/nexus-default.properties`：基础配置（如默认端口）
  - `bin/nexus.vmoptions`：运行配置（如默认内存等）

### 6.3 私服仓库分类

- 私服资源操作逻辑：
  - 自研资源上传到私服仓库，团队成员从私服下载
  - 第三方依赖首次从远程仓库拉取，后续可从私服缓存仓库下载（加速）
  - SNAPSHOT 与 RELEASE 通常分库存放，避免混乱
  - 为了简化客户端配置，会把多个仓库组合成“仓库组”，统一对外提供访问入口

- Nexus 仓库三大类：
  - hosted（宿主仓库）
    - 保存无法从中央仓库获取的资源
    - 自主研发、第三方非开源/付费项目等
  - proxy（代理仓库）
    - 代理远程仓库，通过 Nexus 访问公共仓库（如中央仓库）
  - group（仓库组）
    - 将多个仓库组成群组，简化配置
    - 仓库组不能保存资源（设计型仓库）

### 6.4 本地 Maven 访问私服配置（settings.xml）

- 目标：
  - 本地 Maven 需要知道私服地址
  - 上传/下载需要用户名密码
  - 下载通常走“仓库组”入口（group）
- 配置位置：本地 Maven 的 `settings.xml`

#### 6.4.1 配置访问权限（servers）

```xml
<servers>
  <server>
    <id>itheima-snapshot</id>
    <username>admin</username>
    <password>admin</password>
  </server>
  <server>
    <id>itheima-release</id>
    <username>admin</username>
    <password>admin</password>
  </server>
</servers>
```

- 说明：`id` 需要与工程发布配置中的仓库 `id` 保持一致，用于匹配用户名密码。

#### 6.4.2 配置私服访问路径（mirrors）

- 建议：先把之前配置的阿里云镜像注释掉，避免影响练习；练习完成后再恢复。

```xml
<mirrors>
  <mirror>
    <!--配置仓库组的ID-->
    <id>maven-public</id>
    <!--*代表所有内容都从私服获取-->
    <mirrorOf>*</mirrorOf>
    <!--私服仓库组maven-public的访问路径-->
    <url>http://localhost:8081/repository/maven-public/</url>
  </mirror>
</mirrors>
```

- 完成上述配置后，本地仓库即可与私服交互。

### 6.5 私服资源上传与下载

#### 6.5.1 配置工程发布位置（distributionManagement）

- 在需要发布的工程（或父工程）配置 `distributionManagement`，子工程可继承：

```xml
<distributionManagement>
  <repository>
    <!--和maven/settings.xml中server中的id一致，表示使用该id对应的用户名和密码-->
    <id>itheima-release</id>
    <!--release版本上传仓库的具体地址-->
    <url>http://localhost:8081/repository/itheima-release/</url>
  </repository>

  <snapshotRepository>
    <!--和maven/settings.xml中server中的id一致，表示使用该id对应的用户名和密码-->
    <id>itheima-snapshot</id>
    <!--snapshot版本上传仓库的具体地址-->
    <url>http://localhost:8081/repository/itheima-snapshot/</url>
  </snapshotRepository>
</distributionManagement>
```

#### 6.5.2 发布命令

- 发布到私服：执行

```bash
mvn deploy
```

- 版本与仓库的关系：
  - 发布到 snapshot 仓库：版本一般为 `SNAPSHOT`
  - 如果要发布到 release 仓库：将项目 `version` 修改为 `RELEASE`（或稳定发布版本号）
- 已上传资源的删除：可在 Nexus 管理界面进行删除操作
- 代理下载优化：当私服没有对应依赖时会去远程仓库下载，速度可能较慢，可通过配置代理仓库的远程源来加速（例如改为更快的镜像源）