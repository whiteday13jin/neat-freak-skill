---
name: neat-freak
description: Sync project docs and agent memory with the current codebase at the end of a development milestone. 会话收尾时同步项目文档、交接信息与代码事实。
---

# 洁癖 — Knowledge Base Neat-Freak

> **Cross-platform Agent Skill** — Claude Code · OpenAI Codex · OpenCode · OpenClaw 通用。
> 跨平台 SKILL.md，遵循开放 Agent Skill 规范。

## 模式选择

默认使用 **light mode**，除非用户明确要求完整收尾、跨项目同步、全量审查或交接准备。

## 触发提示

显式触发示例：

- `/neat`
- `/sync`
- `sync docs`
- `sync project memory`
- `update project docs`
- `prepare handoff`
- `同步文档`
- `同步项目记忆`
- `更新项目文档`
- `收尾同步`
- `整理项目文档`
- `准备交接`
- `这个阶段做完了，同步一下`
- `让新人能接手`

不要因为这类泛化请求就触发：

- `整理一下`
- `梳理一下`
- `总结一下`
- `帮我理清思路`
- `tidy this up`

只有当用户明确要修改 `README.md`、`AGENTS.md`、`CLAUDE.md`、`docs/`、handoff notes 或 agent memory 时，才把这些泛化表述视作本 skill 的触发信号。

### dry-run mode：只审查，不修改

触发示例：
- `/neat --dry-run`
- `先审查一下哪些文档需要同步，不要改文件`
- `只列同步计划`

行为：
- 可以读取 Git 状态和 diff 摘要
- 读取必要文件
- 判断哪些文档 / 记忆需要更新
- 输出建议修改清单
- **不得 Edit / Write / Delete**
- 不得创建新文件
- 不得修改 agent memory

### light mode：默认轻量收尾

触发示例：
- `/neat`
- `/sync`
- `这个阶段做完了，同步一下`
- `更新项目文档和记忆`

行为：
- 先做 Git 安全检查
- 优先同步 `README.md`、`CLAUDE.md` / `AGENTS.md`
- 只读取和本次改动直接相关的 `docs/`
- 只更新确定已经稳定的项目事实
- 默认不修改全局 agent memory；只有项目事实长期有效、且不适合放入项目根 markdown 时才更新 memory
- 不做全仓库文档重构
- 不清理历史文档，除非明显过期且和本次改动直接相关

### full mode：完整收尾 / 交接模式

触发示例：
- `/neat --full`
- `完整收尾`
- `准备交接`
- `让新人能直接上手`
- `全量同步项目文档`
- `跨项目文档一起对齐`

行为：
- 执行完整盘点
- 读取所有项目根 markdown 与 `docs/*.md`
- 按 `references/sync-matrix.md` 检查所有受影响文档
- 检查跨项目影响
- 清理过期、重复、冲突、相对时间
- 输出完整同步摘要

你是一个**知识库编辑**，不是记录员。记录员只会往后追加，编辑会审查全局、合并重复、修正过期、删除废弃。你的工作是让整个项目的知识体系始终保持**干净、准确、对新人友好**的状态——像有洁癖一样。

## 为什么这件事重要

在 AI 协作开发中，代码可以随时重写，但**文档和记忆是跨会话、跨 Agent 的唯一桥梁**。如果记忆里有过期信息，下一个 Agent（无论它是 Claude、Codex 还是别的）会基于错误前提做决策。如果 docs/ 混乱或缺失，接手者（尤其是下游项目的同事）会浪费大量时间搞清楚这套系统怎么用。

这个 Skill 的价值就在于：**让知识体系的每一层都跟得上代码的变化。**

## 关键概念：三类知识，三种受众

**必须先理解这件事，否则你会只改 CLAUDE.md 就结束，把下游同事和其他 agent 晾在那儿。**

| 位置 | 受众 | 职责 | 不同步的代价 |
|------|------|------|--------------|
| **Agent 记忆系统**（若 agent 支持） | Agent 自己跨会话复用 | 个人偏好、非显而易见的项目事实、跨项目 reference | 下次会话 Agent 忘记历史决策 |
| 项目根 `CLAUDE.md` / `AGENTS.md` | 当前项目里的 AI（下次会话自己） | 项目约定、结构、红线、环境变量、路由清单 | 下次 AI 在这个项目里走弯路 |
| 项目 `docs/` + `README.md` | **其他人**（人类同事、下游开发者、未来接手的 AI） | 接入指南、架构图、运维手册、交接说明、API 参考 | **其他人或系统无法正确接入或运维** |

这三层**受众不同，职责不重叠**。CLAUDE.md 里写"新增了 device flow 五个路由" ≠ docs/integration-guide.md 里"下游怎么接这套 flow" —— 前者是提醒自己，后者是教别人。**两份都要写。**

## 学习笔记边界

不要把通用学习笔记、教程解释、用户的个人理解过程写进项目文档。

以下内容默认不进入 `README.md` / `docs/` / `CLAUDE.md` / `AGENTS.md`：
- 某个框架的通用概念解释
- 用户为了学习而提出的问题与回答
- 与项目运行无关的编程基础知识
- 临时调试过程中的思维碎片
- 尚未稳定采用的方案草稿
- 用户个人学习复盘

只有当学习内容已经转化为**项目事实**时，才允许写入文档，例如：
- 本项目为什么选择某个架构
- 本项目某个模块应该如何运行
- 本项目某个 API / 环境变量 / 数据表 / 工作流已经存在
- 本项目的踩坑结论会影响后续开发或运维
- 新人接手项目必须知道的约定

一句话：项目文档记录“这个项目现在是什么”，不是记录“用户这轮学到了什么”。

> **Agent 记忆系统的具体位置因平台而异**（Claude Code 在 `~/.claude/projects/<...>/memory/`，Codex 用 `AGENTS.md`，OpenCode 用 `.opencode/`，OpenClaw 用 `~/.openclaw/`）。完整路径速查见 [references/agent-paths.md](references/agent-paths.md)。如果当前 agent 没有独立的记忆系统，直接跳过这一层，把功夫全花在 docs 和项目根 markdown 上。

## 执行流程

### 第零步：Git 安全检查（修改前必须执行）

在修改任何文件之前，必须先确认工作区状态。

必须执行：

```bash
git status --short
git diff --stat
```

### 第一步：盘点现状

**先做 ls，再做判断。盘点范围取决于当前模式。**

#### light mode 盘点范围

默认只盘点：

1. 项目根目录：
   - `README.md`
   - `CLAUDE.md`
   - `AGENTS.md`
   - `AGENTS.override.md`
   - `.env.example`
   - `package.json` / `pyproject.toml` / `Makefile` / `docker-compose.yml` 等运行入口文件

2. 与本次 Git diff 直接相关的 docs：
   - 文件名与改动模块、API、功能名相关
   - `docs/index.md`
   - `docs/README.md`
   - `docs/architecture.md`
   - `docs/integration-guide.md`
   - `docs/operator-runbook.md`
   - `docs/handoff.md`

3. agent 记忆：
   - 只在当前平台明确存在记忆系统时读取
   - 如果路径不存在，不要创建全局记忆目录
   - 如果平台没有独立记忆机制，把项目事实同步到项目根 `AGENTS.md` / `CLAUDE.md`

#### full mode 盘点范围

完整执行以下步骤：

1. 列出 agent 的记忆文件（如有）：
   - Claude Code：`ls ~/.claude/projects/<...>/memory/` 并读 `MEMORY.md` 及所有被引用的 `.md`
   - Codex / OpenCode / 其他：找该 agent 的等价位置（见 references/agent-paths.md）
2. 对本次对话涉及的**每一个项目**：
   - `ls <project-root>/` → 确认根目录结构
   - `ls <project-root>/docs/ 2>/dev/null` → **枚举所有 docs**（缺失也要确认）
   - `find <project-root> -maxdepth 2 -name "*.md" -not -path "*/node_modules/*" -not -path "*/.git/*"` → 兜底抓散落的 .md
   - 读 `README.md`、`CLAUDE.md` / `AGENTS.md`、每一个 `docs/*.md`
3. 读全局 agent 配置（若有，如 `~/.claude/CLAUDE.md`、`~/.codex/AGENTS.md`）
4. 回顾本次对话全部内容

**输出一张文件清单**（内部用，不用给用户看），对每个文件标：「评估过 / 要改 / 不用改」。**漏一个不行**——这是这个 skill 最容易翻车的地方。

### 第二步：识别变更——用"变更影响矩阵"思考

**不要只看对话增量有什么新事实，要看新事实会波及哪些文档层级。**

常见模式速览：
- 新增 API / 路由 → CLAUDE.md 路由清单 + integration-guide + architecture 的 Routes
- 新增 / 改名 环境变量 → CLAUDE.md 环境变量表 + runbook + 下游 integration-guide
- 新增数据库表 → CLAUDE.md + architecture 的 Data Model
- 新增大特性（跨多文件） → 以上全部 + architecture 新章节 + handoff 已完成清单
- 跨项目改动 → 上下游两边的 docs **都要对齐**（最常见的漏改场景）
- 记忆层面：相对时间→绝对日期、过期事实→改、重复→合并、已完成待办→删

完整映射表（覆盖更多变更类型与对应文档）见 **[references/sync-matrix.md](references/sync-matrix.md)**——遇到不确定的改动先查这张表。

**关键检查**：这次对话是不是**跨项目**的？如果改了项目 A 且项目 B 依赖它（通过 SDK、API、子域、环境变量），**项目 B 的 docs 也要改**。这是历次同步最常翻的车。

### 第三步：实际修改（用工具，不只是描述）

#### 修改范围控制

在 light mode 下：
- 只修改与本次改动直接相关的文档
- 优先更新已有条目，不新增大段文档
- 不重构整个 docs 体系
- 不删除历史文件，除非它明显废弃且和本次改动直接相关
- 不修改全局 agent 配置，除非用户明确要求

在 full mode 下：
- 可以系统性整理 docs 结构
- 可以清理重复、过期、冲突内容
- 可以创建缺失的 handoff / architecture / runbook 文档
- 可以根据 sync-matrix 做跨文档补齐

在 dry-run mode 下：
- 不允许修改任何文件
- 只输出计划和风险点

你必须**真的用 Edit 修改现有文件、用 Write 创建新文件、用删除命令清理废弃文件**。"我会怎么改"的描述不算完成。

**顺序建议**：先改 docs/（改错影响外部）→ 再改 CLAUDE.md/AGENTS.md → 最后理记忆。先动外部优先级最高的，即使中途被打断，读者看到的也是对齐的最新状态。

**编辑原则**：

- **合并优于追加**：新信息是对旧信息的更新，改旧条目，不要再加一条
- **删除优于保留**：完成的临时计划、推翻的决策、过期的上下文，删掉
- **精确优于冗长**：一条记忆说清楚一件事，别塞三件
- **绝对时间**：永远 `2026-04-29`，不写"今天"、"最近"
- **面向读者**：docs/ 的读者是"第一次接触这个项目的外部人"，写的时候想象对方只有 5 分钟能看完
- **受众不混**：CLAUDE.md 里不抄 docs/ 的全文，docs/ 里不写"我记得上次……"——这是记忆的事

**全局配置极度克制**：

`~/.claude/CLAUDE.md` / `~/.codex/AGENTS.md` / OpenCode / OpenClaw 的全局配置，只有在用户明确表达了**跨项目长期有效的核心原则**时才允许修改。日常项目细节绝不进全局。

平台路径保守原则：
- 如果平台记忆路径不存在，不要擅自创建全局记忆系统
- 如果当前 agent 不支持独立 memory，直接跳过 memory 层
- 如果不确定当前平台的真实路径，优先同步项目根 `AGENTS.md` / `CLAUDE.md`
- 不要为了“跨平台完整性”创建一堆当前项目不会使用的配置文件
- 项目内同时存在 `CLAUDE.md` 和 `AGENTS.md` 时，优先保持二者一致；如果内容重复太多，可以让其中一个指向另一个

**docs/ 编辑要点**——新增一个能力的文档变更通常要四处都补：
1. **integration-guide** 或对应"外部视角"文档：加**怎么用**（curl / SDK 示例 / 错误码表）
2. **architecture**：加**怎么工作**（数据流、状态机、设计取舍）
3. **runbook**：加**怎么运维**（冒烟命令、故障排查、环境变量）
4. **handoff** 或 CHANGELOG：加**已完成**

#### 文档真实性校验

写入文档前必须确认事实来自代码、配置或本次已完成改动，不得根据“看起来应该有”来补文档。

必须检查：
- README 中的运行命令是否真的存在于 `package.json` / `pyproject.toml` / `Makefile` / `docker-compose.yml` 等入口文件
- 文档提到的 API 路由是否真的能在代码中找到定义
- 文档提到的环境变量是否真的在代码、`.env.example`、配置文件或部署脚本中使用
- 文档提到的数据库表 / 字段是否真的存在于 schema、migration 或 ORM model 中
- 文档提到的脚本路径、文件路径、模块名是否真实存在

如果无法确认：
- 不要把它写成已存在事实
- 可以写入「待确认」或放到最终摘要的「未处理」

API 速查表、环境变量表、术语表是高频查询的结构化信息，**必须保持"所见即最新"**。

### 第四步：自检清单（必须逐项过一遍）

这一步防止"漏改 docs"。改完后逐条检查：

- [ ] 第一步列出的每个文件，都判断了"不用改"或"已改"
- [ ] 记忆索引（若有）里的每个链接指向存在的文件
- [ ] 每个记忆文件的 description 和内容对得上
- [ ] 记忆之间没有互相矛盾
- [ ] CLAUDE.md / AGENTS.md 里提到的路径 / 命令 / 工具 / 环境变量在代码中真实存在
- [ ] README 的安装 / 运行步骤跟代码一致
- [ ] 新增 API 路由：**在 integration-guide 和 architecture 都出现了**
- [ ] 新增环境变量：**在 runbook 和项目根 markdown 都出现了**
- [ ] 新增数据库表：**在 architecture 的 Data Model 和项目根 markdown 都出现了**
- [ ] 跨项目影响：下游项目的 docs 也跟着改了
- [ ] 没有相对时间遗留（`grep -E "今天|昨天|刚刚|最近|上周|today|yesterday|recently"` 清零）
- [ ] 修改前和修改后的 `git status --short` 已检查
- [ ] 修改后的 `git diff --stat` 已输出
- [ ] 没有覆盖用户原本未提交、且与本次同步无关的改动
- [ ] README 中的运行命令在项目配置中真实存在
- [ ] 文档提到的 API 路由在代码中真实存在
- [ ] 文档提到的环境变量在代码或示例配置中真实存在
- [ ] 没有把通用学习笔记写进项目文档
- [ ] dry-run mode 下没有修改任何文件
- [ ] light mode 下没有做 full mode 才该做的全量重构

哪条打不了勾，**回去补**。不要因为"差不多了"就跳过这一步——这是这个 skill 的灵魂。

### 第五步：变更摘要

在所有文件修改完之后（不是之前），给用户简洁摘要。

普通同步摘要：

```md
## 同步完成

### 模式
- 本次模式：dry-run / light / full
- 是否修改文件：是 / 否

### Git 状态
- 修改前：简述 `git status --short`
- 修改后：简述 `git status --short`
- Diff 摘要：简述 `git diff --stat`

### 记忆变更
- 更新：xxx（原因）
- 新增：xxx
- 删除：xxx（原因）

### 文档变更（按项目分组，每个项目列全改动的文件）
- <项目 A>/CLAUDE.md — xxx
- <项目 A>/AGENTS.md — xxx
- <项目 A>/README.md — xxx
- <项目 A>/docs/integration-guide.md — xxx
- <项目 A>/docs/architecture.md — xxx

### 验证
- 已确认命令 / 路由 / 环境变量 / 路径真实存在：xxx
- 未能确认但已列为待处理：xxx

### 未处理
- xxx（为什么没处理，比如需要用户确认、当前不是 Git 仓库、路径不存在、事实无法验证）
```

如果是 dry-run，使用：

```md
## 审查完成，未修改文件

### 建议同步
- xxx

### 风险点
- xxx

### 建议下一步
- xxx
```

## 特殊情况

**项目还没有 README 或 CLAUDE.md/AGENTS.md**：判断项目是不是到了"有可运行代码"的阶段。是 → 创建。还在 vibe 阶段 → 跳过，但在摘要里提一句。

**对话没有产生新事实**：审查现有记忆和文档有没有过期 / 冲突 / 相对时间——审查本身就有价值。

**记忆之间出现无法自动判断的矛盾**：列在「未处理」让用户决定。**这是唯一需要用户介入的情况**，其他都自己拍板。

**跨项目改动**：本次对话改了多个项目，每个项目都要跑一次完整的第一步（ls + 读 docs）。不要假设一个项目的 docs 改了，另一个就不用。尤其是上游-下游对接文档（集成指南 / SDK 说明 / API 协议），两边都要对齐。

**发现之前的同步漏了东西**：修掉。不要说"那不是这次对话的事"——你就是这个项目的持续编辑，过去的漏洞也归你管。

## 参考资料

- **[references/sync-matrix.md](references/sync-matrix.md)** — 完整的"变更类型 → 要改哪些文件"映射表
- **[references/agent-paths.md](references/agent-paths.md)** — Claude Code / Codex / OpenCode 各自的记忆与配置路径速查
