---
tags:
  - Java
created: "2026-10-14"
---

# Maven 基础与构建生命周期

## 是什么

Maven 是 Java 生态最主流的**项目管理与构建工具**。
根目录下的 `pom.xml`（Project Object Model）是项目描述文件。

## 关键心智模型（对照前端 package.json）

| 维度          | 前端 (npm / pnpm)                     | Java (Maven)                             | 核心规则                                                       |
| :---------- | :---------------------------------- | :--------------------------------------- | :--------------------------------------------------------- |
| **项目配置文件**  | `package.json`                      | `pom.xml`                                | XML 树状结构管理                                                 |
| **依赖坐标**    | `@scope/name@version`               | **GAV 坐标**（GroupId, ArtifactId, Version） | `groupId` 是反向组织域名，`artifactId` 是项目名                        |
| **公共中央仓库**  | npmjs.org                           | Maven Central (`repo1.maven.org`)        | 下载的 jar 包缓存在本地 `~/.m2/repository`                          |
| **依赖环境隔离**  | `dependencies` vs `devDependencies` | `<scope>` 作用域标签                          | `compile`（默认，打入生产包）；`test`（仅单测）；`provided`（环境自带/编译期工具，不打包） |
| **环境统一运行器** | `corepack` / `npx`                  | **`mvnw` (Maven Wrapper)**               | 仓库自带脚本，无需全局安装 Maven，自动拉取指定版本构建                             |
|             |                                     |                                          |                                                            |

## 标准工程目录结构

Maven 强制遵循标准目录，无需手动在配置中指定源码路径：

```
my-project/
├── pom.xml                 # 核心构建与依赖配置
├── mvnw / mvnw.cmd         # Maven Wrapper 跨平台脚本
└── src/
    ├── main/
    │   ├── java/           # 业务 Java 源码包（com.xxx.yyy）
    │   └── resources/      # 配置文件（application.yml、静态资源）
    └── test/
        └── java/           # 单元测试代码（打包时自动排除）
```

## 生命周期与常用命令

Maven 采用**链式生命周期**，后置阶段会自动触发前置阶段：

- `mvn clean`：删除 `target/` 目录（对标 `rm -rf dist`）
- `mvn compile`：编译主源码为字节码
- `mvn test`：编译并运行所有单元测试（**单测失败会立刻中断打包**）
- `mvn package`：自动串行执行 `compile → test → package`，输出最终可执行的 `.jar`
- `mvn install`：将 jar 包安装到本地 `~/.m2` 仓库，供本机其他工程引用

> **跳过测试打包**：在紧急构建或无外部测试依赖时，使用 `mvn package -DskipTests`。

## 相关笔记

- [[Java基本类型与包装类型]]
- [[Java常用集合操作]]
