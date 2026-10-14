# TASK_ID: roundtrip-1

## 任务

**先证明回路通了，别的都别做。**

1. 克隆 `https://github.com/hweiei/arena-dispatch` 到 `/tmp/work`，进去
2. 跑这几条，**原样记下输出**：
   ```bash
   uname -a
   python3 --version
   git ls-files | wc -l
   git ls-files | head -20
   ```
3. **在回复的最后，贴一个 ```json 代码块**，形状如下（照抄这个结构，值换成你实测的）：

```json
{
  "uname": "把 uname -a 的完整输出粘在这里",
  "python3": "把 python3 --version 的输出粘在这里，没有就写 没有 python3",
  "fileCount": 2,
  "files": ["AGENTS.md", "TASK.md"],
  "cloneSeconds": 3,
  "failures": []
}
```

## 硬要求

- **`failures` 数组**：哪条命令没跑通就写进去，格式 `"命令 → 报错原文"`。
  **没失败就留空数组。**
- **不要美化、不要改写、不要总结成中文**——`uname` 和 `python3` 的值必须是**命令的原始输出**。
- **哪个值你拿不到，就写 `null` 并在 `failures` 里说明**——**不要编一个看起来合理的值。**
- **只贴一个 ```json 代码块**，不要在它外面重复内容。

## 为什么这样设计

发起方会解析那个 JSON 块。**这是回路的第一段证明**：
你 clone 得到 → 你跑得出 → 你把结果结构化地还回来。

**第二段（你 push 回 GitHub）需要凭据，那一步另说。**
