# TASK_ID: roundtrip-1

## 任务

**先做一件事证明回路通了，别做别的。**

1. 在仓库根目录建 `out/` 目录
2. 在里面写一个文件 `out/hello.md`，内容包含三样：
   - 这个沙箱的 `uname -a` 输出
   - `python3 --version` 的输出（没有就写「没有 python3」）
   - 一句话：你克隆这个仓库用了多少秒（自己掐表，不用很准）
3. 再写一个 `out/inventory.md`，内容是 `git ls-files | wc -l` 的结果，
   以及 `git ls-files | head -20` 的输出
4. 按 `AGENTS.md` 第 4 步提交并推送到 `result/roundtrip-1`

## 说明

**这个任务的产物不重要，回路通不通才重要。** 所以：

- 哪条命令**没跑通，就照实写哪条失败、报什么错** —— 那比一个编出来的成功结果有价值得多
- 不要美化输出，**原样粘贴**
- 做完了在回复里贴 `git push` 的原始输出

## 验收

发起方会 `git pull` 到 `result/roundtrip-1` 分支，检查：
- `out/hello.md` 和 `out/inventory.md` 存在且非空
- `uname -a` 的输出看起来是真的 Linux（不是编的）
