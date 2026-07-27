---
tags: []
created: 2026-07-27
source: 自编范文
topic: startup engineering failure
---

# The AI feature that broke everything — and the board meeting that followed

## 文章

In March 2026, a San Francisco startup called Nomic attempted to [[deliver]] its most ambitious product update: an AI-powered code review tool. The [[concept]] was simple — upload a pull request, and the system would [[assist]] developers by catching bugs before a human reviewer ever looked at the code.

It did not go as planned.

Within 48 hours of launch, the tool began flagging perfectly [[accurate]] code as erroneous. Engineers across three time zones struggled to [[coordinate]] a response. The [[diagnostics]] dashboard — the one that was supposed to provide real-time error logs — displayed a [[baffling]] message: "Error: error."

"At first we thought it was a joke," said one engineer. "Then we realized no one had written [[consistent]] logging in the first place."

The team's [[attempt]] to roll back the update was blocked by a deployment pipeline that had been [[deliberate|deliberately]] configured to prevent exactly that — a safeguard that now worked against them. [[Despite]] frantic efforts, 14 hours passed before the old version was restored.

The [[board-of-directors]] called an emergency meeting. An external [[audit]] was commissioned. The findings were [[concise]] and brutal: the launch timeline had compressed what should have been a six-week QA cycle into six days. [[Essential]] integration tests did not [[exist]]. The engineering [[council]] that was supposed to approve releases had been bypassed entirely.

"There's a [[bent]] in this company — a cultural tendency to ship first and fix later," the auditor's report stated. "The problem is not skill. It's [[cadence]]. When every sprint is a sprint, the [[fraction]] of work that gets proper review approaches zero."

The [[criticism]] stung, but few inside the company disputed it. What made Nomic different — its [[distinctive]] culture of rapid iteration — was also what had nearly killed it. The CEO issued a rare mea culpa: "We confused speed with velocity. Speed gets you there fast. Velocity gets you there in the right direction. We had speed. We needed the [[exact]] opposite of what we did."

Nomic's tool eventually launched — four months later, with a test suite the team described as "obscenely thorough." It now reviews over 50,000 pull requests a month. But for the engineers who lived through that 14-hour outage, the lesson was simpler: some things cannot be hotfixed.

## 覆盖的知识点

| 知识点                    | 文中出现                                                          |
| ---------------------- | ------------------------------------------------------------- |
| [[deliver]]            | attempted to deliver its most ambitious product update        |
| [[concept]]            | The concept was simple                                        |
| [[assist]]             | assist developers by catching bugs                            |
| [[accurate]]           | flagging perfectly accurate code as erroneous                 |
| [[coordinate]]         | struggled to coordinate a response                            |
| [[diagnostics]]        | diagnostics dashboard                                         |
| [[baffling]]           | a baffling message: "Error: error."                           |
| [[consistent]]         | no one had written consistent logging                         |
| [[attempt]]            | The team's attempt to roll back                               |
| [[deliberate]]         | deliberately configured to prevent exactly that               |
| [[despite]]            | Despite frantic efforts                                       |
| [[board-of-directors]] | The board of directors called an emergency meeting            |
| [[audit]]              | An external audit was commissioned                            |
| [[concise]]            | The findings were concise and brutal                          |
| [[essential]]          | Essential integration tests                                   |
| [[exist]]              | did not exist                                                 |
| [[council]]            | The engineering council that was supposed to approve releases |
| [[bent]]               | a bent in this company                                        |
| [[cadence]]            | It's cadence                                                  |
| [[fraction]]           | the fraction of work that gets proper review                  |
| [[criticism]]          | The criticism stung                                           |
| [[distinctive]]        | its distinctive culture of rapid iteration                    |
| [[exact]]              | the exact opposite of what we did                             |
