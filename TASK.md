# TASK_ID: csv-1

## 任务

写一个 Python 脚本 `out/mdtable.py`，把 **Markdown 表格**转成 **CSV**。

用法：`python3 out/mdtable.py < input.md > out.csv`

### 要求

1. 只转换表格行（以 `|` 开头的行），**忽略表格以外的文字**
2. 跳过 Markdown 的分隔行（形如 `|---|---|`）
3. 去掉每个单元格前后的空格
4. 正确处理单元格内容里**不**含 `|` 的情况即可（不用处理转义的 `\|`）
5. **CSV 每格用双引号包起来**，内部的双引号翻倍（标准 CSV 转义）
6. 脚本要能**从 stdin 读**，不要写死文件名

### 你必须自己测

在 `out/` 里建一个 `sample.md`，包含**至少 3 行数据**，**并且表格前后各有一段普通文字**（用来验证第 1 条）。
然后跑：

```bash
python3 out/mdtable.py < out/sample.md
```

**把命令和原始输出一起贴回来。**

## 交付

在回复末尾贴一个 ```json 代码块：

```json
{
  "script": "out/mdtable.py 的完整内容，作为一个字符串",
  "sample": "out/sample.md 的完整内容",
  "output": "python3 out/mdtable.py < out/sample.md 的原始输出",
  "edgeCasesTried": ["你试过的边界情况，每条一句"],
  "failures": []
}
```

## 硬要求

- **`output` 必须是命令的原始输出**，不要重排版、不要美化
- **脚本要真的能跑**——你自己跑一遍再贴
- 哪个要求你做不到，**照实写进 `failures`**，别假装做到了
- **别把 `out/sample.md` 里的普通文字输出成 CSV 行**——那是第 1 条要说的事
