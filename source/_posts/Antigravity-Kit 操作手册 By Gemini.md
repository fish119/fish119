title: Antigravity-kit 操作手册 By Gemini
tags:
  - Vibe Coding
  - Antigravity
categories:
  - Vibe Coding
  - Antigravity
date: 2026-03-15 21:19:00
---

最近在使用 Google Antigravity 进行 Vibe Coding，看到了一个有意思的工具：[antigravity-kit](https://github.com/vudovn/antigravity-kit) ，于是分别利用 Claude 和 Gemini 制作了操作手册，这一份是 Gemini 的

<!-- more -->

# 🚀 Antigravity-Kit 操作手册 By Gemini

**理念强调**：在全栈 Vibecoding 中，你的角色是 **CTO + 产品经理**。不要教 AI 怎么写 SQL，也不要教 AI 怎么写 React 组件，你只需要定义业务流，让 `/orchestrate` 去调度后端的 `@database-expert` 和前端的 `@frontend-specialist`。

## 🛠️ 阶段零：环境武装（全栈基建）

**1. 初始化 Kit（注入全栈专家库）**
在项目根目录终端执行：

```bash
npx @vudovn/ag-kit init
```

**2. 核心避坑：激活本地快捷指令**
千万别把 `.agent` 提交到远程，但必须让本地 IDE 能读取到：

```bash
echo ".agent/" >> .git/info/exclude
```

---

## 🟢 场景一：新建全栈项目（从 0 到 1 的“航母下水”）

**目标**：一次性拉起包含数据库设计、后端 API、前端 UI 的完整 SaaS 级项目。

### Step 1: 架构定调与发散

先不要写代码，先敲定全栈技术栈：

> `/brainstorm 我要建一个类似 Trello 的看板任务管理系统。技术栈要求：前端 Next.js + Tailwind，后端 Node.js，数据库 PostgreSQL + Prisma。请帮我梳理出核心的数据模型和前后端交互逻辑。`

### Step 2: 蓝图生成（全栈任务拆解）

让 AI 生成带有严格依赖关系的任务清单：

> `/plan 根据刚才的讨论，生成第一阶段的全栈开发蓝图，必须按照“数据库模型 -> 后端接口 -> 前端联调”的顺序拆解。`

### Step 3: 🌟 召唤多智能体统筹执行（核心步骤）

**绝对不要用 `/create`，直接上 `/orchestrate`！**

> `/orchestrate 执行 Plan 的第一阶段（用户鉴权与基础看板模块）。请协同 @database-expert 生成 Prisma Schema，交由 @backend-specialist 编写增删改查 API，最后安排 @frontend-specialist 完成页面搭建和数据对接。`

**AI 动作**：后台会开启多线程工作。你会看到它先写 `schema.prisma` 并提示执行 migration，然后生成后端的路由文件，最后生成前端包含 `fetch/axios` 调用的组件。一条龙跑通！

### Step 4: 全栈联调验证

> `/test 针对刚生成的看板 CRUD 接口和前端交互，编写并执行一个端到端（E2E）测试用例。`

---

## 🟡 场景二：已有项目完善（全栈特性的“垂直打通”）

**目标**：在复杂的老项目里加新功能（如：增加一套“支付与订阅”系统），确保前后端数据流不脱节。

### Step 1: 现状扫描（防止新代码破坏老架构）

> `/status 检查当前项目中与 User 相关的数据库表结构和全局状态管理方案，为接入支付系统做准备。`

### Step 2: 🌟 垂直打通全链路开发

老规矩，通过统筹命令一波带走：

> `/orchestrate 我们要增加“月度会员订阅”功能。
> 
> 1. 请设计 `Subscription` 数据库表；
> 2. 编写 Stripe webhook 接收回调的后端接口（注意调用 @security-auditor 确保验签安全）；
> 3. 在前端开发 Pricing 价格页，并对接支付按钮。`

**AI 动作**：此时系统会自动调动安全、数据库、前后端四位专家。如果后端接口字段有变更，它会自动同步修改前端的 TypeScript Type 声明，**绝不出现“后端改了，前端全红”的低级错误**。

### Step 3: UI 降维打击（前端外包化）

如果功能跑通了但界面太丑：

> `/ui-ux-pro-max 刚才的 Pricing 页面逻辑没问题，但太简陋了。请用现代 SaaS 的毛玻璃（Glassmorphism）风格重新设计它，增加动画效果。`

---

## 🔴 场景三：重构项目（“给飞行中的飞机换引擎”）

**目标**：底层架构大换血（例如数据库迁移、重写核心 API），保证业务不断。

### Step 1: 生成兜底防御（重构生命线）

无测试不重构，全栈重构更是如此：

> `/test 请梳理当前项目中订单模块的所有前后端交互，并生成一套完整的回归测试脚本。`

### Step 2: 🌟 架构级大换血

假设你要把杂乱的 RESTful API 重构成 GraphQL，或者把老旧的 MySQL 换成 PostgreSQL：

> `/orchestrate 我们要进行全量数据库迁移：从本地 SQLite 迁移到云端 PostgreSQL。
> 请先更新 ORM 配置，然后排查所有与方言（Dialect）相关的 SQL 报错，并联动测试组件，确保所有 CRUD 接口在新数据库下均能跑通。`

### Step 3: 性能与安全收尾

> `/orchestrate 安排 @performance-expert 和 @security-auditor 对刚才重构的模块进行双重审查，找出 N+1 查询问题和可能的 SQL 注入漏洞，并直接修复。`

---

## 👑 Vibecoding 全栈指挥官的 4 条铁律

1. **永远基于领域模型（Domain Model）思考**：当你用 `/orchestrate` 时，先交代清楚数据（数据库长什么样），AI 就能顺藤摸瓜把接口和界面完美写出来。
2. **警惕“孤岛”指令**：在全栈项目里，尽量少用 `@frontend-specialist 请帮我改一下 API 请求`。直接用 `/orchestrate`，让它自己去核对前后端接口是否匹配。
3. **遇到诡异 Bug 的终极解法**：如果联调失败，不要自己翻 Network，直接输入 `/debug 登录接口报 500 错误，请跨栈分析（从前端请求头 -> 后端中间件 -> 数据库日志）并修复。`
4. **信任并等待**：`/orchestrate` 的执行时间往往比单一命令长，因为它在后台做 Context 共享和互相 Review。去喝杯咖啡，回来时往往就是一个 `git commit` Ready 的状态。
