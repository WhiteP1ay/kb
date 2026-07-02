---
tags:
  - 面试
  - 工程化
  - Monorepo
created: "2026-07-02"
---

# Monorepo 子包依赖管理（pnpm --filter）

## 是什么

在 Monorepo 中给某个子包添加依赖，使用 pnpm 的 --filter 参数，让依赖精准落在目标包上。

## 关键点

- `pnpm add lodash --filter pkg-a` → 在 packages/pkg-a 下安装并写入其 package.json
- --filter 也支持运行脚本、执行命令等操作
- 这是 Monorepo 管理的核心工具，避免依赖混乱

## 相关笔记

[[幽灵依赖npm]]
