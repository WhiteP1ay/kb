---
tags: []
created: "2026-07-29"
---

# uv

## 是什么

uv 是用 Rust 编写的 Python 工具，可以管理 Python 版本、项目、虚拟环境和依赖。依赖管理部分可以类比为 pnpm，但 uv 的职责更宽。

```bash
# 创建项目并指定解释器版本
uv init example --python 3.12
cd example

# 添加项目依赖，同时更新 pyproject.toml、uv.lock 和虚拟环境
uv add httpx

# 根据项目文件同步虚拟环境
uv sync

# 在项目虚拟环境中运行命令
uv run python

# 查看依赖树
uv tree
```

uv 项目通常有三类关键内容：

```text
pyproject.toml：声明项目元数据和依赖范围
uv.lock：记录解析后的精确依赖图
.venv：按前两者生成的本机运行环境
```

`uv sync` 默认执行精确同步。它会安装缺失的依赖，也会移除锁文件中不存在的多余依赖。如果 `.venv` 不存在，uv 会重新创建。

`pyproject.toml` 和 `uv.lock` 应该提交到 Git；`.venv` 可以随时重建，通常不提交。


## 打包与发布

```bash
uv build                                 # 产出 dist/ 下 sdist + wheel
uv publish                               # 上传 dist/* 到 PyPI（需 UV_PUBLISH_TOKEN）
uv publish --publish-url https://test.pypi.org/legacy/   # TestPyPI 演练场
```

发布纪律：PyPI 包名全局唯一、小写、连字符规范化；正式发前先 TestPyPI 演练；版本号只增不减。

## 相关笔记

- [[解释器]]
- [[虚拟环境]]
- [[pyproject.toml]]
- [[wheel]]
