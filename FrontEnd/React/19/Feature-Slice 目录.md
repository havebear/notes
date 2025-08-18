Feature-Slice 目录（也叫 **Feature-based / Slice-based** 结构）是一种 **按“业务功能”纵向切分** 的前端项目目录思想，核心目的是 **“让代码跟着产品功能走，而不是跟着技术类型走”**。

一句话解释  
把「登录」「订单」「用户」等每个可见的业务功能都当成一个独立的 **slice（切片）**，然后为每个 slice 建一个文件夹，里面**把该功能所需要的所有技术栈文件**（组件、路由、状态、API、类型、样式、测试）**全部放在一起**，而不是按“components / pages / services”横向分层。

──────────────────  
典型目录示例

```
src/
└── features/
    ├── auth/                       ← 登录注册功能切片
    │   ├── api/                    ← 登录接口封装
    │   ├── components/             ← LoginForm、RegisterForm
    │   ├── hooks/                  ← useAuth
    │   ├── stores/                 ← authSlice（Zustand/Redux）
    │   ├── routes/                 ← /login、/register 路由
    │   ├── types/                  ← 本功能专用类型
    │   └── index.ts                ← 统一导出公共 API
    ├── todos/
    └── …
```

──────────────────  
优点

1. **高内聚**：改登录就只动 `features/auth`，不会“牵一发动全身”。
2. **低耦合**：功能之间通过 **明确定义的 index.ts 导出** 通信，禁止跨目录直接引用内部文件，天然防止“面条依赖”。
3. **可删除**：一个功能不想要了，直接把对应文件夹删掉即可，不会留下“孤儿代码”。
4. **可复用**：公共逻辑放到 `shared/` 或 `widgets/`，真正的业务代码放在 `features/`；后者可随团队拆分为独立仓库或 npm 子包。

──────────────────  
与“传统三层目录”对比

|传统（横向）|Feature-Slice（纵向）|
|:--|:--|
|components/LoginForm.tsx|features/auth/components/LoginForm.tsx|
|pages/LoginPage.tsx|features/auth/routes/LoginPage.tsx|
|store/authSlice.ts|features/auth/stores/authSlice.ts|
|api/auth.ts|features/auth/api/auth.ts|

──────────────────  
如何落地

1. 在 `src/features` 下为每个功能建文件夹。
2. 内部再细分 `components / hooks / stores / api / routes / types` 等子目录，按需要裁剪。
3. 每个 slice 的 `index.ts` 只导出外部需要的东西（组件、hook、路由配置等）。
4. 其他 slice 只能 `import { useAuth } from '@/features/auth'`，不允许 `import … from '@/features/auth/stores/authSlice'`。

这样，一个新人看到目录就能秒懂“这个 App 有哪些功能”，再也不会被“茫茫 components 海”淹没。

## Feature-Slice 结构适合哪种项目规模

Feature-Slice **越大的项目收益越大，但小项目也能零成本起步**——只要功能 ≥ 2 个，就可以用；随业务膨胀，它能线性扩展，不会返工。

──────────────────  
按规模拆成 4 档

| 项目规模                 | 典型场景           | 是否推荐   | 选型理由                                                         |
| :------------------- | :------------- | :----- | :----------------------------------------------------------- |
| **微型**（1-3 个页面）      | 登陆 + 列表        | ✅ 直接用  | 目录层级浅，心智负担低，后续需求来了无需重构。                                      |
| **小型**（4-10 个页面）     | 用户、权限、报表       | ✅ 强烈推荐 | 开始出现“改 A 功能误伤 B 功能”的风险，Feature-Slice 天然隔离。                   |
| **中型**（10-50 个页面）    | 多角色工作台、工单、财务   | ✅ 必选   | 多人并行开发，用传统横向目录很容易冲突；Feature-Slice 让每人只在自己目录下工作。              |
| **大型**（50+ 页面 / 多团队） | 商品、订单、支付、营销、风控 | ✅ 刚需   | 与微前端、Module Federation 搭配，每个大功能可独立仓库或 npm workspace，拆分/合入无痛。 |

──────────────────  
常见顾虑 & 实际经验

1. “小项目会不会过度设计？”  
    ‑ 目录只是文件夹，无构建成本；不想用可直接删目录回退到扁平结构。
2. “代码重复？”  
    ‑ 公共逻辑下沉到 `shared/` 或单独 npm 包，Feature-Slice 只保留业务差异。
3. “团队只有 1-2 人？”  
    ‑ 仍受益：需求迭代快，功能边界清晰，减少“找文件”时间。

──────────────────  
一句话判断标准  
如果你现在或未来 **需要给“登录 / 订单 / 商品 / 报表”这类名词建文件夹**，就值得用 Feature-Slice；否则纯静态展示页可直接平铺。

## 目录

这四层目录是 **Feature-Slice 架构** 的「一级分区」，每个名字都有明确职责边界，保证“业务代码」和「通用代码」不会混成一团。

|目录|英文含义|中文定位|应该放什么|不应该放什么|
|:--|:--|:--|:--|:--|
|**app/**|Application Shell|应用外壳|全局且**只有一份**的东西：路由总表、Redux/Zustand 根 store、Apollo 客户端、i18n、全局样式、错误边界等。|任何具体业务代码（登录表单、商品列表等）禁止出现。|
|**features/**|Feature Slices|业务功能切片|按“功能”纵向切的完整闭环：登录、订单、用户、报表……每个功能一个子目录，里面包含自己所需的组件、路由、状态、API、类型、测试。|不要把通用 Input/Button 放进来；也不要出现跨功能耦合的代码。|
|**shared/**|Shared Resources|跨功能共享|纯**横向抽象**供所有功能复用：|任何带业务含义的代码（如 useAuth、ProductCard）。|
|**widgets/**|Business Widgets|可独立发布的业务组件|带业务含义、但**可能在多个仓库或微前端被复用**的“高阶组件/模块”：|不要把只在单一功能里用的组件放进来。|

记忆口诀

- **app** 像“壳”，启动就加载一次。
- **features** 像“抽屉”，每个抽屉里装一个完整功能。
- **shared** 像“工具箱”，大家都能拿。
- **widgets** 像“乐高盒”，可以带到别的项目继续拼。

这样分层后，任何新增需求都能一眼定位：  
“改登录” → 只动 `features/auth/`；  
“新增通用日期选择器” → 放 `shared/ui/DatePicker`；  
“把支付弹窗抽成独立包” → 迁到 `widgets/payment-modal`。