---
tags:
  - 语法
created: 2026-07-06
source: 英语时态知识卡
---

# 一般现在时 (Simple Present)

## 结构

### 肯定句

主语 + 动词原形（第三人称单数 +s / +es）

| 人称 | 结构 | 示例 |
|------|------|------|
| I / You / We / They | 动词原形 | I code every day. |
| He / She / It | 动词 + s/es | The server runs on port 8080. |

第三人称单数加 -s/-es 规则：
- 一般情况直接加 -s：work → works, run → runs, build → builds
- 以 s, x, ch, sh, o 结尾加 -es：crash → crashes, push → pushes, go → goes
- 辅音 + y 结尾，变 y 为 i 加 -es：try → tries, deploy → deploys (注意 deploy 是元音 + y，直接加 s)

### 否定句

主语 + do not (don't) / does not (doesn't) + 动词原形 + 其他

```
I don't write tests. (别学我)
The linter doesn't catch this type of error.
```

> 注意：doesn't 后面动词恢复原形，不要再加 s。

### 疑问句

Do / Does + 主语 + 动词原形 + 其他？

```
Does this API return JSON?
Do you use Copilot for code review?
```

## 用法

1. 习惯性、反复发生的动作
   每天、每周、经常做的事情。非常适合描述开发者的日常工作流。
   ```
   I push to GitHub every day.
   Our team does code review on every pull request.
   ```

2. 客观事实、普遍真理
   不随时间改变的陈述，在技术文档中极其常见。
   ```
   Python is dynamically typed.
   HTTP is stateless.
   The Earth orbits the Sun. (这不只是科普，你的 GPS 卫星也需要这个事实来校准)
   ```

3. 描述状态或固定属性
   不一定是动作，也可以是"是怎样的"。适合描述系统特性、工具能力。
   ```
   This function takes two arguments and returns a boolean.
   Docker runs on all major platforms.
   My terminal uses zsh with oh-my-zsh.
   ```

4. 在时间、条件状语从句中代替将来时
   当主句是将来时，when/if/as soon as 等引导的从句用一般现在时。
   ```
   I'll deploy after the CI pipeline passes.  (不是 will pass)
   If the server goes down, the on-call engineer gets paged.  (不是 will go down)
   ```

## 例句

```
My linter screams at me every time I use var instead of const.

This model runs inference in under 10ms on a single GPU. If you quantize it, it even fits on a phone.

Coffee doesn't solve all problems, but it comes close.
```

## 关键词/标志词

以下时间状语经常与一般现在时搭配：

- always / usually / often / sometimes / rarely / never (频率副词)
- every day / every week / every Monday / every time ...
- on Mondays / on weekends
- once a day / twice a week
- in general / generally
- whenever ... / when ... (条件/时间状语从句中)

注意：频率副词通常放在行为动词之前、be 动词之后。
```
I always forget to close stale branches.
Our CI is rarely green on a Friday afternoon.
```

## 相关时态

- [[simple-past]] — 过去某个时间发生的事 (I deploy today vs I deployed yesterday)
- [[simple-future]] — 将来的动作或预测 (This API returns JSON vs This API will return JSON next version)
- [[present-continuous]] — 此刻正在进行的动作 (I code every day vs I am coding right now)
- [[present-perfect]] — 从过去持续到现在的状态 (I use Vim vs I have used Vim for five years)
