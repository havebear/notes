针对「**只读展示**」和「**编辑+预览**」两种场景，给出「**一站式选型表**」和「**即用代码包**」，直接复制即可落地。

---

| 场景                 | 推荐方案                    | 安装                             | 核心代码                                | 特点                     |
| :----------------- | :---------------------- | :----------------------------- | :---------------------------------- | :--------------------- |
| **只读展示**           | **marked + v-html**     | `pnpm add marked`              | `<div v-html="marked(md)"></div>`   | 轻量、零依赖、可流式             |
| **编辑+预览**          | **@vueup/vue-quill**    | `pnpm add @vueup/vue-quill`    | `<QuillEditor v-model="content" />` | 富文本+Markdown混合、上传图片、表格 |
| **专业 Markdown 预览** | **vue-markdown-render** | `pnpm add vue-markdown-render` | `<VueMarkdown :source="md" />`      | 高亮、目录、支持服务端 Markdown   |

---

### 1️⃣ 只读展示（marked）

```vue
<script setup>
import { marked } from 'marked'
const md = ref('# Hello **Vue 3**')
</script>
<template>
  <div v-html="marked(md)"></div>
</template>
```

### 2️⃣ 编辑+预览（Quill 富文本）

```vue
<script setup>
import { QuillEditor } from '@vueup/vue-quill'
import '@vueup/vue-quill/dist/vue-quill.snow.css'
const content = ref('<p>写点什么...</p>')
</script>
<template>
  <QuillEditor v-model:content="content" contentType="html" />
</template>
```

### 3️⃣ 专业 Markdown 渲染（vue-markdown-render）


```vue
<script setup>
import VueMarkdown from 'vue-markdown-render'
const md = ref('# 标题\n- 列表\n![img](url)')
</script>
<template>
  <VueMarkdown :source="md" />
</template>
```

---

### 4️⃣ 流式输出（SSE + marked）


```ts
// 后端
res.write(`data: ${chunk}\n\n`)
// 前端
const es = new EventSource('/stream')
es.onmessage = e => setMarkdown(marked(e.data))
```

---

### 5️⃣ 一键调用

- 复制对应代码 → 安装依赖 → 直接渲染 / 编辑即可。