# 恢复代码历史版本

写代码最怕的是什么？

手滑删了一大段逻辑，保存了，然后发现不对劲。

git 的历史记录就是你的"后悔药"。只要 commit 过，总能找回来。

---

## 查看历史 —— 找到你想回去的位置

```bash
git log --oneline
```

这会列出所有的 commit，每个前面有一串哈希值（SHA），比如：

```
a1b2c3d 修复了装甲板检测的边缘情况
e4f5g6h 添加了自适应阈值
7i8j9k0 初始版本
```

记住你想回去的那个 commit 的哈希值。

---

## git checkout —— 查看某个历史版本

如果你只是想看看某个历史版本长什么样，不打算回退：

```bash
git checkout a1b2c3d
```

这会让你进入"分离 HEAD 状态"，可以看，可以编译运行，但**不要在这里做 commit**。看完想回来：

```bash
git checkout main
```

---

## git reset —— 退回到某个版本

想真的把代码"退回去"？reset 有三种模式。

### 软重置（soft）

```bash
git reset --soft a1b2c3d
```

把 HEAD 移动到那个 commit，但保留之后的修改在暂存区。适合"刚才 commit 得太早了，再合并一下"的情况。

### 混合重置（mixed，默认）

```bash
git reset a1b2c3d
# 或
git reset --mixed a1b2c3d
```

保留修改内容，但取消暂存。相当于什么都没发生，但 HEAD 回到了那个点。

### 硬重置（hard）

```bash
git reset --hard a1b2c3d
```

这个要小心。HEAD 移动到那个 commit，**所有之后的修改全部丢弃**。工作区的代码也会被覆盖。

```bash
git reset --hard HEAD~1
```

上面这个命令的意思是"退回到上一个 commit"，相当于撤销最后一次 commit。

---

## git revert —— 生成一个新的 commit 来"撤销"

reset 会改写历史，有时候这很危险。比如你已经 push 到远程了，reset 会让你的本地和远程对不上。

revert 不一样。它不会删除任何历史，而是生成一个新的 commit，专门用来"撤销"某个 commit 的效果。

```bash
git revert a1b2c3d
```

这会打开编辑器让你写新 commit 的信息，然后创建一个新的 commit，内容刚好和 a1b2c3d 相反。

如果你想撤销一个已经 push 到远程的 commit，用 revert，不要用 reset。

---

## 恢复单个文件到某个版本

有时候你只改坏了一个文件，不需要回退整个项目：

```bash
git checkout a1b2c3d -- src/vision.c
```

这会把 `src/vision.c` 恢复到 a1b2c3d 那个 commit 时的状态。

---

## reflog —— 恢复"丢失"的 commit

这是 git 的救命命令。

有时候你 reset 过头了，或者分支被误删了，觉得 commit 都没了。先别慌：

```bash
git reflog
```

这会列出你在这个仓库里做过的所有操作，包括 reset、checkout、commit、merge……每一行的前面也有哈希值。

找到你想恢复的那个操作对应的哈希值，然后：

```bash
git checkout a1b2c3d
```

或者

```bash
git reset --hard a1b2c3d
```

reflog 默认保存 90 天的历史。只要不是硬盘坏了，基本都能找回来。

---

## 什么时候用什么

| 场景 | 建议 |
|------|------|
| 想撤销刚才的 commit，还没 push | `git reset --soft HEAD~1` |
| 想完全回到某个历史状态，本地使用 | `git reset --hard` |
| 想撤销某个已经 push 的 commit | `git revert` |
| 想看某个历史版本是什么样的 | `git checkout` |
| 想恢复单个文件 | `git checkout [hash] -- [文件]` |
| commit 好像丢了，不知道在哪 | `git reflog` |

---

说真的，刚学 Git 的时候我对这些命令又爱又怕。爱是因为真的能找回代码，怕是因为用错一次可能把事情搞得更糟。

后来想明白一件事：**commit 记录不会消失，reset 也不会真正删除东西**。最坏的情况不过是 checkout 到某个旧版本重新来。

别太保守。大胆用，用错了 reflog 救回来就是了。
