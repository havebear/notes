在 NestJS 里把 **Markdown 字符串** 渲染成 HTML 或纯文本，可按场景选下面 3 种方式：

────────────────────────

1. 后端 → 前端（HTML 渲染）  
    安装库

```bash
pnpm add marked
```

Service 转 HTML

```ts
import { marked } from 'marked';

const html = marked('# 标题\n- 列表');
```

把 `html` 直接返回给前端，前端用 `v-html` 或 `dangerouslySetInnerHTML` 渲染。

──────────────────────── 2. 后端 → PDF / 邮件  
安装库

```bash
pnpm add marked pdfkit
```

转 PDF

```ts
import PDFDocument from 'pdfkit';
const pdf = new PDFDocument();
pdf.text(marked.parseInline('# 标题'));
```

──────────────────────── 3. 前端纯展示（无依赖）  
React 示例

```tsx
import { marked } from 'marked';
<div dangerouslySetInnerHTML={{ __html: marked(mdString) }} />
```

Vue3 示例

```vue
<div v-html="marked(mdString)"></div>
```

---

一句话  
后端 `marked.parse()` 转 HTML，前端用 `v-html / dangerouslySetInnerHTML` 或第三方组件即可实时展示 Markdown。