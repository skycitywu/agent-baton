# AgentBaton —— Skill 源码仓库

> 本文件给 Claude Code 自动读取，让你（AI）理解这个仓库是什么、不该做什么。
> **不要**把这个文件和 `assets/templates/CLAUDE.md` 混淆——后者是给"使用 AgentBaton 框架的项目"的模板，是本 skill 的产出物。

---

## 这是什么仓库

**AgentBaton skill 本身的源码**，已开源到 https://github.com/skycitywu/agent-baton。

这里**不是**一个使用 AgentBaton 框架的业务项目。所以：

- **不要在本仓库内启动 BA / Arch / Dev / TechLead 角色**——那些角色是给业务项目用的
- **不要把 `assets/templates/` 的模板拷贝到本仓库根目录**——本仓库不是被初始化出来的项目
- 如果 AgentBaton skill 在本仓库内被错误触发，**立即退出**，按普通方式回答

---

## 仓库结构与各文件用途

| 路径 | 角色 |
|------|------|
| `SKILL.md` | Skill 入口，Claude Code 按 frontmatter `description` 判断触发；改它会改变触发行为 |
| `README.md` | 给 GitHub 访客看的开源介绍（中文） |
| `LICENSE` | MIT |
| `assets/templates/` | **关键资产**：用户初始化新项目时被拷贝过去的全套模板，改这里影响所有未来用户 |
| `assets/templates/CLAUDE.md` | 模板 CLAUDE.md（给用户项目用，不是给本仓库用） |
| `assets/templates/config/TEAM_GUIDE.md` | 协作协议主体（给用户项目用） |
| `assets/templates/config/roles/*.md` | 4 个角色详细指令（给用户项目用） |
| `assets/templates/docs/` | PRD / ARCH / TASKS / INIT_REQ 等文档模板 |
| `docs/HUMAN_GUIDE.md` | 给人类用户读的详细使用指南 |
| `CLAUDE.md`（本文件） | 给 Claude 读，让 AI 理解本仓库性质 |

---

## 关键约束（修改前必读）

### SKILL.md 规范
- 必须 < 500 行（Anthropic Skill 规范，progressive disclosure）
- frontmatter 必须含 `name` + `description` 两个字段
- `description` 是 skill 触发的唯一依据，修改后必须双向回归测试：
  - 正向：明确触发场景能起来（"用 AgentBaton 初始化"、"启动 BA 角色"）
  - 反向：日常改代码不会被误触发（"帮我改 main.py 的 bug"）

### assets/templates/ 是公共契约
- 任何修改都会传递给所有未来克隆/更新本 skill 的用户
- 模板内文件之间的路径引用**必须**假设：拷贝到用户项目根目录后，相对路径仍然有效（例如 `config/roles/BA.md` 引用 `config/TEAM_GUIDE.md`，假设两者都在用户项目的 `config/` 下）
- 不要在模板里写绝对路径或本机路径

### 本仓库的 commit/push
- 默认用 global git 身份 `skycitywu <161581125+skycitywu@users.noreply.github.com>`
- 公开仓库，**不要**把私密讨论、个人邮箱、内网信息 commit 进来
- push 前用户会确认（已是 Claude Code 默认行为）

---

## 测试本地改动

本仓库通过软链注册到 Claude Code 的 skills 目录：

```
~/.claude/skills/agent-baton -> ~/code/agent-baton
```

所以**改完不需要重新安装**，新会话立即生效。验证步骤：

```bash
# 1. 全新模式触发测试
mkdir /tmp/baton-test-new && cd /tmp/baton-test-new
claude
# 输入：用 AgentBaton 初始化新项目
# 期望：skill 触发，生成完整目录结构

# 2. 二期接入模式触发测试
mkdir /tmp/baton-test-phase2 && cd /tmp/baton-test-phase2
claude
# 输入：用 AgentBaton 初始化二期接入项目
# 期望：含 docs/phase1/，CLAUDE.md 项目阶段为"二期接入"

# 3. 反向：不应触发
cd ~/code/CyTeamCoding  # 任一非 AgentBaton 项目
claude
# 输入：帮我看下 README 有没有错别字
# 期望：Claude 直接回答，不进入 AgentBaton 流程
```

---

## 历史与脉络

- **前身**：`~/code/CyTeamCoding/framework/`（CyTeam 框架原型，在企业项目 JGBS 中实战打磨）
- **演进决策档案**：`~/Documents/Obsidian Vault/研究类/AI Coding框架/框架开发记录.md`（含 antigravity 协助起名等）
- 本仓库是 CyTeam 框架的开源版本，命名 AgentBaton

---

## 工作风格约定

- 沟通用中文
- 改 SKILL.md / templates 时，先解释 why，再动手
- 重大决策（重写 SKILL.md、改触发逻辑、加新角色）先问用户
- 小改动（修 typo、调措辞、补 FAQ）可以直接做
