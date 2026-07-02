---
tags:
  - 工程化
  - Node
created: "2026-07-02"
---

# Node.js 环境变量配置

## 是什么

环境变量让应用在不同环境表现不同行为。NODE_ENV 是社区约定而非官方。常用 dotenv（.env）和 node-config（JSON/YAML）读取配置。

## 关键点

- 跨平台设置：`cross-env NODE_ENV=production node app.js`
- dotenv：`require('dotenv').config()` 加载 .env 文件到 process.env
- node-config：支持 JSON/YAML、配置文件合并、按层级读取
- 前端构建时需注入环境变量：webpack DefinePlugin + dotenv-webpack
- scripty/npm-run-all：管理复杂启动脚本、并行执行多个 npm script
