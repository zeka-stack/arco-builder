## 依赖关系

该项目是一个典型的 Maven
多模块项目，各个模块之间通过 [pom.xml](file:///Users/dong4j/Developer/0.Worker/opensource/zeka.stack/arco/arco-builder/pom.xml)
文件定义了依赖和继承关系。以下是详细的依赖关系：

---

### 1. 顶级项目 (`arco-builder`)

- **坐标**：`dev.dong4j:arco-builder`
- **父级**：`dev.dong4j:arco-supreme`（无本地路径，直接从仓库获取）
- **子模块**：
    - `arco-dependencies-parent`
    - `arco-distribution-parent`

---

### 2. 依赖管理模块 (`arco-dependencies-parent`)

- **坐标**：`dev.dong4j:arco-dependencies-parent`
- **父级**：`dev.dong4j:arco-builder`
- **子模块**：
    - `arco-project-dependencies`

#### 子模块分析

- **`arco-project-dependencies`**
    - **坐标**：`dev.dong4j:arco-project-dependencies`
    - **父级**：`dev.dong4j:arco-dependencies-parent`
    - **子模块**：
        - `arco-project-builder`

---

### 3. 构建工具模块 (`arco-project-builder`)

- **坐标**：`dev.dong4j:arco-project-builder`
- **父级**：`dev.dong4j:arco-project-dependencies`
- **子模块**：
    - `arco-business-parent`
    - `arco-component-parent`

#### 子模块分析

- **`arco-business-parent`**
    - **坐标**：`dev.dong4j:arco-business-parent`
    - **父级**：`dev.dong4j:arco-project-builder`

- **`arco-component-parent`**
    - **坐标**：`dev.dong4j:arco-component-parent`
    - **父级**：`dev.dong4j:arco-project-builder`

---

### 4. 分发模块 (`arco-distribution-parent`)

- **坐标**：`dev.dong4j:arco-distribution-parent`
- **父级**：`dev.dong4j:arco-builder`
- **子模块**：
    - `arco-business-distribution`
    - `arco-doc-distribution`

#### 子模块分析

- **`arco-business-distribution`**
    - **坐标**：`dev.dong4j:arco-business-distribution`
    - **父级**：`dev.dong4j:arco-distribution-parent`

- **`arco-doc-distribution`**
    - **坐标**：`dev.dong4j:arco-doc-distribution`
    - **父级**：`dev.dong4j:arco-distribution-parent`

---

### 总体依赖结构图

```
dev.dong4j:arco-supreme (外部依赖)
└── dev.dong4j:arco-builder
    ├── arco-dependencies-parent
    │   └── arco-project-dependencies
    │       └── arco-project-builder
    │           ├── arco-business-parent
    │           └── arco-component-parent
    └── arco-distribution-parent
        ├── arco-business-distribution
        └── arco-doc-distribution
```

---

### 关键点总结

1. **继承关系**：
    - 每个模块都通过 `<parent>` 标签指定了其父模块或外部依赖。
    - 最顶层的父模块是 `arco-supreme`，它不是本项目的代码，而是从外部仓库获取。

2. **模块化设计**：
    - 项目分为两大类模块：
        - **依赖管理类**：用于统一管理第三方依赖版本，如 `arco-dependencies-parent` 和 `arco-project-dependencies`。
        - **构建类**：提供具体的构建逻辑，如 `arco-project-builder` 和 `arco-distribution-parent`。

3. **多层级嵌套**：
    - `arco-project-dependencies` 是一个中间层模块，既继承自 `arco-dependencies-parent`，又作为 `arco-project-builder` 的父模块。

4. **分布部署**：
    - `arco-distribution-parent` 提供一键部署功能，包含两个子模块：
        - `arco-business-distribution`：用于多个项目的部署。
        - `arco-doc-distribution`：用于知识库项目的部署。

5. **可扩展性**：
    - 所有模块的设计都具有良好的可扩展性，可以轻松添加新的业务模块或组件模块。

---

