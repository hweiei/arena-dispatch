# 远程工作者说明（Remote Worker Brief）

你（Arena 里的 agent）要用**自己的 bash** 完成一个任务。**不需要调用任何工具协议，
不需要任何 GitHub 凭据——你本地的 shell 就够了。**

---

## 交付方式：两种都行，优先推回 GitHub

**优先：把产物推回本仓库的 `result/<TASK_ID>` 分支。** 发起方直接收。

**前提是你收到了凭据。** 发起方会在消息里给你一段私钥和命令。**没收到就别试 push**——会失败并浪费一轮，改成写文件。

**兜底：写文件。** 产物写进工作目录的 `out/`，发起方从文件查看器收取。
**两条路都不通时，才把少量内容贴在回复里。**

---

## 怎么推（拿到私钥时）

**22 端口在国内会被重置，必须走 443：**

```bash
mkdir -p ~/.ssh && chmod 700 ~/.ssh
cat > ~/.ssh/id_ed25519 <<'KEYEOF'
<发起方给你的私钥，整段照抄，包括 BEGIN/END 两行>
KEYEOF
chmod 600 ~/.ssh/id_ed25519
ssh-keyscan -p 443 ssh.github.com >> ~/.ssh/known_hosts 2>/dev/null

cd <你的工作目录>
git add -A out/
git -c user.name=arena-worker -c user.email=arena@local commit -m "result: <一句话>"
git remote set-url origin ssh://git@ssh.github.com:443/hweiei/arena-dispatch.git
git push origin HEAD:refs/heads/result/<TASK_ID>
```

**`<TASK_ID>` 见 `TASK.md` 顶部。**

**推完在回复里贴出 `git push` 的原始输出**（成功或失败都贴）。

**如果 push 失败**：把 git 的原始报错贴出来，**同时把产物写进 `out/` 兜底**，不要反复重试。

---

## 怎么做

1. **工作目录**

   如果你**已经** clone 过 `https://github.com/hweiei/arena-dispatch`，**就在那个目录里改，
   不要重新 clone**——重复 clone 会让你上一轮的产物丢失。

   如果还没有：
   ```bash
   git clone --depth 1 https://github.com/hweiei/arena-dispatch /tmp/work
   cd /tmp/work
   ```

2. **读任务** —— `TASK.md` 里是要你做的事。**先复述一遍你的理解，一句话就够。**

3. **干活** —— 用你自己的办法。

4. **交付** —— 推回 GitHub（见上），或写进 `out/`。

5. **回报** —— 回复里写：
   - 你跑了哪些命令
   - 结果如何（**贴原始输出，别改写**）
   - **哪条失败了、报什么错**

**做完就停，等下一步。不要反复报告同一件事。**

## 硬要求

- **不要编造。** 没跑通的命令，就说没跑通、报什么错。**那比一个编的成功结果有用得多。**
- **不要美化输出**，原样粘贴。
- **不要重复 clone**（见第 1 条，它会让你丢掉上一轮的工作）。
- **不要碰 `AGENTS.md` 和 `TASK.md`** —— 那是发起方写的。
- **不要把凭据写进任何要提交的文件。**

## 上下文不够用时

任务可能很大。**别一次读完整棵树**：

```bash
git ls-files | head -100                    # 先看有什么
grep -rn "关键字" --include="*.md" | head -30   # 按需定位
```

**只读你要改的那几个文件。** 需要理解结构时用 `git ls-files` 和 `grep`，不要 `cat` 整棵目录。

## 这个仓库是什么

`arena-dispatch` 是**发起方和工作者之间的交接盘**。

```
发起方 push:  AGENTS.md（本文件）+ TASK.md（任务）
           ↓
你：读 → 干活 → 产物写进 out/
           ↓
发起方从文件查看器收取 out/ 里的东西
```

**没有别的通道。** 你不需要 API、不需要凭据、不需要工具协议。
