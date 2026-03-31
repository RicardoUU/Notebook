# Claude Code 最佳实践

> 来源：[Claude Code 官方文档](https://code.claude.com/docs/zh-CN/best-practices)
> 从配置环境到跨并行会话扩展，充分利用 Claude Code 的提示和模式。

Claude Code 是一个**代理式编码环境**。与等待回答问题的聊天机器人不同，Claude Code 可以读取文件、运行命令、进行更改，并自主解决问题。

核心约束：**Claude 的 context window 填充速度很快，随着填充，性能会下降。** Context window 是最重要的资源。

---

## 1. 给 Claude 一种验证其工作的方式

> 包括测试、屏幕截图或预期输出，以便 Claude 可以检查自己。这是你能做的**最高杠杆**的事情。

| 策略 | 之前 | 之后 |
|---|---|---|
| **提供验证标准** | "实现一个验证电子邮件地址的函数" | "编写一个 validateEmail 函数。示例测试用例：user@example.com 为真，invalid 为假。实现后运行测试" |
| **以视觉方式验证 UI 更改** | "让仪表板看起来更好" | "[粘贴屏幕截图] 实现此设计。对结果进行屏幕截图并与原始设计进行比较。列出差异并修复它们" |
| **解决根本原因，而不是症状** | "构建失败" | "构建失败，出现此错误：[粘贴错误]。修复它并验证构建成功。解决根本原因，不要抑制错误" |

---

## 2. 先探索，再规划，最后编码

> 将研究和规划与实现分开，以避免解决错误的问题。

推荐工作流四阶段：

1. **探索**：进入 Plan Mode，Claude 读取文件并回答问题，不进行任何更改
2. **规划**：要求 Claude 创建详细的实现计划。按 `Ctrl+G` 在文本编辑器中打开计划进行直接编辑
3. **实现**：切换回 Normal Mode 并让 Claude 编码，根据其计划进行验证
4. **提交**：要求 Claude 使用描述性消息进行提交并创建 PR

> 对于范围明确且修复很小的任务（如修复拼写错误、添加日志行或重命名变量），要求 Claude 直接执行。如果你能用一句话描述 diff，跳过计划。

---

## 3. 在提示中提供具体的上下文

> 你的指令越精确，你需要的更正就越少。

| 策略 | 之前 | 之后 |
|---|---|---|
| **限定任务范围** | "为 foo.py 添加测试" | "为 foo.py 编写测试，涵盖用户已注销的边界情况。避免 mock。" |
| **指向来源** | "为什么 ExecutionFactory 有这样奇怪的 api？" | "查看 ExecutionFactory 的 git 历史并总结其 api 是如何形成的" |
| **参考现有模式** | "添加日历小部件" | "查看主页上现有小部件的实现方式以了解模式。HotDogWidget.php 是一个很好的例子。按照模式实现一个新的日历小部件。" |
| **描述症状** | "修复登录错误" | "用户报告会话超时后登录失败。检查 src/auth/ 中的身份验证流程。编写一个失败的测试来重现问题，然后修复它" |

### 提供丰富的内容

- 使用 `@` 引用文件，而不是描述代码的位置
- 直接粘贴图像（复制/粘贴或拖放）
- 提供 URL 用于文档和 API 参考
- 管道数据：`cat error.log | claude`
- 让 Claude 自己拉取上下文

---

## 4. 配置你的环境

### 编写有效的 CLAUDE.md

运行 `/init` 生成启动 CLAUDE.md 文件。

```markdown
# Code style
- Use ES modules (import/export) syntax, not CommonJS (require)
- Destructure imports when possible

# Workflow
- Be sure to typecheck when you're done making a series of code changes
- Prefer running single tests, and not the whole test suite, for performance
```

**CLAUDE.md 原则：**

| ✅ 包括 | ❌ 排除 |
|---|---|
| Claude 无法猜测的 Bash 命令 | Claude 可以通过读取代码弄清楚的任何东西 |
| 与默认值不同的代码风格规则 | Claude 已经知道的标准语言约定 |
| 测试指令和首选测试运行器 | 详细的 API 文档（改为链接） |
| 存储库礼仪（分支命名、PR 约定） | 经常变化的信息 |
| 特定于项目的架构决策 | 长解释或教程 |
| 开发者环境怪癖 | 自明的实践 |

CLAUDE.md 放置位置：
- **主文件夹** `~/.claude/CLAUDE.md`：适用于所有会话
- **项目根目录** `./CLAUDE.md`：检入 git 与团队共享
- **父目录**：对 monorepos 有用
- **子目录**：按需拉入

### 配置权限

使用 `/permissions` 来允许安全命令，或 `/sandbox` 用于操作系统级隔离。

### 使用 CLI 工具

告诉 Claude 使用 `gh`、`aws`、`gcloud`、`sentry-cli` 等 CLI 工具与外部服务交互。

### 连接 MCP 服务器

运行 `claude mcp add` 连接 Notion、Figma、数据库等外部工具。

### 设置 Hooks

Hooks 在 Claude 工作流中的特定点自动运行脚本，是确定性的，保证操作发生。

### 创建 Skills

在 `.claude/skills/` 中创建 `SKILL.md` 文件，为 Claude 提供域知识和可重用工作流。

### 创建自定义 Subagents

在 `.claude/agents/` 中定义专门的助手，Claude 可以委托给它们处理隔离的任务。

---

## 5. 有效沟通

### 提出代码库问题

问 Claude 你会问资深工程师的问题：
- 日志如何工作？
- 我如何创建新的 API 端点？
- `foo.rs` 第 134 行的 `async move { ... }` 做什么？

### 让 Claude 采访你

对于更大的功能，让 Claude 先采访你：

```
I want to build [brief description]. Interview me in detail using the AskUserQuestion tool.
Ask about technical implementation, UI/UX, edge cases, concerns, and tradeoffs.
Keep interviewing until we've covered everything, then write a complete spec to SPEC.md.
```

---

## 6. 管理你的会话

### 尽早且经常改正方向

- **`Esc`**：中途停止 Claude，context 保留
- **`Esc + Esc` 或 `/rewind`**：打开 rewind 菜单，恢复之前的对话和代码状态
- **`"撤销那个"`**：让 Claude 恢复其更改
- **`/clear`**：在不相关的任务之间重置 context

> 如果你在一个会话中对同一问题改正了 Claude 两次以上，运行 `/clear` 并使用更具体的提示重新开始。

### 积极管理 Context

- 在任务之间频繁使用 `/clear`
- 运行 `/compact <instructions>` 自定义压缩
- 使用 `/btw` 进行快速问题，不进入对话历史
- 在 CLAUDE.md 中自定义压缩行为

### 使用 Subagents 进行调查

```
Use subagents to investigate how our authentication system handles token
refresh, and whether we have any existing OAuth utilities I should reuse.
```

Subagents 在单独的 context windows 中运行并报告摘要，不会使主对话混乱。

### 恢复对话

```bash
claude --continue    # 恢复最近的对话
claude --resume      # 从最近的对话中选择
```

使用 `/rename` 给会话起描述性名称。

---

## 7. 自动化和扩展

### 运行非交互模式

```bash
claude -p "Explain what this project does"                       # 一次性查询
claude -p "List all API endpoints" --output-format json          # 结构化输出
claude -p "Analyze this log file" --output-format stream-json    # 流式处理
```

### 运行多个 Claude 会话

- **Claude Code 桌面应用**：以视觉方式管理多个本地会话
- **Claude Code 在网络上**：在 Anthropic 的安全云 VM 上运行
- **Agent teams**：多个会话的自动协调

**Writer/Reviewer 模式**：一个会话编写代码，另一个会话审查。

### 跨文件扇出

```bash
for file in $(cat files.txt); do
  claude -p "Migrate $file from React to Vue. Return OK or FAIL." \
    --allowedTools "Edit,Bash(git commit *)"
done
```

---

## 8. 避免常见失败模式

| 失败模式 | 问题 | 修复 |
|---|---|---|
| **厨房水槽会话** | 一个会话中混杂不相关任务 | 在不相关任务之间 `/clear` |
| **一次又一次地改正** | context 被失败方法污染 | 两次失败后 `/clear`，写更好的初始提示 |
| **过度指定的 CLAUDE.md** | 重要规则在噪音中丢失 | 无情地修剪 |
| **信任然后验证的差距** | 看起来合理但不处理边界情况 | 始终提供验证（测试、脚本、屏幕截图） |
| **无限探索** | 不限定范围的调查填满 context | 狭隘限定调查或使用 subagents |

---

## 9. 培养你的直觉

注意什么有效。当 Claude 产生很好的输出时，注意你做了什么：提示结构、你提供的 context、你所在的模式。当 Claude 遇到困难时，问为什么。

随着时间推移，你会知道何时具体、何时开放、何时规划、何时探索、何时清除 context、何时让它累积。
