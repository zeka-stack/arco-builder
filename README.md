# Arco Builder

> Zeka.Stack 项目构建管理框架

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Maven Central](https://img.shields.io/maven-central/v/dev.dong4j/arco-builder.svg)](https://search.maven.org/artifact/dev.dong4j/arco-builder)
[![GitHub release](https://img.shields.io/github/release/zeka-stack/arco-builder.svg)](https://github.com/zeka-stack/arco-builder/releases)

## 📖 简介

`arco-builder` 是 Zeka.Stack 生态中的项目构建管理框架，基于 Maven 的多层 parent 设计，为不同类型的项目提供统一的构建配置和依赖管理。它继承自 `arco-supreme`，是整个 Zeka.Stack 构建体系的核心组件。

## 🏗️ 架构设计

### 整体架构图

```
arco-supreme (全局基础父项目)
└── arco-builder (项目构建总控层)
    ├── arco-dependencies-parent (依赖管理中枢)
    │   └── arco-project-dependencies (插件与构建配置聚合)
    │       └── arco-project-builder (构建逻辑抽象层)
    │           ├── arco-business-parent (业务型父级)
    │           └── arco-component-parent (组件型父级)
    └── arco-distribution-parent (一键部署层)
        ├── arco-business-distribution (业务项目部署包)
        └── arco-doc-distribution (文档部署包)
```

### 模块职责

| 模块 | 职责 | 特点 |
|------|------|------|
| `arco-builder` | 项目构建总控层 | 继承 arco-supreme，定义两大子模块 |
| `arco-dependencies-parent` | 依赖管理中枢 | 统一管理第三方依赖版本 |
| `arco-project-dependencies` | 插件与构建配置聚合 | 集成代码质量检查、测试覆盖率等插件 |
| `arco-project-builder` | 构建逻辑抽象层 | 提供基础依赖和插件配置 |
| `arco-business-parent` | 业务型父级 | 支持复杂部署需求，包含打包、脚本生成等 |
| `arco-component-parent` | 组件型父级 | 轻量级，适合 SDK 或框架组件 |
| `arco-distribution-parent` | 一键部署层 | 提供业务和文档的部署包管理 |

## 🎯 设计理念

### 1. 分层管理，职责清晰

通过多层 parent 结构，将不同层级的职责进行明确划分：

- **全局层**：JDK 版本、编码格式、基础插件版本
- **依赖层**：第三方依赖版本管理
- **插件层**：构建插件配置和代码质量检查
- **业务层**：针对不同项目类型的定制化配置

### 2. 差异化配置，灵活扩展

针对不同类型的项目提供专门的 parent：

- **业务型项目**：支持复杂的打包需求，包含启动脚本、Docker 支持等
- **组件型项目**：轻量级配置，专注于框架组件和 SDK 开发

### 3. 统一规范，集中维护

所有子项目共享统一的构建规范，避免重复配置，提高可维护性。

## 📦 核心模块详解

### arco-dependencies-parent

**职责**：统一管理第三方依赖版本

**核心功能**：
- 管理 Spring Boot 及其生态依赖版本
- 统一日志框架配置（Log4j2）
- 管理 Spring Cloud 和 Spring Cloud Alibaba 版本
- 提供依赖版本冲突解决方案

**关键配置**：
```xml
<dependencyManagement>
    <dependencies>
        <!-- Spring Boot 依赖管理 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>${spring-boot-dependencies.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!-- 全局排除默认日志，使用 Log4j2 -->
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
    </dependencies>
</dependencyManagement>
```

### arco-project-dependencies

**职责**：插件与构建配置聚合

**核心功能**：
- 集成代码质量检查插件（Checkstyle、PMD）
- 配置测试覆盖率分析（JaCoCo）
- 管理 Git 提交信息记录
- 提供依赖冲突检测

**关键插件**：
- `maven-checkstyle-plugin`：代码风格检查
- `maven-pmd-plugin`：代码质量分析
- `jacoco-maven-plugin`：测试覆盖率
- `maven-enforcer-plugin`：依赖冲突检测

### arco-project-builder

**职责**：构建逻辑抽象层

**核心功能**：
- 提供基础依赖（Lombok、JUnit、SLF4J）
- 配置注解处理器路径
- 集成自定义 Maven 插件
- 提供编译优化配置

**关键特性**：
- 支持 Lombok + MapStruct 组合使用
- 集成自定义注解处理器
- 优化编译参数，支持 JDK 模块化

### arco-business-parent

**职责**：业务型项目构建配置

**核心功能**：
- 支持复杂打包需求（tar.gz、自解压包）
- 自动生成启动脚本
- 支持 Docker 容器化
- 提供资源文件过滤

**关键插件**：
- `maven-assembly-plugin`：打包部署包
- `arco-script-maven-plugin`：生成启动脚本
- `arco-container-maven-plugin`：Docker 支持
- `arco-makeself-maven-plugin`：生成自解压包

**资源处理**：
```xml
<resources>
    <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
        <includes>
            <include>**/*.properties</include>
            <include>**/*.yml</include>
        </includes>
    </resource>
    <resource>
        <directory>src/main/resources</directory>
        <filtering>false</filtering>
        <includes>
            <include>includes/**</include>
            <include>static/**</include>
        </includes>
    </resource>
</resources>
```

### arco-component-parent

**职责**：组件型项目构建配置

**核心功能**：
- 轻量级配置，适合 SDK 开发
- 支持源码打包（可选）
- 简化的资源处理

**关键特性**：
- 默认不打包源码，可通过 `-P source` 启用
- 简化的资源文件处理
- 专注于框架组件开发

## 🚀 使用指南

### 1. 业务型项目

对于需要复杂部署的业务项目，使用 `arco-business-parent`：

```xml
<parent>
    <groupId>dev.dong4j</groupId>
    <artifactId>arco-business-parent</artifactId>
    <version>2.0.0-SNAPSHOT</version>
    <relativePath/>
</parent>

<artifactId>my-business-service</artifactId>
<packaging>jar</packaging>
```

**特性**：
- 自动生成启动脚本
- 支持 Docker 容器化
- 提供完整的部署包
- 支持资源文件过滤

### 2. 组件型项目

对于框架组件或 SDK，使用 `arco-component-parent`：

```xml
<parent>
    <groupId>dev.dong4j</groupId>
    <artifactId>arco-component-parent</artifactId>
    <version>2.0.0-SNAPSHOT</version>
    <relativePath/>
</parent>

<artifactId>my-framework-component</artifactId>
<packaging>jar</packaging>
```

**特性**：
- 轻量级配置
- 可选源码打包
- 适合框架组件开发

### 3. 依赖管理

所有项目都会自动继承统一的依赖版本管理：

```xml
<dependencies>
    <!-- 无需指定版本，自动使用统一版本 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
</dependencies>
```

### 4. 构建命令

**基础构建**：
```bash
mvn clean compile
mvn clean package
```

**包含源码打包**（仅组件型项目）：
```bash
mvn clean package -P source
```

**Docker 支持**（仅业务型项目）：
```bash
mvn clean package -P docker
```

## 🔧 配置说明

### 环境变量配置

在使用前，需要配置以下环境变量：

```bash
# Maven 私服配置
export MVN_PRIVATE_USERNAME=your_username
export MVN_PRIVATE_PASSWORD=your_password
export MVN_PRIVATE_PUBLIC_URL=https://your-nexus.com/repository/maven-public/
export MVN_PRIVATE_SNAPSHOTS_URL=https://your-nexus.com/repository/maven-snapshots/
export MVN_PRIVATE_RELEASE_URL=https://your-nexus.com/repository/maven-releases/

# Maven Central 发布配置（可选）
export MVN_CENTRAL_USERNAME=your_central_username
export MVN_CENTRAL_PASSWORD=your_central_password
```

### 项目属性配置

可以在项目中覆盖默认配置：

```xml
<properties>
    <!-- 自定义包名 -->
    <package.name>my-custom-name</package.name>
    <!-- 跳过测试 -->
    <maven.test.skip>true</maven.test.skip>
    <!-- 跳过代码质量检查 -->
    <checkstyle.skip>true</checkstyle.skip>
    <pmd.skip>true</pmd.skip>
</properties>
```

## 🎨 设计优势

### 1. 统一性
- 所有项目使用相同的构建规范
- 统一的依赖版本管理
- 一致的代码质量检查标准

### 2. 灵活性
- 支持不同类型的项目需求
- 可选的插件和功能
- 易于扩展和定制

### 3. 可维护性
- 集中式配置管理
- 清晰的模块职责划分
- 减少重复配置

### 4. 可扩展性
- 支持自定义 Maven 插件
- 易于添加新的项目类型
- 支持多种部署方式

## 🔍 最佳实践

### 1. 项目结构建议

```
my-project/
├── pom.xml (使用 arco-business-parent 或 arco-component-parent)
├── src/
│   ├── main/
│   │   ├── java/
│   │   └── resources/
│   │       ├── application.yml
│   │       └── includes/ (业务文件，不参与过滤)
│   └── test/
└── bin/launcher (自定义启动脚本，可选)
```

### 2. 依赖管理建议

- 优先使用 `dependencyManagement` 中定义的版本
- 避免在子项目中重复定义版本
- 使用 `provided` 作用域管理编译时依赖

### 3. 构建优化建议

- 合理使用 Maven Profile 进行环境区分
- 利用增量编译提高构建速度
- 定期更新依赖版本

## 🤝 贡献指南

欢迎贡献代码和建议！请遵循以下步骤：

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 创建 Pull Request

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 🔗 相关链接

- [Zeka.Stack 官网](https://github.com/zeka-stack)
- [arco-supreme](https://github.com/zeka-stack/arco-supreme) - 全局基础父项目
- [设计思路文档](https://github.com/zeka-stack/zeka-stack.github.io/blob/main/docs/day0/5.如何用多层%20parent%20管理%20Maven%20项目依赖/🧩%20如何用多层%20parent%20管理%20Maven%20项目依赖.md)

## 📞 联系方式

- 作者：dong4j
- 邮箱：dong4j@gmail.com
- 项目地址：https://github.com/zeka-stack/arco-builder

---

**Zeka.Stack** - 让 Java 开发更简单、更规范、更高效！