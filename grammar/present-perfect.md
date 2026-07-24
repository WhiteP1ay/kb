---
tags:
  - 语法
created: 2026-07-06
source: 英语时态知识卡
---

# 现在完成时 (Present Perfect)

## 结构

### 肯定句

主语 + have / has + 过去分词 + 其他

| 人称 | 助动词 | 示例 |
|------|--------|------|
| I / You / We / They | have | I have fixed the bug. |
| He / She / It | has | The build has failed again. |

过去分词规则：
- 规则动词：动词原形 + -ed（与过去式同形）→ worked, deployed, fixed
- 不规则动词：需要单独记忆 → go → gone, see → seen, write → written, break → broken, take → taken

> 提醒：现在完成时的过去分词和一般过去时的过去式容易搞混。规则动词两者一样，但不规则动词往往不同：I did it（过去式） vs I have done it（过去分词）。

### 否定句

主语 + have not (haven't) / has not (hasn't) + 过去分词 + 其他

```
I haven't pushed the latest commit yet.
She hasn't reviewed my pull request.
The test suite hasn't passed since Tuesday.
```

### 疑问句

Have / Has + 主语 + 过去分词 + 其他？

```
Have you ever rewritten a project from scratch in a weekend?
Has the database migration completed?
```

## 用法

### 1. 过去动作对现在的影响（结果用法）

动作发生在过去，但结果或影响延续到现在。说话人关心的不是"什么时候做的"，而是"做完了没有 / 现在状态如何"。这是与一般过去时最核心的区别。

```
I have fixed the memory leak.        → 现在没问题了，可以上线了
I fixed the memory leak yesterday.   → 只是陈述昨天发生的一件事

She has lost her SSH key.            → 她现在进不去服务器了
She lost her SSH key last week.      → 只是说上周发生过这件事
```

> 关键判断：如果句子可以自然接上 "so now..." 或 "that's why now..."，用现在完成时。如果只是讲一个过去的故事，用一般过去时。

### 2. 从过去持续到现在的动作或状态（持续用法）

动作或状态从过去的某个时间点开始，一直持续到现在，并且可能继续持续下去。常与 since（自从）、for（持续了多久）搭配。

```
I have used Vim since 2018.              → 2018 年开始用，现在还在用
We have worked on this project for six months.  → 六个月前开始，现在还在做
The server has been running without a restart for 437 days.  → 还在跑
```

> 对比：I used Vim in 2018.（2018 年用过，现在可能不用了）

### 3. 经历（经验用法）

描述"曾经做过 / 从未做过"的经历，不关心具体时间点。常与 ever, never, once, twice 搭配。

```
Have you ever deployed to production on a Friday?   → 问人生经历
I have never written a line of Java.                 → 到目前为止的人生中
She has rewritten that module three times.           → 累计经历
```

> 对比：Did you deploy to production last Friday?（问上周五具体有没有部署，不是问人生经历）

### 4. 刚刚完成的动作（与 just 搭配）

强调动作在不久前刚刚完成，结果还"热乎"。

```
I have just pushed the hotfix.      → 刚推，你现在去拉就行
The CI has just turned green.       → 刚变绿，最新鲜的状态
```

一般过去时 vs 现在完成时——一张表说清楚：

| | 一般过去时 (Simple Past) | 现在完成时 (Present Perfect) |
|---|---|---|
| 关注点 | 动作本身，过去什么时候发生的 | 动作的结果/影响，现在怎么样了 |
| 时间 | 有明确的过去时间点 | 不关心具体时间，或者时间区间跨越到现在 |
| 典型问句 | When did you ...? What time ...? | Have you ever ...? How long have you ...? |
| 标志词 | yesterday, last week, in 2020, ago | already, yet, just, ever, never, since, for, so far |
| 信号 | 纯叙事，讲过去的故事 | 与现在有关，能接 so now... |

```
❌ I have fixed the bug yesterday.     → 有 yesterday 这个具体的过去时间，不能用现在完成时
✅ I fixed the bug yesterday.
✅ I have already fixed the bug.       → already 暗示"现在已经是修好的状态"
```

## 例句

```
I have pushed to main directly three times this week. My team lead has already scheduled a meeting about it.

Have you ever tried to explain a race condition to a non-programmer? I have, and I've never felt more alone.

Our database has been running on the same instance since 2019. Nobody has dared to touch it.
```

## 关键词/标志词

以下词语经常与现在完成时搭配，是判断是否使用该时态的重要信号：

- already（已经）— 肯定句，强调比预期更早完成
- yet（还没 / 已经...了吗）— 否定句和疑问句，强调预期的动作尚未发生
  ```
  I haven't reviewed the code yet.
  Have you merged the PR yet?
  ```
- just（刚刚）— 动作刚刚发生，结果新鲜
- ever（曾经）— 疑问句，问人生经历
- never（从未）— 否定句，强调零经历
- since（自从）— 接过去的时间点或从句（从句用一般过去时）
  ```
  I have worked here since the company had only five people.
  ```
- for（持续了）— 接一段时间长度
- so far / up to now / until now（到目前为止）
- recently / lately（最近）
- in the last / past + 时间段（在过去某段时间内）
  ```
  We have shipped three features in the last two weeks.
  ```
- this week / this month / today — 如果这个时间段还没结束
  ```
  I have written 200 lines of code today.  → 今天还没过完，可能还会写更多
  I wrote 200 lines of code today.         → 今天的工作结束了（比如已经下班了）
  ```

## 相关时态

- [[simple-past]] — 过去某个时间点完成的动作，与现在无直接联系（I fixed the bug vs I have fixed the bug）
- [[simple-present]] — 习惯性动作或客观事实（I use Vim vs I have used Vim for five years）
- [[past-perfect]] — 过去的过去（I had already fixed the bug before the meeting started）
