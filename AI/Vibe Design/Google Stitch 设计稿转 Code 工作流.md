# Google Stitch 设计稿转 Code 工作流

> 更新时间：2026-04-15

## 先说结论

`Google Stitch` 更像是设计到代码之间的桥，而不是一个“一键生成可上线前端”的工具。

我会把它放在工作流的前半段：

> 用 Stitch 把想法变成高保真界面和设计上下文，再把截图、页面说明、`DESIGN.md` 交给 Claude Code / Codex / Cursor / Antigravity 这类开发工具实现。

真正稳的方式不是“从图直接生成整站”，而是：

> 设计稿 → 设计规则 → 组件拆分 → 页面实现 → 状态补齐 → 人工收口。

## Stitch 产物应该拆成什么

从 Stitch 出来的东西，不要只当成一张图。最好拆成三类输入：

### 1. 视觉参考

包括：
- 页面截图
- 关键状态截图
- 不同屏幕尺寸截图
- 原型交互路径

这部分解决的是“长什么样”。

### 2. 设计规则

最好整理成 `DESIGN.md`：
- 颜色
- 字体
- 间距
- 圆角
- 阴影
- 按钮规则
- 卡片规则
- 表单规则
- Do / Don't

这部分解决的是“为什么这么长，以及后续页面怎么保持一致”。

### 3. 页面说明

需要补一份人类说明：
- 页面目标是什么
- 主要用户动作是什么
- 每个区块的作用是什么
- 哪些数据是静态的，哪些数据要接接口
- 有哪些状态：loading / empty / error / success

这部分解决的是“这个界面在真实产品里怎么工作”。

## 推荐工作流

### Step 1：在 Stitch 里先收敛方向

不要一开始就想着代码。
先在 Stitch 里做几轮探索：
- 出 2-3 个视觉方向
- 选一个主方向
- 做一版核心页面
- 补关键状态或移动端版本

这个阶段的目标不是完美，而是把方向定下来。

### Step 2：整理 `DESIGN.md`

从选定的 Stitch 方案里抽设计规则。

最少要写清楚：
- 品牌气质
- 主色与辅助色
- 字体层级
- spacing scale
- 组件风格
- 禁止事项

如果不做这一步，coding agent 很容易只复刻当前截图，而无法稳定生成同风格的新页面。

### Step 3：先让 agent 做组件拆分

不要直接说“实现这个页面”。

更好的第一步是让 agent 先拆：
- 有哪些组件
- 哪些是基础组件
- 哪些是业务组件
- 哪些状态需要支持
- 哪些样式应该沉淀成 token

推荐提示词：

```text
根据这张 Stitch 页面截图和 DESIGN.md，先不要写代码。
请先输出组件拆分方案：
1. 基础组件
2. 业务组件
3. 页面结构
4. 需要支持的交互状态
5. 应该沉淀成 design token 的样式
```

### Step 4：先实现基础组件

先做：
- Button
- Card
- Input
- Badge
- Modal
- Header
- Layout

不要一上来就拼完整页面。

推荐提示词：

```text
按 DESIGN.md 先实现这些基础组件。
要求：
- 不要自由发挥新的视觉风格
- 使用现有技术栈
- 保持响应式
- 组件 API 简洁
- 先不要接真实数据
```

### Step 5：再实现页面骨架

组件稳定后，再拼页面：
- 页面布局
- 区块顺序
- 文案占位
- 静态数据
- 响应式结构

推荐提示词：

```text
使用已经实现的组件，按 Stitch 截图实现该页面的静态版本。
要求：
- 视觉优先贴近截图
- 所有样式遵守 DESIGN.md
- 不新增未定义的颜色、阴影和间距规则
- 暂时使用 mock 数据
```

### Step 6：补状态和真实逻辑

最后再补：
- loading
- empty
- error
- disabled
- success
- form validation
- API integration
- routing

这一步不能跳，因为 AI 生成的 UI 最容易只覆盖 happy path。

推荐提示词：

```text
现在补齐页面状态：loading / empty / error / success。
请保持 UI 风格不变，只补状态逻辑和状态展示。
不要重写已经稳定的组件结构。
```

### Step 7：人工收口

最后一定要人工检查：
- 是否偏离 Stitch 视觉
- 是否符合 `DESIGN.md`
- 响应式是否自然
- hover / focus / disabled 是否完整
- 文案是否真实
- 组件是否可复用
- 是否过度写死样式

这一轮很关键，因为设计转代码最容易失败在最后 20%。

## 最推荐的输入组合

交给 coding agent 时，最好同时给：

1. Stitch 截图
2. `DESIGN.md`
3. 页面说明
4. 技术栈要求
5. 现有组件路径
6. 不允许自由发挥的约束

一个比较完整的提示可以这样写：

```text
请根据 Stitch 截图和 DESIGN.md 实现这个页面。

目标：实现静态页面，先不接真实 API。
技术栈：React + TypeScript + Tailwind。
要求：
- 先阅读 DESIGN.md
- 先输出组件拆分方案，等我确认后再写代码
- 优先复用现有组件
- 不要引入新的颜色、字号、圆角、阴影规则
- 所有视觉规则以 DESIGN.md 为准
- 截图只作为视觉参考，不要硬编码像素
- 页面需要支持桌面和移动端
```

## 什么情况下可以直接生成代码

可以直接生成的情况：
- landing page
- 简单 dashboard
- onboarding 页面
- 活动页
- 设置页
- 无复杂状态的展示页

不建议直接生成的情况：
- 复杂后台系统
- 多角色权限页面
- 高度动态表单
- 强交互编辑器
- 金融 / 医疗 / 合规类核心流程
- 设计系统本身

复杂页面最好还是走：

> 组件拆分 → 组件实现 → 页面实现 → 状态补齐 → review。

## 常见坑

### 1. 只给截图，不给规则

结果通常会像，但不可持续。
下一页就开始漂。

### 2. 让 agent 一次性生成完整项目

容易出现：
- 样式写死
- 组件边界混乱
- 状态缺失
- 响应式崩掉
- 后续不好维护

### 3. 把高保真当成完整需求

设计稿只能说明“看起来怎样”，不能说明：
- 数据从哪来
- 状态怎么变
- 权限怎么处理
- 错误怎么展示
- 用户下一步去哪

这些必须额外说明。

### 4. 忽略 design token

如果没有 token，生成代码会充满散落的颜色、字号、间距。
后续改风格会很痛苦。

### 5. 没有人做最后收口

AI 生成代码很容易“差不多能看”，但正式产品需要的是：
- 一致
- 稳定
- 可维护
- 可访问

这一步还是要人来判断。

## 我推荐的目录产物

如果是一个真实项目，可以把 Stitch 相关产物整理成这样：

```text
docs/design/
├── DESIGN.md
├── pages/
│   ├── dashboard.md
│   ├── onboarding.md
│   └── settings.md
└── references/
    ├── dashboard-desktop.png
    ├── dashboard-mobile.png
    └── onboarding-flow.png
```

其中：
- `DESIGN.md` 放全局设计规则
- `pages/*.md` 放页面目标、结构、状态说明
- `references/*` 放 Stitch 导出的视觉参考

这样 coding agent 读起来会很稳定。

## 一句话工作流

如果只记一条：

> 不要让 Stitch 设计稿直接变代码；先把它变成 `DESIGN.md` 和组件拆分，再让 coding agent 实现。

## 相关资料

- Google Stitch 官方介绍：<https://blog.google/innovation-and-ai/models-and-research/google-labs/stitch-ai-ui-design/>
- Google Stitch 产品入口：<https://stitch.withgoogle.com/>
- DESIGN.md 与 Stitch 说明：<https://designmd.app/en/guides/google-stitch/>
