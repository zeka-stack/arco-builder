---
published: 2022.01.11
---

# 一键部署层

## 📖 作用

`arco-distribution-parent` 是 Zeka.Stack 框架的**一键部署层**，继承自 `arco-builder`。它提供了业务项目和文档的版本化部署管理，支持自动化部署和版本回滚。

## 🎯 为什么这么设计

### 1. 部署管理需求

在大型项目中，部署管理是一个重要环节：

- **版本化部署**：每个版本应该有独立的部署包，支持版本回滚
- **自动化部署**：减少人工操作，提高部署效率和可靠性
- **多环境支持**：开发、测试、生产环境的部署配置管理

### 2. 部署类型区分

框架区分了两种部署类型：

- **业务项目部署**：`arco-business-distribution` - 可执行的业务服务
- **文档部署**：`arco-doc-distribution` - 项目文档和 API 文档

**设计原因**：

- **不同需求**：业务项目和文档的部署流程不同
- **独立管理**：便于分别管理和版本控制
- **灵活扩展**：未来可以添加其他类型的部署（如前端项目）

### 3. 版本化管理

框架强调版本化管理：

- **版本目录**：按版本号创建独立的部署目录
- **版本标识**：部署包包含版本信息，便于识别
- **版本回滚**：支持快速回滚到历史版本

## 🚀 如何使用

### 1. 业务项目部署

使用 `arco-business-distribution` 模块：

```xml
<parent>
    <groupId>dev.dong4j</groupId>
    <artifactId>arco-business-distribution</artifactId>
    <version>2.0.0-SNAPSHOT</version>
</parent>
```

### 2. 文档部署

使用 `arco-doc-distribution` 模块：

```xml
<parent>
    <groupId>dev.dong4j</groupId>
    <artifactId>arco-doc-distribution</artifactId>
    <version>2.0.0-SNAPSHOT</version>
</parent>
```

### 3. 部署配置

配置部署服务器和环境：

```xml
<properties>
    <!-- 服务器配置 -->
    <publish.hosts.test>192.168.1.100</publish.hosts.test>
    <publish.hosts.prod>192.168.1.101,192.168.1.102</publish.hosts.prod>

    <!-- 部署路径 -->
    <publish.target.path>/opt/apps</publish.target.path>
</properties>
```

### 4. 执行部署

```bash
# 一键部署到测试环境
mvn clean deploy -Dpublish.switch=true -Dpublish.env=test

# 一键部署到生产环境
mvn clean deploy -Dpublish.switch=true -Dpublish.env=prod
```

## 📦 子模块说明

### arco-business-distribution

业务项目部署模块，提供：

- ✅ 自动化部署流程
- ✅ 多环境配置支持
- ✅ 版本化部署管理
- ✅ 部署状态验证

### arco-doc-distribution

文档部署模块，提供：

- ✅ 文档版本化管理
- ✅ API 文档部署
- ✅ 用户手册部署
- ✅ 数据库脚本部署

## 🔧 部署流程

### 业务项目部署流程

1. **构建阶段**：`mvn clean package` 生成部署包
2. **上传阶段**：将部署包上传到目标服务器
3. **解压阶段**：在服务器上解压部署包
4. **停止服务**：停止旧版本服务（如果存在）
5. **启动服务**：启动新版本服务
6. **验证阶段**：检查服务启动状态

### 文档部署流程

1. **构建阶段**：生成文档文件
2. **版本目录**：创建版本号目录（如 `docs/1.0.0/`）
3. **上传阶段**：上传文档到文档服务器
4. **索引更新**：更新文档索引和导航

## 📝 最佳实践

1. **版本管理**：
    - 每次发布创建新的版本目录
    - 保留历史版本，便于回滚

2. **部署前检查**：
    - 验证部署包完整性
    - 检查服务器环境
    - 备份当前版本

3. **部署后验证**：
    - 检查服务状态
    - 验证功能正常
    - 监控日志输出

4. **回滚准备**：
    - 保留历史版本部署包
    - 记录部署时间和服务版本
    - 准备回滚脚本

## 🔗 相关链接

- [[arco-meta/arco-builder/index|构建框架总览]]
- [[arco-meta/arco-builder/arco-distribution-parent/arco-business-distribution|业务项目部署]]
- [[arco-meta/arco-builder/arco-distribution-parent/arco-doc-distribution|文档部署]]
