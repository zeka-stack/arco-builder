---
published: 2022.01.10
---

# 业务型父项目

## 📖 作用

`arco-business-parent` 是 Zeka.Stack 框架中专门为**业务型项目**设计的父级 POM，继承自 `arco-project-builder`
。它提供了完整的业务项目构建配置，包括打包、部署、启动脚本生成、Docker 容器化等企业级功能。

## 🎯 为什么这么设计

### 1. 业务项目的特殊需求

业务型项目（如微服务、Web 应用）通常需要：

- **完整的部署包**：包含应用 JAR、依赖库、配置文件、启动脚本
- **多环境支持**：开发、测试、生产环境的配置管理
- **容器化支持**：Docker 镜像构建和部署
- **自动化部署**：一键部署到远程服务器
- **资源文件处理**：配置文件过滤、环境变量替换

### 2. 与组件型项目的区别

相比 `arco-component-parent`（轻量级，适合 SDK 和框架组件），业务型项目需要更复杂的构建配置：

| 特性        | 业务型项目         | 组件型项目  |
|-----------|---------------|--------|
| 打包方式      | tar.gz + 自解压包 | 可选源码打包 |
| 启动脚本      | ✅ 自动生成        | ❌ 不需要  |
| Docker 支持 | ✅ 完整支持        | ❌ 不需要  |
| 部署插件      | ✅ 一键部署        | ❌ 不需要  |
| 资源过滤      | ✅ 支持环境变量      | ✅ 基础支持 |

### 3. 约定大于配置

框架内置了所有必要的插件配置，业务项目无需手动配置：

- **自动生成启动脚本**：`arco-script-maven-plugin`
- **自动生成部署包**：`maven-assembly-plugin`
- **自动生成 Dockerfile**：`arco-container-maven-plugin`
- **自动生成自解压包**：`arco-makeself-maven-plugin`
- **一键部署支持**：`arco-publish-maven-plugin`

## 🚀 如何使用

### 1. 在项目中继承

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

### 2. 基础使用

执行 `mvn clean package` 后会自动：

- ✅ 生成 `target/项目名_时间戳.tar.gz` 部署包
- ✅ 生成 `bin/launcher` 启动脚本
- ✅ 生成 `build-info.properties` 构建信息
- ✅ 复制依赖到 `lib/` 目录
- ✅ 处理配置文件（支持环境变量替换）

### 3. 高级功能

#### 生成自解压部署包

```bash
mvn clean package -Dmakeself.skip=false
```

生成 `xxx.run` 自解压包，上传到服务器后直接执行即可部署。

#### Docker 容器化

```bash
# 多层构建模式（推荐）
mvn clean package -Pdocker

# 单层构建模式
mvn clean package -Ddockerfile.skip=false
```

#### 一键部署

```bash
# 配置服务器信息后
mvn clean deploy -Dpublish.switch=true -Dpublish.env=test
```

### 4. 自定义配置

#### JVM 参数配置

```xml
<properties>
    <!-- 非生产环境 JVM 参数 -->
    <jvm.options>-Xms256M -Xmx512M</jvm.options>
    <!-- 生产环境 JVM 参数 -->
    <prod.jvm.options>-Xms1G -Xmx2G -XX:+UseG1GC</prod.jvm.options>
</properties>
```

#### 资源文件过滤

框架会自动处理 `src/main/resources` 下的配置文件：

- **支持过滤**：`application*.yml`、`application*.properties`（支持 `${变量名}` 替换）
- **不参与过滤**：`includes/`、`static/`、`templates/` 目录（业务文件，保持原样）

#### 自定义启动脚本

在项目根目录创建 `bin/launcher`，框架会优先使用自定义脚本：

```bash
mkdir -p bin
# 编辑 bin/launcher 自定义启动逻辑
```

## 📦 内置插件说明

| 插件                            | 功能         | 执行阶段            |
|-------------------------------|------------|-----------------|
| `arco-assist-maven-plugin`    | 构建自动化辅助    | 全生命周期           |
| `arco-script-maven-plugin`    | 生成启动脚本     | package         |
| `arco-container-maven-plugin` | Docker 容器化 | package         |
| `arco-makeself-maven-plugin`  | 自解压部署包     | package         |
| `arco-publish-maven-plugin`   | 一键部署       | deploy          |
| `maven-assembly-plugin`       | 打包部署包      | package         |
| `maven-dependency-plugin`     | 复制依赖到 lib  | prepare-package |

## 🔧 配置示例

### 完整配置示例

```xml
<parent>
    <groupId>dev.dong4j</groupId>
    <artifactId>arco-business-parent</artifactId>
    <version>2.0.0-SNAPSHOT</version>
</parent>

<artifactId>user-service</artifactId>
<packaging>jar</packaging>

<properties>
    <!-- 自定义包名 -->
    <package.name>user-service</package.name>

    <!-- JVM 参数 -->
    <jvm.options>-Xms256M -Xmx512M</jvm.options>
    <prod.jvm.options>-Xms1G -Xmx2G -XX:+UseG1GC</prod.jvm.options>

    <!-- 部署配置 -->
    <publish.enable>true</publish.enable>
    <publish.hosts.test>192.168.1.100</publish.hosts.test>
</properties>
```

## 📝 最佳实践

1. **使用默认配置**：大多数场景下无需额外配置，直接使用框架默认设置
2. **环境区分**：通过 `jvm.options` 和 `prod.jvm.options` 区分不同环境的 JVM 参数
3. **资源管理**：业务文件放在 `includes/` 目录，避免被过滤处理
4. **版本管理**：使用时间戳后缀的部署包，便于版本管理和回滚

## 🔗 相关链接

- [[arco-builder/index|构建框架总览]]
- [[arco-builder/arco-component-parent|组件型项目父级]]
- [[arco-builder/arco-project-builder|构建逻辑抽象层]]
