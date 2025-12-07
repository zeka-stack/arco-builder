---
published: 2022.01.04
---

# 插件配置聚合

## 📖 作用

`arco-project-dependencies` 是 Zeka.Stack 框架的**插件与构建配置聚合层**，继承自 `arco-dependencies-parent`。它统一管理 Maven
插件版本，集成代码质量检查、测试覆盖率等构建工具，为所有项目提供标准化的构建配置。

## 🎯 为什么这么设计

### 1. 插件版本统一管理

Maven 插件版本管理同样重要：

- **版本一致性**：确保所有项目使用相同版本的构建工具
- **功能统一**：代码质量检查、测试覆盖率等标准一致
- **升级便利**：统一升级插件版本，所有项目自动受益

### 2. 构建工具集成

框架集成了完整的构建工具链：

- **代码质量检查**：Checkstyle（代码风格）、PMD（代码质量）
- **依赖冲突检测**：Enforcer Plugin
- **测试覆盖率**：JaCoCo
- **Git 信息**：Git Commit ID Plugin

### 3. 分层职责

```
arco-dependencies-parent (依赖版本管理)
└── arco-project-dependencies (插件版本管理) ← 当前模块
    └── arco-project-builder (构建逻辑实现)
```

**职责划分**：

- `arco-dependencies-parent`：管理**依赖库**版本（如 Spring Boot、MyBatis）
- `arco-project-dependencies`：管理**Maven 插件**版本（如 Checkstyle、PMD）
- `arco-project-builder`：实现具体的构建逻辑和配置

## 🚀 如何使用

### 1. 自动继承

所有继承 `arco-project-builder` 的项目都会自动获得插件配置，无需手动添加。

### 2. 代码质量检查

框架默认启用代码质量检查：

```bash
# 编译时自动执行检查
mvn clean compile

# 如果检查失败，编译会中断
# 紧急情况下可以跳过：
mvn clean compile -Dcheckstyle.skip=true -Dpmd.skip=true
```

### 3. 测试覆盖率

```bash
# 运行测试并生成覆盖率报告
mvn clean test

# 查看报告
open target/site/jacoco/index.html
```

### 4. 依赖冲突检测

```bash
# 检查依赖冲突
mvn enforcer:enforce

# 查看依赖树
mvn dependency:tree
```

## 📦 集成的插件

### 代码质量插件

| 插件                        | 功能     | 规则集           |
|---------------------------|--------|---------------|
| `maven-checkstyle-plugin` | 代码风格检查 | Zeka Stack 规范 |
| `maven-pmd-plugin`        | 代码质量检查 | 阿里巴巴 P3C 规范   |
| `maven-enforcer-plugin`   | 依赖冲突检测 | 自定义规则         |

### 测试插件

| 插件                    | 功能      |
|-----------------------|---------|
| `jacoco-maven-plugin` | 测试覆盖率分析 |

### 构建信息插件

| 插件                     | 功能         |
|------------------------|------------|
| `git-commit-id-plugin` | Git 提交信息记录 |

## 🔧 配置说明

### 代码质量检查配置

框架已内置完整的检查规则：

- **Checkstyle**：基于 Zeka Stack 代码规范
- **PMD**：基于阿里巴巴 P3C 规范
- **Enforcer**：依赖收敛检查

### 自定义配置

如果需要自定义，可以在子项目中覆盖：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-checkstyle-plugin</artifactId>
            <configuration>
                <configLocation>custom-checkstyle.xml</configLocation>
            </configuration>
        </plugin>
    </plugins>
</build>
```

## 📝 最佳实践

1. **遵循代码规范**：不要随意跳过代码质量检查
2. **保持测试覆盖率**：建议覆盖率不低于 60%
3. **解决依赖冲突**：发现冲突及时处理，不要忽略
4. **定期更新插件**：关注插件版本更新，及时升级

## 🔗 相关链接

- [[arco-builder/index|构建框架总览]]
- [[arco-builder/arco-dependencies-parent|依赖管理中枢]]
- [[arco-builder/arco-project-builder|构建逻辑抽象层]]
