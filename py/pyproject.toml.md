---
tags: []
created: "2026-07-29"
---

# pyproject.toml

## 是什么

`pyproject.toml` 是 Python 项目的标准配置入口，可以类比为 `package.json`。它可以记录项目元数据、支持的 Python 版本、直接依赖和工具配置。

```toml
[project]
name = "python-env-lab"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = [
    "httpx>=0.28.1",
]
```

`pyproject.toml` 描述项目允许使用的依赖范围，不负责锁定整个依赖树的精确版本。uv 会把解析结果写进 `uv.lock`。

```text
pyproject.toml：项目需要什么
uv.lock：最终解析出了什么
```

`pyproject.toml` 和 `uv.lock` 都应该提交到 Git。


## 发布段：build-system 与 scripts

项目要被构建/发布，pyproject 顶部必须声明构建后端（npm 无对应概念，npm 的构建器是用户在 devDependencies 里自选的，Python 要求包自己声明）：

```toml
[build-system]
requires = ["uv_build>=0.11"]
build-backend = "uv_build"
```

常用后端：uv_build（uv 全家桶默认）、hatchling（现代通用）、setuptools（老牌，flat-layout 多模块会拒绝构建并报 Multiple top-level modules，要求改 src-layout 或显式声明）。

命令入口对应 npm `bin`：

```toml
[project.scripts]
demo-hello = "demo_pkg:greet_cli"
```

装包时 pip 自动生成可执行脚本：shebang + import 目标函数 + `__main__` 调用。

## 布局：src-layout 才配发布

flat-layout（`.py` 与 pyproject 同级）里模块一多，setuptools 拒绝猜测哪个要发布。可发布包用 src-layout：源码收进 `src/<包名>/`，杂物天然在扫描范围外，wheel 里只有包代码。

版本号走 PEP 440：`主.次.补丁`，可选 `rc1`/`post1`，发布后只增不减、不能改不能删。

## 相关笔记

- [[解释器]]
- [[虚拟环境]]
- [[uv]]
- [[wheel]]
