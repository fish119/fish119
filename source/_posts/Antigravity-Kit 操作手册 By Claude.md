title: Antigravity-kit 操作手册 By Claude
tags:
  - Vibe Coding
  - Antigravity
  
categories:
  - Vibe Coding
  - Antigravity

date: 2026-03-15 21:20:00

---

最近在使用 Google Antigravity 进行 Vibe Coding，看到了一个有意思的工具：[antigravity-kit](https://github.com/vudovn/antigravity-kit) ，于是分别利用 Claude 和 Gemini 制作了操作手册，这一份是 Claude 的

<!-- more -->

# Antigravity Kit 最佳实践操作手册

> 照着做就 OK — 全栈全链路 Vibecoding 场景专版

**版本：** 基于 antigravity-kit v2.0.0  
**适用编辑器：** Google Antigravity / Cursor / Windsurf  
**核心前提：** 本手册的所有场景均假设项目为全栈全链路开发（前端 + 后端 + 数据库），因此 `/orchestrate` 是执行阶段的默认命令，而非特殊选项。

---

## 目录

1. [三件事先搞清楚](#三件事先搞清楚)
2. [全局准备：安装与配置](#全局准备安装与配置)
3. [场景一：新建项目](#场景一新建项目)
4. [场景二：已有项目完善](#场景二已有项目完善)
5. [场景三：重构项目](#场景三重构项目)
6. [全栈场景 Prompt 写法指南](#全栈场景-prompt-写法指南)
7. [Agents 速查手册](#agents-速查手册)
8. [Workflows 速查手册](#workflows-速查手册)
9. [常见问题 & 避坑指南](#常见问题--避坑指南)

---

## 三件事先搞清楚

### 1. 三个核心概念

| 概念           | 是什么                       | 怎么触发                               |
| ------------ | ------------------------- | ---------------------------------- |
| **Agent**    | 专家 AI 角色（前端/后端/安全等 20 种）  | 自动路由，或手动 `Use the xxx-agent to...` |
| **Skill**    | 领域知识模块（React/测试/部署等 37 个） | 按上下文自动加载，无需手动操作                    |
| **Workflow** | Slash 命令工作流（11 个标准流程）     | 直接输入 `/命令 描述`                      |

### 2. 全栈场景的命令默认逻辑

| 阶段          | 默认命令                   | 理由                   |
| ----------- | ---------------------- | -------------------- |
| 需求不清晰       | `/brainstorm`          | 先想清楚再动手              |
| 需求清晰、准备开工   | `/plan`                | 生成任务分解               |
| **执行任何功能**  | **`/orchestrate`**     | 全栈项目每个功能必然跨前端+后端+数据库 |
| 仅改 UI 样式/文案 | `/ui-ux-pro-max` 或直接描述 | 唯一不需要多 Agent 的情况     |
| 出现 Bug      | `/debug`               | 系统性排查                |
| 纯代码质量改善     | `/enhance`             | 不涉及功能变更的重构           |
| 上线前         | `/test` → `/deploy`    | 固定顺序                 |

### 3. `/orchestrate` 的本质

`/orchestrate` 不是"高级的 `/create`"，它是一个**项目经理**：把你的需求拆解成子任务，然后依次调度 `frontend-specialist`、`backend-specialist`、`database-architect`、`security-auditor`、`test-engineer` 等专家顺序执行，最后合并验证。
![](/uploads/20260315/0101.png)
全栈项目里，每个功能都会同时触碰前端+后端+数据库，因此 `/orchestrate` 是你在执行阶段应该形成肌肉记忆的命令。

---

## 全局准备：安装与配置

### 第一步：安装 CLI

```bash
# 一次性使用（推荐，无需全局污染）
npx @vudovn/ag-kit init

# 或全局安装
npm install -g @vudovn/ag-kit
ag-kit init
```

安装完成后，项目根目录会出现 `.agent/` 文件夹：

```
.agent/
├── agents/        # 20 个专家角色定义
├── skills/        # 37 个领域知识模块
├── workflows/     # 11 个 Slash 命令
├── rules/         # 全局规则（GEMINI.md）
└── ARCHITECTURE.md
```

### 第二步：配置 Git（关键，不可跳过）

⚠️ **不要把 `.agent/` 加入 `.gitignore`**，否则 Antigravity 无法索引 workflows，所有 Slash 命令都不会出现在建议下拉框。

正确做法：加入**本地排除文件**（仅对你本机生效，不提交到远端）：

```bash
echo ".agent/" >> .git/info/exclude
```

### 第三步：验证安装

```bash
ag-kit status
```

### 更新（日后需要时）

```bash
ag-kit update
```

---

## 场景一：新建项目

> 从零开始，有想法但尚未动工。

### 标准流程

```
/brainstorm  →  /plan  →  /orchestrate  →  /test  →  /preview  →  /deploy
```

---

#### Step 1 — 想清楚再动手：`/brainstorm`

```
/brainstorm 我想做一个 SaaS 任务管理工具，多用户，有团队协作，支持截止日期和提醒
```

Kit 用苏格拉底式提问引导你明确：目标用户、核心功能边界、技术约束、MVP 范围。

✅ **产出：** 结构化需求文档。把这份输出保留，下一步直接用。

---

#### Step 1.5 — 确立数字契约：生成 `docs/PRD.md`

`/brainstorm` 结束、需求基本对齐之后，在进入任何执行步骤之前，先让 AI 把共识固化成一个项目内的真实文件：

```
Use the product-manager to generate docs/PRD.md based on the above brainstorm，
包含：
1. 产品一句话定位
2. 核心 User Story
3. 核心实体的数据模型草图（表名 + 关键字段）
4. 前后端核心交互逻辑（API 草图）
5. MVP 边界——明确列出哪些功能现在不做
```

生成后**你来审核**：检查数据模型有没有漏字段、MVP 边界有没有写错、有没有你不想要的功能被 AI 自作主张加进去。确认后再往下走。

如果审核发现问题，直接告诉 AI 修正：

```
PRD 里数据模型漏了 avatar_url 字段；
MVP 阶段不做邮件找回密码，只用 Google OAuth 登录；
请更新 docs/PRD.md。
```

✅ **为什么这一步不能跳过：**

`/orchestrate` 会同时调度数据库、后端、前端三个 Agent。如果没有一份统一的 PRD，前端生成的字段可能跟数据库对不上，后端的接口返回格式可能跟前端预期不符。`docs/PRD.md` 是多 Agent 协作的公共上下文——它是宪法，不是文档。

此外，它还解决了 Vibecoding 最大的实际痛点：**跨会话的项目记忆**。几天后你重新打开项目、AI 上下文已清空时，一句话就能找回全部状态：

```
/status 请阅读 docs/PRD.md 并告诉我当前开发进度和待完成的功能
```

---

#### Step 2 — 分解任务：`/plan`

```
/plan 请严格遵循 docs/PRD.md 中的要求和 MVP 边界，生成全栈开发任务拆解清单，
包含前端、后端、数据库三层的任务分解和优先级
```

✅ **产出：** 带优先级的任务列表 + 技术选型建议（前端框架、后端语言、数据库方案）。

---

#### Step 3 — 生成完整项目骨架：`/orchestrate`

把 Step 2 确认的技术选型填进去，**并显式引用 PRD**：

```
/orchestrate 请所有参与的 Agent 在写代码前先读取 docs/PRD.md 对齐业务逻辑和数据模型，
然后基于 plan 的任务清单搭建完整的全栈项目骨架：
- 前端：Next.js 14 App Router + Tailwind CSS + shadcn/ui
- 后端：Next.js API Routes + tRPC
- 数据库：Prisma + PostgreSQL，设计 users/teams/tasks/notifications 表
- 认证：NextAuth.js（邮箱 + Google OAuth）
- 生成项目结构、核心配置文件、数据库迁移文件
```

`/orchestrate` 内部会依次调用：

- `@backend-specialist` 搭建 API 层
- `@database-architect` 设计 Schema 和迁移
- `@frontend-specialist` 搭建页面结构
- `@security-auditor` 检查认证安全性
- `@test-engineer` 生成基础测试

---

#### Step 4 — UI 精调（可选）：`/ui-ux-pro-max`

```
/ui-ux-pro-max 为任务管理应用设计 Dashboard 主页面，风格：现代简洁，暗色主题，数据密度适中
```

内置 50 种风格 + 21 种调色板，会给出多方案供选。

---

#### Step 5 — 补全测试：`/test`

```
/test 为整个项目生成测试套件：单元测试（Jest）+ E2E 测试（Playwright），覆盖核心用户流程
```

---

#### Step 6 — 本地跑起来：`/preview`

```
/preview
```

---

#### Step 7 — 部署：`/deploy`

```
/deploy 部署到 Vercel，连接 Supabase PostgreSQL，配置环境变量
```

---

### 新项目标准 Prompt 模板（复制即用）

```
# Step 1
/brainstorm [一句话描述你的产品想法]

# Step 1.5（brainstorm 确认后）
Use the product-manager to generate docs/PRD.md based on the above brainstorm，
包含：产品定位、User Story、数据模型草图、API 草图、MVP 边界（哪些不做）
→ 审核 PRD，确认无误后继续

# Step 2
/plan 请严格遵循 docs/PRD.md 中的要求和 MVP 边界，
生成包含前端/后端/数据库三层的全栈开发计划

# Step 3（Step 2 确认后）
/orchestrate 请所有 Agent 在写代码前先读取 docs/PRD.md，
然后搭建完整项目骨架：
- 前端：[框架 + UI 库]
- 后端：[语言/框架 + API 风格]
- 数据库：[ORM + 数据库]，设计 [核心表名] 等表结构
- 认证：[认证方案]

# Step 4
/test 生成覆盖核心流程的测试套件

# Step 5
/deploy 部署到 [平台]
```

---

## 场景二：已有项目完善

> 项目已在运行，需要加功能、修 Bug、做优化。

### 加新功能（全栈功能，每次都用 `/orchestrate`）

全栈项目里，几乎所有功能都会同时动到三层，直接用 `/orchestrate` 就对了：

```
/orchestrate 添加消息通知系统：
- 数据库：notifications 表（type, user_id, content, read_at, created_at）
- 后端：发送通知 API + 标记已读 API + 通知偏好设置
- 前端：顶部导航栏通知图标 + 小红点 + 通知下拉面板
- 测试：通知触发、未读数更新、标记已读的 E2E 测试
```

```
/orchestrate 接入 Stripe 订阅支付：
- 数据库：subscriptions 表 + 价格方案表
- 后端：Stripe webhook 处理 + 订阅状态同步 + 权限守卫
- 前端：定价页面 + 付款流程 + 当前套餐展示 + 升级/降级 UI
```

```
/orchestrate 添加团队协作功能：
- 数据库：teams 表 + team_members 表（含角色字段）+ 操作日志表
- 后端：邀请邮件发送 + 角色权限中间件 + 操作审计 API
- 前端：团队设置页 + 成员管理 + 权限角色选择器
- 安全：Use the security-auditor to review all permission logic
```

**唯一不需要 `/orchestrate` 的情况：** 纯 UI/样式调整，不涉及数据或逻辑变更。

```
# 这种直接描述就行
把用户列表页的表格改成卡片式布局，加上头像和状态标签
```

```
# 或用 /ui-ux-pro-max
/ui-ux-pro-max 重新设计设置页面，更简洁，分组更清晰
```

---

### 修 Bug：`/debug`

```
/debug 用户登录后跳转 /dashboard 出现 500 错误，日志如下：
[粘贴完整错误堆栈]
```

Kit 加载 `systematic-debugging` Skill，执行：根因分析 → 定位代码 → 给出修复 → 建议验证步骤。

**给的信息越完整效果越好**，理想信息包括：错误日志、复现步骤、相关代码片段、最近一次改动了什么。

---

### 性能问题

```
产品列表页 LCP 超过 4 秒，需要优化，目标 LCP < 2.5s
```

或指定 Agent：

```
Use the performance-optimizer to analyze and fix the slow loading of the product listing page
```

---

### 安全审查（每次加涉及权限的功能后必做）

```
Use the security-auditor to review the new subscription and permission system for security vulnerabilities
```

---

### 检查项目整体健康状态

```
/status 请阅读 docs/PRD.md 并告诉我当前开发进度、已完成功能和待完成功能
```

这个用法在**跨会话回到项目**时特别有用——AI 上下文已清空，但 PRD 文件还在，一句话就能恢复全部项目记忆。

---

### 已有项目 Prompt 快速参考表

| 需求类型      | 命令                                      | 备注                      |
| --------- | --------------------------------------- | ----------------------- |
| 加任何全栈功能   | `/orchestrate [功能描述，说清三层]`              | **默认选项**                |
| 纯 UI 样式调整 | 直接描述 或 `/ui-ux-pro-max`                 | 唯一不用 `/orchestrate` 的情况 |
| Bug 修复    | `/debug [描述 + 日志]`                      | 附上完整错误信息                |
| 代码质量改善    | `/enhance [模块描述]`                       | 不改功能，只改代码质量             |
| 加测试覆盖     | `/test [要测试的功能/模块]`                     | 功能完成后必做                 |
| 性能优化      | 描述问题 或 `Use the performance-optimizer`  |                         |
| 安全审查      | `Use the security-auditor to review...` | 涉及权限/认证的功能完成后必做         |
| 写 API 文档  | `Use the documentation-writer to...`    |                         |
| 查项目现状     | `/status`                               |                         |

---

## 场景三：重构项目

> 老项目技术债严重，或需要系统性架构改造。

**重构铁律：** 分析 → 计划 → 分阶段执行 → 持续验证。每个阶段结束后系统必须仍可运行，不允许出现"重构到一半无法启动"的状态。

---

#### Step 1 — 读懂现有代码库

先让专门的 Agent 探索，不要自己猜：

```
Use the code-archaeologist to analyze the entire codebase and output:
1. 整体架构图（前端/后端/数据库的关系）
2. 技术债清单（按严重程度排序）
3. 高风险区域（改动容易出问题的模块）
4. 推荐的重构优先级
```

或者侧重理解结构：

```
Use the explorer-agent to map the codebase structure and identify all data flows between frontend, backend, and database
```

---

#### Step 2 — 讨论重构策略

把 Step 1 的分析结果直接喂进去：

```
/brainstorm 当前代码库分析结果如下：[粘贴 Step 1 输出]
讨论最优重构策略，要求：风险可控、可分阶段交付、不中断线上服务
```

---

#### Step 3 — 制定重构计划

```
/plan 基于以上讨论，制定详细的全栈重构计划：
- 每个阶段的具体范围（前端/后端/数据库分别做什么）
- 阶段之间的依赖关系
- 每阶段完成的验收标准
- 数据迁移方案（如有）
```

---

#### Step 4 — 分阶段执行

**架构级重构（涉及多层）：`/orchestrate`**

```
/orchestrate 执行重构计划第一阶段：将 Express 路由层重构为分层架构（Controller → Service → Repository）：
- 后端：抽离业务逻辑到 Service 层，数据访问到 Repository 层
- 数据库：对应调整 Prisma query 的封装方式
- 前端：更新 API 调用地址（如有变动）
- 要求：保持所有现有接口的输入输出不变
```

**纯代码质量改善（不改架构）：`/enhance`**

```
/enhance 重构 src/services/ 目录：消除重复代码，提取公共工具函数，补充 TypeScript 类型，遵循 clean-code 原则
```

**数据库结构改造：**

```
/orchestrate 重构数据库层以支持多租户：
- 数据库：为所有核心表添加 tenant_id，设计行级隔离策略，编写迁移脚本
- 后端：在所有查询中注入 tenant_id 过滤，更新权限中间件
- 前端：更新请求头携带租户标识
```

---

#### Step 5 — 每阶段结束后补测试

```
/test 为刚完成重构的 [模块名] 生成完整测试，覆盖所有原有功能点，确保无回归
```

---

#### Step 6 — 验证

```bash
# 开发阶段快速验证
python .agent/scripts/checklist.py .

# 部署前完整验证
python .agent/scripts/verify_all.py .
```

---

#### Step 7 — 性能和安全复查

```
Use the performance-optimizer to verify no performance regression after the refactoring
Use the security-auditor to ensure no security vulnerabilities were introduced
```

---

### 重构 Prompt 快速参考表

| 阶段         | 命令                                                           |
| ---------- | ------------------------------------------------------------ |
| 读懂老代码      | `Use the code-archaeologist to analyze...`                   |
| 摸清代码结构     | `Use the explorer-agent to map...`                           |
| 讨论策略       | `/brainstorm [粘贴分析结果]`                                       |
| 制定计划       | `/plan 制定全栈重构计划，含每阶段范围和验收标准`                                 |
| 架构重构（多层联动） | `/orchestrate [本阶段范围，三层分别说清楚]`                               |
| 纯代码质量改善    | `/enhance [模块描述]`                                            |
| 阶段验收测试     | `/test 覆盖 [模块名] 所有原有功能点`                                     |
| 完整验证       | `python .agent/scripts/verify_all.py .`                      |
| 上线前复查      | `Use the security-auditor` + `Use the performance-optimizer` |

---

## 全栈场景 Prompt 写法指南

### 核心原则：把三层说清楚

`/orchestrate` 的效果取决于你的描述质量。描述全栈功能时，养成**同时说清前端/后端/数据库**各自要做什么的习惯：

**❌ 模糊写法：**

```
/orchestrate 添加评论功能
```

**✅ 清晰写法：**

```
/orchestrate 添加文章评论功能：
- 数据库：comments 表（id, article_id, user_id, content, parent_id, created_at），支持嵌套回复
- 后端：发布评论 API + 获取评论列表 API（分页）+ 删除评论 API（仅限本人）
- 前端：文章底部评论区组件、评论输入框、嵌套展示、点击回复
- 测试：评论发布、嵌套回复、权限控制（非本人无法删除）
```

---

### 不知道技术细节怎么写？先跑 `/plan`

如果你对某层不熟悉（比如不清楚数据库表应该怎么设计），先用 `/plan` 让系统帮你想：

```
/plan 为评论功能制定详细实现方案，包含前端/后端/数据库的完整设计
```

确认 `/plan` 的输出后，再把它的结论作为 `/orchestrate` 的输入。

---

### 安全和测试不要忘记显式要求

Kit 会自动做基本检查，但在以下情况下最好显式要求：

```
# 涉及权限的功能，在 /orchestrate 末尾加一行
- 安全：Use the security-auditor to review all permission checks

# 上线前
/test 生成完整测试覆盖，包含权限边界、异常路径、数据完整性验证
```

---

## Agents 速查手册

**绝大多数时候不需要手动指定 Agent**，系统自动路由。以下情况手动指定效果更好：需要某个特定专家视角（如专门要安全审计视角）、系统路由不准（出现在描述模糊的请求上）。

**手动指定语法：**

```
Use the [agent-name] to [描述任务]
```

### 全部 20 个 Agent

| Agent                    | 最适合的场景                                 |
| ------------------------ | -------------------------------------- |
| `orchestrator`           | 复杂多步全栈任务的调度协同                          |
| `project-planner`        | 需求分析、任务拆解、项目规划                         |
| `frontend-specialist`    | React / Next.js / Vue UI 开发            |
| `backend-specialist`     | API 设计、业务逻辑、Node.js / Python 后端        |
| `database-architect`     | Schema 设计、SQL 优化、迁移方案                  |
| `mobile-developer`       | iOS / Android / React Native / Flutter |
| `game-developer`         | 游戏逻辑、物理引擎、游戏机制                         |
| `devops-engineer`        | CI/CD、Docker、部署流水线                     |
| `security-auditor`       | 安全合规审查、OWASP 检查                        |
| `penetration-tester`     | 渗透测试、漏洞挖掘                              |
| `test-engineer`          | 测试策略、Jest / Vitest / Playwright        |
| `debugger`               | 根因分析、系统性 Debug                         |
| `performance-optimizer`  | 速度优化、Web Vitals 提升                     |
| `seo-specialist`         | SEO、Core Web Vitals、E-E-A-T            |
| `documentation-writer`   | API 文档、用户手册、README                     |
| `product-manager`        | 需求文档、用户故事、PRD                          |
| `product-owner`          | 产品策略、Backlog 管理、MVP 定义                 |
| `qa-automation-engineer` | E2E 测试、CI 测试流水线                        |
| `code-archaeologist`     | 遗留代码分析、技术债挖掘                           |
| `explorer-agent`         | 代码库结构探索和理解                             |

---

## Workflows 速查手册

### 全部 11 个 Workflow

| 命令                 | 作用                | 全栈场景下的典型用法        |
| ------------------ | ----------------- | ----------------- |
| `/brainstorm`      | 苏格拉底式需求探索         | 需求模糊时的第一步         |
| `/plan`            | 任务拆解和技术方案         | 执行前的设计环节          |
| **`/orchestrate`** | **多 Agent 全栈执行**  | **执行任何全栈功能的默认命令** |
| `/enhance`         | 改善现有代码质量          | 纯技术债清理，不改功能       |
| `/ui-ux-pro-max`   | 高质量 UI 设计（50 种风格） | 纯视觉/交互调整          |
| `/debug`           | 系统性 Bug 排查        | 出现问题时             |
| `/test`            | 生成测试套件            | 功能完成后必做           |
| `/preview`         | 本地预览              | 上线前确认             |
| `/status`          | 项目健康状态检查          | 阶段性回顾             |
| `/deploy`          | 部署到生产环境           | 上线                |
| `/create`          | 创建单一模块/组件         | 仅纯前端组件或纯后端脚本等单层内容 |

**关于 `/create`：** 在全栈项目里，`/create` 的使用场景非常有限——仅当你需要生成一个确实不涉及其他层的孤立内容时（如一个纯展示的 UI 组件、一个独立的数据导出脚本）。只要触碰两层及以上，就用 `/orchestrate`。

---

## 常见问题 & 避坑指南

### ❌ Slash 命令不出现在建议下拉框

**根因：** `.agent/` 被加入了 `.gitignore`，Antigravity IDE 无法索引。

```bash
# 从 .gitignore 中移除 .agent/ 这一行
# 改为加入本地排除（不影响其他协作者）
echo ".agent/" >> .git/info/exclude
```

---

### ❌ `/orchestrate` 生成的代码前后端接口对不上

**根因：** Prompt 里没有说清楚接口契约。

**解决：** 在 Prompt 里明确说明期望的接口格式，或先跑 `/plan` 让系统设计接口，确认后再执行。

```
/orchestrate 实现评论功能，API 格式统一为 tRPC procedures：
- createComment(input: {articleId, content, parentId?})
- listComments(input: {articleId, page, limit})
- deleteComment(input: {commentId})
```

---

### ❌ 重构到一半项目跑不起来

**根因：** 重构步子太大，多个模块同时改动造成临时不一致。

**解决：** 把每次 `/orchestrate` 的范围缩小，确保每个请求完成后系统仍可运行：

```
# ❌ 一次重构太多
/orchestrate 把整个项目从 REST 迁移到 tRPC

# ✅ 分模块迁移
/orchestrate 把 user 相关的 3 个接口从 REST 迁移到 tRPC，其他接口保持不变
```

---

### ❌ 想在 CI/CD 中自动验证

```yaml
- name: Validate with ag-kit
  run: python .agent/scripts/verify_all.py .
```

---

### ❌ 想加自定义 Workflow

在 `.agent/workflows/` 下新建 Markdown 文件：

```markdown
---
description: 全量验证并部署到 staging
---

# Deploy to Staging

1. 运行全套测试
2. 运行 verify_all.py
3. Build 生产包
4. 部署到 staging 环境
5. 运行 smoke test
6. 通知团队
```

保存为 `.agent/workflows/deploy-staging.md`，之后用 `/deploy-staging` 触发。

---

## 心法三条

**先想清楚，再动手，先立契约。** 用 `/brainstorm` 对齐想法，用 `docs/PRD.md` 把共识固化成文件，用 `/plan` 分解任务，然后再执行。PRD 不是文档，是多 Agent 的宪法，也是你跨会话的项目记忆。

**描述结果，不是步骤。** 告诉 Kit "我要一个带分页的评论系统，含嵌套回复"，而不是"先建表，再写 API，再做组件"——让 `/orchestrate` 去编排顺序，你只需要描述终态。

**小步走，每步可验证。** 不要一次 `/orchestrate` 整个大功能，按业务边界拆分成小块逐一交付。每块完成后跑测试确认，出问题定位容易，回滚也方便。