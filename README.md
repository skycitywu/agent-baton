# AgentBaton

> **轻量级多 Agent 接力 AI Coding 框架**
> A lightweight multi-agent relay coding skill for Claude Code.

把"BA → Arch → Dev → TechLead"的团队工作流变成 4 个 Agent 角色，通过 Markdown 文档接力，状态沉淀在文件里，跨会话/跨 Agent/跨模型都能断点续接。

---

## 一句话定位

**给企业级复杂项目用的最小可用 vibe coding 框架** —— 解决 AI Coding 中"上下文丢失、多人/多 Agent 协作混乱、停不下来"三大痛点。

---

## 为什么不用 superpowers / GSD / gstack？

| | superpowers / GSD / gstack | **AgentBaton** |
|---|---|---|
| 依赖 | 重型 hooks / MCP / 编排脚本 | 纯 Markdown，零依赖 |
| 学习成本 | 几十个组件，上手陡 | 4 个角色 + 3 个状态文件 |
| Token 消耗 | 高（每次大量上下文注入） | 低（progressive disclosure，按需加载） |
| 跨 Agent | 强耦合 Claude Code | 协议是文档，**任何 LLM/Agent 都能续接** |
| 适用规模 | 单人 vibe 到中型项目 | 企业级、多人、长周期 |

如果你已经被那些框架的复杂度劝退过、又不愿放弃多角色协作的好处，AgentBaton 是给你的。

---

## 安装（30 秒）

```bash
git clone https://github.com/skycitywu/agent-baton.git ~/.claude/skills/agent-baton
```

就这一行。在 Claude Code 里它会作为 Skill 自动按需触发。

要确认安装成功，在任意目录启动 Claude Code，输入：
```
用 AgentBaton 初始化新项目
```
看到 Claude 开始拷贝模板就 OK。

---

## 5 分钟上手

### 创建一个新项目

```bash
mkdir my-project && cd my-project
claude
```

然后告诉 Claude：
```
用 AgentBaton 初始化新项目
```

它会自动生成完整目录结构：
```
my-project/
├── CLAUDE.md                 # 项目说明书
├── config/
│   ├── TEAM_GUIDE.md         # 协作协议
│   ├── PROJECT_STATUS.md     # AI 的"工作日记"
│   ├── TECH_STATUS.md        # Review 意见 ↔ Dev 修复
│   └── roles/
│       ├── BA.md             # 业务分析师角色
│       ├── ARCH.md           # 架构师角色
│       ├── DEV.md            # 开发者角色
│       └── TECHLEAD.md       # 技术负责人角色
└── docs/
    ├── INIT_REQ.md           # 原始需求（你来写）
    ├── PRD.md                # BA 产出
    ├── ARCH.md               # Arch 产出
    ├── TASKS.md              # Arch 产出
    └── prototypes/           # HTML 原型
```

### 各角色启动指令

```
你现在是 BA 角色，请读取 config/roles/BA.md 并按其中第一步完整了解背景。
```

把 `BA` 替换成 `Arch` / `Dev` / `TechLead` 就能切换角色。

---

## 核心概念

### 4 个 AI 角色

| 角色 | 谁来操作 | 做什么 |
|------|---------|--------|
| **BA**（业务分析师） | 产品经理 | 把模糊需求变成结构化的 PRD + HTML 原型 |
| **Arch**（架构师） | 开发负责人 | 设计系统架构，把需求拆成开发任务 |
| **Dev**（开发者） | 开发负责人/成员 | 先写测试、再写代码，测试通过才算完成 |
| **TechLead**（技术负责人） | 开发负责人 | 审查架构设计 + 审查代码质量 |

### 3 个关键状态文件

| 文件 | 作用 |
|------|------|
| `CLAUDE.md` | 告诉 AI 项目的技术栈、规范、Git 配置 |
| `config/PROJECT_STATUS.md` | AI 的工作日志，每次开工读、收工写 |
| `config/TECH_STATUS.md` | TechLead 审查意见 ↔ Dev 修复记录的通信板 |

### 接力机制（核心哲学）

```
原始需求 INIT_REQ.md
    ↓ BA
PRD.md + HTML 原型
    ↓ Arch
ARCH.md + TASKS.md
    ↓ TechLead 审查架构
    ↓ Arch 搭脚手架
    ↓ TechLead 审查脚手架
TDD 开发循环：
    Dev 写测试 → 写代码 → 测试通过
    ↓
    TechLead Review
    ↓
    Dev 修复
    ↓
    完成 → 下一个任务
```

每个角色完成自己阶段就**停下来**，等下一个角色或人接手。这是 AgentBaton 解决"AI 停不下来"的核心机制。

---

## 三种项目模式

| 模式 | 适用场景 | 启动方式 |
|------|---------|---------|
| **全新开发** | 从零到一的新项目 | "用 AgentBaton 初始化新项目" |
| **二期接入** | 已有人工开发的一期代码，需做二期 | "用 AgentBaton 初始化二期接入项目"，会额外生成 `docs/phase1/` 目录用于基线重建 |
| **迭代优化** | 已用本框架开发过一轮，需求变更 | 在 `docs/INIT_REQ.md` 末尾追加变更，开发负责人选路径 A（直接开发）或 B（先评估再开发） |

---

## FAQ

**Q：AI 能记住昨天做了什么吗？**
A：本身不能。但每次开工时 AI 会读 `PROJECT_STATUS.md`，里面有完整的工作记录，能"恢复记忆"。**收工前一定要让 AI 更新这个文件。**

**Q：产品经理不懂技术，能操作 Claude Code 吗？**
A：能。BA 角色完全用自然语言对话，零技术门槛。

**Q：多个开发成员同时开发不同任务会冲突吗？**
A：不会。每个人操作自己的 Claude Code 会话，TASKS.md 里标注了任务负责人，通过 Git 管理合并。

**Q：需求变更怎么办？**
A：在 `docs/INIT_REQ.md` 的「迭代需求」区域追加变更（不修改原始需求部分），然后启动 BA 角色评估，或直接让 Dev 改（取决于变更复杂度）。

**Q：Skill 会不会误触发？我有时只想简单改个 bug。**
A：不会。SKILL.md 的触发条件做了双层防护：(1) description 明确排除常规改代码任务；(2) skill 触发后第一步会自检"当前是否真的是 AgentBaton 项目场景"，不符合就立刻退出。

**Q：可以和别的 Agent（Cursor / Google Antigravity 等）混用吗？**
A：可以。协议是纯文档，Claude Code 写完的状态文件，换 Cursor 打开继续接力没有任何问题——这是 AgentBaton 设计上的核心优势之一。

**Q：内网测试发现 bug 怎么办？**
A：在 `PROJECT_STATUS.md` 记录 bug 为阻塞项，启动 Dev 角色修复，重新打包验证。

---

## 深入了解

更详细的工作流、阶段交接、各角色详细工作指令，见：
- [docs/HUMAN_GUIDE.md](docs/HUMAN_GUIDE.md) — 人类版完整使用指南
- 项目初始化后的 `config/TEAM_GUIDE.md` — Agent 版协作协议
- 项目初始化后的 `config/roles/*.md` — 各角色详细指令

---

## 贡献 / 反馈

发 Issue 或 PR 到 [github.com/skycitywu/agent-baton](https://github.com/skycitywu/agent-baton)。

特别欢迎的反馈类型：
- 在真实项目落地后的体感（什么 work、什么不 work）
- 角色边界的边界 case（哪些场景不清楚谁该做）
- 与其他 AI Coding 工具（Cursor / Antigravity / Codex 等）混用的实战经验

---

## License

[MIT](LICENSE) © 2026 skycitywu

---

## 致谢

- 起名"AgentBaton"由 Google Antigravity 协助调研确认
- 框架在真实企业项目 JGBS 中实战打磨
- 灵感来源：与 superpowers / GSD / gstack 等开源 AI Coding 框架的"反面"探索
