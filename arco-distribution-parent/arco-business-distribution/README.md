---
published: 2022.01.12
---

# 业务项目部署

## 📖 作用

`arco-business-distribution` 是 Zeka.Stack 框架中专门用于**业务项目版本化部署**的模块，继承自 `arco-distribution-parent`
。它提供了完整的业务服务自动化部署能力，支持多环境、多服务器、版本化管理。

## 🎯 为什么这么设计

### 1. 业务项目部署的特殊性

业务项目（微服务、Web 应用）的部署需求：

- **完整部署包**：包含应用 JAR、依赖库、配置文件、启动脚本
- **多环境支持**：开发、测试、预演、生产环境的独立配置
- **多服务器支持**：支持集群部署，同时部署到多台服务器
- **版本化管理**：每个版本独立部署，支持版本回滚
- **自动化流程**：减少人工操作，提高部署效率

### 2. 与文档部署的区别

业务项目部署与文档部署有本质区别：

| 特性   | 业务项目部署         | 文档部署    |
|------|----------------|---------|
| 部署内容 | 可执行应用          | 静态文档    |
| 部署流程 | 停止→备份→解压→启动→验证 | 上传→索引更新 |
| 版本管理 | 服务版本 + 部署时间戳   | 文档版本    |
| 回滚需求 | 需要快速回滚         | 可以保留多版本 |

### 3. 集成部署插件

框架集成了 `arco-publish-maven-plugin` 实现自动化部署：

- **SSH 连接**：安全地连接远程服务器
- **文件传输**：自动上传部署包
- **远程执行**：在服务器上执行部署命令
- **状态验证**：检查部署是否成功

## 🚀 如何使用

### 1. 在项目中继承

```xml
<parent>
    <groupId>dev.dong4j</groupId>
    <artifactId>arco-business-distribution</artifactId>
    <version>2.0.0-SNAPSHOT</version>
</parent>

<artifactId>user-service</artifactId>
<packaging>jar</packaging>
```

### 2. 配置部署信息

```xml
<properties>
    <!-- 服务分组 -->
    <publish.group.id>user-service</publish.group.id>

    <!-- 服务器配置 -->
    <publish.hosts.test>192.168.1.100</publish.hosts.test>
    <publish.hosts.prod>192.168.1.101,192.168.1.102</publish.hosts.prod>

    <!-- 部署路径 -->
    <publish.target.path>/opt/apps</publish.target.path>
    <publish.upload.path>/home/zekastack</publish.upload.path>

    <!-- JVM 配置 -->
    <jvm.options>-Xms256M -Xmx512M</jvm.options>
    <prod.jvm.options>-Xms1G -Xmx2G -XX:+UseG1GC</prod.jvm.options>
</properties>
```

### 3. 执行部署

```bash
# 部署到测试环境
mvn clean deploy -Dpublish.switch=true -Dpublish.env=test

# 部署到生产环境
mvn clean deploy -Dpublish.switch=true -Dpublish.env=prod

# 启用 APM 监控
mvn clean deploy -Dpublish.switch=true -Dapm.enable=true
```

### 4. 批量部署

对于多模块项目，使用批量部署：

```xml
<!-- distribution/pom.xml -->
<plugin>
    <groupId>dev.dong4j</groupId>
    <artifactId>arco-publish-maven-plugin</artifactId>
    <executions>
        <execution>
            <id>publish-batch</id>
            <phase>package</phase>
            <goals>
                <goal>publish-batch</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <groups>
            <group>
                <enable>true</enable>
                <env>test</env>
                <servers>
                    <server>
                        <host>192.168.1.100</host>
                        <names>
                            <name>user-service</name>
                            <name>order-service</name>
                        </names>
                    </server>
                </servers>
            </group>
        </groups>
    </configuration>
</plugin>
```

## 📦 部署流程详解

### 1. 构建阶段

```bash
mvn clean package
```

生成：

- `target/项目名_时间戳.tar.gz` - 部署包
- `target/项目名_时间戳.run` - 自解压包（可选）

### 2. 上传阶段

插件自动执行：

- 通过 SSH 连接服务器
- 上传部署包到临时目录（`/home/zekastack/{env}/{group}/`）
- 验证文件完整性

### 3. 部署阶段

在服务器上执行：

- 停止旧版本服务（如果存在）
- 备份当前部署（带时间戳）
- 解压新部署包到目标目录（`/opt/apps/{group}/`）
- 修改文件权限和所有者
- 启动新版本服务

### 4. 验证阶段

- 检查服务进程是否运行
- 验证服务健康状态
- 输出部署结果和日志

## 🔧 配置说明

### 服务器配置

```xml
<properties>
    <!-- 单服务器 -->
    <publish.hosts.test>192.168.1.100</publish.hosts.test>

    <!-- 多服务器（逗号分隔） -->
    <publish.hosts.prod>192.168.1.101,192.168.1.102,192.168.1.103</publish.hosts.prod>
</properties>
```

### 路径配置

```xml
<properties>
    <!-- 上传路径（临时目录） -->
    <publish.upload.path>/home/zekastack</publish.upload.path>

    <!-- 部署路径（最终目录） -->
    <publish.target.path>/opt/apps</publish.target.path>
</properties>
```

### JVM 配置

```xml
<properties>
    <!-- 非生产环境 -->
    <jvm.options>-Xms256M -Xmx512M</jvm.options>

    <!-- 生产环境 -->
    <prod.jvm.options>-Xms1G -Xmx2G -XX:+UseG1GC</prod.jvm.options>
</properties>
```

## 📝 最佳实践

1. **环境隔离**：
    - 不同环境使用不同的服务器配置
    - 生产环境使用独立的 JVM 参数

2. **版本管理**：
    - 部署包包含时间戳，便于版本识别
    - 保留历史版本，支持快速回滚

3. **安全考虑**：
    - 使用 SSH 密钥认证，避免明文密码
    - 限制部署用户的权限范围

4. **监控集成**：
    - 启用 APM 监控（SkyWalking）
    - 配置健康检查端点

## 🔗 相关链接

- [[arco-meta/arco-builder/arco-distribution-parent/index|部署层总览]]
- [[arco-meta/arco-maven-plugin/arco-publish-maven-plugin|部署插件详情]]
