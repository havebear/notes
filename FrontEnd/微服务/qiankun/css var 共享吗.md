**“根级” CSS 自定义属性（`:root{ --x:1 }`）是全局共享的，所有微应用都能读到/覆盖；**  
**但 Shadow DOM 里的 `:root` 只作用于该 Shadow 树，天然隔离。**

────────────────  
1️⃣ 共享场景（qiankun 默认无 Shadow DOM）

```css
/* 主应用写的 */
:root { --primary: #1890ff; }

/* 子应用也能读/改 */
body { color: var(--primary); }   /* 读到 #1890ff */
:root { --primary: #ff4d4f; }    /* 主应用也被覆盖 */
```

因此 **多个子应用同时写同名变量会互相污染**。

────────────────  
2️⃣ 隔离场景（qiankun 开启 Shadow DOM）

```css
/* 子应用挂载在 Shadow DOM 内 */
#shadow-root
└─ :root { --primary: #52c41a; }   /* 仅本微应用可见 */

/* 主应用不会受到影响 */
```

────────────────  
3️⃣ 如何安全使用

|需求|做法|
|:--|:--|
|全局主题色|主应用统一写 `--theme-primary`，子应用只读不写|
|子应用独立主题|子应用给变量加前缀 `--sub-primary`，或开启 Shadow DOM|
|动态切换主题|主应用通过 `actions.setGlobalState({ theme })` 下发，主/子统一监听并替换 CSS 变量|

示例：主应用动态换肤

```ts
// 主应用
actions.setGlobalState({ theme: 'dark' })

// 主/子公共的 CSS
:root { --primary: v-bind(themeColor); }
```

────────────────  
一句话记忆

- **没开 Shadow DOM** → `:root` 变量全局共享，需加前缀或主应用统一管控；
- **开了 Shadow DOM** → 每个微应用拥有独立 `:root`，互不干扰。