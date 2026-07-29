---
tags: []
created: "2026-07-29"
---

# pyenv

## 是什么

pyenv 是 Python 版本管理工具，可以类比为 Node.js 生态中的 nvm。

它把一组 shim 放进 `PATH`。当我们输入 `python` 或 `python3` 时，pyenv 会根据当前配置把命令转发给选中的解释器。

```shell
# 安装某个版本
pyenv install 3.10.4

# 设置当前用户的默认版本
pyenv global 3.10.4

# 设置当前目录及其子目录使用的版本
# 该命令会在当前目录写入 .python-version
pyenv local 3.10.4

# 查看已安装版本，星号表示当前选中的版本
pyenv versions

# 查看当前版本及其选择来源
pyenv version
```

安装版本和选择版本是两件事。`pyenv install` 只负责安装，不会自动切换当前版本。

## 相关笔记

- [[环境变量]]
- [[解释器]]
- [[虚拟环境]]
