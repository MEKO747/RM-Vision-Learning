# 实践分支管理

你有没有过这种经历：正在写一个新功能，突然接到通知说之前那个版本有个 bug 得马上修。

怎么办？把现在的代码全部删掉？新建一个文件夹备份？

不用。分支就是来解决这个问题的。

---

## 什么是分支

简单理解，分支就是一条独立的时间线。你可以在这条线上专心写新功能，同时另一条线在修 bug。两条线互不干扰。

main 分支（以前叫 master）是项目的"正式版本"，一般只有经过测试的代码才会合并进去。

---

## 创建和切换分支

**新建一个分支：**
```bash
git branch feature/vision算法优化
```

**切换到这个分支：**
```bash
git checkout feature/vision算法优化
```

或者一次性搞定：
```bash
git checkout -b feature/vision算法优化
```

切换之后，你所有的 commit 都只在这个新分支上。main 分支完全不受影响。

---

## 查看分支

```bash
git branch
```

带 * 号的是你当前所在的分支。

```bash
git branch -a
```

加上 `-a` 可以看到远程的所有分支。

---

## 合并分支

假设你在 `feature/vision算法优化` 上开发完了，想把它合并回 main：

```bash
git checkout main
git merge feature/vision算法优化
```

合并分为两种情况：

**快速合并（Fast Forward）**：main 没有新提交，直接把指针往前移。没有任何冲突的可能。

**三方合并**：两个分支都有新提交，Git 会把两边的改动合并起来。有时候同一个文件两边都改了，就会产生冲突。

---

## 冲突处理

冲突长这样：

```
<<<<<<< HEAD
    int threshold = 128;
=======
    int threshold = 100;
>>>>>>> feature/vision
```

Git 用特殊标记标出了两个版本的内容。你需要手动决定保留哪个，或者把两者都保留。

处理完之后：
```bash
git add filename.c
git commit -m "解决与 feature/vision 的冲突"
```

合并冲突这件事，说起来挺吓人的，其实每次就那么几步。遇到一次就懂了。

---

## 删除分支

功能开发完了，记得删掉没用的分支：
```bash
git branch -d feature/vision算法优化
```

如果分支还没合并，Git 会报错，防止你误删。用 `-D` 强制删除。

---

## rebasing —— 另一种合并方式

merge 和 rebase 都能合并分支，但效果不一样。

rebase 会把你的分支"接"到目标分支的最新提交上，提交历史会更干净整洁。

```bash
git checkout feature/vision算法优化
git rebase main
```

但这里有个重要原则：**不要 rebase 已经推送到远程的分支。** 因为 rebase 会改写提交历史，其他人的分支可能会出问题。

---

## 实用的分支命名

团队里一般有约定俗成的命名规范：

- `feature/` —— 新功能，如 `feature/自动瞄准`
- `bugfix/` —— 修复 bug，如 `bugfix/装甲板识别`
- `hotfix/` —— 紧急修复，如 `hotfix/电机控制`

用 `/` 分隔是有原因的。Git 会把它们归类显示，看起来更清楚。

---

## 我的习惯

每次开始新任务前，先 `git pull` 一下确保最新，然后 `git checkout -b 新分支名`。开发完测试没问题就合并，合并完删掉这个分支。

简单几步，代码乱成一团的情况基本没再出现过。

分支管理不难，难的是形成习惯。
