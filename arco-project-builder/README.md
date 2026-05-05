---
published: 2022.01.05
---

# 构建逻辑抽象层

## 📖 作用

`arco-project-builder` 是 Zeka.Stack 框架的**构建逻辑抽象层**，继承自 `arco-project-dependencies`。它提供了所有项目共享的基础依赖和构建配置，是业务型项目和组件型项目的共同父级。

## 🎯 为什么这么设计

### 1. 构建逻辑抽象

`arco-project-builder` 将构建逻辑从具体项目类型中抽象出来：

- **基础依赖**：Lombok、JUnit、SLF4J 等所有项目都需要的基础依赖
- **编译配置**：注解处理器路径、编译参数优化
- **代码质量**：Checkstyle、PMD、Enforcer 等检查工具
- **测试支持**：JaCoCo 测试覆盖率

### 2. 项目类型分支

从 `arco-project-builder` 开始，框架分为两个分支：

```
arco-project-builder (构建逻辑抽象层) ← 当前模块
├── arco-business-parent (业务型项目)
└── arco-component-parent (组件型项目)
```

**设计原因**：

- **共同基础**：两种项目类型都需要代码质量检查、测试支持等
- **差异化配置**：业务型项目需要部署相关配置，组件型项目不需要
- **职责清晰**：构建逻辑与项目类型分离，便于维护

### 3. 注解处理器集成

框架集成了多个注解处理器：

- **Lombok**：代码简化
- **MapStruct**：对象映射
- **arco-processor**：Spring Boot 自动配置生成

**关键配置**：

```xml
<annotationProcessorPaths>
    <path>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </path>
    <path>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct-processor</artifactId>
    </path>
    <path>
        <groupId>dev.dong4j</groupId>
        <artifactId>arco-processor-core</artifactId>
    </path>
    <!-- lombok-mapstruct-binding：Lombok 1.18.16+ 必需 -->
    <path>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok-mapstruct-binding</artifactId>
    </path>
</annotationProcessorPaths>
```

## 🚀 如何使用

### 1. 不直接使用

`arco-project-builder` 通常不直接作为项目的 parent，而是通过以下方式使用：

- **业务型项目**：继承 `arco-business-parent`（已继承 `arco-project-builder`）
- **组件型项目**：继承 `arco-component-parent`（已继承 `arco-project-builder`）

### 2. 自动获得的功能

所有继承此模块的项目都会自动获得：

- ✅ 基础依赖（Lombok、JUnit、SLF4J）
- ✅ 注解处理器配置
- ✅ 代码质量检查
- ✅ 测试覆盖率分析
- ✅ 依赖冲突检测
- ✅ 构建信息生成

### 3. 编译优化

框架对编译过程进行了优化：

```xml
<compilerArgs>
    <!-- 保留方法参数名称（反射时可获取真实参数名） -->
    <arg>-parameters</arg>
    <!-- 开启 lint 检查，屏蔽常见噪音警告 -->
    <arg>-Xlint:all,-classfile,-rawtypes,-unchecked,-deprecation,-processing</arg>
    <!-- JDK 模块化支持 -->
    <arg>--add-exports</arg>
    <arg>java.base/sun.net=ALL-UNNAMED</arg>
    <!-- ... 更多配置 -->
</compilerArgs>
```

## 📦 提供的功能

### 基础依赖

| 依赖                          | 作用域      | 说明            |
|-----------------------------|----------|---------------|
| `lombok`                    | provided | 代码简化工具        |
| `annotations`               | compile  | JetBrains 注解库 |
| `arco-processor-annotation` | provided | 注解定义          |
| `junit-jupiter`             | test     | JUnit 5 测试框架  |
| `slf4j-api`                 | compile  | 日志接口          |

### 构建插件

| 插件                         | 功能      |
|----------------------------|---------|
| `arco-assist-maven-plugin` | 构建自动化辅助 |
| `maven-compiler-plugin`    | 编译配置优化  |
| `maven-checkstyle-plugin`  | 代码风格检查  |
| `maven-pmd-plugin`         | 代码质量检查  |
| `maven-enforcer-plugin`    | 依赖冲突检测  |
| `jacoco-maven-plugin`      | 测试覆盖率   |

## 🔧 配置说明

### 注解处理器顺序

框架正确配置了注解处理器的执行顺序：

1. **Lombok**：首先处理，生成 getter/setter 等方法
2. **MapStruct**：处理对象映射注解
3. **arco-processor**：生成 Spring Boot 配置文件
4. **lombok-mapstruct-binding**：确保 Lombok 和 MapStruct 兼容

### 编译参数说明

- **`-parameters`**：保留方法参数名称，支持 Spring MVC 参数绑定
- **`-Xlint`**：开启编译警告，但屏蔽常见噪音
- **`--add-exports`**：JDK 9+ 模块化支持，允许访问内部包

## 📝 最佳实践

1. **使用正确的父级**：
    - 业务项目 → `arco-business-parent`
    - 组件项目 → `arco-component-parent`
    - 不要直接继承 `arco-project-builder`

2. **注解处理器兼容**：
    - 使用 Lombok 1.18.16+ 时，必须包含 `lombok-mapstruct-binding`
    - 确保注解处理器版本兼容

3. **编译优化**：
    - 不要随意修改编译参数
    - 如需自定义，在子项目中覆盖配置

## 🔗 相关链接

- [[arco-meta/arco-builder/index|构建框架总览]]
- [[arco-meta/arco-builder/arco-business-parent|业务型项目父级]]
- [[arco-meta/arco-builder/arco-component-parent|组件型项目父级]]
- [[arco-meta/arco-builder/arco-project-dependencies|插件配置聚合]]
