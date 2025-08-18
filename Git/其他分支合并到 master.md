下面给出 3 种常见场景下“把分支合并到 master”的完整操作示例，按风险从低到高排列。请先确认你当前所处的场景，再选择对应方案。

---

## ✅ 场景 1：本地仓库，无其他协作者

需求：把 `feature-branch` 合并到本地 `master`，然后一次性推到远程。

```bash
# 1. 切到 master 并更新
git checkout master
git pull origin master     # 如远端有更新

# 2. 合并分支（保留分叉历史）
git merge --no-ff feature-branch

# 3. 推送
git push origin master
```

> 如需线性历史，可把第 2 步换成  
> `git rebase feature-branch`（先切到 master 再 rebase），然后 `git push`.

---

## ✅ 场景 2：使用 GitHub / GitLab 等平台

需求：通过 Pull Request / Merge Request 合并，强制要求 Code Review。

1. 本地把 master 最新代码同步到分支（避免冲突）
    
    ```bash
    git checkout feature-branch
    git fetch origin
    git rebase origin/master        # 或 git merge origin/master
    git push --force-with-lease     # rebase 后需强推
    ```
    
2. 在网页上发起 PR（`feature-branch → master`）
3. Review 通过后点击 **“Merge pull request”**
4. 选择合并策略
    - Create a merge commit（默认，保留分叉）
    - Squash and merge（把分支多个提交压成 1 个）
    - Rebase and merge（快进，线性历史）

---

## ⚠️ 场景 3：我要“强行”让分支内容覆盖 master

（**危险！会丢掉 master 的现有历史**；仅当团队允许或你独自维护仓库时使用）

```bash
# 1. 备份旧 master（可选但强烈建议）
git checkout master
git tag master-backup-$(date +%Y%m%d)
git push origin master-backup-$(date +%Y%m%d)

# 2. 强制把 master 指向 feature-branch
git checkout feature-branch
git pull origin feature-branch       # 确保最新
git push origin feature-branch:master --force-with-lease
```

---

### 🔍 冲突处理小结

- **merge** 时只会在合并点出现一次冲突；解决后 `git commit` 即可。
- **rebase** 时每遇到一个提交就可能冲突；依次解决，然后  
    `git add . && git rebase --continue`。

---

### 📌 一句话速查表

|目标|命令|
|:--|:--|
|保留历史|`git checkout master && git merge --no-ff feature-branch`|
|线性历史|`git rebase master`（在分支上）|
|强制覆盖|`git push origin feature-branch:master --force-with-lease`|
