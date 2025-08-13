一句话先给结论

- **Prettier 只管「代码长什么样」**（风格）。
- **ESLint 只管「代码写得对不对」**（质量 + 部分风格）。

二者职责边界、运行顺序和典型示例如下。

---

1. Prettier —— 纯粹「格式化器」 | 关注点 | 示例 | |---|---| | 缩进、换行、空格 | 一行 100 字符后自动折行 | | 引号风格 | `'single'` vs `"double"` | | 逗号、分号 | `trailingComma: 'all'` | | 对象/数组括号 | `{ foo: 1 }` vs `{foo: 1}` |

特点

- 无业务规则，只关心排版；
- 速度极快，一键 `npx prettier --write .`；
- 输出结果**永远一样**，无“可配置规则”。

---

2. ESLint —— 纯粹「代码质量检查器」 | 关注点 | 示例 | |---|---| | 潜在 Bug | `no-unused-vars`, `no-undef` | | 代码逻辑 | `eqeqeq`, `no-console` | | 最佳实践 | `@typescript-eslint/no-explicit-any` | | **少量风格**（历史包袱） | `semi`, `quotes` |

特点

- 可自定义规则集（extends / rules）；
- 支持自动修复（`--fix`），但只限于自家规则；
- 不关心排版细节，只关心「写得对不对」。

---

3. 为什么需要 `eslint-config-prettier` ESLint 早期自带了 `semi`、`quotes` 等风格规则，会与 Prettier 撞车。  
    `eslint-config-prettier` 把这些**冲突规则全部关掉**，让两者各司其职：

JavaScript

```js
extends: [
  '@nestjs/eslint-config/recommended',
  'prettier'   // 放在最后，关闭冲突
]
```

---

4. 典型协作流程（保存文件时）

```
源代码
   │
   ├─ ESLint 检查（质量）
   │
   └─ Prettier 格式化（风格）
   ↓
最终代码
```

---

记忆口诀  
**Prettier 做“美颜”，ESLint 做“体检”。**  
美颜与体检不冲突，但体检里别管头发颜色。