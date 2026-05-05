---
published: 2022.01.03
---

# 依赖管理中枢

## 📖 作用

`arco-dependencies-parent` 是 Zeka.Stack 框架的**依赖管理中枢**，继承自 `arco-builder`。它统一管理所有第三方依赖的版本，确保整个框架生态中依赖版本的一致性，避免依赖冲突问题。

## 🎯 为什么这么设计

### 1. 依赖版本统一管理

在大型项目中，依赖版本管理是一个关键问题：

- **版本冲突**：不同模块使用不同版本的同一依赖，导致运行时错误
- **维护困难**：每个模块单独管理版本，升级时需要修改多处
- **兼容性问题**：Spring Boot、Spring Cloud、Spring Cloud Alibaba 等版本需要严格匹配

### 2. 分层管理架构

通过多层 parent 结构实现职责分离：

```
arco-supreme (全局基础)
└── arco-builder (构建总控)
    └── arco-dependencies-parent (依赖管理中枢) ← 当前模块
        └── arco-project-dependencies (插件配置聚合)
            └── arco-project-builder (构建逻辑)
```

**职责划分**：

- `arco-dependencies-parent`：**只管理依赖版本**，不涉及构建配置
- `arco-project-dependencies`：管理插件版本和构建配置
- `arco-project-builder`：提供构建逻辑和基础依赖

### 3. 智能依赖管理

框架对关键依赖进行了智能管理：

#### 日志框架统一

```xml
<!-- 全局排除默认的 logback -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
    <exclusions>
        <exclusion>
            <groupId>*</groupId>
            <artifactId>*</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<!-- 统一使用 Log4j2 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

**设计原因**：

- 避免 "发现多个 slf4j 实现类" 的常见问题
- 统一技术栈选择，减少团队分歧
- 支持用户按需引入其他实现

#### Web 服务器选择

```xml
<!-- 排除默认的 Tomcat，支持用户选择其他服务器 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

**设计原因**：

- 提供灵活性，支持 Jetty、Undertow 等替代方案
- 避免不必要的依赖传递

## 🚀 如何使用

### 1. 在子模块中继承

```xml
<parent>
    <groupId>dev.dong4j</groupId>
    <artifactId>arco-dependencies-parent</artifactId>
    <version>2.0.0-SNAPSHOT</version>
    <relativePath/>
</parent>
```

### 2. 使用统一版本

继承后，所有依赖都可以不指定版本，自动使用统一版本：

```xml
<dependencies>
    <!-- 无需指定版本，自动使用统一版本 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
</dependencies>
```

### 3. 覆盖版本（不推荐）

只有在特殊情况下才覆盖版本：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>2.7.0</version> <!-- 覆盖统一版本 -->
</dependency>
```

## 📦 管理的依赖范围

### Spring 生态

- Spring Framework
- Spring Boot
- Spring Cloud
- Spring Cloud Alibaba

### 工具库

- Lombok
- MapStruct
- Hutool
- Jackson

### 测试框架

- JUnit 5
- Mockito
- Spring Boot Test

### 其他

- MyBatis Plus
- Nacos
- Sentinel
- Seata

## 🔧 版本兼容性

框架严格管理版本兼容性：

| Spring Boot | Spring Cloud | Spring Cloud Alibaba | Java |
|-------------|--------------|----------------------|------|
| 3.x         | 2025.0.0     | 2023.0.3.3           | 17+  |
| 2.7.x       | 2021.0.x     | 2021.0.5.0           | 8+   |

## 📝 最佳实践

1. **优先使用统一版本**：不要随意覆盖依赖版本
2. **版本升级策略**：在 `arco-dependencies-parent` 统一升级，所有子项目自动受益
3. **依赖冲突处理**：使用 `mvn dependency:tree` 分析依赖关系
4. **定期更新**：关注 Spring 生态的版本更新，及时升级

## 🔗 相关链接

- [[arco-meta/arco-builder/index|构建框架总览]]
- [[arco-meta/arco-builder/arco-project-dependencies|插件配置聚合]]
- [[arco-meta/arco-builder/arco-project-builder|构建逻辑抽象层]]
