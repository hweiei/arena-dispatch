# Arena 子智能体技能包（skillpack）

> 用途：把**刚抽取并归因完毕的 Arena 会话**武装成可用的子智能体。
> **选型标准（这一版按此重写）**：只收**智能体开箱即用**的东西 —— 一条命令能跑的 CLI、能直连的 MCP server、能被直接读/执行的技能包。
> **库和清单不算**（要写代码集成才能用 ✗）——这类统一降到文末「附录：需要集成才能用」并明确标注。
> 星数为 **2026-10-16 检索读数**，会变；未核到确切数字的会写明「未核到」。

## 一、一条命令就能跑（CLI）

| 工具 | 星 | 一条命令 | 它替你解决什么 |
|---|---|---|---|
| [yamadashy/repomix](https://github.com/yamadashy/repomix) | 未核到 | `npx repomix` | 把整个仓库打成**一个 AI 友好文件**，自带 **token 计数**、尊重 `.gitignore`、能读私有仓（用本地 git）→ **上下文打包** |
| [gitingest](https://gitingest.com) | 未核到 | `gitingest <repo-url>` | 任意 git 仓 → **一页摘要**（结构 / 体积 / token 估算）→ 快速判断"这仓值不值得整个读" |
| [obra/superpowers](https://github.com/obra/superpowers) | **280k+** | 按仓内说明装 | 一套**编排好的技能链**：脑暴 → worktree → 计划 → 子智能体执行 → TDD → 合并前评审 → **直接照做** |
| [antigravity-awesome-skills](https://github.com/) | **22k** | `npx antigravity-awesome-skills --claude` | **1,234+ 个技能**一键装，跨多个编码助手通用（SKILL.md 格式）→ **能力面扩展** |
| [anthropics/skills](https://github.com/anthropics/skills) | 未核到 | `npx skills add anthropics/skills` | 官方 **17 个生产级 Agent Skills**（文档 / 设计 / 前端等）→ 拿来即用 |
| [Forrest Chang 的行为准则 CLAUDE.md](https://github.com/) | **210k+** | 把该文件放进仓库根 | 四条行为原则的单文件（零依赖）→ 先把"怎么干活"钉死 |

> 说明：`awesome-*` 一类**清单**（如 [awesome-claude-skills](https://github.com/) 13k）我们只当**索引**用，不进主表 ✗。

## 二、能直连的 MCP server

| MCP | 星 | 怎么接 | 它替你解决什么 |
|---|---|---|---|
| [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)（官方） | **88k+** | `npx -y @modelcontextprotocol/server-memory` 等 | 官方参考实现：**memory（持久记忆）** / filesystem / git / fetch / time / sequential-thinking → 记忆与基础能力 |
| [upstash/context7](https://github.com/upstash/context7) | **46k** | 见其 README（HTTP 或 npx） | **版本对应的最新库文档**（`resolve-library-id` + `query-docs`）→ **别再往上下文里塞整本文档**（DSH 自己就在用它 ✓） |
| [oraios/serena](https://github.com/oraios/serena) | **30.1k** | `uvx --from git+https://github.com/oraios/serena serena start-mcp-server` | **语义**级代码检索与编辑（按意图找代码，不是字符串匹配）→ 大仓导航 |
| [mcp-memory-keeper](https://github.com/) | 136 | `npx -y mcp-memory-keeper` | 轻量记忆服务器（会话级要点留存）→ 长任务不丢线索 |

## 三、上下文工程三件套（把上面的工具串成方法）

1. **打包而不是全读**：`npx repomix`（或 `gitingest`）先拿到"整仓一张图 + token 账"；
2. **按需取而不是预塞**：库的用法问 **Context7**；代码定位用 **Serena**；
3. **记下来而不是重读**：要点写进 **memory server**（官方 memory / memory-keeper）。

> 三条都指向同一件事：**上下文是预算**。先压缩（打包/摘要）、再外置（文档/MCP）、最后持久化（记忆）。

## 四、选型四条纪律（我们踩出来的）

1. **只收"能直接跑"的**：CLI / MCP / 技能包进主表；**要写代码集成的进附录** ✓。
2. **压缩有损、记忆有偏、文档会过期**：判决性事实回原文核（数字、路径、错误串）。
3. **先摘要再压缩**：把原始日志直接丢给压缩器，丢的往往正是关键那几行。
4. **不引入只能跑在别人云上的依赖**（本仓红线：不引入不可内化的依赖）。

## 附录：需要集成才能用（**不是** agent 直接可用 ✗）

| 项目 | 星 | 为什么放这里 |
|---|---|---|
| [microsoft/LLMLingua](https://github.com/microsoft/llmlingua) | 6.7k | 提示词压缩**库**：要在自己的管线里调用它才生效（不是一条命令） |
| [mem0](https://github.com/mem0ai/mem0) / [Letta](https://github.com/letta-ai/letta) / [Zep](https://github.com/getzep/zep) | ~41k–60k / ~24k / ~28k | 记忆**框架**：要么按它们的 SDK 写，要么经它们的 MCP（`mcp-mem0` 等）间接用 |
| [stanfordnlp/dspy](https://github.com/stanfordnlp/dspy) | 38.6k | 提示词**优化框架**：要写评测与优化循环 |
| [promptfoo](https://github.com/promptfoo/promptfoo) | 未核到 | **评测 CLI**：介于两者之间 —— 能直接跑，但价值在 CI 里，不是单次调用 |

## 注入流程（把一个已归因的会话变成子智能体）

前提：该会话已**归因出模型名**（`ModelTrace` → `arena-model.json`）并按模型名**重命名**（见 `arena-protocol.md`）。
注入由 `tools/arena-inject-pack.mjs` 完成（一条消息：clone 交付仓 → 读本文件 → 配凭据 → 回报）。
**密钥纪律**：优先让工作端**自己生成密钥对、只回报公钥**（私钥从不进入聊天 ✓）；把私钥贴进消息等于暴露一次 ✗。

## 五、我们装的插件 → 它的通用版（2026-10-16 检索）

这一节回答的是：**DSH 里那几个插件，各自对应的开源通用版是什么** —— 好让子智能体在没有 DSH 的环境里也能拿到同等能力。

| 我们的插件 | 它干什么 | 通用版（开源，能直接接给 agent） | 星 |
|---|---|---|---|
| engram（记忆宫殿：事实/偏好/决策/情景/技法 + 复习排期） | 本地优先的长期记忆与会话记忆 | MemPalace：本地优先记忆 + 时序实体关系图（含有效期）+ MCP server，SQLite 支撑；合格对照还有 Cognee、claude-obsidian | 59.5k / 32k / 15.5k |
| llm-deepseek、MCP 连接器 | 模型接入与工具网关 | LiteLLM（100+ provider 一个 OpenAI 兼容口）、Bifrost（MCP 网关：聚合多个 MCP、语义缓存、治理） | 33k / 8.6k |
| mattskillsdeck（ISSUE/map 管理） | 任务与地图的本地台账 | Agent Plugins 1.0 打包标准（plugin.json + skills/ + mcp.json，跨客户端；2026-08 由 Vercel/AWS/GitHub/Microsoft/OpenAI 发起）+ 各家技能库 | 标准，无星 |
| univer（文档/表格/幻灯片套件） | 文档与表格的 agent 操作面 | 未核到与它对等的单一开源项目（LibreOffice 无头 + 办公格式库只是方向） | 未核到 |
| scopesentry（ASM 资产测绘） | 子域/端口/漏洞/URL 扫描与资产库 | 自身即开源；同级通用版未核到 | 未核到 |
| oss-prompt-optimizer | 提示词优化 | DSPy（需集成，已在本包附录） | 38.6k |
| dsh-purge、guardian、agents-anywhere、lingshu、dsh-chat-import | DSH 特有的指令层/守卫/会话迁移能力 | 未核到通用版（这类是本机形态特有的，不硬凑） | — |

> 纪律：**没核到的就写未核到** —— 不拿相近项目冒充。星数为 2026-10-16 检索读数。
