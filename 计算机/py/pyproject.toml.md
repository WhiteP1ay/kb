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

## 相关笔记

- [[解释器]]
- [[虚拟环境]]
- [[uv]]
