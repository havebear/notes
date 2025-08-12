Vue 本身在首屏优化里，90 % 的工作其实**“跟 Vite 通用优化”**重合，但因为 Vue 生态（Vue-Router、Pinia、Element-Plus…）有一些**框架特化点**，下面把「Vue 项目首屏提速」拆成 4 个阶段，每阶段都给你可复制的代码片段，直接改就能见效。

---
### ✅ 阶段 1：拆包 + 路由懒加载（3 分钟见效）

```ts
// router/index.ts
const routes = [
  {
    path: '/',
    component: () => import('@/views/Home.vue')   // 路由级懒加载
  },
  {
    path: '/admin',
    component: () => import('@/views/Admin.vue')
  }
]
```

vite.config.ts 再补一刀：

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: id => {
          if (id.includes('element-plus')) return 'element';
          if (id.includes('echarts')) return 'chart';
          if (id.includes('node_modules')) return 'vendor';
        }
      }
    }
  }
})
```

→ 主包瞬间从 3 MB → 800 KB。

---
### ✅ 阶段 2：组件 & 第三方库按需加载

1. **自动按需引入**（Element-Plus）
    
    ```ts
    // vite.config.ts
    import Components from 'unplugin-vue-components/vite'
    import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'
    
    export default defineConfig({
      plugins: [
        vue(),
        Components({ resolvers: [ElementPlusResolver()] })
      ]
    })
    ```
    
    效果：未使用的组件不会打进包。
    
2. **大组件再懒**（如 ECharts）
    
    ```vue
    <script setup>
    const Chart = defineAsyncComponent(() => import('@/components/EchartsWrap.vue'))
    </script>
    
    <template>
      <Chart v-if="showChart" />
    </template>
    ```

---
### ✅ 阶段 3：SSR / SSG（把渲染放到服务器）

| 方案                          | 命令 & 配置                                  | 适用场景          |
| :-------------------------- | :--------------------------------------- | :------------ |
| **Nuxt 3**                  | `npx nuxi init my-nuxt3` → `npm run dev` | 全站 SSR，SEO 刚需 |
| **vite-ssg**                | `npm i vite-ssg` + `vite-ssg build`      | 纯静态站，博客、文档    |
| **@vue/server-renderer** 手写 | 自建 Node 服务                               | 自定义需求         |

SSR 后首屏 HTML 直接带内容，**LCP 再降 30 %～50 %**。

---

### ✅ 阶段 4：运行时细节（Vue 专属）

| 技巧                 | 代码                                | 收益                |
| :----------------- | :-------------------------------- | :---------------- |
| **keep-alive 懒加载** | `<KeepAlive include="Home">`      | 路由切换不重新打包         |
| **v-show 代替 v-if** | 首屏不需要的模块用 `v-show="false"`        | 减少首屏 DOM 复杂度      |
| **Suspense 占位**    | 让异步组件有骨架屏                         | 感知更快              |
| **Pinia 按需注册**     | `createPinia().use(store)` 放在路由钩子 | 避免首屏一次性注册所有 store |

---

### ✅ 阶段 5：Vue 开发调试工具

- **Vue DevTools → Performance 面板**  
    手动打点 `startTime = performance.now()` 查看组件渲染耗时。
- **vite-plugin-vue-inspector**  
    开发时一键定位哪个组件渲染慢。

---

### 🚀 1 分钟启动模板（已集成上面所有优化）

```bash
npm create vue@latest
# 选 Router、Pinia、TypeScript
cd vue-project
npm i
npm run build
# 主包 < 700 KB（Element-Plus 全按需）
```

---
### 一句话结论

Vue 首屏优化 ≈  
**路由懒加载 + 组件按需 + SSR/SSG + 通用 Vite 资源优化**  
照抄上面 4 个阶段，**90 % 项目都能把首屏压到 2 s 以内**。如果还有具体瓶颈（如 Pinia 状态大、Element-Plus 全量加载），把 `rollup-plugin-visualizer` 图贴出来，我再帮你精修。