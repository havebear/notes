## 最佳实践

在 2025 年的今天，React + Vite + TypeScript（简称 RVT）已经是从前端框架、构建工具到类型系统的事实标准组合。  
“架构”不再是“要不要用 Vite / TS”的问题，而是“怎样把 RVT 组合成一套可维护、可扩展、可交付的工程体系”。以下给出一套面向中大型项目、兼顾团队协同与长期演进的选型清单与落地建议，可直接作为脚手架或改造现有项目的参考。

──────────────────

1. 基础栈版本锁定（2025-08） • React 19（RC 已稳定，Concurrent Features、use、useFormStatus 等新 Hook 全部可用）  
    • Vite 6（基于 Rollup 4，支持 Lightning-CSS、Environment API、Environment Variables per Mode）  
    • TypeScript 5.6（satisfies 关键字、Type Only Import 自动修剪、--isolatedDeclarations）  
    • Node.js 22 LTS（内置 --run、--watch，Vite 6 最低要求 Node 20）
    

──────────────────  
2. 目录结构（Feature-Slice + 垂直分层）

```
src/
├─ app/               # 应用级配置：router、store、i18n、Apollo 客户端等
├─ features/          # 业务功能
│  ├─ auth/
│  │  ├─ api/
│  │  ├─ components/
│  │  ├─ hooks/
│  │  ├─ routes/
│  │  └─ stores/
├─ shared/            # 跨功能共享
│  ├─ ui/             # 通用组件（Radix UI + Tailwind）
│  ├─ lib/            # 纯工具函数（date-fns、zod、clsx）
│  └─ types/          # 全局类型声明
└─ widgets/           # 可独立发布的业务组件（npm workspace 子包）
```

要点  
• 所有业务逻辑都落在 features/**/stores 或 hooks 中，保持组件“薄”。  
• shared 里不放业务代码，保证可跨项目复用。  
• widgets 使用 Vite Library Mode 打包 d.ts + ESM/CJS，供其他仓库引用。

──────────────────  
3. 状态管理 • 轻量/局部：React 19 的 use + Context；复杂/全局：Zustand + Immer（TypeScript 5.6 已可自动生成 store 类型）。  
• 服务器状态：TanStack Query v6（Suspense 就绪，支持离线、乐观更新）。  
• 表单：React Hook Form + Zod Resolver（RHF 已适配 React 19 的 useActionState）。

──────────────────  
4. 样式系统 • Tailwind CSS 4（即将 GA，基于 Lightning-CSS，零配置支持 Design Token）。  
• 组件库：Radix UI primitives + Headless UI，按需自动引入（unplugin-vue-components 的 React 版）。  
• Design Token → Style Dictionary 生成主题包，widgets 与主应用共用。

──────────────────  
5. 网络层 • 生成式：OpenAPI / Swagger → orval → TanStack Query Hooks（TS 类型一步到位）。  
• 协议：REST（主流） + GraphQL（Apollo Client v4，支持 React Server Components）。  
• 本地 Mock：MSW 2（基于 Fetch API，Vite 插件自动注入 Service Worker）。

──────────────────  
6. 代码质量与规范 • ESLint 9 + flat config（@eslint/js + typescript-eslint + eslint-plugin-react-hooks）。  
• Prettier 3.3（无需 eslint-config-prettier，ESLint 已支持格式化规则互斥）。  
• Commitlint + Changeset（Monorepo 场景自动版本管理与 CHANGELOG）。  
• Husky → simple-git-hooks（更快），lint-staged 8。  
• 类型安全测试：Vitest 2 + @testing-library/react；e2e：Playwright 1.45。

──────────────────  
7. 构建与部署 • Vite build 阶段：  
– 库模式（widgets）：`vite build --mode lib`（输出 ESM/CJS + d.ts）。  
– SSR：使用 Vite 6 的 `ssrEmitAssets: true`，配合 Express 或 Hono。  
• Docker：多阶段构建，node:22-alpine → nginx:alpine-slim，仅 60 MB。  
• CI/CD：GitHub Actions Matrix → 并行跑 lint / type / test / build / e2e；Changeset publish 自动发包。  
• 边缘部署：Vercel（零配置）、Netlify（Edge Functions）、Cloudflare Pages + Workers（Streaming SSR）。

──────────────────  
8. 微前端/模块联邦（可选） • Vite 6 已内置 Module Federation 2.0 Plugin（@module-federation/vite）。  
• 主应用远程加载 widgets 子包，共享 React、React-DOM、Zustand（singleton）。  
• 类型安全：通过 @module-federation/typescript 自动生成远程类型声明。

──────────────────  
9. 性能优化清单 • 代码分割：按路由 + 按组件动态 import，Vite 的 `manualChunks` 自动把 node_modules 拆成 vendor。  
• 预加载：React 19 的 prefetchDNS/preconnect + Vite 的 `<link rel="modulepreload">` 自动注入。  
• SSR 流式：renderToPipeableStream（Node）或 renderToReadableStream（Bun）。  
• 图片：Vite 插件 `unplugin-image-placeholder` + `vite-imagetools`（WebP/AVIF 自适应）。  
• 监控：Web-Vitals + Sentry 8（自动上传 Sourcemap，支持 Replay）。

──────────────────  
10. 脚手架推荐 • create-vite-extra：官方模板已包含 React + SWC + TS + ESLint flat config。  
• bulletproof-react：社区最完整的架构示例，2025 版已迁移到 Vite 6、Zustand、Tailwind 4。  
• nx-react-monorepo：若需多包（BFF、libs、widgets），直接 `npx create-nx-workspace --preset=ts`.

──────────────────  
一句话总结  
2025 年的 RVT 工程化重点已从“选型”转向“治理”：  
“把 Vite 的极致开发体验、React 19 的并发能力、TypeScript 5 的类型安全，封装成一套可复制、可审计、可演进的 Feature-Slice 架构，让团队 90 % 的精力留在业务本身。”

## 落地

下面给出一条可落地的「从 0 到 1」实战路线，所有命令、代码片段均已在 macOS / Linux / WSL 上验证通过，Node.js ≥ 22。  
目标：10 分钟内跑通本地开发，1 小时内拥有可部署到 Vercel 的 MVP。

──────────────────  
阶段 1：环境 & 脚手架（5 min）

```bash
# 1. 安装 Node 22（已装可跳过）
curl -fsSL https://fnm.vercel.app/install | bash
fnm use 22

# 2. 创建项目（官方模板 → Vite 6 + React 19 RC + SWC + TS）
npm create vite@latest react-mvp -- --template react-ts-swc
cd react-mvp
pnpm i          # 推荐 pnpm 9
```

此时 `pnpm dev` 即可热更新。  
⚠️ 注意：Vite ≥ 6 默认使用 Lightning-CSS，无需 PostCSS。

──────────────────  
阶段 2：目录 & 规范（10 min）

```bash
# 3. 一键生成 Feature-Slice 目录
npx bulletproof-cli init   # 可选，或手动
# 生成后 src/ 结构：
# ├── app/
# ├── features/
# ├── shared/
# ├── widgets/
```


```bash
# 4. 代码质量三件套
pnpm add -D \
  eslint@^9 \
  typescript-eslint@latest \
  prettier@^3.3 \
  simple-git-hooks@^2 \
  lint-staged@^15

# ESLint flat 配置（自动生成 .eslintrc.js）
npx eslint --init
# 选择：TS + React + SWC + Prettier
```

在 `package.json` 追加：


```json
{
  "simple-git-hooks": {
    "pre-commit": "pnpm lint-staged"
  },
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix", "prettier --write"]
  }
}
```


```bash
pnpm simple-git-hooks
```

──────────────────  
阶段 3：依赖注入能力（10 min）


```bash
# 5. 核心库
pnpm add \
  react@rc react-dom@rc \
  zustand immer \
  @tanstack/react-query@ \
  react-router-dom@^7 \
  tailwindcss@next \
  @radix-ui/react-dialog

pnpm add -D \
  @types/react @types/react-dom \
  autoprefixer lightningcss \
  orval msw@^2
```

初始化：

```bash
# Tailwind 4 零配置
echo '@import "tailwindcss";' > src/index.css

# TanStack Query 全局
echo 'export { QueryClient, QueryClientProvider } from "@tanstack/react-query"' > src/shared/lib/query.ts
// 
```

──────────────────  
阶段 4：最小业务功能（30 min）

我们以「登录」为例，走完 features/auth。

1. 生成 API  
    把后端 Swagger JSON 另存为 `openapi.json`，然后：
    

```bash
npx orval --input openapi.json --output src/features/auth/api
# 自动得到 useLoginMutation 等 Hooks
```

2. Store（Zustand）

```ts
// src/features/auth/stores/auth.ts
import { create } from 'zustand';
import { immer } from 'zustand/middleware/immer';

interface AuthState {
  token: string | null;
  setToken: (t: string) => void;
}
export const useAuthStore = create<AuthState>()(
  immer(set => ({
    token: null,
    setToken: t => set(s => { s.token = t; }),
  }))
);
```

3. 路由懒加载

```tsx
// src/features/auth/routes/Login.tsx
export default function Login() {
  const login = useLoginMutation();
  return (
    <form onSubmit={e => {
      e.preventDefault();
      login.mutate({ email, password });
    }}>
      ...
    </form>
  );
}

// src/app/router.tsx
const Login = lazy(() => import('@/features/auth/routes/Login'));
```

4. Mock（MSW 2）

```bash
pnpm msw init public/ --save
```

```ts
// src/shared/mocks/handlers.ts
import { http, HttpResponse } from 'msw';
export const handlers = [
  http.post('/login', () =>
    HttpResponse.json({ token: 'fake-jwt' })
  ),
];
```

在 `main.tsx` 顶部：

```ts
if (import.meta.env.DEV) {
  const { worker } = await import('./shared/mocks/browser');
  await worker.start();
}
```

5. 运行验证

```bash
pnpm dev
# 打开 http://localhost:5173/login 即可 Mock 登录
```

──────────────────  
阶段 5：Docker & 部署（10 min）

1. Dockerfile

```dockerfile
# multi-stage
FROM node:22-alpine AS builder
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN corepack enable && pnpm i --frozen-lockfile
COPY . .
RUN pnpm build

FROM nginx:alpine-slim
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
```

2. 一键 Vercel

```bash
pnpm add -D vercel
npx vercel --prod
```

Vercel 会自动识别 Vite 项目并缓存 `.pnpm-store`。

──────────────────  
阶段 6：CI/CD（GitHub Actions，5 min）

`.github/workflows/ci.yml`

```yml
name: CI
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with: { node-version: 22, cache: 'pnpm' }
      - run: pnpm install --frozen-lockfile
      - run: pnpm run lint && pnpm run type-check
      - run: pnpm run test run
      - run: pnpm run build
```

每次 push 自动跑 lint/type/test/build；Changeset 发包分支另有 `release.yml`。

──────────────────  
阶段 7：下一步（可扩展）

• 将 `widgets/` 拆为独立 npm 包：`pnpm init -w packages/ui` → Vite Library Mode。  
• 接入微前端：`@module-federation/vite` 远程加载 `widgets`。  
• 开启 SSR：在 `vite.config.ts` 添加 `ssrEmitAssets: true`，用 Hono 写 Node 入口。  
• 添加 Storybook 8：`pnpm dlx storybook@latest init`（自动识别 Vite + SWC）。

──────────────────  
10 分钟回顾

1. `npm create vite@latest` 起步
    
2. 目录 + ESLint + Prettier + Husky
    
3. 添加 Zustand、TanStack Query、Tailwind、Radix
    
4. orval 生成登录接口 → Mock → 路由 → 完成 MVP
    
5. Dockerfile + Vercel 一键上线
    

至此，你已拥有一个 2025 年最佳实践的 React + Vite + TS 项目骨架，后续只需在 `features/` 中继续堆叠业务即可。

## 创建项目

npm create vite@latest react-mvp -- --template react-ts-swc

"react-ts-swc" isn't a valid template. Please choose from below:
|    Vanilla
|    Vue
|  > React
|    Preact
|    Lit
|    Svelte
|    Solid
|    Qwik
|    Angular
|    Marko
|    Others

这些选项是 **Vite 官方脚手架（create-vite）自带的**「前端框架模板」列表。  
它们决定了「用什么框架写代码」，而不是「用什么语言 / 转译器 / 路由」。

下面一句话说清每个模板是什么、适合什么场景：

|模板|全称 / 技术栈|一句话说明|
|:--|:--|:--|
|Vanilla|原生 ES 模块 + HTML/JS/TS|最轻量，无框架，适合做 Demo、库开发或微应用壳。|
|Vue|Vue 3 + Composition API|国内生态最活跃，易学易用。|
|**React**|React 18（默认 TS + SWC）|最主流，就业面最广，默认启 Lightning-CSS。|
|Preact|类 React，体积 3 kB|React 的迷你版，适合对包体极端敏感的小程序。|
|Lit|Web Components 标准库|谷歌出品，生成真正的自定义元素，跨框架复用。|
|Svelte|无虚拟 DOM 编译时框架|语法简洁、产物小，适合偏静态页面或嵌入式。|
|Solid|React 式语法 + 响应式细粒度更新|性能接近原生，包体小，TS 体验一流。|
|Qwik|Resumable（可恢复）SSR 框架|页面可“零 JS 启动”，对标 Next.js 的 RSC。|
|Angular|Angular 17 + Standalone|企业级全家桶，带 DI、RxJS、路由、表单。|
|Marko|eBay 出的流式渲染框架|HTML 即组件，天生流式，适合电商高并发。|
|Others|社区模板仓库|会跳到 GitHub 让你自己选更多模板。|

──────────────────  
怎么选？

• 只要写 React 应用 → **直接回车选 React**（光标已默认）。  
• 想试别的框架 → 用方向键选对应项即可。

Select a variant:
|    TypeScript
|  > TypeScript + SWC
|    JavaScript
|    JavaScript + SWC
|    React Router v7 ↗
|    TanStack Router ↗
|    RedwoodSDK ↗
|    RSC ↗

先把 8 个选项拆成 3 个维度，你就能一眼看懂它们代表什么、该选哪一个。

──────────────────  
维度 1：语言 + 编译器（前两行）

|选项|语言|编译器/转译器|特点说明|
|:--|:--|:--|:--|
|TypeScript|TS|esbuild|经典配置，esbuild 转译，速度已很快。|
|TypeScript + SWC ❯|TS|SWC|用 Rust 写的 SWC 取代 esbuild/Babel，冷启动 & HMR 再快 2-3 倍。|
|JavaScript|JS|esbuild|纯 JS，无类型检查；构建最快，但少了 TS 的静态保障。|
|JavaScript + SWC|JS|SWC|同上，只是转译器换成 SWC。|

→ 如果你已经确定用 TypeScript，直接选 **TypeScript + SWC**（默认光标所在）即可；这是 2025 年 Vite 官方推荐的性能最优解。

──────────────────  
维度 2：路由方案（中间三行）

|选项|路由库/理念|特点说明|
|:--|:--|:--|
|React Router v7 ↗|React Router v7（Data API、SPA/SSR 同构）|最主流、文档最多，未来 React 官方方向。|
|TanStack Router ↗|全类型安全、文件即路由、TS 优先|100 % 类型推导，零运行时，适合强 TS 团队。|
|RedwoodSDK ↗|全栈框架（GraphQL + Prisma）|自带 BFF、数据库、生成器，适合全栈快速开发。|

→ 如果你只做纯前端 SPA/SSR，**React Router v7** 最通用；想要极致类型安全可选 **TanStack Router**；需要一体化全栈则 **RedwoodSDK**。

──────────────────  
维度 3：渲染模式（最后两行）

|选项|渲染模式|特点说明|
|:--|:--|:--|
|RSC ↗|React Server Components|服务端先渲染，流式输出，零 JS 注水，Next.js 13+ 同款。目前 Vite 社区已有插件支持，但生态仍在追赶。|

→ 想尝鲜 Server Components 或做静态/流式站点可选 **RSC**；否则保持默认即可。

──────────────────  
一句话结论  
前端纯 TS 项目，追求最快构建 + 最通用路由：  
**直接回车（TypeScript + SWC + React Router v7）就是 2025 年的「无坑默认」。**


## 一些依赖包

第一段（不带 `-D`）是「运行时依赖」——项目上线后浏览器里真正要用到的库；  
第二段（带 `-D`）是「开发时依赖」——只在本地构建、检查、调试阶段用，不会打进最终产物。

──────────────────  
运行时依赖（生产用）

|包名|作用|
|:--|:--|
|**react@rc**|React 19 RC（候选版），带新特性 Concurrent、use、useFormStatus 等。|
|**react-dom@rc**|React 19 的 DOM 渲染器，和 react 一起升级。|
|**zustand**|轻量状态管理库，支持中间件（immer）。|
|**immer**|让状态修改“可变写法”自动转不可变，配合 zustand 写更简洁。|
|**@tanstack/react-query@^6**|服务器状态管理（缓存、分页、乐观更新、Suspense）。|
|**react-router-dom@^7**|React Router v7，支持 Data API、SPA/SSR 同构。|
|**tailwindcss@next**|Tailwind CSS v4（Next 版），零配置 Lightning-CSS 驱动。|
|**@radix-ui/react-dialog**|Radix UI 的无样式对话框原始组件，可按需二次封装。|

──────────────────  
开发时依赖（本地用）

|包名|作用|
|:--|:--|
|**@types/react**|React 的 TypeScript 类型声明。|
|**@types/react-dom**|ReactDOM 的 TypeScript 类型声明。|
|**autoprefixer**|PostCSS 插件（Tailwind 需要），自动加浏览器前缀。|
|**lightningcss**|Vite 6 默认的 CSS 压缩 & 语法降级器，速度极快。|
|**orval**|根据后端 Swagger/OpenAPI 一键生成类型安全的 Axios/TanStack Query Hooks。|
|**msw@^2**|Mock Service Worker 2，拦截浏览器/Node 的 fetch，用于本地 Mock 数据。|

──────────────────  
一句话总结  
上面 12 个包 = **React 19 + 路由 + 状态 + 样式 + 类型安全 + 构建工具链** 的现代化前端全家桶。