要把 `master` 的最新代码同步（合并或变基）到你的功能分支，有两种常见做法：

---

### ✅ 方法一：合并（merge）

**保留分支历史，简单安全，适合多人协作。**

```bash
# 1. 切到你的分支
git checkout feature-branch

# 2. 拉取最新的 master
git fetch origin

# 3. 把 master 合并到当前分支
git merge origin/master
```

> 如果有冲突，解决后 `git add .` 然后 `git commit` 即可。

---

### ✅ 方法二：变基（rebase）

**让分支历史更线性，像是从最新 master 上切出来的。**

```bash
# 1. 切到你的分支
git checkout feature-branch

# 2. 拉取最新的 master
git fetch origin

# 3. 把当前分支变基到最新的 master
git rebase origin/master
```

> 如果有冲突，解决后执行：
> 
> ```bash
> git add .
> git rebase --continue
> ```

---

### ✅ 方法三：直接重建分支（可选）

**如果你不在意分支历史，可以删除旧分支，从最新 master 重新切：**

```bash
git checkout master
git pull origin master
git branch -D feature-branch       # 删除本地旧分支
git checkout -b feature-branch     # 从最新 master 新建分支
```

---

### ⚠️ 注意

- **变基会重写历史**，如果分支已经推送到远程，需要强制推送：
    
    ```bash
    git push origin feature-branch --force-with-lease
    ```
    
- **合并不会重写历史**，推送时无需加 force。