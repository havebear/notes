**浏览器原生提供的“DOM 沙箱”**：把一段 **完整的 DOM + 样式 + 脚本** 封装成 **独立的子树**，与外部页面 **样式/逻辑完全隔离**。

- 外部的 CSS、JS **无法直接选中** Shadow 树里的节点。
- Shadow 树里的样式 **不会泄露**到外部，反之亦然。

### 什么时候使用

典型使用场景对照表

|场景|用 Shadow DOM？|原因|
|:--|:--|:--|
|**UI 组件库**（Ant Design、Element Plus 等）|✅ 推荐|避免组件 CSS 与业务样式冲突|
|**微前端子应用**（qiankun single-spa）|✅ 可选|彻底隔离子应用样式，无需额外 CSS 命名规则|
|**浏览器扩展**（Chrome 插件）|✅ 必用|页面样式无法污染插件 UI，反之亦然|
|**视频/音频播放器** 等嵌入式小部件|✅ 必用|内部样式、控件不受外部干扰|
|**整站微前端**（需要弹窗、全局浮层）|⚠️ 需评估|弹窗需挂载到 ShadowRoot，否则逃逸|
|**传统多页应用**|❌ 不需要|无样式隔离需求|

────────────────  
2️⃣ 决策树（30 秒判断）

1. 你是否需要 **“组件/微应用样式 100% 不泄露”**？  
    ➜ **是** → 继续 2  
    ➜ **否** → 用普通 CSS / CSS Modules 即可。
    
2. 你是否能接受 **弹窗/全局浮层需手动改挂载点**？  
    ➜ **能接受** → 用 Shadow DOM。  
    ➜ **不能接受** → 退回到「命名空间 + CSS Modules」或 iframe。

**Shadow DOM = “不用 iframe 的 iframe”**  
**需要样式隔离、作用域隔离，就用它；需要全局弹窗，就慎用。**

### 如何使用

浏览器原生

```html
<!doctype html>
<html>
  <body>
    <!-- 1. 使用自定义标签 -->
    <my-card></my-card>

    <!-- 2. 外部样式无法影响里面 -->
    <style>
      h1 { color: blue !important; }
    </style>

    <script>
      class MyCard extends HTMLElement {
        constructor() {
          super();
          // 创建 Shadow Root
          const shadow = this.attachShadow({ mode: 'open' });
          shadow.innerHTML = `
            <style>
              h1 { color: red; }
            </style>
            <h1>Hello Shadow DOM</h1>
          `;
        }
      }
      customElements.define('my-card', MyCard);
    </script>
  </body>
</html>
```