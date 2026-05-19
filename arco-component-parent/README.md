---
published: 2022.01.17
---

# 组件型父项目

## 📖 作用

`arco-component-parent` 是 Zeka.Stack 框架中专门为**组件型项目**设计的父级 POM，继承自 `arco-project-builder`
。它提供了轻量级的构建配置，适合框架组件、SDK、工具库等不需要复杂部署流程的项目。

## 🎯 为什么这么设计

### 1. 组件型项目的特殊需求

组件型项目（如框架模块、工具库、SDK）通常：

- **不需要部署包**：作为依赖库被其他项目引用，不需要 tar.gz 部署包
- **不需要启动脚本**：不是可执行应用，不需要 launcher 脚本
- **不需要 Docker**：不需要容器化部署
- **可选源码打包**：某些场景下需要提供源码包，但不是必须的
- **简化资源处理**：资源文件处理相对简单

### 2. 与业务型项目的区别

相比 `arco-business-parent`（完整的企业级构建配置），组件型项目更轻量：

| 特性        | 组件型项目     | 业务型项目         |
|-----------|-----------|---------------|
| 打包方式      | JAR（可选源码） | tar.gz + 自解压包 |
| 启动脚本      | ❌ 不需要     | ✅ 自动生成        |
| Docker 支持 | ❌ 不需要     | ✅ 完整支持        |
| 部署插件      | ❌ 不需要     | ✅ 一键部署        |
| 资源过滤      | ✅ 基础支持    | ✅ 完整支持（环境变量）  |

### 3. 轻量级设计理念

组件型项目应该：

- **专注功能**：专注于提供功能实现，而不是部署配置
- **减少开销**：避免不必要的构建步骤，提高构建速度
- **灵活可选**：需要源码打包时通过 Profile 启用，不需要时保持轻量

## 🚀 如何使用

### 1. 在项目中继承

```xml
<parent>
    <groupId>dev.dong4j</groupId>
    <artifactId>arco-component-parent</artifactId>
    <version>3.0.0-SNAPSHOT</version>
    <relativePath/>
</parent>

<artifactId>my-framework-component</artifactId>
<packaging>jar</packaging>
```

### 2. 基础使用

执行 `mvn clean package` 后：

- ✅ 生成标准的 JAR 包
- ✅ 处理资源文件（支持过滤）
- ✅ 执行代码质量检查（Checkstyle、PMD）
- ✅ 生成测试覆盖率报告（JaCoCo）

### 3. 可选源码打包

如果需要提供源码包，使用 `source` Profile：

```bash
mvn clean package -P source
```

这会额外生成 `xxx-sources.jar` 源码包。

### 4. 资源文件处理

框架会自动处理 `src/main/resources` 下的所有文件：

- **支持过滤**：所有资源文件都支持 `${变量名}` 变量替换
- **统一处理**：简化配置，无需区分文件类型

## 📦 内置功能

### 继承的基础功能

从 `arco-project-builder` 继承：

- ✅ 代码质量检查（Checkstyle、PMD）
- ✅ 依赖冲突检测（Enforcer）
- ✅ 测试覆盖率（JaCoCo）
- ✅ 构建信息生成
- ✅ 注解处理器支持

### 组件型项目特有

- ✅ 简化的资源处理
- ✅ 可选的源码打包（通过 Profile）
- ✅ 轻量级构建配置

## 🔧 配置示例

### 基础配置

```xml
<parent>
    <groupId>dev.dong4j</groupId>
    <artifactId>arco-component-parent</artifactId>
    <version>3.0.0-SNAPSHOT</version>
</parent>

<artifactId>my-utils</artifactId>
<packaging>jar</packaging>
```

### 需要源码打包

```xml
<parent>
    <groupId>dev.dong4j</groupId>
    <artifactId>arco-component-parent</artifactId>
    <version>3.0.0-SNAPSHOT</version>
</parent>

<artifactId>my-sdk</artifactId>
<packaging>jar</packaging>

<!-- 发布到 Maven 仓库时包含源码 -->
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-source-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

## 📝 最佳实践

1. **选择合适的父级**：
    - 框架组件、工具库 → `arco-component-parent`
    - 微服务、Web 应用 → `arco-business-parent`

2. **源码打包策略**：
    - 发布到 Maven 公共仓库：建议提供源码包
    - 内部使用：可选，按需启用

3. **资源文件管理**：
    - 使用标准的 Maven 资源过滤机制
    - 避免复杂的资源处理逻辑

## 🔗 相关链接

- [[arco-meta/arco-builder/index|构建框架总览]]
- [[arco-meta/arco-builder/arco-business-parent|业务型项目父级]]
- [[arco-meta/arco-builder/arco-project-builder|构建逻辑抽象层]]

