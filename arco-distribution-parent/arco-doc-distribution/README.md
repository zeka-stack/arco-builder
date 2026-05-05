---
published: 2022.01.13
---

# 文档部署

## 📖 作用

`arco-doc-distribution` 是 Zeka.Stack 框架中专门用于**项目文档版本化部署**的模块，继承自 `arco-distribution-parent`
。它提供了文档的版本化管理、自动化部署和版本同步能力，确保文档与代码版本保持一致。

## 🎯 为什么这么设计

### 1. 文档版本化管理的重要性

在软件开发中，文档管理是一个容易被忽视但非常重要的环节：

- **版本同步**：文档应该与代码版本保持同步，避免版本混乱
- **历史追溯**：保留历史版本的文档，便于查看和对比
- **团队协作**：统一的文档管理，提高团队协作效率
- **知识沉淀**：文档是项目知识的重要载体

### 2. 文档与代码分离

文档部署与业务项目部署有本质区别：

| 特性   | 文档部署   | 业务项目部署     |
|------|--------|------------|
| 部署内容 | 静态文档文件 | 可执行应用      |
| 部署频率 | 随版本发布  | 频繁迭代       |
| 版本管理 | 文档版本号  | 服务版本 + 时间戳 |
| 回滚需求 | 保留多版本  | 快速回滚       |

### 3. 文档类型支持

框架支持多种文档类型的版本化管理：

- **API 文档**：Swagger、OpenAPI 等接口文档
- **用户手册**：使用指南、操作手册
- **数据库脚本**：Schema、Migration 脚本
- **设计文档**：架构设计、技术方案

## 🚀 如何使用

### 1. 在文档项目中继承

```xml
<parent>
    <groupId>dev.dong4j</groupId>
    <artifactId>arco-doc-distribution</artifactId>
    <version>2.0.0-SNAPSHOT</version>
</parent>

<artifactId>my-project-docs</artifactId>
<packaging>pom</packaging>

<properties>
    <!-- 必填：部门名称 -->
    <publish.department.name>技术部</publish.department.name>
    <!-- 必填：项目名称 -->
    <publish.project.name>My Project</publish.project.name>
</properties>
```

### 2. 组织文档结构

推荐的文档目录结构：

```
docs/
├── 1.0.0/
│   ├── api-docs/          # API 文档
│   │   ├── swagger.json
│   │   └── postman-collection.json
│   ├── user-guide/        # 用户指南
│   │   ├── installation.md
│   │   └── configuration.md
│   └── database/          # 数据库脚本
│       ├── schema.sql
│       ├── data.sql
│       └── migration/
│           └── 001_initial_schema.sql
└── 2.0.0/
    ├── api-docs/
    ├── migration-guide/   # 迁移指南
    └── breaking-changes.md
```

### 3. 配置部署信息

```xml
<properties>
    <!-- 文档服务器配置 -->
    <publish.hosts.doc>192.168.1.200</publish.hosts.doc>

    <!-- 文档部署路径 -->
    <publish.doc.path>/mnt/zeka-stack/wiki</publish.doc.path>

    <!-- 部门信息 -->
    <publish.department.name>技术部</publish.department.name>
    <publish.project.name>User Service</publish.project.name>
</properties>
```

### 4. 执行文档部署

```bash
# 部署文档
mvn clean deploy -Dpublish.switch=true

# 或使用专门的文档部署命令
mvn arco-publish:publish-doc
```

## 📦 文档类型说明

### API 文档

API 文档通常包括：

- **Swagger/OpenAPI**：接口定义和示例
- **Postman Collection**：接口测试集合
- **接口说明**：参数说明、返回值说明

### 用户手册

用户手册包括：

- **安装指南**：环境要求、安装步骤
- **配置说明**：配置文件说明、参数说明
- **使用教程**：功能使用说明、常见问题

### 数据库脚本

数据库脚本包括：

- **Schema 脚本**：表结构定义
- **Data 脚本**：初始数据
- **Migration 脚本**：版本迁移脚本

## 🔧 配置说明

### 文档项目配置

```xml
<properties>
    <!-- 跳过 Maven 标准流程 -->
    <maven.install.skip>true</maven.install.skip>
    <maven.deploy.skip>true</maven.deploy.skip>

    <!-- 部门信息（必填） -->
    <publish.department.name>技术部</publish.department.name>

    <!-- 项目信息（必填） -->
    <publish.project.name>User Service</publish.project.name>
</properties>
```

### 部署插件配置

```xml
<plugin>
    <groupId>dev.dong4j</groupId>
    <artifactId>arco-publish-maven-plugin</artifactId>
    <executions>
        <execution>
            <id>publish-doc</id>
            <goals>
                <goal>publish-doc</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

## 📝 最佳实践

1. **版本同步**：
    - 每次代码发布时，同步更新文档
    - 文档版本号与代码版本号保持一致

2. **目录组织**：
    - 按版本号分目录管理
    - 清晰的文档分类（API、用户手册、数据库等）

3. **文档维护**：
    - 及时更新文档内容
    - 保留历史版本，便于追溯

4. **自动化部署**：
    - 集成到 CI/CD 流程
    - 代码发布时自动部署文档

## 🔗 相关链接

- [[arco-meta/arco-builder/arco-distribution-parent/index|部署层总览]]
- [[arco-meta/arco-maven-plugin/arco-publish-maven-plugin|部署插件详情]]
