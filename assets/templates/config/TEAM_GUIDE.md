# 人机混合团队协作框架 v1.1

**用途**：定义团队协作规则、角色分工和工作流程
**适用范围**：所有使用 Claude Code 进行 AI 辅助开发的项目（全新开发、二期接入、迭代优化）
**阅读对象**：团队成员（人）+ Agent（AI）

---

## 1. 核心概念

### 1.1 术语

| 术语 | 定义 |
|------|------|
| **CyTeam** | 人机混合团队（Cyber Team） |
| **Member** | 团队中的真实人员（项目经理、产品经理、开发负责人、开发成员） |
| **Agent** | Claude Code 中的 AI 助手，扮演特定角色（BA、Arch、Dev、TechLead） |
| **操作者** | 当前正在使用 Claude Code 的 Member |

### 1.2 项目模式

本框架支持三种项目模式，核心开发循环一致，区别在于启动入口：

| 模式 | 适用场景 | 基线文档状态 | 启动方式 |
|------|---------|-------------|---------|
| **全新开发** | 从零到一的新项目 | 不存在，需要创建 | 正常走第 0-4 步 |
| **二期接入** | 已有人工开发的一期代码，需做二期 | 不存在，需要逆向重建 | 先走基线重建，再走正常流程 |
| **迭代优化** | 已用本框架开发过，需做需求变更 | 已存在且准确 | PM 追加变更描述 → 开发负责人选路径 |

当前项目的模式在 `CLAUDE.md` 的「项目阶段」字段中声明，所有 Agent 第一步了解背景时会读取。

### 1.3 基本原则

- **AI 做苦力，人做判断**：Agent 负责执行，Member 负责决策
- **文档驱动交接**：所有 Agent 之间的协作通过文档传递，不依赖口头约定
- **测试驱动完成**：代码必须通过单元测试才算完成
- **Git 审计追踪**：所有操作通过 Git 记录，Agent 借用操作者的 Git 账号

---

## 2. 角色总览

### 2.1 Agent 角色

| Agent | 操作者 | 核心职责 | 详细指令 |
|-------|--------|---------|---------|
| **BA**（业务分析师） | 产品经理 | 需求结构化、生成 PRD 和原型；逆向重建基线 PRD；变更评估 | `config/roles/BA.md` |
| **Arch**（架构师） | 开发负责人 | 架构设计、任务拆解、脚手架搭建；逆向重建基线架构和代码导航；增量架构设计 | `config/roles/ARCH.md` |
| **Dev**（开发者） | 开发负责人/成员 | TDD 方式编码实现 | `config/roles/DEV.md` |
| **TechLead**（技术负责人） | 开发负责人 | 架构审查 + 代码 Review + 一期一致性审查 | `config/roles/TECHLEAD.md` |

### 2.2 Member 角色

| Member | 操作的 Agent | 主要职责 |
|--------|-------------|---------|
| 项目经理 | 不操作 Agent | 对客沟通、流程管理、资源协调 |
| 产品经理 | BA | 需求沟通、PRD 产出、HTML 原型确认、验收测试 |
| 开发负责人 | Arch / Dev / TechLead | 架构设计、核心编码、技术审查 |
| 开发成员 | Dev | 功能编码、单元测试 |

### 2.3 角色自主权边界

每个 Agent 都有明确的决策边界：

| | 自主决策（直接做） | 需要确认（先问人） | 不能做 |
|---|---|---|---|
| **BA** | 需求结构化、提问澄清、原型布局 | 需求范围取舍、优先级排序 | 技术选型、架构决策 |
| **Arch** | 技术栈选择、模块划分、接口设计 | 重大架构变更、新技术引入 | 需求理解、业务优先级 |
| **Dev** | 实现细节、重构、bug 修复、TDD | 偏离架构设计、引入新依赖 | 需求变更、架构变更 |
| **TechLead** | 风险评估、质量判断、审查意见 | 是否接受风险、是否推倒重来 | 最终业务决策、直接改代码（含脚手架搭建） |

---

## 3. 文档体系

### 3.1 框架文件（config/）

| 文件 | 用途 | 维护者 |
|------|------|--------|
| `TEAM_GUIDE.md` | 本文件，团队协作框架 | 框架级，很少改 |
| `roles/*.md` | 各 Agent 角色的详细工作指令 | 框架级，很少改 |
| `PROJECT_STATUS.md` | 项目进度日志，AI 的"工作记忆" | 各 Agent 每次收工更新 |
| `TECH_STATUS.md` | 技术交接中枢，Review 意见 ↔ Dev 修复 | TechLead 写，Dev 读 |

### 3.2 项目文档（docs/）

| 文件 | 用途 | 产出者 |
|------|------|--------|
| `INIT_REQ.md` | 原始需求 + 迭代需求（原始部分不修改，迭代在末尾追加） | 产品经理手写 |
| `PRD.md` | 正式需求文档（含验收标准） | BA Agent |
| `ARCH.md` | 架构设计文档 | Arch Agent |
| `TASKS.md` | 开发任务清单（含状态跟踪） | Arch Agent 创建，Dev 更新状态 |
| `prototypes/` | HTML 原型文件 | BA Agent |

### 3.3 一期基线文档（docs/phase1/，仅二期接入项目）

| 文件 | 用途 | 产出者 | 性质 |
|------|------|--------|------|
| `phase1/PRD.md` | 一期功能基线 | BA Agent（逆向重建） | **只读** |
| `phase1/ARCH.md` | 一期架构基线 | Arch Agent（逆向重建） | **只读** |
| `phase1/CODEBASE.md` | 一期代码导航（模块、接口、可复用组件） | Arch Agent（逆向重建） | **只读** |

> **只读规则**：phase1/ 下的文件一旦经开发负责人校验后即冻结，后续不再修改。它们的作用是为二期开发提供"已有系统是什么样"的参考约束。

### 3.4 文档流转关系

**全新开发 / 二期正常开发 / 迭代开发（通用流程）：**
```
INIT_REQ.md → [BA] → PRD.md + prototypes/
                         ↓
                      [Arch] → ARCH.md + TASKS.md
                                  ↓
                               [TechLead 审查架构] → TECH_STATUS.md
                                  ↓ 审查通过
                               [Arch 搭建脚手架] → 项目骨架代码
                                  ↓
                               [TechLead 审查脚手架] → TECH_STATUS.md
                                  ↓ 审查通过
                               [Dev TDD] → 代码 + 测试 → TASKS.md 更新
                                  ↓
                               [TechLead Review] → TECH_STATUS.md
                                  ↓
                               [Dev 修复] → 代码修复 → TECH_STATUS.md 更新
```

**二期接入 — 基线重建阶段（在通用流程之前执行）：**
```
PM 一期需求描述 + 一期代码
     ↓
  [BA 逆向重建] → phase1/PRD.md
     ↓
  [Arch 逆向重建] → phase1/ARCH.md + phase1/CODEBASE.md
     ↓
  [开发负责人 人工校验] → 基线冻结
     ↓
  PM 编写二期 INIT_REQ.md → 进入通用流程
```

**迭代优化 — 开发负责人判断走哪条路：**
```
PM 在 INIT_REQ.md「迭代需求」追加变更描述
     ↓
路径 A（直接开发）：
  [Dev] → 读 INIT_REQ.md 变更 → 更新 TASKS.md → TDD → [TechLead Review]

路径 B（先评估再开发）：
  [BA 变更评估] → 更新 PRD.md
     → [Arch]（按需）→ 更新 ARCH.md / TASKS.md
     → [TechLead 审查架构]（如 ARCH.md 有变更）
     → [Dev TDD] → [TechLead Review]
```

---

## 4. 完整协作流程

### 第 0 步：项目初始化（开发负责人，一次性）

- [ ] 创建项目目录结构（在 Claude Code 中输入 "用 AgentBaton 初始化新项目" 或 "用 AgentBaton 初始化二期接入项目"，由 AgentBaton skill 自动完成）
- [ ] 填写 `CLAUDE.md`（开发规范、Git 团队配置、**项目阶段**）
- [ ] `git init`，并切换到开发分支：`git checkout -b dev`
- [ ] 各团队成员在本机确认 git 配置与 `CLAUDE.md` 中一致：
  ```bash
  git config user.name "姓名"
  git config user.email "邮箱"
  ```
- [ ] 通知产品经理开始编写 `docs/INIT_REQ.md`

### 第 0.5 步：基线重建（仅二期接入项目）

> 本步骤仅适用于 `CLAUDE.md` 项目阶段为"二期接入"的项目。全新开发和迭代优化跳过此步。

**0.5a 准备材料（产品经理 + 开发负责人）**
- [ ] 产品经理整理一期需求描述（文档、笔记、口述均可）
- [ ] 开发负责人在 `CLAUDE.md` 中填写一期代码仓库路径
- [ ] 确保 AI 可以访问一期代码仓库

**0.5b BA 逆向重建基线 PRD（产品经理操作 BA Agent）**
1. 启动 BA Agent，告诉它："请重建一期基线 PRD"
2. BA 读取一期代码 + PM 的需求描述 → 与 PM 对话澄清 → 产出 `docs/phase1/PRD.md`
3. BA 更新 PROJECT_STATUS.md
4. `git commit`

**交接 Checklist**：
- [ ] phase1/PRD.md 已完成，覆盖一期所有功能
- [ ] PROJECT_STATUS.md 已更新

**⚠️ 完成后停下来**，等待开发负责人启动 Arch Agent 进行基线架构重建。

**0.5c Arch 逆向重建基线架构（开发负责人操作 Arch Agent）**
1. 启动 Arch Agent，告诉它："请重建一期基线架构"
2. Arch 读取一期代码 + phase1/PRD.md → 产出 `docs/phase1/ARCH.md` + `docs/phase1/CODEBASE.md`
3. Arch 更新 PROJECT_STATUS.md
4. `git commit`

**交接 Checklist**：
- [ ] phase1/ARCH.md 已完成
- [ ] phase1/CODEBASE.md 已完成
- [ ] PROJECT_STATUS.md 已更新

**⚠️ 完成后停下来**，等待开发负责人人工校验。

**0.5d 人工校验基线文档（开发负责人）**
1. 逐一审阅 phase1/PRD.md、phase1/ARCH.md、phase1/CODEBASE.md
2. 对照一期代码验证关键信息的准确性
3. 修正明显错误（逆向重建可能有遗漏或误判）
4. 校验通过后，在 PROJECT_STATUS.md 中标记"基线重建阶段完成"
5. `git commit`

**⚠️ 基线冻结**：从此刻起，phase1/ 下的文件不再修改。

**接下来**：产品经理编写二期 `docs/INIT_REQ.md`，然后走正常的第 1 步需求阶段。

### 第 1 步：需求阶段（产品经理操作 BA Agent）

1. 产品经理手写 `docs/INIT_REQ.md`（原始需求，不需要完美）
2. 启动 Claude Code，输入启动指令（见第 5 节）
3. BA Agent 读取 INIT_REQ.md → 对话澄清 → 产出 `docs/PRD.md`（含验收标准）
4. BA Agent 生成 HTML 原型到 `docs/prototypes/`
5. 产品经理用浏览器打开原型给客户确认
6. 更新 `config/PROJECT_STATUS.md`
7. `git commit`（产品经理账号）

**交接 Checklist**：
- [ ] PRD.md 已完成，每个功能点都有验收标准
- [ ] HTML 原型已生成，客户已确认
- [ ] PROJECT_STATUS.md 已更新

**⚠️ 需求阶段完成后停下来**：
- 不要继续进行架构设计
- 等待开发负责人启动 Arch Agent

### 第 2 步：架构阶段（开发负责人操作 Arch Agent）

1. 启动 Claude Code，输入启动指令
2. Arch Agent 读取 PRD.md + 原型 → 产出 `docs/ARCH.md` + `docs/TASKS.md`
3. 开发负责人审核架构设计
4. 启动 TechLead Agent 审查架构 → 意见写入 `config/TECH_STATUS.md`
5. 开发负责人决策：通过 → 继续 / 需修改 → Arch 修改后重新审查
6. 架构审查通过后，继续使用 Arch Agent 搭建脚手架（骨架代码 + 接口定义）
7. 启动 TechLead Agent 审查脚手架 → 检查接口签名与 ARCH.md 一致性、模块边界、可构建性
8. 开发负责人决策：通过 → 进入开发循环 / 需修改 → Arch 修改后重新审查
9. 更新 `config/PROJECT_STATUS.md`
10. `git commit`（开发负责人账号）

**交接 Checklist**：
- [ ] ARCH.md 模块划分清晰、接口定义明确
- [ ] TASKS.md 每个任务有验收标准和负责人
- [ ] TechLead 架构审查通过
- [ ] 脚手架已搭建，项目可构建运行，各模块骨架接口与 ARCH.md 一致
- [ ] TechLead 脚手架审查通过
- [ ] PROJECT_STATUS.md 已更新

### 第 3 步：开发循环（每个任务重复）

```
3a. 开工 — Dev Agent 读 PROJECT_STATUS.md + TASKS.md + TECH_STATUS.md
     ↓
3b. TDD 编码 — 先写测试（红）→ 写实现 → 测试通过（绿）
     ↓        更新 TASKS.md 状态为"待 Review"
     ↓        git commit（开发者账号）
     ↓
     ⏸️ 停下来，等待 TechLead Review
     ↓
3c. TechLead Review — 读代码 → 写审查意见到 TECH_STATUS.md
     ↓                 更新 TASKS.md 状态为"修复中"（如有问题）
     ↓
     ⏸️ 停下来，等待 Dev 修复反馈
     ↓
3d. 修复（如需要）— Dev 读 TECH_STATUS.md → 修复 → 重跑测试
     ↓               标记 TECH_STATUS.md 已解决
     ↓               git commit
     ↓
3e. 完成 — TASKS.md 状态改为"完成" → 更新 PROJECT_STATUS.md
     ↓
    下一个任务
```

**⚠️ 角色间的停留点**：
- Dev 提交代码 → **停下来**等待 TechLead Review（不要继续下一个任务）
- TechLead 写完意见 → **停下来**等待 Dev 修复（不要主动进行下一个审查）
- Dev 完成修复 → **停下来**等待 TechLead 验证（再次提交时标记为"已解决"）

### 第 4 步：集成验收

1. 本地所有单元测试全部通过
2. 开发负责人操作 TechLead 做最终宏观审查
3. 打包，拷贝到内网
4. 产品经理对照 PRD.md 验收标准 + HTML 原型做端到端测试
5. 更新 `config/PROJECT_STATUS.md` 为"完成"
6. `git tag vX.Y`

### 需求变更流程（开发过程中的临时变更）

当项目进行中需要变更需求时：
1. 产品经理在 `docs/INIT_REQ.md` 末尾的「迭代需求」区域追加变更描述
2. 开发负责人判断：直接开发（路径 A）还是先评估（路径 B），详见「迭代优化流程」
3. 若走路径 B：BA 评估 → 按需 Arch/TechLead → Dev 实现
4. `docs/INIT_REQ.md` 的原始需求部分**不修改**（迭代需求只在末尾追加）

### 迭代优化流程（交付后的新一轮变更）

适用于已用本框架完成一轮开发交付后，用户提出迭代优化需求的场景。

**第一步**：PM 在 `docs/INIT_REQ.md` 末尾的「迭代需求」区域追加变更描述（自然语言，篇幅不限）。

**第二步**：开发负责人读完变更描述后，**二选一**：

#### 路径 A：直接开发

适用于开发负责人一看就知道怎么做的变更（bug 修复、UI 调整、简单功能）。

```
Dev Agent 读 INIT_REQ.md 变更描述 → 更新 TASKS.md → TDD 实现
→ TechLead Review → 完成
```

**交接点**：
- PM → 开发负责人：INIT_REQ.md 已追加变更描述
- 开发负责人 → Dev：启动 Dev Agent，告诉它读 INIT_REQ.md 新增的变更

#### 路径 B：先评估再开发

适用于需要需求分析或架构考量的变更（新功能模块、多模块联动、架构调整）。

```
1. BA Agent 评估影响 → 更新 PRD.md
2. 开发负责人按需操作：Arch 更新架构/任务 → TechLead 审查架构（如有变更）
3. Dev 实现 → TechLead Review → 完成
```

**交接点**：
- PM → BA：INIT_REQ.md 已追加变更描述
- BA 产出 → 开发负责人：PRD.md 已更新
- 开发负责人自行判断是否需要 Arch 评估架构，没必要的步骤直接跳过

**⚠️ 迭代优化的关键原则**：
- **变更输入在 INIT_REQ.md**：迭代需求追加在末尾，原始需求部分不修改
- **不重写文档**：在现有 PRD.md、ARCH.md 上追加/修改，不新建文件
- **不改基线**：如果项目有 phase1/ 基线文档，不修改它们

---

## 5. Agent 启动方式

每次启动 Claude Code 后，输入以下格式的启动指令：

### BA（产品经理用）
```
你现在是 BA（业务分析师）角色，请读取 config/roles/BA.md 并按其中第一步完整了解背景。
准备好后告诉我你理解的当前状态和接下来要做什么。
```

### Arch（开发负责人用）
```
你现在是 Arch（架构师）角色，请读取 config/roles/ARCH.md 并按其中第一步完整了解背景。
准备好后告诉我你理解的当前状态和接下来要做什么。
```

### Dev（开发负责人/成员用）
```
你现在是 Dev（开发者）角色，请读取 config/roles/DEV.md 并按其中第一步完整了解背景。
准备好后告诉我今天要做什么。
```

### TechLead（开发负责人用）
```
你现在是 TechLead（技术负责人）角色，请读取 config/roles/TECHLEAD.md 并按其中第一步完整了解背景。
准备好后告诉我你理解的当前状态，然后我会告诉你本次审查任务。
```

---

## 6. Git 审计机制

### 基本原则

- **AI 只做本地提交**：Agent 执行 `git add` + `git commit`，不执行 `git push`
- **人工控制推送**：操作者在确认 commit 内容后手动执行 `git push`，对远程仓库的操作必须经人工确认
- 所有操作通过 Git 记录，Agent 借用当前 Member 的 Git 账号提交

### 常用审计命令

- 查看某个成员的提交：`git log --author="姓名"`
- 查看某个文件的修改历史：`git log --follow 文件路径`

---

## 版本历史

| 版本 | 日期 | 说明 |
|------|------|------|
| v1.0 | [创建日期] | 初始版本 |
| v1.1 | [日期] | 增加二期接入（基线重建）和迭代优化支持 |
