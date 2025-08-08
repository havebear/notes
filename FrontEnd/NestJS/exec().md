在 NestJS + Mongoose 里

TypeScript

复制

```ts
Model.findOne({ ... }).exec()
```

和

```ts
Model.findOne({ ... })
```

**查询结果完全一样**——都会返回同一条文档；  
差别只在 **返回值的类型**：

- `findOne(...)` → 返回 **Mongoose Query**（可以链式 `.populate()`、`.sort()` …）。
- `.exec()` → 把 Query **真正执行**并返回 **Promise<Document | null>**。
    

因此：

1. 你用了 `await`，两种写法都能得到相同数据。
2. 加 `.exec()` 的好处是 **类型推断更精确**（TypeScript 会推断成 `Promise<DiaryDocument | null>`），也符合 Mongoose 官方推荐写法。
3. 不加 `.exec()` 也不会影响查询结果，但链式写法里必须加，否则 Query 不会被执行。

结论：**建议保留 `.exec()`** —— 统一风格、类型安全，也不会带来副作用。

### 什么情况下加

只有当你手里拿到的是 **Mongoose Query 实例** 时，才需要（或说可以）链 `.exec()`。  
一句话记忆：

> **Query 才加 `.exec()`，Document 直接 `await` 完事。**

下面给出常见 API 对照表，一眼就能判断该不该加。

|API/方法|返回值类型|是否需要 `.exec()`|
|:--|:--|:--|
|`Model.find()`|Query|✅ 建议加|
|`Model.findOne()`|Query|✅ 建议加|
|`Model.findById()`|Query|✅ 建议加|
|`Model.findOneAndUpdate()`|Query|✅ 建议加|
|`Model.updateMany()`|Query|✅ 建议加|
|`Model.deleteOne()`|Query|✅ 建议加|
|`Model.aggregate()`|Aggregate|❌ 不需要（不是 Query）|
|`Model.create()`|Document（或数组）|❌ 不能加|
|`Model.insertMany()`|Document[]|❌ 不能加|
|`Model.findOne().remove()`|Query|✅ 建议加|
|`Query.populate()`|还是 Query|✅ 最终要加|
|`Query.lean()`|还是 Query|✅ 最终要加|
