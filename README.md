# Smart Retrospective 智能项目复盘

> **让 AI 做你的项目考古学家** — 从飞书数据中重建项目真实历史，发现人工复盘看不见的隐藏模式。

---

## 这个 Skill 解决什么问题？

每个团队都做复盘，但绝大多数复盘是这样的：

```
周五下午 → 拉一个会 → "大家觉得这次项目怎么样？"
→ 3个人说了自己记住的事 → 写了一页PPT → 下次犯同样的错
```

**根本原因**：人的记忆是有偏差的。

- 你记得的 ≠ 实际发生的（选择性记忆）
- 你以为的原因 ≠ 真正的原因（归因偏差）
- 你觉得"一切正常" ≠ 数据上正常（感知盲区）

而项目的真实历史，其实全部记录在飞书里——群聊、妙记、任务、文档、日历。只是没有人把它们串起来看。

**Smart Retrospective 做的就是这件事：** 派出5个AI Agent并行采集飞书数据，交叉分析，找出人眼看不见的跨源模式。

---

## 核心创新：跨源模式检测

市面上的工具都是"单源分析"——总结会议、整理消息、统计任务。

**Smart Retrospective 做的是"跨源关联"：**

| 单源能看到的 | 跨源才能发现的 |
|-------------|-------------|
| "群里讨论减少了" | **危机前静默**：讨论量下降73% → 5天后3个任务逾期（如果提前检测到静默，可提前5天预警） |
| "这个任务逾期了" | **沉默瓶颈**：张三是跨3个群的信息枢纽，响应延迟直接导致下游18小时/周的等待 |
| "这周开了很多会" | **会议通胀**：会议数+75%，但任务完成率-7%——会议正在从工具变成负担 |
| "会上说了要做X" | **承诺衰减**：42个会议承诺 → 28个建了任务 → 19个完成，整体闭环率仅45% |

这些模式**对单个数据源是不可见的**——只有同时看消息+任务+会议+文档+日历，才能发现因果链。

---

## 7种检测模式

| # | 模式 | 数据源 | 检测的是什么 |
|---|------|--------|------------|
| P1 | **决策漂移** | 妙记+群聊 | 同一议题反复讨论、结论不断翻转 |
| P2 | **沉默瓶颈** | 群聊+任务 | 某人是隐藏的信息枢纽，拖慢整体进度 |
| P3 | **知识蒸发** | 群聊+文档 | 重要讨论未文档化，知识随消息流沉没 |
| P4 | **危机前静默** | 群聊+任务+日历 | 沟通频率异常下降预示即将到来的问题 |
| P5 | **会议通胀** | 日历+任务 | 会议增加但产出不增——协调开销失控 |
| P6 | **承诺衰减** | 妙记+任务 | 从"说了"到"做了"每一步都在丢失 |
| P7 | **信息孤岛** | 多群聊 | 不同团队讨论同一话题但结论不同 |

---

## 架构

```
                     ┌─────────────────────────┐
                     │      主 CC (调度者)        │
                     │  范围确认 → 派发 → 合成    │
                     └──────────┬──────────────┘
                                │ Phase 1: 并行采集
           ┌────────────────────┼────────────────────┐
           ▼                    ▼                     ▼
    ┌─────────────┐    ┌──────────────┐    ┌──────────────┐
    │ IM-Collector │    │Min-Collector │    │Task-Collector│
    │  群消息采集   │    │  妙记采集     │    │  任务采集     │
    └──────┬──────┘    └──────┬───────┘    └──────┬───────┘
           │                  │                    │
    ┌──────┴──────┐    ┌──────┴───────┐           │
    │Doc-Collector│    │Cal-Collector │           │
    │  文档采集    │    │  日历采集     │           │
    └──────┬──────┘    └──────┬───────┘           │
           │                  │                    │
           └─────────────┬────┴────────────────────┘
                         ▼
              ┌─────────────────────┐
              │ Phase 2: 指标引擎    │  ← 主CC计算四维指标
              │ 沟通/执行/会议/知识  │
              └──────────┬──────────┘
                         ▼
              ┌─────────────────────┐
              │ Phase 3: 模式检测    │  ← 核心创新
              │ 7种跨源模式交叉验证  │     双源验证 + 证据链
              └──────────┬──────────┘
                         ▼
              ┌─────────────────────┐
              │ Phase 4: 报告生成    │  → 飞书文档
              │ 金字塔结构+白板可视化 │  → 飞书白板
              └─────────────────────┘  → 飞书多维表
```

---

## 快速开始

### 前置条件

1. 安装 [飞书 CLI](https://github.com/nicepkg/feishu-cli) 并完成授权
2. 安装 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 
3. 将本 skill 安装到 Claude Code skills 目录

### 安装

```bash
# 克隆到 Claude Code skills 目录
cd ~/.claude/skills
git clone https://github.com/Evan-miwillbe/smart-retrospective.git

# 或手动下载 SKILL.md 到 ~/.claude/skills/smart-retrospective/
```

### 使用

在 Claude Code 中输入：

```
/smart-retrospective

项目：Q2产品迭代
群ID：oc_xxxxxxxxx, oc_yyyyyyyyy
时间：2026-03-01 ~ 2026-04-30
输出：~/Desktop/retro-q2
```

AI 会自动完成：
1. 发现并确认数据源范围
2. 派出5个Agent并行采集飞书数据
3. 计算四维指标（沟通/执行/会议/知识）
4. 检测7种跨源模式
5. 生成结构化复盘报告到飞书文档

### 分析深度

| 模式 | 时间 | 场景 |
|------|------|------|
| `depth: quick` | ~15分钟 | 快速健康检查（仅消息+任务） |
| `depth: standard` | ~45分钟 | 标准复盘（全5源+全7模式） |
| `depth: deep` | ~90分钟 | 重大项目/事故复盘（深度采集+交叉验证） |

---

## 示例输出

<details>
<summary>点击展开：复盘报告示例片段</summary>

```markdown
# Q2产品迭代 智能复盘报告

## 执行摘要

复盘范围：2026-03-01 ~ 04-30 | 2群 + 6妙记 + 32任务 + 15文档 + 20日历

### 核心发现
1. **承诺衰减严重**：会议承诺闭环率仅45%，最大丢失在"说了但没建任务"环节
2. **沉默瓶颈**：@张三 是跨群信息枢纽，其响应延迟每周造成约18小时下游等待
3. **知识蒸发**：5次关键技术讨论（共200+条消息）未产出任何文档

### 关键建议
1. 每次会议结束5分钟内，由会议主持人创建飞书任务（预期提升闭环率至70%+）
2. 给张三设立backup联系人，分散信息枢纽压力
3. 技术讨论超过20条消息时，自动提醒创建文档（可通过飞书机器人实现）
```

</details>

---

## 设计原则

| 原则 | 含义 |
|------|------|
| **跨源才是模式** | 单源发现只是信号，双源交叉验证才升级为模式 |
| **证据链不可断** | 每个发现可追溯到具体消息/任务/会议（时间戳+来源） |
| **量化 > 定性** | "响应时间从2h增至8h" > "沟通效率下降了" |
| **归因不归咎** | 指向系统问题，不指向个人责任 |
| **数据不足则降级** | 某源无数据不阻塞，标注后继续分析 |

---

## 使用的飞书 CLI 能力

| 模块 | 用途 |
|------|------|
| `lark-im` | 读取群消息历史、发送报告通知 |
| `lark-minutes` | 读取妙记/会议记录 |
| `lark-task` | 读取任务状态和时间线 |
| `lark-doc` / `lark-wiki` | 读取文档信息、创建复盘报告 |
| `lark-calendar` | 读取会议日程、参与者 |
| `lark-whiteboard` | 创建项目时间线可视化 |
| `lark-base` | 存储指标数据到多维表 |

---

## 技术栈

- **运行环境**: Claude Code (Claude Opus/Sonnet)
- **数据采集**: 飞书 CLI（5个数据源并行采集）
- **分析方法**: Multi-Agent并行 + 跨源交叉验证 + 统计模式检测
- **输出格式**: 飞书文档 + 白板 + 多维表

---

## 为什么不用现有工具？

| 工具 | 能做到 | 做不到 |
|------|--------|--------|
| 飞书妙记 | 会议转文字 | 不追踪决策是否被执行 |
| 飞书任务看板 | 看任务状态 | 不关联任务延期与沟通模式 |
| 人工复盘会 | 收集主观感受 | 无法发现感知盲区中的问题 |
| **Smart Retrospective** | **全部以上 + 跨源模式检测** | **需要飞书CLI+Claude Code环境** |

---

## 这不是一个假想需求

我们在 Reddit、Hacker News、知乎、V2EX 等平台搜集了大量真实用户反馈，验证了这个痛点的普遍性：

### 数据说话

| 数据 | 来源 |
|------|------|
| **73%** 的会议 action items 永远不会被完成 | PMI 社区调研 |
| **45%** 的开发者每周 3 次以上因知识孤岛受阻 | StackOverflow 2024 开发者调查 |
| **80%** 的企业复盘会是无效劳动 | 知乎专栏（1.6万阅读） |
| **38%** 的会议决策在执行前蒸发 | 飞书妙记内部数据（使用前执行率仅62%） |
| **90%** 的中国企业受困于信息孤岛 | 知乎行业分析 |
| 企业每年因知识孤岛损失 **$47M** | CIO Dive 行业报告 |
| 某团队同一问题**连续 5 次复盘提出，零改变** | GoRetro 开发者社区 |

### 真实用户怎么说

> "80%的复盘会都是无效劳动，不是**邀功仪式**就是**甩锅大会**。" — 知乎

> "没有结果的会议是白开，**没有跟踪的会议是多开**。" — 知乎

> "IM工具和会议都不是好的协同工具，因为**无法把信息做到真正的结构化**。" — 左耳朵耗子（CoolShell）

> "I've repeated the same retrospective items sprint over sprint without them being addressed." — Hacker News

> "Post-mortem lessons end up in a document that sits in some knowledge repo and **is hardly ever read**." — Scrum Alliance

---

## 路线图

- [x] v1.0: 4 Phase + 7 Pattern + Multi-Agent 采集
- [ ] v1.1: 定期自动巡检模式（schedule + 趋势对比）
- [ ] v1.2: 团队级聚合分析（跨项目对比）
- [ ] v2.0: 自迭代优化检测规则（根据用户反馈调整模式权重）

---

## License

MIT

---

## 致谢

本项目参加 [飞书 CLI 创作者大赛](https://bytedance.larkoffice.com/docx/HWgKdWfeSoDw36xu7EYctBrUnsg)，感谢飞书团队和 Way to AGI 社区。

Built with [Claude Code](https://claude.ai/claude-code) + [飞书 CLI](https://github.com/nicepkg/feishu-cli)

---

## 参考资料与来源

### 行业研究
- [PMI: 73% of meeting action items go unfinished](https://www.projectmanagement.com/) — PMI Community Survey
- [StackOverflow 2024 Developer Survey: Knowledge Silos](https://survey.stackoverflow.co/2024/) — 45% of devs impacted 3+ times/week
- [CIO Dive: Companies lose $47M/year to poor knowledge sharing](https://www.ciodive.com/)
- [Ponemon Institute: Average unplanned outage costs $9,000/minute](https://www.ponemon.org/)
- [PagerDuty 2024: Customer-impacting incidents up 43% YoY](https://www.pagerduty.com/)

### 中文平台讨论
- [知乎：80%公司都不会复盘，如何开好一场复盘会？](https://zhuanlan.zhihu.com/p/629720809)
- [知乎：你的项目复盘会，为什么开不好？](https://zhuanlan.zhihu.com/p/1976973852446844258)
- [知乎：为什么我们的会议开得多却没产出](https://zhuanlan.zhihu.com/p/1961170631522521882)
- [知乎：90%的中国企业受困于信息孤岛](https://zhuanlan.zhihu.com/p/166543943)
- [知乎：跨部门协同的五个障碍](https://zhuanlan.zhihu.com/p/375152039)
- [V2EX：连续加班一个多月后的反思（159回复）](https://www.v2ex.com/t/927862)
- [V2EX：每天早会制度讨论（43回复）](https://hk.v2ex.com/t/980106)
- [V2EX：求推荐公司用的知识库系统（73回复）](https://v2ex.com/t/914777)
- [CoolShell：聊聊团队协同和协同工具](https://coolshell.cn/articles/22298.html) — 左耳朵耗子
- [CSDN：企业知识库的三大痛点](https://blog.csdn.net/yufeishuju/article/details/150587846)

### 英文社区讨论
- [Hacker News: Retrospectives as box-ticking exercises](https://news.ycombinator.com/item?id=28352828)
- [Hacker News: Why aren't retrospectives a thing in traditional industries?](https://news.ycombinator.com/item?id=17063246)
- [GoRetro: Same issue raised 5 retros in a row with zero change](https://www.goretro.ai/)
- [Scrum Alliance: Why post-mortems fail](https://www.scrumalliance.org/)
- [Easy Agile: Retro action item completion rate 40-50%](https://www.easyagile.com/)
