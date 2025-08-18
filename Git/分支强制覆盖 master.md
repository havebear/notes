要**用分支代码完全覆盖主分支**（即丢弃 master 的所有更改，强制用分支内容替换），有两种场景，分别给出安全做法和强制做法：

---

### ✅ **安全做法（推荐）**

**保留历史记录**，但让 `master` 的代码和 `feature-branch` 完全一致：

1. **切到 master 并拉取最新**
    
    ```bash
    git checkout master
    git pull origin master
    ```
    
2. **用分支内容覆盖工作区
    
    ```bash
    git checkout feature-branch -- .
    ```
    
3. **提交覆盖后的代码
    
    ```bash
    git add .
    git commit -m "feat: 用 feature-branch 覆盖 master"
    git push origin master
    ```
    
    > 这样 `master` 的历史会保留，但文件内容和 `feature-branch` 完全一致。
    

---

### ⚠️ **强制做法（危险！会丢失 master 的历史）**

**完全丢弃 master 的旧历史**，强制让 `master` 指向 `feature-branch`：

1. **切到 feature-branch 并确保最新
    
    ```bash
    git checkout feature-branch
    git pull origin feature-branch
    ```
    
2. **强制覆盖远程 master
    
    ```bash
    git push origin feature-branch:master --force-with-lease
    ```
    
    > `--force-with-lease` 比 `--force` 更安全，会检查是否有其他人推送了新提交。
    
3. **本地同步 master
    
    ```bash
    git checkout master
    git reset --hard origin/master
    ```
    

---

### 📌 **注意事项**

- **备份 master**：强制覆盖前，建议给旧 master 打 tag 备份：

    
    ```bash
    git tag master-backup-$(date +%Y%m%d)
    git push origin master-backup-$(date +%Y%m%d)
    ```
    
- **团队协作**：如果其他人基于旧 master 开发，强制推送会导致冲突，需提前沟通！
    
- **CI/CD 影响**：强制推送可能触发部署，确保不会破坏生产环境。
    

---

需要更温和的方式（如 PR 合并 + 解决冲突）吗？