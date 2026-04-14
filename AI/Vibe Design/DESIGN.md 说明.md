# DESIGN.md 说明

> 更新时间：2026-04-14
> 主题：`DESIGN.md` 是什么、为什么重要、适合怎么用，以及它和 Google Stitch / 代码生成工作流的关系。

## 一句话理解

`DESIGN.md` 可以理解为：**一份给 AI 和人类都能读的设计系统说明文件**。

它的目标不是替代完整设计稿，而是把项目里的视觉规则、设计原则和组件约束，用结构化 Markdown 写下来，让像 `Google Stitch`、coding agent、设计 agent 这类工具能更稳定地生成一致的界面。

## 1. 官方定义里它是什么

根据 Google 官方对 `Stitch` 的介绍，`DESIGN.md` 是一个 **agent-friendly markdown file**，用于把设计规则导出到其他设计工具或编码工具中，也可以从其他地方导入回 Stitch。

Google 在官方文章里强调了两点：
- 它可以让你从任意 URL 提取设计系统
- 它也可以作为一个跨工具复用的设计规则文件

所以它的核心定位不是“设计稿文件”，而是：
- 设计系统的可移植文本表示
- 设计与代码之间的共享上下文
- AI 工具可读取的视觉规范入口

## 2. 为什么它会重要

传统设计交付通常有两个问题：
- 设计规范散落在 Figma 页面、口头约定、组件库和截图里
- AI 生成界面时经常只能“猜”品牌风格和组件规则

`DESIGN.md` 解决的是这个问题：
- 把视觉规则文本化
- 把品牌约束变成可重复使用的输入
- 让不同工具更容易共享同一套设计上下文

所以它的价值并不只是“多一个 md 文件”，而是让设计系统开始变成：
- 可版本管理
- 可复用
- 可被 agent 理解
- 可进入设计到代码流水线

## 3. 它通常会写什么

虽然 Google 官方公开文章没有给出完整固定 schema，但结合 Stitch 的定位和当前社区实践，`DESIGN.md` 一般会包含这些部分：

### 3.1 品牌与设计原则
- 产品气质
- 品牌关键词
- 不希望出现的风格
- 体验目标

例如：
- 可信、克制、专业
- 不要过度拟物
- 首屏必须给人安全感

### 3.2 颜色系统
- 主色、辅助色、语义色
- 背景 / surface / text / border 颜色
- 不同颜色的使用规则

### 3.3 字体与排版
- 字体家族
- 标题、正文、说明文字的层级
- 字重、字号、行高
- 不同端的排版偏好

### 3.4 间距与布局
- spacing scale
- 圆角
- 阴影
- 栅格 / 密度 / 页面宽度倾向

### 3.5 组件模式
- 按钮长什么样
- 卡片怎么组织信息
- 表单组件的状态和风格
- 导航、列表、弹窗的通用模式

### 3.6 约束与禁忌
- 不要使用哪些视觉语言
- 不要让 CTA 淹没在背景里
- 不要混用多个强调色
- 不要过度依赖阴影和渐变

这一类内容对 AI 很关键，因为它比“做得高级一点”更可执行。

## 4. 它和普通设计稿有什么不同

`DESIGN.md` 不是 Figma 的替代品，也不是最终交付物本身。

它更像：
- 设计系统的文字版源文件
- 视觉规则的 portable layer
- design-to-code 工作流里的“共享上下文文件”

简单对比：

### Figma / 设计稿擅长
- 精准视觉呈现
- 页面间关系
- 高保真评审
- 团队协作和注释

### DESIGN.md 擅长
- 给 AI 一个统一设计上下文
- 跨工具复用风格规则
- 让代码生成更稳定
- 让设计规则跟仓库一起版本化

所以它们更像互补关系，而不是替代关系。

## 5. 它在 Stitch 工作流里怎么用

按照 Google 官方对 Stitch 的描述，`DESIGN.md` 的关键角色是：
- 从 Stitch 导出设计规则
- 导入到另一个 Stitch 项目
- 导入到开发 / 编码工具里继续使用

这意味着典型工作流会变成：

1. 在 Stitch 里生成或整理一个视觉方向
2. 抽取 / 形成设计系统
3. 导出成 `DESIGN.md`
4. 把 `DESIGN.md` 放到代码仓库或其他工具里
5. 让 coding agent 生成新页面时遵守这些规则

这样做的最大好处是：
- 新页面不会每次都从“风格猜测”开始
- 不同 agent 生成的结果更容易保持一致
- 设计语言可以跨项目迁移

## 6. 它和 coding agent 的关系

如果你已经在用 Claude Code、Cursor、Codex、Gemini CLI 这类工具，`DESIGN.md` 的价值会很直接：

它能成为前端生成时的上游约束。

例如你可以要求 agent：
- 按 `DESIGN.md` 实现一个 dashboard 页面
- 按 `DESIGN.md` 的组件规则重写 onboarding
- 检查当前页面是否违反 `DESIGN.md` 的设计原则

这比只说“做得像我们品牌一点”有效得多，因为：
- 规则更明确
- 结果更一致
- 修改成本更低

## 7. 适合什么团队

最适合：
- 正在试 AI 生成 UI 的团队
- 有基础 design system，但还没很好沉淀文本规范的团队
- 前端和设计协作频繁的团队
- 想把设计规则一起纳入 Git 管理的团队

尤其适合这些场景：
- 多个 agent / 工具同时生成页面
- 新产品方向快速扩张
- 需要跨项目复用品牌规则
- 设计和开发之间想减少来回解释

## 8. 它的局限

### 8.1 它不是完整设计系统平台

`DESIGN.md` 很有用，但它不是完整 design ops 工具。
它不能天然替代：
- 组件库治理
- 复杂 token 管理
- Figma 协作流程
- 精细交互稿和审稿流程

### 8.2 它写得不好就会变成“模糊 prompt 汇总”

如果里面只有这种话：
- 看起来现代一点
- 更有科技感
- 高级一些

那 AI 还是只能猜。

一个好的 `DESIGN.md` 应该尽量：
- 明确
- 可执行
- 有边界
- 有风格判断，也有具体规则

### 8.3 它可能过时

如果设计系统在变，但 `DESIGN.md` 不更新，就会出现：
- 设计稿一套
- 代码生成一套
- agent 理解一套

所以它要被当成“活文档”，而不是一次性导出文件。

## 9. 我建议的写法

如果你自己要写一份 `DESIGN.md`，建议结构像这样：

```md
# DESIGN.md

## Brand
- 关键词：可信、冷静、专业
- 避免：过度炫技、强烈霓虹色、过密布局

## Colors
- Primary: #2563EB
- Surface: #FFFFFF
- Text Primary: #111827
- Accent 使用要克制，只用于关键 CTA

## Typography
- Heading: Inter / 600
- Body: Inter / 400
- 小屏设备优先保证可读性，不要过度压缩行高

## Spacing
- 使用 4/8/12/16/24/32 spacing scale
- 卡片内边距优先 16 或 24

## Components
### Button
- 主按钮：实色背景，圆角中等，阴影轻
- 次按钮：描边，避免与主按钮争夺视觉优先级

### Card
- 信息层级先标题再关键数字再辅助说明

## Do / Don't
- Do: 强调清晰层级和可读性
- Don't: 不要把多个重点 CTA 放在同一视觉层
```

重点不是格式有多神圣，而是让规则：
- 结构稳定
- 容易读
- 容易维护
- 容易被 agent 消化

## 10. 我对它的判断

我觉得 `DESIGN.md` 是一个 **很有长期价值的小文件**。

它短期看像是 Stitch 的附属能力，但长期看，它可能会变成：
- AI 时代设计系统的一种通用中间层
- 设计规范与代码仓库之间的新接口
- design-to-code 自动化里非常关键的一层上下文

如果要一句话总结：

> `DESIGN.md` 的真正价值，不是描述设计，而是让设计规则可以在 AI 工具之间流动。

## 11. 相关资料

### Google 官方
- Stitch 产品页：<https://stitch.withgoogle.com/>
- Stitch 官方文章：<https://blog.google/innovation-and-ai/models-and-research/google-labs/stitch-ai-ui-design/>

### 文档链接
- Google Stitch 官方入口：<https://stitch.withgoogle.com/>
- Google Stitch 官方文章（含 `DESIGN.md` 提法）：<https://blog.google/innovation-and-ai/models-and-research/google-labs/stitch-ai-ui-design/>
- 社区整理的 `DESIGN.md` 与 Stitch 说明：<https://designmd.app/en/guides/google-stitch/>
- 一篇解释 `DESIGN.md` 背景的文章：<https://www.designwhine.com/what-the-hell-is-google-stitchs-design-md-and-why-should-you-care/>

### 社区补充理解
- DESIGN.md + Google Stitch 配置说明：<https://designmd.app/en/guides/google-stitch/>
- 介绍 DESIGN.md 的文章：<https://www.designwhine.com/what-the-hell-is-google-stitchs-design-md-and-why-should-you-care/>

## 12. 可继续补充的方向

后续可以继续写：
- 如何为你的产品手写第一版 `DESIGN.md`
- `DESIGN.md` 和 `CLAUDE.md` / `AGENTS.md` 的相似与区别
- `DESIGN.md` 如何接入前端组件库和 design token 流程
