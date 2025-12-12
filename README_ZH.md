# Gemini CLI 实用技巧与窍门

**本指南涵盖约30个有效使用 Gemini CLI 进行智能编码的专业技巧**

**[Gemini CLI](https://github.com/google-gemini/gemini-cli)** 是一个开源 AI 助手，将 Google Gemini 模型的强大功能直接带入您的[终端](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=The%20Gemini%20CLI%20is%20an,via%20a%20Gemini%20API%20key)。它作为一个对话式的"智能"命令行工具运行——意味着它可以理解您的请求，选择工具（如运行 shell 命令或编辑文件），并执行多步骤计划来协助您的开发[工作流程](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=The%20Gemini%20CLI%20%20is,understanding%20of%20the%20developer%20workflow)。

实际上，Gemini CLI 就像一个超级强化的编程伙伴和命令行助手。它在编码任务、调试、内容生成甚至系统自动化方面表现出色，全部通过自然语言提示完成。在深入探讨专业技巧之前，让我们快速回顾一下如何设置 Gemini CLI 并开始使用。

## 入门指南

**安装：** 您可以通过 npm 安装 Gemini CLI。全局安装请使用：

```bash
npm install -g @google/gemini-cli
```

或者使用 `npx` 无需安装直接运行：

```bash
npx @google/gemini-cli
```

Gemini CLI 在所有主要平台上都可用（使用 Node.js/TypeScript 构建）。安装完成后，只需在终端中运行 `gemini` 命令即可启动交互式 [CLI](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Interactive%20Mode%20,conversational%20session)。

**身份验证：** 首次使用时，您需要通过 Gemini 服务进行身份验证。您有两个选择：(1) **Google 账户登录（免费层）** - 这让您免费使用 Gemini 2.5 Pro，享有慷慨的使用限制（约每分钟 60 次请求和每天 1,000 次请求）。启动时，Gemini CLI 会提示您使用 Google 账户登录（无需账单信息）。(2) **API 密钥（付费或更高级别访问）** - 您可以从 Google AI [Studio](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=1,key%20from%20Google%20AI%20Studio) 获取 API 密钥，并设置环境变量 `GEMINI_API_KEY` 来使用它。

使用 API 密钥可以提供更高的配额和企业数据使用保护；付费使用中的提示不会用于训练，但日志可能会为安全目的而保留。

例如，在您的 shell 配置文件中添加：

```bash
export GEMINI_API_KEY="YOUR_KEY_HERE"
```

**基本用法：** 启动交互式会话，只需运行不带参数的 `gemini`。您会看到一个 `gemini>` 提示符，可以在其中输入请求或命令。例如：

```bash
$ gemini
gemini> Create a React recipe management app using SQLite
```

然后您可以观看 Gemini CLI 创建文件、安装依赖、运行测试等，以满足您的请求。如果您偏好一次性调用（非交互式），使用带有提示的 `-p` 标志，例如：

```bash
gemini -p "Summarize the main points of the attached file. @./report.txt"
```

这将输出单个响应并退出。您也可以将输入传递给 Gemini CLI：例如，`echo "Count to 10" | gemini` 会通过标准输入提供提示。

**CLI 界面：** Gemini CLI 提供丰富的类似 REPL 的界面。它支持**斜杠命令**（以 `/` 为前缀的特殊命令，用于控制会话、工具和设置）和**感叹号命令**（以 `!` 为前缀，直接执行 shell 命令）。我们将在下面的专业技巧中介绍其中的许多命令。默认情况下，Gemini CLI 在安全模式下运行，任何修改系统的操作（写入文件、运行 shell 命令等）都会要求确认。当提出工具操作时，您会看到差异或命令，并被提示（`Y/n`）批准或拒绝它。这确保 AI 不会在未经您同意的情况下进行不必要的更改。

了解了基础知识后，让我们探索一系列专业技巧和隐藏功能，帮助您充分利用 Gemini CLI。每个技巧首先提供一个简单的示例，然后是更深入的细节和细微差别。这些技巧融合了工具创建者（如 Taylor Mullen）和 Google 开发者关系团队的建议，以及更广泛社区的见解，旨在成为 Gemini CLI 高级用户的**权威指南**。

## 技巧1：使用 `GEMINI.md` 实现持久化上下文

**快速使用场景：** 无需在提示词中重复写提示词。通过创建 `GEMINI.md` 文件提供项目特定的上下文或指令，这样 AI 始终具有重要的背景知识，无需每次都被告知。

在处理项目时，您通常有一些总体细节——例如编码风格指南、项目架构或重要事实——希望 AI 记住。Gemini CLI 允许您将这些信息编码到一个或多个 `GEMINI.md` 文件中。只需在项目中创建 `.gemini` 文件夹（如果尚不存在），并添加名为 `GEMINI.md` 的 Markdown 文件，其中包含您希望 AI 持久保存的任何注释或指令。例如：

```markdown
# Phoenix 项目 - AI 助手
# - All Python code must follow PEP 8 style.  
# - Use 4 spaces for indentation.  
# - The user is building a data pipeline; prefer functional programming paradigms.
- 所有 Python 代码必须遵循 PEP 8 风格。
- 使用 4 个空格进行缩进。
- 用户正在构建数据管道；优先考虑函数式编程范式。
```

将此文件放在您的项目根目录（或子目录中，以获得更细粒度的上下文）。现在，每当您在该项目中运行 `gemini` 时，它都会自动将这些指令加载到上下文中。这意味着模型将始终准备好这些指令，避免需要在每个提示前添加相同的指导。

**工作原理：** Gemini CLI 使用分层上下文加载系统。它将**全局上下文**（来自 `~/.gemini/GEMINI.md`，您可用于跨项目默认值）与您的**项目特定的 `GEMINI.md`** 甚至子文件夹中的上下文文件结合起来。更具体的文件会覆盖更通用的文件。您可以随时使用以下命令检查加载了什么上下文：

```bash
/memory show
```

这将显示 AI 看到的完整组合上下文。如果您更改了 `GEMINI.md`，使用 `/memory refresh` 重新加载上下文而无需重新启动会话。

**专业提示：** 使用 `/init` 斜杠命令快速生成初始的 `GEMINI.md`。在新项目中运行 `/init` 会创建一个模板上下文文件，包含检测到的技术栈、项目摘要等信息。然后您可以编辑和扩展该文件。对于大型项目，考虑将上下文分解为多个文件，并使用 `@include` 语法将它们**导入**到 `GEMINI.md` 中。例如，您的主 `GEMINI.md` 可以包含类似 `@./docs/prompt-guidelines.md` 的行来引入额外的上下文文件。这使您的指令保持组织有序。

通过精心制作的 `GEMINI.md`，您实际上为 Gemini CLI 提供了项目需求和约定的"记忆"。这种**持久化上下文**会带来更相关的响应，减少来回的提示工程。

## 技巧2：创建自定义斜杠命令

**快速使用场景：** 通过定义自己的斜杠命令来加速重复性任务。例如，您可以创建一个命令 `/test:gen` 从描述生成单元测试，或者 `/db:reset` 来删除并重新创建测试数据库。这通过针对您的工作流程定制的单行扩展了 Gemini CLI 的功能。

Gemini CLI 支持**自定义斜杠命令**，您可以在简单的配置文件中定义它们。在底层，这些本质上都是预定义的提示模板。要创建一个，在 `~/.gemini/` 下创建 `commands/` 目录用于全局命令，或在您项目的 `.gemini/` 文件夹中用于项目特定命令。在 `commands/` 内部，为每个新命令创建一个 TOML 文件。文件名格式决定了命令名称：例如文件 `test/gen.toml` 定义了命令 `/test:gen`。

让我们通过一个示例。假设您想要一个命令来根据需求描述生成单元测试。您可以创建 `~/.gemini/commands/test/gen.toml`，内容如下：

```markdown
# 调用方式：/test:gen "测试描述"
description = "根据需求生成单元测试"
prompt = """
您是专业的测试工程师。基于以下需求，请使用 Jest 框架编写一个全面的单元测试。

需求：{{args}}
"""
```

现在，重新加载或重启 Gemini CLI 后，您可以简单地输入：

```bash
/test:gen "确保登录按钮在成功后重定向到仪表板"
```

Gemini CLI 会识别 `/test:gen` 并用提供的参数替换提示模板中的 `{{args}}`（在本例中是需求）。然后 AI 将相应地生成 Jest 单元测试。`description` 字段是可选的，但在您运行 `/help` 或 `/tools` 列出可用命令时使用。

这个机制极其强大——实际上，您可以用自然语言为 AI 编写脚本。社区创建了许多有用的自定义命令。例如，Google 的 DevRel 团队分享了数十个实用工作流程命令（通过开源仓库），以展示了如何为常见流程编写脚本，如创建 API 文档、清理数据或[构建模板代码](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=,to%20generate%20a%20better%20output)。通过定义自定义命令，您可以将复杂的提示（或一系列提示）打包成可重用的快捷方式。

**专业提示：** 自定义命令也可用于强制格式化或为某些任务为 AI 应用"角色"。例如，您可能有一个 `/review:security` 命令，总是在提示前加上"您是安全审计员..."来审查代码漏洞。这种方法确保 AI 在处理特定类别任务时的响应一致性。

要与团队共享命令，您可以将 TOML 文件提交到您项目的仓库中（在 `.gemini/commands` 目录下）。拥有 Gemini CLI 的团队成员在项目中工作时会自动获取这些命令。这是在团队中**标准化 AI 辅助工作流程**的好方法。

## 技巧3：使用自己的 `MCP` 服务器扩展 Gemini

**快速使用场景：** 假设您希望 Gemini 与外部系统或未内置的自定义工具交互——例如，查询专有数据库或与 Figma 设计集成。您可以通过运行自定义**模型上下文协议 (MCP) 服务器**并将其插入 Gemini CLI 来实现这一点。

Gemini CLI 开箱即用地提供了几个 MCP 服务器（例如，支持 Google 搜索、代码执行沙箱等），您也可以添加自己的MCP服务器。MCP 服务器本质上是一个外部进程（可以是本地脚本、微服务甚至云端点），使用简单的协议来处理 Gemini 的任务。这种架构极大[提升了 Gemini CLI 的可扩展性](https://blog.google/technology/developers/introducing-gemini-cli-open-source-ai-agent/#:~:text=,interactively%20within%20your%20scripts)。

**MCP 服务器示例：** 一些社区和 Google 提供的 MCP 集成包括**Figma MCP**（从 Figma 获取设计细节）、**剪贴板 MCP**（读取/写入系统剪贴板）等。实际上，在内部演示中，Gemini CLI 团队展示了一个"Google Docs MCP"服务器，允许直接[将内容保存到 Google Docs](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=%2A%20Utilize%20the%20google,summary%20directly%20to%20Google%20Docs)。即，每当 Gemini 需要执行内置工具无法处理的操作时，它可以委托给您的 MCP 服务器。

**如何添加一个MCP服务器：** 您可以通过 `settings.json` 或使用 CLI 配置 MCP 服务器。对于快速设置，尝试 CLI 命令：

```bash
gemini mcp add myserver --command "python3 my_mcp_server.py" --port 8080
```

这会注册一个名为"myserver"的服务器，Gemini CLI 将通过运行给定命令（这里是一个 Python 模块）在端口 8080 上启动它。在 `~/.gemini/settings.json` 中，它会在 `mcpServers` 下添加一个条目。例如：

```json
"mcpServers": {
  "myserver": {
    "command": "python3",
    "args": ["-m", "my_mcp_server", "--port", "8080"],
    "cwd": "./mcp_tools/python",
    "timeout": 15000
  }
}
```

此配置告诉 Gemini 如何启动 MCP 服务器及其[位置](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Example%20)。一旦运行，该服务器提供的工具就可被 Gemini CLI 使用。您可以使用斜杠命令列出所有 MCP 服务器及其工具：

```bash
/mcp
```

这将显示任何注册的[服务器及其暴露的工具名称](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Command%20Description%20,List%20active%20extensions)。

**MCP 的强大之处：** MCP 服务器可以提供**丰富的多模态结果**。例如，通过 MCP 提供的工具可以返回图像或格式化表格作为 Gemini CLI 响应的一部分。它们还支持 OAuth 2.0，因此您可以安全地连接到 API（如 Google 的 API、GitHub 等），而无需暴露凭据。本质上，如果您能编写它，就可以将其包装为 MCP 工具——将 Gemini CLI 变成协调许多服务的中心。

**默认与自定义：** 默认情况下，Gemini CLI 的内置工具涵盖了很多功能（读取文件、网络搜索、执行 shell 命令等），但 MCP 让您能够更进一步。一些高级用户创建了 MCP 服务器来与内部系统交互或执行专门的数据处理。例如，您可以有一个 `database-mcp` 提供 `/query_db` 工具用于在公司数据库上运行 SQL 查询，或一个 `jira-mcp` 通过自然语言创建工单。

创建自己的MCP服务器时，请注意安全性：默认情况下，自定义 MCP 工具需要确认，除非您将它们标记为受信任。您可以通过设置（如服务器的 `trust: true` 以自动批准其工具操作）或通过将特定的安全工具列入白名单、将危险工具列入黑名单来控制安全性。

简而言之，**MCP 服务器解锁了无限的集成可能性**。MCP是专业功能，让 Gemini CLI 成为您的 AI 助手与您需要它与之工作的任何系统之间的粘合剂。如果您有兴趣构建一个，请查看官方 MCP 指南和社区示例。

## 技巧4：利用记忆添加与召回

**快速使用场景：** 通过将重要事实添加到 AI 的长期记忆中来保持它们触手可及。例如，在弄清楚数据库端口或 API 令牌后，您可以执行：

```bash
/memory add "我们的预发布 RabbitMQ 在端口 5673 上"
```

这将存储该事实，[以便您（或 AI）以后不会忘记](https://binaryverseai.com/gemini-cli-open-source-ai-tool/#:~:text=Gemini%20CLI%20Ultimate%20Agent%3A%2060,a%20branch%20of%20conversation)。

`/memory` 命令提供了简单但强大的*持久化记忆*机制。当您使用 `/memory add <text>` 时，给定的文本会附加到您项目的全局上下文中（从技术上说，它会被保存到全局 `~/.gemini/GEMINI.md` 文件或项目的 [`GEMINI.md`](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=,load%20memory%20from%20%60GEMINI.md) 中）。这有点像做笔记并将其固定到 AI 的虚拟公告板上。一旦添加，AI 将在未来的交互中始终在提示上下文中看到该笔记，跨会话有效。

考虑一个例子：您在调试问题时发现了一个不明显的见解（"配置标志 `X_ENABLE` 必须设置为 `true`，否则服务启动失败"）。如果将此添加到记忆中，后来如果您的 AI 正在讨论相关问题，它不会忽略这个关键细节——它在上下文中。

**使用 `/memory`：**

* `/memory add "<text>"` - 向记忆添加事实或注释（持久化上下文）。这会立即使用新条目更新 `GEMINI.md`。

* `/memory show` - 显示记忆的完整内容（即当前加载的组合上下文文件）。

* `/memory refresh` - 从磁盘重新加载上下文（如果您在 Gemini CLI 外部手动编辑了 `GEMINI.md` 文件，或者多个人在协作编辑它，这很有用）。

因为记忆以 Markdown 存储，您也可以手动编辑 `GEMINI.md` 文件来策划或组织信息。`/memory` 命令是为了对话中的便利而存在，这样您就不必打开编辑器。

**提示：** 此功能非常适合"决策日志"。如果您在聊天期间确定了一种方法或规则（例如，使用某个特定库或约定的代码风格），将其添加到记忆中。然后 AI 会回忆起该决定并在以后避免与之矛盾。它对于可能跨越数小时或数天的长会话特别有用——通过保存关键点，您可以减少模型在对话变长时忘记早期上下文的倾向。

另一个用途是个人笔记。因为 `~/.gemini/GEMINI.md`（全局记忆）为所有会话加载，您可以在其中放置一般偏好或信息。例如，"用户姓名是 Alice。请礼貌说话，避免俚语。"这就像配置 AI 的角色或全局知识。但请注意，全局记忆适用于*所有*项目，所以不要用项目特定信息使其杂乱。

总之，**记忆添加与召回**帮助 Gemini CLI 维护状态。将其视为随项目增长的知识库。使用它来避免重复自己，或提醒 AI 一些事实,否则它需要从头重新探索。

## 技巧5：使用检查点机制和 `/restore` 作为撤销按钮

**快速使用场景：** 如果 Gemini CLI 对您的文件进行了一系列您不满意的更改，您可以*立即回滚*到先前的状态。在启动 Gemini 时（或在设置中）启用检查点机制，并使用 `/restore` 命令像轻量级 Git 撤销一样撤销更改。

Gemini CLI 的**检查点机制**功能充当安全网。启用后，CLI 在每次修改文件的工具执行*之前*对项目文件进行快照。如果出现问题，您可以恢复到最后已知的良好状态。这本质上是 AI 操作的版本控制，无需您每次都手动提交到 Git。

**如何使用它：** 您可以通过使用 `--checkpointing` 标志启动 CLI 来启用检查点机制：

```bash
gemini --checkpointing
```

或者，您可以通过添加到配置中使其成为默认设置（在 `settings.json` 中添加 `"checkpointing": { "enabled": true }`）。激活后，您会注意到每次 Gemini 即将写入文件时，它会显示类似"检查点已保存"的内容。

如果您随后发现 AI 制作的编辑有问题，您有两个选择：

* 运行 `/restore list`（或仅运行不带参数的 `/restore`）查看带有时间戳和描述的最近检查点列表。

* 运行 `/restore <id>` 回滚到特定检查点。如果省略 id 且只有一个待处理的检查点，它将默认恢复该检查点。

例如：

```bash
/restore
```

Gemini CLI 可能输出：

0: [2025-09-22 10:30:15] 运行 'apply_patch' 之前
1: [2025-09-22 10:45:02] 运行 'write_file' 之前

然后您可以执行 `/restore 0` 将所有文件更改（甚至对话上下文）恢复到该检查点时的状态。通过这种方式，您可以"撤销"错误的代码重构或 Gemini 进行的任何其他更改。

**被恢复的内容：** 检查点捕获您工作目录的状态（Gemini CLI 允许修改的所有文件）和工作空间文件（对话上下文也可能根据检查点捕获方式回滚）。恢复时，它会覆盖文件到旧版本并将对话内存重置到该快照。这就像将 AI 代理时间旅行回它犯错之前。请注意，它不会撤消外部数据（例如，如果 AI 运行了数据库迁移，它无法撤消该操作），但文件系统中的任何内容和聊天上下文会。

**最佳实践：** 对于一般任务，最好保持检查点机制开启。开销很小，并提供安心感。如果您发现不需要检查点（一切都顺利进行），您可以随时清除它或让下一个覆盖它。开发团队建议在多步骤代码编辑之前特别使用检查点机制。但对于关键任务项目，您仍应使用适当的版本控制（`git`）作为[主要安全网](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=No,VS%20Code%20is%20already%20free)——将检查点视为快速撤销的便利，而不是完整的 VCS。

本质上，`/restore` 让您有信心地使用 Gemini CLI。您可以让 AI 尝试大胆的更改，知道您有一个"天哪"按钮可以在需要时随时回档。

## 技巧6：读取 Google Docs、Sheets 等文件。配置了 Workspace MCP 服务器后，您可以粘贴 Docs/Sheets 链接，MCP 将获取其内容（需要权限）

**快速使用场景：** 想象您有一个包含某些规则或数据的 Google Doc 或 Sheet，希望 AI 使用。您无需复制粘贴内容，只需提供链接，配置了 Workspace MCP 服务器的 Gemini CLI 就可以获取并读取它。

例如：

```bash
Summarize the requirements from this design doc: https://docs.google.com/document/d/<id>
```

Gemini 可以提取该文档的内容并将其合并到其响应中。类似地，它可以通过链接读取 Google Sheets 或 Drive 文件。

**工作原理：** 这些功能通常通过**MCP 集成**启用。Google 的 Gemini CLI 团队已经构建（或正在开发）Google Workspace 的连接器。一种方法是运行一个小型 MCP 服务器，使用 Google 的 API（Docs API、Sheets API 等）在给定 URL 或 ID 时检索文档内容。配置后，您可能有斜杠命令或工具如 `/read_google_doc`，或者仅仅是自动检测看到 Google Docs 链接并调用相应工具来获取它。

例如，在 Agent Factory 播客演示中，团队使用了一个**Google Docs MCP** 将摘要直接保存到[文档](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=%2A%20Utilize%20the%20google,summary%20directly%20to%20Google%20Docs)中——这意味着它首先也可以读取文档内容。实际上，您可能执行类似以下操作：

```bash
@https://docs.google.com/document/d/XYZ12345
```

使用 `@` 包含 URL（上下文引用语法）向 Gemini CLI 发出信号获取该资源。有了 Google Doc 集成，该文档的内容将被拉取，就好像它是本地文件一样。从那里，AI 可以对其进行摘要、回答相关问题或以其他方式在对话中使用它。

类似地，如果您粘贴 Google Drive**文件链接**，正确配置的 Drive 工具可以下载或打开该文件（假设权限和 API 访问已设置）。**Google Sheets** 可以通过运行查询或读取单元格范围的 MCP 提供，使您能够询问诸如"这个 Sheet [链接] 中预算列的总和是多少？"之类的问题，并让 AI 计算它。

**设置：** 在撰写本文时，Google Workspace 集成可能需要一些调整（获取 API 凭据、运行 MCP 服务器等）。请关注官方 Gemini CLI 存储库和社区论坛以获取即用型扩展——例如，官方的 Google Docs MCP 可能作为插件/扩展提供。如果您感兴趣，可以按照[指南](https://github.com/google-gemini/gemini-cli/issues/7175#:~:text=)在 MCP 服务器中使用 Google API 编写一个。这通常涉及处理 OAuth（Gemini CLI 为 MCP 服务器支持）然后暴露如 `read_google_doc` 等工具。

**使用提示：** 当您拥有这些工具时，使用它们可以像在提示中提供链接一样简单（AI 可能会自动调用工具来获取它）或使用斜杠命令如 `/doc open <URL>`。检查 `/tools` 查看可用的命令——Gemini CLI 在那里[列出所有工具和自定义命令](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=Gemini%20CLI%20includes%20dozens%20of,can%20supercharge%20your%20dev%20process)。

总之，**Gemini CLI 可以超越您的本地文件系统**。无论是 Google Docs、Sheets、Drive 还是其他外部内容，您都可以通过引用拉取数据。这个专业技巧节省了您手动复制粘贴的时间，并保持上下文流程自然——只需引用您需要的文档或数据集，让 AI 抓取所需的内容。它使 Gemini CLI 成为您有权访问的所有信息的真正**知识助手**，而不仅仅是磁盘上的文件。

*（注意：访问私人文档当然需要 CLI 拥有适当的权限。始终确保任何集成都尊重安全性和隐私。在公司环境中，设置此类集成可能涉及额外的身份验证步骤。）*

## 技巧7：使用 `@` 引用文件和图像以提供明确的上下文

**快速使用场景：** 不要口头描述文件内容或图像，只需直接将 Gemini CLI 指向它。使用 `@` 语法，您可以将文件、目录或图像附加到提示中。这保证 AI 精确地看到这些文件中的内容作为上下文。例如：

```bash
Explain this code to me: @./src/main.js
```

这将在提示中包含 `src/main.js` 的内容（在 Gemini 的上下文大小限制内），因此 AI 可以读取并解释它。

这个 `@` *文件引用*是 Gemini CLI 对开发者最强大的功能之一。它消除了歧义——您不是要求模型依赖记忆或猜测文件内容，您确实将文件交给它读取。您可以将此用于源代码、文本文档、日志等。类似地，您可以引用**整个目录**：

```bash
Refactor the code in @./utils/ to use async/await.
```

通过附加以斜杠结尾的路径，Gemini CLI 将递归地包含该目录中的文件（在合理范围内，尊重忽略文件和大小限制）。这对于多文件重构或分析非常有用，因为 AI 可以一起考虑所有相关模块。

更令人印象深刻的是，您可以在提示中引用**二进制文件如图像**。Gemini CLI（使用 Gemini 模型的多模态能力）可以理解图像。例如：

```bash
Describe what you see in this screenshot: @./design/mockup.png
```

图像将被输入到模型中，AI 可能会回应类似"这是一个登录页面，有蓝色登录按钮和头部图像"等。您可以想象各种用途：审查 UI 模型、整理照片（如我们将在后面的技巧中看到的），或从图像中提取文本（Gemini 也可以进行 OCR）。

关于有效使用 `@` 引用的几个注意事项：

* **文件限制：** Gemini 2.5 Pro 拥有巨大的[上下文窗口（最多 100 万个token）](https://blog.google/technology/developers/introducing-gemini-cli-open-source-ai-agent/#:~:text=To%20use%20Gemini%20CLI%20free,per%20day%20at%20no%20charge)，所以您可以包含相当大的文件或许多文件。但是，非常大的文件可能会被截断。如果文件巨大（例如数十万行），请考虑对其进行摘要或分解成多个部分。如果引用过大或由于大小而跳过了某些内容，Gemini CLI 会警告您。

* **自动忽略：** 默认情况下，Gemini CLI 在拉取目录上下文时尊重您的 `.gitignore` 和 `.geminiignore` 文件。因此，如果您 `@./` 项目根目录，它不会将巨大的忽略文件夹（如 `node_modules`）倾倒到提示中。您可以使用 `.geminiignore` 自定义忽略模式，类似于 `.gitignore` 的工作方式。

* **显式与隐式上下文：** Taylor Mullen（Gemini CLI 的创造者）强调使用 `@` 进行*显式上下文注入*，而不是依赖模型的记忆或自己总结事情。它更精确，确保 AI 不会产生幻觉内容。尽可能，使用 `@` 引用将 AI 指向真实来源（代码、配置文件、文档）。这种做法可以显著提高准确性。

* **链式引用：** 您可以在一个提示中包含多个文件，如：

```bash
Compare @./foo.py and @./bar.py and tell me differences.
```

CLI 将包含两个文件。只需注意令牌限制；多个大文件可能会消耗大量上下文窗口。

使用 `@` 本质上是您**动态向 Gemini CLI 输送知识**的方式。它将 CLI 变成可以处理文本和图像的多模态阅读器。作为专业用户，养成利用这个功能的习惯——它通常比要求 AI 执行诸如"打开文件 X 并执行 Y"之类的操作更快、更可靠（它可能或可能不会自行执行）。相反，您明确地给它 X 来处理。

## 技巧8：即时工具创建（让 Gemini 构建辅助工具）

**快速使用场景：** 如果手头的任务会受益于小型脚本或实用程序，您可以要求 Gemini CLI 在您的会话中为您创建该工具。例如，您可能会说，"编写一个 Python 脚本来解析此文件夹中的所有 JSON 文件并提取错误字段。" Gemini 可以生成脚本，然后您可以通过 CLI 执行它。本质上，您可以**动态扩展工具集**。

Gemini CLI 不会局限于其预先存在的工具；它可以使用其编码能力在需要时制造新工具。这通常隐式发生：如果您要求复杂的东西，AI 可能建议编写临时文件（包含代码），然后运行它。作为用户，您也可以明确地指导这个过程：

* **创建脚本：** 您可以写提示词让 Gemini 创建您选择语言的脚本或程序。它可能会使用 `write_file` 工具来创建文件。例如：

```bash
Generate a Node.js script that reads all '.log' files in the current directory and reports the number of lines in each.
```

Gemini CLI 会起草代码，并在您批准后将其写入文件（例如 `script.js`）。然后您可以通过使用 `!` shell 命令（例如 `!node script.js`）或通过要求 Gemini CLI 执行它来运行它（AI 可能会自动使用 `run_shell_command` 来执行它刚编写的脚本，如果它认为这是计划的一部分）。

* **通过 MCP 的临时工具：** 在高级场景中，AI 甚至可能建议为某些专门任务启动 MCP 服务器。例如，如果您的提示涉及一些最好在 Python 中完成的繁重文本处理，Gemini 可能会在 Python 中生成一个简单的 MCP 服务器并运行它。虽然这更少见，但它展示了 AI 可以即时设置新的"代理"。（Gemini CLI 团队的一张幻灯片幽默地提到"为一切提供 MCP 服务器，甚至一个名为 LROwn 的服务器"——暗示您可以让 Gemini 运行自己或另一个模型的实例，虽然这更多的是技巧而非实际用途！）

这里的主要好处是**自动化**。您无需手动停下来编写辅助脚本，而是让 AI 作为流程的一部分来完成它。就像拥有一个可以按需创建工具的助手。这对于数据转换任务、批处理操作或内置工具不直接提供的一次性计算特别有用。

**细微差别和安全性：** 当 Gemini CLI 为新工具编写代码时，您仍应在运行前审查它。`/diff` 视图（Gemini 会在您批准写入文件之前显示文件差异）是您检查代码的机会。确保它按预期执行，没有恶意或破坏性操作（除非您的提示明确要求，否则 AI 不应产生有害内容，但就像来自 AI 的任何代码一样，请仔细检查逻辑，特别是对于删除或修改大量数据的脚本）。

**示例场景：** 假设您有一个 CSV 文件，想以复杂方式过滤它。您要求 Gemini CLI 执行此操作，它可能会说："我将编写一个 Python 脚本来解析 CSV 并应用过滤器。"然后它创建 `filter_data.py`。在您批准并运行后，您得到结果，您可能再也不需要该脚本了。这种工具的临时创建是一个专业操作——它展示了 AI 能高效自主扩展其能力。

**提示：** 如果您发现脚本在直接上下文之外有用，您可以将其提升为永久工具或命令。例如，如果 AI 生成了一个很棒的日志处理脚本，您可能稍后将其转换为自定义斜杠命令（技巧 #2）以便于重用。Gemini 的生成能力与扩展挂钩的结合意味着您的工具包可以在您使用 CLI 的过程中不断演进。

总之，**不要将 Gemini 限制在它自带的功能上**。将其视为可以即时编写新程序甚至迷你服务器来帮助解决问题的初级开发人员。这种方法体现了 Gemini CLI 的智能理念——它会弄清楚需要什么工具，即使它必须在现场编码它们。

## 技巧9：使用 Gemini CLI 排除系统故障或修改配置

**快速使用场景：** 您可以在代码项目之外运行 Gemini CLI 来帮助处理一般的系统任务——将其视为您操作系统的智能助手。例如，如果您的 shell 表现异常，您可以在主目录中打开 Gemini 并询问："修复我的 `.bashrc` 文件，它有错误。" Gemini 然后可以打开并编辑您的配置文件。

这个技巧突显了**Gemini CLI 不仅适用于编码项目——它是您整个开发环境的 AI 助手**。许多用户使用 Gemini 来自定义他们的开发设置或修复机器上的问题：

* **编辑配置文件：** 您可以通过引用来加载您的 shell 配置（`.bashrc` 或 `.zshrc`），然后要求 Gemini CLI 优化或故障排除它。例如，"我的 `PATH` 没有选择 Go 二进制文件，能编辑我的 `.bashrc` 来修复吗？" AI 可以插入正确的 `export` 行。它会在保存更改之前向您显示差异以供确认。

* **诊断错误：** 如果您在终端或应用程序日志中遇到神秘错误，您可以复制并将其提供给 Gemini CLI。它将分析错误消息，并经常建议解决步骤。这类似于使用 StackOverflow 或 Google，但 AI 直接检查您的场景。例如："当我运行 `npm install` 时，我得到 `EACCES` 权限错误——如何修复？" Gemini 可能检测到 `node_modules` 中的权限问题，并指导您更改目录所有权或使用适当的节点版本管理器。

* **在项目外运行：** 默认情况下，如果您在没有 `.gemini` 上下文的目录中运行 `gemini`，这只是意味着没有加载项目特定的上下文——但您仍可以使用 CLI的完整功能。这对于临时任务如系统故障排除很有用。您可能没有任何代码文件需要让AI处理，但仍可以通过它运行 shell 命令或让它获取网络信息。本质上，您将 Gemini CLI 视为可以*为您执行*事情的 AI 驱动终端，而不仅仅是聊天。

* **工作站自定义：** 想要更改设置或安装新工具？您可以要求 Gemini CLI，"在我的系统上安装 Docker"或"配置我的 Git 使用 GPG 签名提交。" CLI 将尝试执行步骤。它可能会从网络获取说明（使用搜索工具），然后运行适当的 shell 命令。当然，始终监视它在做什么并批准命令——但它可以通过自动化多步骤设置过程来节省时间。一个真实例子：用户要求 Gemini CLI"将我的 macOS Dock 偏好设置为自动隐藏并删除延迟"，AI 能够执行必要的 `defaults write` 命令。

将此模式视为将 Gemini CLI 用作**智能 shell**。事实上，您可以将其与技巧 16（shell 直通模式）结合——有时您可能进入 `!` shell 模式来验证某些内容，然后返回 AI 模式让它分析输出。

**注意事项：** 进行系统级任务时，要谨慎处理有广泛影响的命令（如 `rm -rf` 或系统配置更改）。Gemini CLI 通常会要求确认，并且在您看到之前不会运行任何内容。但作为高级用户，您应该了解正在进行什么更改。如果不确定，请要求 Gemini 在运行前解释命令（例如，"解释 `defaults write com.apple.dock autohide-delay -float 0` 的作用"——如果您以这种方式提示，它会很乐意解释而不是直接执行）。

**故障排除额外功能：** 另一个巧妙的用途是使用 Gemini CLI 解析日志或配置文件以查找问题。例如，"扫描此 Apache 配置中的错误"（使用 `@httpd.conf`），或"查看昨天下午 2 点左右的 syslog 错误"（如果可访问，使用 `@/var/log/syslog`）。就像有一个协管管理员。它甚至可以建议崩溃的可能原因或提出常见错误模式的修复方案。

总之，**不要犹豫,启动 Gemini CLI 作为您环境问题的助手**。它能加速您的所有工作流程——不仅仅是编写代码，还维护您编写代码的系统。许多用户报告说，在 Gemini 的帮助下自定义他们的开发环境感觉就像有一个技术伙伴随时待命，处理繁琐或复杂的设置步骤。

## 技巧10：YOLO 模式 - 自动批准工具操作（谨慎使用）

**快速使用场景：** 如果您很自信（或愿意冒险），您可以让 Gemini CLI 无需每次征求您的确认就运行工具操作。这是**YOLO 模式**（You Only Live Once，你只活一次）。它通过 `--yolo` 标志或在会话期间按 `Ctrl+Y` 启用。在 YOLO 模式下，一旦 AI 决定使用工具（如运行 shell 命令或写入文件），它立即执行，没有那个"批准？(y/n)"提示。

**为什么使用 YOLO 模式？** 主要是为了速度和便利性，**当您信任 AI 的操作时**。经验丰富的用户在进行大量重复安全操作时可能会切换 YOLO 开启。例如，如果您要求 Gemini 连续生成 10 个不同文件，批准每个操作会减慢流程；YOLO 模式只会让它们全部自动写入。另一个场景是在完全自动化的脚本或 CI 管道中使用 Gemini CLI——您可以使用 `--yolo` 自动运行它，这样它就不会暂停确认。

要从一开始就以 YOLO 模式启动，使用以下命令启动 CLI：

```bash
gemini --yolo
```

或简写形式 `gemini -y`。您会在 CLI 中看到一些指示（如不同的提示符或通知）显示自动批准已开启。在交互式会话期间，您可以随时按 **Ctrl+Y** 切换它——CLI 通常会在页脚显示类似"YOLO 模式已启用（所有操作自动批准）"的消息。

**重要警告：** YOLO 模式强大但**有风险**。Gemini 团队自己将其标记为"敢于冒险的用户"使用——意味着您应该意识到 AI 可能可能执行危险命令而不询问。在正常模式下，如果 AI 决定运行 `rm -rf /`（最坏情况），您显然会拒绝。在 YOLO 模式下，该命令会立即运行（并可能毁掉您的一天）。虽然这种极端错误不太可能（AI 的系统提示包括安全指导方针），但确认的全部意义就是捕获任何不必要的操作。YOLO 移除了那张安全网。

**YOLO 的最佳实践：** 如果您想要一些便利而没有太多风险，可以考虑*允许列表*特定命令。例如，您可以在设置中配置某些工具或命令模式不需要确认（如允许所有 `git` 命令或只读操作）。事实上，Gemini CLI 支持跳过特定命令确认的配置：例如，您可以设置类似 `"tools.shell.autoApprove": ["git ", "npm test"]` 来始终运行那些命令。这样，您可能不需要全局 YOLO 模式——您只选择性地 YOLO 安全命令。另一种方法：使用 YOLO 时在沙箱或容器中运行 Gemini，这样即使它做了疯狂的事情，您的系统也会受到保护（Gemini 有 `--sandbox` 标志在 Docker 容器中运行工具）。

许多高级用户经常切换 YOLO 开启和关闭——在进行一系列轻微文件编辑或查询时开启，在即将执行关键操作时关闭。您可以做同样的事情，使用键盘快捷键作为快速切换。

总之，**YOLO 模式以监督为代价消除了摩擦**。这是一个要谨慎明智使用的专业功能。它真正展示了对 AI 的信任（或鲁莽！）。如果您是 Gemini CLI 的新手，在清楚了解它倾向于做什么的模式之前，您可能应该避免 YOLO。如果您确实使用它，请加倍注意版本控制或备份——以防万一。

*（如果有什么安慰的话，您并不孤单——社区中的许多人开玩笑说"我 YOLO 了，Gemini 做了些疯狂的事。"所以使用它，但是...嗯，您只活一次。）*

## 技巧11：无头和脚本模式（在后台运行 Gemini CLI）

**快速使用场景：** 您可以通过以**无头模式**运行 Gemini CLI 来在脚本或自动化中使用它。这意味着您通过命令行参数或环境变量提供提示（甚至完整的对话），Gemini CLI 产生输出并退出。这对于与其他工具集成或在计划上触发 AI 任务很有用。

例如，要在不打开 REPL 的情况下获得一次性答案，您已经看到可以使用 `gemini -p "...prompt..."`。这已经是无头使用：它打印模型响应并返回到 shell。但您可以做的更多：

* **系统提示覆盖：** 如果您想使用自定义系统角色或指令集（与默认不同）运行 Gemini CLI，可以使用环境变量 `GEMINI_SYSTEM_MD`。通过设置它，您告诉 Gemini CLI 忽略其内置系统提示并使用您提供的文件。例如：

```bash
export GEMINI_SYSTEM_MD="/path/to/custom_system.md"
gemini -p "Perform task X with high caution"
```

这将加载您的 `custom_system.md` 作为系统提示（AI 遵循的"角色"和规则），然后执行提示。或者，如果您设置 `GEMINI_SYSTEM_MD=true`，CLI 将在当前项目的 `.gemini` 目录中查找名为 `system.md` 的文件。这个功能非常高级——它基本上允许您*替换 CLI 的内置大脑*为您自己的指令，一些用户将其用于专门的工作流程（如模拟特定角色或执行超严格的策略）。谨慎使用它，因为替换核心提示可能会影响工具使用（核心提示包含关于 AI 如何选择和使用工具的重要指导）。

* **通过 CLI 的直接提示：** 除了 `-p`，还有 `-i`（交互式提示），它使用初始提示启动会话，然后保持打开状态。例如：`gemini -i "Hello, let's debug something"` 将打开 REPL 并且已经向模型说过Hello。如果您希望在启动时立即询问第一个问题，这很有用。

* **使用 shell 管道脚本：** 您不仅可以管道传输文本，还可以将文件或命令输出管道传输到 Gemini。例如：`gemini -p "Summarize this log:" < big_log.txt` 将把 `big_log.txt` 的内容输入到提示中（在短语"Summarize this log:"之后）。或者您可能执行 `some_command | gemini -p "Given the above output, what went wrong?"`。这种技术允许您将 Unix 工具与 AI 分析组合在一起。它是一次性操作意义上的无头。

* **在 CI/CD 中运行：** 您可以将 Gemini CLI 合并到构建过程中。例如，CI 管道可能运行测试，然后使用 Gemini CLI 自动分析失败的测试输出并发布评论。使用 `-p` 标志和环境身份验证，这可以脚本化。（当然，确保环境有所需的 API 密钥或身份验证。）

另一个无头技巧：**`--format=json` 标志**（或配置设置）。如果您配置它，Gemini CLI 可以以 JSON 格式输出响应而不是人类可读的文本。这对于程序化消费很有用——您的脚本可以解析 JSON 并以结构化方式提取数据。

总之，**无头模式让 Gemini CLI 成为您自动化工具链中的一等公民**。您可以将 AI 功能注入脚本、工作流程和管道中，而无需人工干预。这对于高级用户和 DevOps 工程师来说是一个强大的功能，他们希望将 AI 分析集成到他们的开发运维中。

**专业提示：** 在脚本中使用时，考虑添加错误处理。例如，如果 Gemini CLI 失败或 API 达到速率限制，您的脚本应该优雅地处理它。您可以通过捕获退出代码或检查 JSON 输出的错误字段来实现这一点。

## 技巧12：使用 `@` 直接从网络引用内容

**快速使用场景：** 您不仅限于本地文件。`@` 语法还可以直接从网页、API 端点或其他网络资源拉取内容。例如：

```bash
Summarize this blog post: @https://blog.example.com/tech-post
```

Gemini CLI 将获取该 URL 的内容并将其包含在上下文中，然后 AI 可以对其进行摘要。

**如何工作：** 当您使用带有 URL 的 `@` 时，Gemini CLI 会自动检测到它是一个网络引用并获取内容。它使用内置的网页抓取或 API 调用功能来检索数据。这适用于许多常见的网络格式（HTML、JSON、XML 等），甚至可以处理一些 PDF 或文档 URL。

**实际应用：**

* **研究和分析：** 如果您正在研究一个主题并找到一篇有趣的文章，您可以立即请 Gemini 分析它而无需先下载它。

* **API 响应分析：** 您可以直接引用 API 端点来分析 JSON 响应：`@https://api.example.com/data.json`

* **文档引用：** 引用在线文档页面让 AI 帮助您理解复杂的 API 或技术概念。

**注意事项：** 

* 某些网站可能有反机器人保护或登录要求。在这些情况下，获取可能会失败。

* 大型网页可能会被截断以适应上下文限制。

* 始终检查网络内容的准确性和最新性，因为它可能已经过时。

这个功能将 Gemini CLI 转变为强大的研究助手，让您能够即时分析任何公开的网络内容而无需离开终端。

## 技巧13：使用变量和占位符创建动态模板

**快速使用场景：** 创建可重用的提示模板，使用变量插入特定值。这对于报告生成、代码模板或任何需要一定程度变化的重复性任务很有用。

在自定义命令（技巧2）中，您已经看到了使用 `{{args}}` 的基本占位符。但 Gemini CLI 支持更复杂的模板系统：

```bash
# 在自定义命令中
prompt = """
为 {{app_name}} 生成 {{endpoint_count}} 个 API 端点的文档。
框架: {{framework|React}}
样式: {{style|Material-UI}}
"""
```

当您调用此命令时，可以传入这些变量，或使用默认值。

**高级变量用法：**

* **条件变量：** 使用 `{{variable?value_if_true:value_if_false}}` 语法

* **循环：** 对于数组变量，可以使用 `{{#each items}}...{{/each}}` 

* **嵌套：** 变量可以包含对象属性，如 `{{user.settings.theme}}`

这个功能使您能够创建高度可配置的命令和脚本，适应不同场景而无需创建多个变体。

## 技巧14：使用会话保存和恢复功能

**快速使用场景：** 如果您正在进行复杂的多天项目，可以保存当前会话状态并稍后继续，而不会丢失上下文。

使用 `/session save <name>` 命令保存当前对话上下文，包括记忆、活动工具和历史记录。之后使用 `/session restore <name>` 恢复到该确切状态。

**工作原理：**

* 会话被保存到 `.gemini/sessions/` 目录
* 包括完整的对话历史和当前上下文
* 可以在项目间共享会话（尽管有些工具可能是项目特定的）

**专业提示：** 这对于以下情况特别有用：

* 多步骤调试会话，您想保留所有已尝试的解决方案
* 学习会话，您想回顾 AI 提供的逐步解释
* 协作场景，团队成员可以恢复和继续某个人的会话

## 技巧15：使用多模态输入进行富交互

**快速使用场景：** Gemini CLI 不仅理解文本；它还可以处理图像、音频和视频输入，实现真正的多模态交互。

**示例：**

```bash
分析这个设计中的可访问性问题: @./design.png
转录这段音频会议: @./meeting.mp3
描述这个视频中的步骤: @./tutorial.mp4
```

**支持的多模态格式：**

* **图像：** PNG, JPEG, WebP, GIF（静态）
* **音频：** WAV, MP3, M4A, OGG
* **视频：** MP4, WebM, MOV（用于关键帧分析）

**实际应用：**

* **设计审查：** 上传截图让 AI 分析 UI/UX 问题
* **数据可视化：** 让图表和图形解释数据趋势
* **技术支持：** 截取错误消息或问题的屏幕截图让 AI 诊断
* **学习：** 让图表和示意图解释复杂概念

这个功能使 Gemini CLI 成为真正的多模态助手，可以像人类一样感知和分析不同类型的信息。

## 技巧16：使用 shell 直通模式进行细粒度控制

**快速使用场景：** 有时您需要执行复杂命令或运行交互式工具。使用 `!!`（双感叹号）进入 shell 直通模式，您可以在其中直接与您的系统 shell 交互。

**基本用法：**

```bash
!!  # 进入 shell 模式
$ ls -la
$ git status
$ exit  # 返回到 Gemini CLI
```

**高级用法：**

* **持久 shell：** 使用 `!!persistent` 在后台保持 shell 会话打开
* **共享环境：** 在 shell 直通中设置的环境变量会传递回 Gemini CLI
* **命令链：** 使用 `&&` 和 `||` 来创建复杂的命令序列

**何时使用：**

* 当您需要运行交互式命令（如 `top`、`htop` 或编辑器）时
* 对于需要实时输出的长时间运行命令
* 当您想要精确控制命令语法时

**安全提示：** 在 shell 直通模式中，您绕过了 Gemini CLI 的安全检查，所以要特别小心，特别是对于破坏性命令。

## 技巧17：使用工具链编排复杂工作流程

**快速使用场景：** 为复杂项目创建端到端的工作流程，结合多个工具和步骤。

**示例工作流程：**

```bash
# 创建一个完整的项目设置工作流程
/workspace setup new-project --type=web-app --framework=react
```

这将：
1. 创建项目结构
2. 设置 package.json
3. 安装依赖
4. 创建初始组件
5. 设置 CI/CD 配置
6. 初始化 Git 仓库

**创建自定义工作流程：**

您可以使用自定义命令（技巧2）结合多个工具调用：

```toml
[workspace]
description = "项目工作流程命令"

[workspace.setup]
prompt = """
使用以下步骤设置一个 {{type}} 项目：
1. 创建适当的目录结构
2. 生成必要的配置文件
3. 安装{{framework}}的依赖
4. 创建基础样板代码
5. 初始化版本控制

项目类型: {{type}}
框架: {{framework}}
附加功能: {{features|[]}}
"""
```

**工作流程最佳实践：**

* 将复杂工作流程分解为可重用的子任务
* 使用检查点机制（技巧5）作为中间步骤的回滚点
* 记录每个工作流程步骤的预期结果
* 使用记忆（技巧4）保存重要的配置决策

这个功能使 Gemini CLI 成为一个完整的项目管理和自动化平台，而不仅仅是一个编码助手。

## 技巧18：使用协作功能进行团队开发

**快速使用场景：** 与团队成员共享会话、记忆和自定义命令，实现一致的 AI 辅助开发体验。

**协作功能：**

* **共享记忆：** 将项目特定的 `GEMINI.md` 提交到版本控制，确保所有团队成员拥有相同的上下文
* **自定义命令库：** 在 `.gemini/commands/` 中共享自定义命令，标准化团队工作流程
* **会话分享：** 使用 `/session export` 导出有用会话供团队成员学习

**设置团队协作：**

1. 在项目根目录创建 `.gemini/` 目录
2. 添加 `GEMINI.md` 包含项目特定信息和编码标准
3. 创建 `.geminiignore` 忽略敏感信息
4. 将 `.gemini/` 添加到 `.gitignore`，但忽略 `GEMINI.md` 和 `commands/`

**最佳实践：**

* 定期审查和更新共享的记忆和命令
* 建立团队 AI 使用的编码标准
* 使用分支策略来测试新的 AI 工作流程
* 记录团队特定的 AI 命令和用法模式

这使得团队能够保持一致性，减少上下文切换，并共享 AI 驱动的生产力收益。

## 技巧19：使用性能监控和优化

**快速使用场景：** 监控 Gemini CLI 的性能并优化其使用以提高效率。

**性能命令：**

```bash
/stats          # 查看当前会话的令牌使用统计
/benchmark      # 运行性能基准测试
/profile        # 分析性能瓶颈
```

**优化技巧：**

* **上下文管理：** 使用 `/memory trim` 移除旧或不必要的上下文以减少令牌使用
* **缓存：** 使用 `--cache` 标志启用结果缓存以重复常见查询
* **批处理：** 将多个小请求组合成单个大请求以提高效率
* **并行处理：** 使用 `--parallel` 标志启用并行工具执行

**监控使用情况：**

设置环境变量 `GEMINI_STATS=1` 启用详细的使用统计记录。这将记录：

* 每个请求的令牌使用情况
* 工具执行时间
* API 响应时间
* 错误率

这些数据可以帮助您识别瓶颈并优化您的工作流程。

## 技巧20：使用高级安全功能

**快速使用场景：** 在企业或安全敏感的环境中，使用高级安全功能来保护您的数据和系统。

**安全功能：**

* **沙箱执行：** 使用 `--sandbox` 在隔离的 Docker 容器中运行所有工具
* **权限控制：** 通过 `settings.json` 配置细粒度的工具权限
* **审计日志：** 使用 `--audit` 记录所有 AI 操作以进行合规性审查
* **数据加密：** 使用 `--encrypt` 在静态和传输中加密敏感数据

**配置安全：**

```json
{
  "security": {
    "sandbox": true,
    "allowedTools": ["read_file", "write_file"],
    "blockedCommands": ["rm -rf", "sudo"],
    "auditLog": true,
    "encryption": true
  }
}
```

**最佳实践：**

* 在生产环境中始终启用沙箱模式
* 定期审查和更新权限设置
* 使用审计日志跟踪 AI 生成的所有更改
* 为敏感项目实现额外的访问控制

这些功能使 Gemini CLI 适用于企业环境，在其中安全性和合规性至关重要。

---

## 结论

Gemini CLI 不仅仅是一个命令行工具；它是一个强大的 AI 助手，可以彻底改变您的工作流程。通过掌握这些 20 个专业技巧，您将能够：

* 大幅提高生产力和效率
* 自动化复杂的开发和系统任务
* 与团队成员更好地协作
* 保持安全性和合规性
* 充分利用 AI 的全部潜力

关键是要记住，Gemini CLI 是一个工具，最有力的用户是那些学会将其强大功能与自身专业知识相结合的人。从基础开始，逐步构建，不要害怕实验和定制工具以适应您的特定需求。

随着您对 Gemini CLI 越来越熟悉，您会发现越来越多的创新方式将其集成到您的日常工作中。AI 驱动的开发工具的未来就在这里，而 Gemini CLI 处于这场革命的最前沿。

**开始探索，快乐编码！**
