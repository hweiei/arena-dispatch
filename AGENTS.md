# 远程工作者说明（Remote Worker Brief）

你（Arena 里的 agent）要用**自己的 bash** 完成一个任务，然后把结果**推回本仓库**。
不需要调用任何外部工具协议——你本地的 shell 就够了。

---

## 你在哪

- 工作目录：`/home/user`（或你的沙箱的对应位置）
- 你有 `git`、网络、`curl`。**已实测。**

## 怎么做

1. **克隆**
   ```bash
   git clone --depth 1 https://github.com/hweiei/arena-dispatch /tmp/work
   cd /tmp/work
   ```

2. **读任务** —— `TASK.md` 里是要你做的事，**先在回复里复述一遍你的理解**，
   一句话就够，让发起方有机会在你跑偏之前叫停。

3. **干活** —— 用你自己的办法。**读文件、写代码、跑测试，随便。**

4. **交付** —— 把产物写进 `out/` 目录（不存在就建），然后提交：
   ```bash
   git add -A out/
   git -c user.name="arena-worker" -c user.email="arena@local" \
       commit -m "result: <一句话说明>"
   git push origin HEAD:refs/heads/result/<TASK_ID>
   ```
   其中 `<TASK_ID>` 见 `TASK.md` 顶部。

5. **回报** —— 在回复里贴出：
   - `git push` 的原始输出（成功或失败都贴）
   - `git log --oneline -1`
   - `ls -R out/` 的结果

## 硬要求

- **不要**编造任何东西。**没跑通的命令，就说没跑通、报什么错。**
- **不要**修改 `out/` 以外的文件（`AGENTS.md` / `TASK.md` 是发起方写的，改了就收不到）。
- **不要**在一次回复里做完全部步骤就报「完成」——**如果你在沙箱里跑不了某条命令，
  照实说哪条失败**，比编一个成功的结果有用得多。
- 产出**放在 `out/` 里**，不要只贴在回复里——回复会很长，`out/` 才是交付物。

## 上下文不够用时

任务可能很大。**别试图一次读完整棵树**：

```bash
git ls-files | head -100          # 先看有什么
grep -rn "关键字" --include="*.md" | head -30   # 按需定位
```

**只读你要改的那几个文件。** 需要理解结构时用 `git ls-files` 和 `grep`，
不要 `cat` 整个目录。

## 这个仓库是什么

`arena-dispatch` 是**发起方（DSH）和工作者（你）之间的交接盘**。

```
发起方 push:  AGENTS.md（本文件）+ TASK.md（任务）+ 输入文件
           ↓
你 clone → 干活 → 把产物写进 out/ → push 到 result/<TASK_ID> 分支
           ↓
发起方 pull result/<TASK_ID> 收结果
```

**没有别的通道。** 你不需要调用任何 API、不需要任何凭据、不需要任何工具协议。
