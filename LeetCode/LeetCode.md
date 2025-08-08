## Objective（目标）
### 字段

| 字段名           | 类型         | 必填  | 默认值         | 说明                        |
| :------------ | :--------- | :-- | :---------- | :------------------------ |
| `_id`         | ObjectId   | √   | -           | 主键                        |
| `userId`      | ObjectId   | √   | -           | 所属用户                      |
| `title`       | String     | √   | -           | 目标标题                      |
| `description` | String     | ×   | ''          | 目标描述                      |
| `dueDate`     | Date       | √   | -           | 截止日                       |
| `cycle`       | String     | √   | 'quarterly' | quarterly / yearly        |
| `progress`    | Number     | √   | 0           | 0-1 完成度                   |
| `status`      | String     | √   | 'active'    | active / done / cancelled |
| `krs`         | [ObjectId] | √   | []          | 关联 KR 列表                  |
### 接口

```
POST   /okr/objectives          创建（可带 KR）
GET    /okr/objectives          列表（?cycle=quarterly&page=1）
GET    /okr/objectives/:id      详情（含 KR & Task 树）
PATCH  /okr/objectives/:id      修改标题/描述/截止日
DELETE /okr/objectives/:id      软删（级联删 KR/Task）
POST   /okr/objectives/:id/duplicate 复制到下一周期
```

## KeyResult（关键结果）

### 字段

| 字段名           | 类型       | 必填  | 默认值 | 说明                    |
| :------------ | :------- | :-- | :-- | :-------------------- |
| `_id`         | ObjectId | √   | -   | 主键                    |
| `objectiveId` | ObjectId | √   | -   | 所属目标                  |
| `title`       | String   | √   | -   | KR 标题                 |
| `target`      | Number   | √   | -   | 目标值                   |
| `current`     | Number   | √   | 0   | 当前值                   |
| `unit`        | String   | √   | -   | km / kg / 次 / % / ... |
| `confidence`  | Number   | √   | 0.7 | 信心指数 0-1              |
| `weight`      | Number   | √   | 1   | 权重 ≥ 1                |
### 接口

```
POST   /okr/key-results              新增
PATCH  /okr/key-results/:id          更新 current / title / target
DELETE /okr/key-results/:id          删除
```

## Task（任务）

### 字段

| 字段名        | 类型       | 必填  | 默认值   | 说明               |
| :--------- | :------- | :-- | :---- | :--------------- |
| `_id`      | ObjectId | √   | -     | 主键               |
| `krId`     | ObjectId | √   | -     | 所属 KR            |
| `title`    | String   | √   | -     | 任务标题             |
| `done`     | Boolean  | √   | false | 是否完成             |
| `priority` | Number   | √   | 1     | 1 高 / 2 中 / 3 低  |
| `dueDate`  | Date     | ×   | null  | 任务截止日            |
### 接口

```
POST   /okr/tasks               新增
PATCH  /okr/tasks/:id           更新标题/优先级/截止日
PATCH  /okr/tasks/:id/status    完成/撤销
DELETE /okr/tasks/:id           删除
GET    /okr/tasks/tree/:krId    树形任务列表
```

## Review（复盘）

### 字段

| 字段名           | 类型       | 必填  | 默认值      | 说明     |
| :------------ | :------- | :-- | :------- | :----- |
| `_id`         | ObjectId | √   | -        | 主键     |
| `objectiveId` | ObjectId | √   | -        | 所属目标   |
| `score`       | Number   | √   | -        | 0-1 打分 |
| `note`        | String   | ×   | ''       | 文字总结   |
| `reviewedAt`  | Date     | √   | Date.now | 复盘时间   |

### 接口

```
POST /okr/reviews 提交复盘
修改复盘
删除复盘
```

## 统计 & 辅助

| 功能      | 方法  | 路径                   | 说明                  |
| :------ | :-- | :------------------- | :------------------ |
| 当前季度完成率 | GET | `/okr/stats/summary` | 当前用户 O/KR/Task 汇总   |
| 进度预警    | GET | `/okr/stats/alerts`  | confidence < 0.5 列表 |
## 索引速览

```
Objective.index({ userId, dueDate })
KeyResult.index({ objectiveId })
Task.index({ krId, done })
Review.index({ objectiveId })
```

关于索引，可在现有基础上完善