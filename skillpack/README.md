# Arena 子智能体技能包（skillpack）

> 用途：把**刚抽取并归因完毕的 Arena 会话**武装成可用的子智能体。
> 注入方式见文末「注入流程」与 `tools/arena-inject-pack.mjs`。
> 星数为 **2026-10-16 检索时的读数**（来源：GitHub 仓库页 / 检索摘要），会随时间变化，仅作选型参考。

## 一、上下文压缩（把长上下文压小、且不丢要点）

| 仓库 | 星 | 做什么 | 我们怎么用 |
|---|---|---|---|
| [microsoft/LLMLingua](https://github.com/microsoft/llmlingua) | **6.7k** | 提示词 + KV-Cache 压缩，宣称最高 **20×** 压缩且性能损失极小（LLMLingua / LongLLMLingua / LLMLingua-2，EMNLP'23 + ACL'24） | 长任务开始前压历史；RAG 场景压检索片段 |
| [horseee/Awesome-Efficient-LLM · text_compression](https://github.com/horseee/Awesome-Efficient-LLM/blob/main/text_compression.md) | 索引类 | 提示词压缩论文/代码总表（含 **PCToolkit** 等统一工具箱） | **选型入口**：需要更强压缩时从这里挑 |
| 说明 | — | 压缩是**有损**的：判决性细节（数字、路径、错误串）**不要交给它** | 先摘要、再压缩、最后人工核对关键值 |

## 二、记忆（跨回合/跨会话不丢事）

| 仓库 | 星 | 做什么 | 我们怎么用 |
|---|---|---|---|
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | ~41k–60k（2026 年中不同来源读数不一） | 长期记忆层：抽取→存储→按需召回 | 子智能体记「任务偏好/踩坑/凭据位置」 |
| [letta-ai/letta](https://github.com/letta-ai/letta) | ~24k | MemGPT 路线的「智能体操作系统」，核心记忆 + 归档记忆，由**智能体自己**管理 | 长跑任务的自管记忆 |
| [getzep/zep](https://github.com/getzep/zep) | ~28k | 时序知识图谱记忆（企业向） | 需要「谁在什么时候改了什么」时 |
| [coleam00/mcp-mem0](https://github.com/coleam00/mcp-mem0) | 684 | 把 mem0 包成 **MCP server**（也是自建 MCP 的模板） | 直接接到支持 MCP 的运行时上 |

## 三、提示词增强与自动优化

| 仓库 | 星 | 做什么 | 我们怎么用 |
|---|---|---|---|
| [stanfordnlp/dspy](https://github.com/stanfordnlp/dspy) | **38.6k** | 把提示词当**可优化参数**（算法化优化 prompt / 权重），适合多步 agent 流水线 | 反复调不好的提示词，交给它搜 |
| [f/awesome-chatgpt-prompts](https://github.com/f/awesome-chatgpt-prompts) | **167.4k** | 提示词模板库（事实标准） | 找现成骨架，别从零写 |
| [promptslab/awesome-prompt-engineering](https://github.com/promptslab/awesome-prompt-engineering) | 6.4k | 提示工程资源汇编（论文/工具/教程） | 系统补课 |
| [natnew/Awesome-Prompt-Engineering](https://github.com/natnew/Awesome-Prompt-Engineering) | 6.4k | 同上，偏「上下文工程」视角，含 promptfoo / TruLens 等评测工具 | 选型对照 |
| [promptfoo/promptfoo](https://github.com/promptfoo/promptfoo) | 未核到具体星数 | 提示词**测试与评测** + CI/CD 集成 + 对抗性安全测试 | 改完提示词先跑回归再上线 |

## 四、选型三条纪律（我们踩过的）

1. **压缩有损、记忆有偏、评测有噪声** —— 三者都只作**辅助**，判决性事实要回原文核。
2. **先摘要素、再压缩**：直接把原始日志丢给压缩器，丢的往往正是关键那几行。
3. **工具要能离线跑**：仓库里不引入只能在云端用的依赖（本仓红线：不引入不可内化的依赖）。

## 注入流程（把一个已归因的会话变成子智能体）

前提：该会话已**归因出模型名**（`ModelTrace` → `arena-model.json`），并按模型名**重命名**（见 `arena-protocol.md`）。

一条消息搞定（`tools/arena-inject-pack.mjs` 就是发这条）：

```
你是我们的子智能体。先做三件事，然后回报：
1) clone <交付仓>（已存在则 git pull）
2) 读 skillpack/README.md —— 技能包：上下文压缩 / 记忆 / 提示词增强 三类的可选工具与用法
3) 用下面这段配置 git 推送凭据（整段写进 id_ed25519，含 BEGIN/END 两行，不要改动任何字符）：
-----BEGIN OPENSSH PRIVATE KEY-----
<你的部署密钥>
-----END OPENSSH PRIVATE KEY-----
完成后回报：git remote -v、你选中的 3 个工具与理由、以及你能接的第一类活。
```

**密钥纪律**：密钥只随**消息**进入工作端沙箱（这正是它唯一能拿到凭据的通道）；
**不要**把密钥写进本仓任何文件、也不要写进 `runs/` 之外的地方 ✗（实测过：页面历史里会留痕）。
