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

**提示：** 使用 `/init` 斜杠命令快速生成初始的 `GEMINI.md`。在新项目中运行 `/init` 会创建一个模板上下文文件，包含检测到的技术栈、项目摘要等信息。然后您可以编辑和扩展该文件。对于大型项目，考虑将上下文分解为多个文件，并使用 `@include` 语法将它们**导入**到 `GEMINI.md` 中。例如，您的主 `GEMINI.md` 可以包含类似 `@./docs/prompt-guidelines.md` 的行来引入额外的上下文文件。这使您的指令保持组织有序。

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

**提示：** 自定义命令也可用于强制格式化或为某些任务为 AI 应用"角色"。例如，您可能有一个 `/review:security` 命令，总是在提示前加上"您是安全审计员..."来审查代码漏洞。这种方法确保 AI 在处理特定类别任务时的响应一致性。

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

* **通过 MCP 实现的临时工具：** 在高级场景中，AI 甚至可能建议为某些专门任务启动 MCP 服务器。例如，如果您的提示涉及一些最好在 Python 中完成的繁重文本处理，Gemini 可能会在 Python 中生成一个简单的 MCP 服务器并运行它。虽然这更少见，但它展示了 AI 可以即时设置新的"代理"。（Gemini CLI 团队的一张幻灯片幽默地提到"为一切提供 MCP 服务器，甚至一个名为 LROwn 的服务器"——暗示您可以让 Gemini 运行自己或另一个模型的实例，虽然这更多的是技巧而非实际用途！）

这里的主要好处是**自动化**。您无需手动停下来编写辅助脚本，而是让 AI 作为流程的一部分来完成它。就像拥有一个可以按需创建工具的助手。这对于数据转换任务、批处理操作或内置工具不直接提供的一次性计算特别有用。

**细微差别和安全性：** 当 Gemini CLI 为新工具编写代码时，您仍应在运行前审查它。`/diff` 视图（Gemini 会在您批准写入文件之前显示文件差异）是您检查代码的机会。确保它按预期执行，没有恶意或破坏性操作（除非您的提示明确要求，否则 AI 不应产生有害内容，但仍需您像对待来自 AI 的任何代码一样，仔细检查逻辑，特别是对于删除或修改大量数据的脚本）。

**示例场景：** 假设您有一个 CSV 文件，想以复杂方式过滤它。您要求 Gemini CLI 执行此操作，它可能会说："我将编写一个 Python 脚本来解析 CSV 并应用过滤器。"然后它创建 `filter_data.py`。在您批准并运行后，您得到结果，您可能再也不需要该脚本了。这种工具的临时创建是一个专业操作——它展示了 AI 能高效自主扩展其能力。

**提示：** 如果您发现脚本在直接上下文之外有用，您可以将其保存为永久工具或命令。例如，如果 AI 生成了一个很棒的日志处理脚本，您可能稍后将其转换为自定义斜杠命令（技巧 #2）以便于重用。Gemini 的生成能力与扩展挂钩的结合意味着您的工具包可以在您使用 CLI 的过程中不断演进。

总之，**不要让 Gemini 局限于它自带的功能上**。将其视为可以即时编写新程序甚至迷你服务器来帮助解决问题的初级开发人员。这种方法体现了 Gemini CLI 的智能理念——它会弄清楚需要什么工具，即使它必须在现场编码它们。

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

**无头模式的重要性：** 它将 Gemini CLI 从交互式助手转变为**后端服务**或实用程序，其他程序可以调用。您可以安排一个 cronjob，每晚运行一个 Gemini CLI 提示（想象生成一个报告或使用 AI 逻辑清理某些东西）。您可以将 IDE 中的按钮连接到触发无头 Gemini 运行来执行特定任务。

**示例：** 假设您想要一个新闻网站的每日摘要。您可以有一个脚本：

```bash
gemini -p "Web-fetch \"https://news.site/top-stories\" and extract the headlines, then write them to headlines.txt"
```

也可使用 `--yolo`，这样它就不会要求确认写入文件。这将使用网络获取工具来获取页面并使用文件写入工具保存标题。全部自动完成，无需人工干预。一旦您将 Gemini CLI 视为可脚本化组件，可能性是无穷的。

总之，**无头模式**将启用自动化。它是 Gemini CLI 与其他系统之间的桥梁。掌握它意味着您可以扩展您的 AI 使用——不仅仅是当您在终端中输入时，当您不在时，您的 AI 代理也可以为您工作。

*提示：对于真正长时间运行的非交互式任务，您还可以研究 Gemini CLI 的"Plan"模式或它如何在不干预的情况下生成多步骤计划。这些是超出此范围的高级主题。在大多数情况下，通过无头模式精心制作单个提示可以实现很多目标。*

## 技巧12：保存和恢复聊天会话

**快速使用场景：** 如果您已经使用 Gemini CLI 调试了一小时需要暂停，您可以不用丢失对话上下文。使用 `/chat save <name>` 保存会话。之后（即使重启 CLI），您可以使用 `/chat resume <name>` 从您离开的地方继续。这样，长时间运行的对话可以暂停并无缝继续。

Gemini CLI 本质上有一个内置的聊天会话管理器。需要了解的命令有：

* `/chat save <tag>` - 将当前对话状态保存在您提供的标签/名称下。标签就像该会话的文件名或键。如果需要，您可以经常保存，如果标签存在，它将覆盖它。（使用描述性名称很有帮助——例如，`chat save fix-docker-issue`。）

* `/chat list` - 列出您所有保存的会话（您使用的标签）。这有助于您记住之前保存的名称。

* `/chat resume <tag>` - 恢复具有该标签的会话，将整个对话上下文和历史记录恢复到保存时的状态。就像您从未离开过一样。然后您可以从那个点继续聊天。

* `/chat share` - （保存到文件）这很有用，因为您可以将整个聊天与其他人分享，他们可以继续会话。几乎像协作一样。

在底层，这些会话很可能存储在 `~/.gemini/chats/` 或类似位置中。它们包括对话消息和任何相关状态。这个功能对于以下情况非常有用：

* **长时间调试会话：** 有时与 AI 调试可能是一个漫长的来回过程。如果您无法一次性解决，保存它并稍后回来（也许带着清醒的头脑）。AI 仍然会"记住"之前的所有内容，因为整个上下文被重新加载。

* **多日任务：** 如果您将 Gemini CLI 用作项目的助手，您可能有一个"重构模块 X"的聊天会话跨越多天。每天您可以恢复该特定聊天，这样上下文不会每天重置。同时，您可能将另一个"编写文档"的会话单独保存。切换上下文只是保存一个和恢复另一个的问题。

* **团队交接：** 这更具实验性，但理论上，您可以将保存的聊天内容与同事分享（保存的文件很可能是可移植的）。如果他们将它们放在他们的 `.gemini` 目录中并恢复，他们可以看到相同的上下文。协作的**更简单实用的方法**只是从日志中复制相关的问答并使用共享的 `GEMINI.md` 或提示，但值得注意的是会话数据是您保留的。

**使用示例：**

```bash
/chat save api-upgrade
```

*(会话保存为"api-upgrade"）*

```bash
/quit
```

*（稍后，重新打开 CLI）*

```bash
$ gemini
gemini> /chat list
```

*（显示：api-upgrade）*

```bash
gemini> /chat resume api-upgrade
```

现在模型用上次交换的状态向您问好。您可以通过向上滚动来确认所有之前的消息都存在。

**提示：** 保存聊天时使用有意义的[标签](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=Naming%20conventions%20to%20keep%20projects,organized)。不要使用 `/chat save session1`，给它一个与主题相关的名称（例如 `/chat save memory-leak-bug`）。这将帮助您稍后通过 `/chat list` 找到正确的那个。没有严格限制您可以保存多少会话，但偶尔清理旧会话可能是明智的。

这个功能将 Gemini CLI 变成持久的顾问。您不会丢失在对话中获得的知识；您可以随时暂停和恢复。这是与其他一些在关闭时会忘记上下文的 AI 界面的区别所在。对于高级用户来说，这意味着**您可以与 AI 维护并行的工作线程**。就像您为不同任务有多个终端标签页一样，您可以有多个保存的聊天会话，并在任何给定时间恢复您需要的那个。

## 技巧13：多目录工作区 - 一个 Gemini，多个文件夹

**快速使用场景：** 您的项目是否分散在多个仓库或目录中？您可以启动 Gemini CLI 同时访问*所有这些*，这样它会看到一个统一的工作区。例如，如果您的后端和前端是独立的文件夹，您可以同时包含这两个，这样 Gemini 可以编辑或引用两者中的文件。

有两种使用**多目录模式**的方式：

* **启动参数：** 启动 Gemini CLI 时使用 `--include-directories`（或 `-I`）指定。例如：

```bash
gemini --include-directories "../backend:../frontend"
```

这假设您从例如 `scripts` 目录运行命令，并希望包含两个同级文件夹。您提供以冒号分隔的路径列表。然后 Gemini CLI 将所有这些目录视为一个大工作区的一部分。

* **保存设置：** 在您的 `settings.json` 中，您可以定义 `"includeDirectories": ["path1", "path2", ...]`。如果您总是希望加载某些公共目录（例如，多个项目使用的共享库文件夹），这很有用。路径可以是相对的或绝对的。路径中的环境变量（如 `~/common-utils`）是允许的。

当多目录模式激活时，CLI 的上下文和工具考虑所有包含位置的文件。`> /directory show` 命令将列出当前[工作区](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=How%20to%20add%20multiple%20directories,step)中的哪些目录。您还可以在会话期间使用 `/directory add <path>` 动态添加目录——它将即时加载它（可能像启动时那样扫描它以获取上下文）。

**为什么要使用多目录模式？** 在微服务架构或模块化代码库中，通常一部分代码存在于一个仓库中，另一部分在不同的仓库中。如果您只在一个中运行 Gemini，它不会"看到"其他的。通过组合它们，您启用跨项目推理。例如，您可以问，"更新前端中的 API 客户端以匹配后端的新 API 端点"——Gemini 可以打开后端文件夹查看 API 定义，并同时打开前端代码相应地修改它。没有多目录模式，您必须一次处理一边并手动传递信息。

**示例：** 假设您有 `client/` 和 `server/`。这样启动Gemini CLI：

```bash
cd client
gemini --include-directories "../server"
```

现在在 `gemini>` 提示符下，如果您执行 `> !ls`，您将看到它可以列出 `client` 和 `server` 中的文件（它可能将它们显示为单独的路径）。您可以执行：

```bash
Open server/routes/api.py and client/src/api.js side by side to compare function names.
```

AI 将可以访问两个文件。或者您可能说：

```bash
The API changed: the endpoint "/users/create" is now "/users/register". Update both backend and frontend accordingly.
```

它可以同时在后端路由中创建补丁并调整前端获取调用。

在底层，Gemini 合并这些目录的文件索引。如果每个目录都很大，可能会有一些性能考虑，但通常它可以很好地处理多个中小型项目。备忘单指出这有效地创建了一个具有多个根目录的工作区。

**技巧：** 即使您不总是使用多目录模式，要知道您仍然可以通过提示中的绝对路径（`@/path/to/file`）引用跨文件系统的文件。但是，没有多目录，Gemini 可能没有权限编辑这些文件或知道主动从中加载上下文。多目录正式地将它们包含在范围内，因此它了解整个集合的任务（如搜索或跨整个集合的代码生成）的所有文件。

**删除目录：** 如果需要，`/directory remove <path>`（或类似命令）可以从工作区中删除一个目录。这不太常见，但如果您意外包含了某些东西，可以删除它。

总之，**多目录模式统一您的上下文**。对于 polyrepo 项目或代码分散的任何情况，这是必须的。它使 Gemini CLI 的行为更像是一个打开了整个解决方案的 IDE。作为高级用户，这意味着您的项目中没有任何部分超出了 AI 的可控范围。

## 技巧14：使用 AI 协助组织和清理您的文件

**快速使用场景：** 对凌乱的 `Downloads` 文件夹或无组织的项目资产感到厌倦？您可以招募 Gemini CLI 充当智能组织者。通过向它提供目录概览，它可以分类文件甚至将它们移动到子文件夹中（在您批准的情况下）。例如，"清理我的 `Downloads`：将图像移动到 `Images` 文件夹，PDF 移动到 `Documents`，并删除临时文件。"

因为 Gemini CLI 可以读取文件名、大小，甚至查看文件内容，它可以就文件[组织](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=We%20built%20a%20CLI%20tool,trash%20folder%20for%20manual%20deletion)做出明智的决定。一个社区创建的工具称为**"Janitor AI"**展示了这一点：它通过 Gemini CLI 运行，将文件分类为重要与垃圾，并相应地[分组](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=We%20built%20a%20CLI%20tool,trash%20folder%20for%20manual%20deletion)。过程涉及扫描目录，使用 Gemini 对文件名和元数据（如果需要还有内容）的推理，然后将文件移动到类别中。值得注意的是，它没有自动删除垃圾——而是将它们移动到 `Trash` 文件夹以供[审查](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=organize%20files,trash%20folder%20for%20manual%20deletion)。

以下是如何手动使用 Gemini CLI 复制这样的工作流程：

1. **扫描：** 使用提示让 Gemini 列出文件和分类。例如：

```bash
列出当前目录中的所有文件，并将它们分类为"图像"、"视频"、"文档"、"档案"或"其他"。
```

Gemini 可能使用 `!ls` 或类似命令获取文件列表，然后分析文件名称/扩展名以对文件进行分类。

1. **规划：** 询问 Gemini 它想要如何优化。例如：

```bash
为这些文件提出新的文件夹结构。我想按类型（图像、视频、文档等）分开。同时识别任何看起来像重复或不必要的文件。
```

AI 可能以一个计划响应：例如，*"创建文件夹：`Images/`、`Videos/`、`Documents/`、`Archives/`。将 `X.png`、`Y.jpg` 移动到 `Images/`；将 `A.mp4` 移动到 `Videos/`；等等。文件 `temp.txt` 看起来不必要（也许是一个临时文件）。"*

1. **通过确认执行移动：** 然后您可以指示它执行计划。它可能为每个文件使用 `mv` 等shell命令。因为这会修改您的文件系统，您将获得每个操作的确认提示（除非您 YOLO 它）。仔细批准移动。完成后，您的目录将按照建议整齐地组织。

在整个过程中，Gemini 的自然语言理解是关键。例如，它可以推理出 `IMG_001.png` 是一个图像或 `presentation.pdf` 是一个文档，即使没有明确说明。它甚至可以打开图像（使用其视觉能力）查看其中的内容——例如，区分截图与照片或图标——并相应地[命名或排序](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=If%20your%20project%20folder%20is,using%20relevant%20and%20descriptive%20terms)。

**按内容重命名文件：** 一个特别神奇的用途是让 Gemini 将文件重命名为更具描述性。Dev Community 文章"7 Insane Gemini CLI Tips"描述了 Gemini 如何**扫描图像并[根据其内容自动重命名它们](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=If%20your%20project%20folder%20is,using%20relevant%20and%20descriptive%20terms)。例如，名为 `IMG_1234.jpg` 的文件如果 AI 看到它是登录屏幕的截图，可能被重命名为 `login_screen.jpg`。为此，您可以提示：

```bash
对于这里的每个 .png 图像，查看其内容并重命名为具有描述性的内容。
```

Gemini 将打开每个图像（通过视觉工具），获取描述，然后提议一个 `mv IMG_1234.png login_screen.png` [操作](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=If%20your%20project%20folder%20is,using%20relevant%20and%20descriptive%20terms)。这可以显著改善资产的组织，特别是在设计或照片文件夹中。

**二次处理法：** Janitor AI 讨论指出了一个两步过程：首先是广泛分类（重要与垃圾与其他），然后完善[组](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=organize%20files,trash%20folder%20for%20manual%20deletion)。你可以效仿这种方法：先将可能需要删除的文件（比如大型安装程序 .dmg 文件或重复文件）与需要保留的文件分开，然后专注于整理保留的文件。一定要再次检查 AI 标记为垃圾的文件；它的判断并不总是正确，因此需要人工监督。

**安全提示：** 当让 AI 对文件移动或删除时，要有备份或至少准备好撤销（使用 `/restore` 或您自己的备份）。进行试运行是明智的：询问 Gemini 打印它*将要*运行以组织文件的命令，但不执行它们，这样您可以审查。例如："列出此计划所需的 `mv` 和 `mkdir` 命令，但还不要执行它们。"一旦您审查了列表，您可以复制粘贴执行它们，或指示 Gemini 继续。

这是使用 Gemini CLI 处理"非显而易见"任务的绝佳示例——它不仅仅是写代码，而是用 AI 智慧进行系统整理。它可以节省时间，为混乱的环境带来一些秩序。毕竟，作为开发者，我们都会积累各种杂乱文件（日志、旧脚本、下载文件），而一个 AI 管家会非常有用。

## 技巧15：压缩长对话以保持在上下文内

**快速使用场景：** 如果您与 Gemini CLI 聊天了很长时间，您可能会达到模型的上下文长度限制，或者只是发现会话变得难以处理。使用 `/compress` 命令总结到目前为止的对话，用简洁的摘要替换完整的聊天记录。这为更多讨论释放空间，而无需从头开始。

大语言模型有固定的上下文窗口（Gemini 2.5 Pro 的非常大，但不是无限的）。如果您超过它，模型可能会开始忘记较早的消息或失去连贯性。`/compress` 功能本质上是您会话的 **AI 生成的 tl;dr**，保留重要要点但删除逐分钟的对话。

**它如何工作：** 当您键入 `/compress` 时，Gemini CLI 将获取整个对话（除系统上下文外）并生成一个摘要。然后它用该摘要替换聊天记录作为单个系统或助手消息，保留基本细节但丢弃每分钟对话。它将指示发生了压缩。例如，在 `/compress` 之后，您可能会看到类似的内容：

--- 对话压缩 ---  
讨论摘要：用户和助手一直在调试应用程序中的内存泄漏。关键点：问题可能在 `DataProcessor.js` 中，其中对象没有被释放。助手建议添加日志记录并识别可能的无限循环。用户即将测试修复。  
--- 摘要结束 ---

从那时起，模型只有该摘要（加上新消息）作为之前发生的事情的上下文。如果摘要捕获了突出信息，这通常就足够了。

**何时压缩：** 理想情况下是在您*遇到*限制之前。如果您注意到会话变得冗长（数百回合或上下文中有很多代码），主动压缩。备忘单提到了自动压缩设置（例如，当上下文超过最大值的 60% 时压缩）。如果启用该功能，Gemini 可能自动压缩并让您知道。否则，手动 `/compress` 在您的工具包中。

**压缩之后：** 您可以正常继续对话。如果需要，您可以在非常长的会话中多次压缩。每次，您都会失去一些细节，所以不要没有理由地过于频繁压缩——您可能最终得到复杂讨论的过于简化的记忆。但通常模型自己的摘要在保留关键事实方面相当好（您可以随时重新陈述任何关键内容）。

**上下文窗口示例：** 假设您通过引用许多文件提供了大型代码库，并有 1M token 上下文（最大值）。如果您想要转换到项目的不同部分，而不是开始新会话（失去所有这些理解），您可以压缩。摘要将浓缩从代码中获得的知识（比如"我们加载了模块 A、B、C。A 有这些函数... B 以这些方式与 C 交互..."）。现在您可以在保留这些抽象知识的情况下继续询问新事物。

**记忆与压缩：** 请注意压缩不会保存到长期记忆，它对对话是局部的。如果您有想*永不丢失*的事实，请考虑技巧 4（添加到 `/memory`）——因为记忆条目将在压缩中存活（无论如何它们将被重新插入，因为它们在 `GEMINI.md` 上下文中）。压缩更多是关于临时聊天内容。

**一个小的注意事项：** 压缩之后，AI 的风格可能会略有改变，因为它实际上看到一个"全新的"带有摘要的对话。它可能会重新介绍自己或改变语气。您可以指示它如"从这里继续...（我们压缩了）"来平滑它。实际上，它通常都能很好地继续。

总结，**随着会话变长，请使用 /compress **来保持性能和相关性。这有助于 Gemini CLI 专注于大局，而不是对话历史中的每一个细节。这样，你就可以进行马拉松式的调试会话或广泛的设计讨论，而不会耗尽 AI 正在书写的"思维纸张"。

## 技巧16: 用 `!` 执行 Shell 命令（和你的终端对话）

**快速应用场景：** 在 Gemini CLI 的任何时刻，你都可以在命令前加上 `!` 来执行真实的 shell 命令。比如，你想查看 git 状态，直接输入 `!git status` 就会在你的终端中执行。这样就不用来回切换窗口或上下文了——你仍然在 Gemini CLI 中，但实际上是在告诉它"让我快速执行这个命令"。

这个技巧介绍的是 Gemini CLI 中的 **Shell 模式**。有两种使用方式：

* **单次命令：** 在提示符前加上 `!`，后面跟上任何命令和参数。这会在当前工作目录执行该命令，并直接显示输出结果。例如：

```bash
!ls -lh src/
```

会列出 `src` 目录中的文件，输出效果和正常终端一样。输出完成后，Gemini 提示符会重新出现，你可以继续聊天或执行更多命令。

* **持续 Shell 模式：** 如果只输入 `!` 并按回车，Gemini CLI 会切换到一个子模式，出现 shell 提示符（通常显示为 `shell>` 或类似形式）。现在你可以交互式地输入多个 shell 命令。这基本上就是 CLI 内置的一个迷你终端。再次输入 `!`（或 `exit`）就可以退出这个模式。例如：

```bash
!
shell> pwd
/home/alice/project
shell> python --version
Python 3.x.x
shell> !
```

最后一个 `!` 之后，你就回到了正常的 Gemini CLI。

**这有什么用？** 因为开发工作本身就是在操作和询问之间不断切换。你可能正在和 AI 讨论某个问题，突然需要编译代码或运行测试来验证什么。不用离开对话，你就能快速完成操作，然后把结果反馈给聊天。事实上，Gemini CLI 本身也经常在工具使用中这样做（比如你要求修复测试时，它可能会自动运行 `!pytest`）。但作为用户，你完全可以手动控制。

**实际例子：**

* Gemini 建议修改代码后，你可以用 `!npm run build` 检查是否能编译通过，然后复制任何错误信息让 Gemini 帮忙解决。

* 想用 `vim` 或 `nano` 打开文件？你甚至可以通过 `!nano filename` 来启动（不过要注意，由于 Gemini CLI 有自己的界面，在里面使用交互式编辑器可能有点别扭——最好还是用内置的编辑器集成或复制到你的编辑器中）。

* 可以用 shell 命令为 AI 收集信息：比如用 `!grep TODO -R .` 找到项目中所有的 TODO，然后让 Gemini 帮忙处理这些待办事项。

* 或者简单用于环境任务：需要时用 `!pip install some-package` 安装包，都不用离开 CLI。

**无缝交互：** 一个很酷的特性是，对话内容可以直接引用命令输出。比如，你可以用 `!curl http://example.com` 获取一些数据，看到输出后，立即对 Gemini 说"把上面的输出格式化成 JSON"——因为输出内容显示在聊天中，AI 就有了处理它的上下文（只要内容不太大）。

**将终端作为默认 Shell：** 如果你发现自己总是在命令前加 `!`，其实可以把 shell 模式设为默认。一种方法是用特定工具模式启动 Gemini CLI（有默认工具的概念）。但更简单的方法：如果你计划运行大量手动命令而只是偶尔和 AI 交流，就在会话开始时直接进入 shell 模式（只输入 `!`）。然后你想提问时随时退出 shell 模式。这就像把 Gemini CLI 变成了你的正常终端，只是恰好有个 AI 随时待命。

**与 AI 规划的集成：** 有时 Gemini CLI 本身会建议运行某个 shell 命令。如果你同意，效果就和你手动输入 `!command` 一样。理解了这一点，你就知道可以随时干预。如果 Gemini 卡住了或者你想尝试什么，不用等它建议——你直接做就是，然后继续。

总而言之，`!` **透传机制**意味着*你不需要为了 shell 任务离开 Gemini CLI*。它打破了和 AI 聊天与执行系统命令之间的界限。作为专业用户，这对效率来说太棒了——你的 AI 和你的终端成了一个持续的工作环境。

## 技巧17：将每个 CLI 工具视为潜在的 Gemini 工具

**快速使用场景：** Gemini CLI 可以利用您系统上安装的**任何**命令行工具解决问题。AI 可以访问 shell，所以如果您有 `cURL`、`ImageMagick`、`git`、`Docker` 或任何其他工具，Gemini 可以在适当时调用它。换句话说，*您的整个 `$PATH` 是 AI 的工具包*。这极大地扩展了它能做的事情——远超出了其内置工具的范围。

例如，假设您问："将此文件夹中的所有 PNG 图像转换为 WebP 格式。"如果您安装了 ImageMagick 的 `convert` 实用程序，Gemini CLI 可能计划类似于：为每个[文件](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=%3E%20%21for%20f%20in%20,png%7D.webp%22%3B%20done)使用带有 `convert` 命令的 shell 循环。确实，之前一篇博客的示例显示了这一点，其中用户提示批量转换图像，Gemini 使用 `convert` [工具](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=)执行了一个 shell 单行命令。

另一个场景："将我的应用程序部署到 Docker。"如果存在 `Docker CLI`，AI 可以根据需要调用 `docker build` 和 `docker run` 步骤。或者"使用 FFmpeg 从 `video.mp4` 提取音频"——它可以构建 `ffmpeg` 命令。

这个技巧是关于心态：**Gemini 不仅限于工具内部已有的功能**（这已经相当广泛）。它可以弄清楚如何使用其他可用的程序来实现[目标](https://medium.com/google-cloud/gemini-cli-tutorial-series-part-4-built-in-tools-c591befa59ba#:~:text=In%20this%20part%2C%20we%20looked,In%20the%20next%20part%2C%20we)。它知道常见的语法，如果需要可以读取帮助文本（它可以在工具上调用 `--help`）。唯一的限制是安全性：默认情况下，它会对它想到的任何 `run_shell_command` 请求确认。但当您变得舒适时，您可能允许某些良性命令自动执行（请参阅 YOLO 或允许工具配置）。

**注意：** "能力越大，责任越大。"由于每个 shell 工具都是公平的游戏，您应该确保您的 `$PATH` 不包含任何您不希望 AI 意外运行的内容。这就是技巧 19（自定义 PATH）的用武之地——一些用户为 Gemini 创建了一个受限制的 `$PATH`，因此它不能，比如说，直接调用系统破坏性命令，或者可能不递归调用 `gemini`（以避免循环）。关键是，默认情况下如果 `gcc` 或 `terraform` 或任何东西在 `$PATH` 中，Gemini 可以调用它。这并不意味着它会随机这样做——只有当任务需要它时——但这是可能的。

**思维过程示例：** 想象一下你让 Gemini CLI："搭建一个基础的 HTTP 服务器来提供当前目录的服务。"AI 可能会想："我可以用 Python 内置的服务器来实现这个功能。"然后它会执行 !python3 -m http.server 8000 命令。看，它就这样使用了系统工具（Python）来启动服务器。这只是一个无害的例子。再比如："检查这个 Linux 系统的内存使用情况。"AI 可能会使用 free -h 命令或者读取 /proc/meminfo 文件。它实际上就是通过使用可用的命令来执行系统管理员会做的事情。

**所有工具都是 AI 能力的延伸**： 这听起来有点未来感，但不妨这样理解：任何命令行程序都可以看作是 AI 可以调用来扩展自身能力的"函数"。需要解决数学问题？它可以调用 bc （计算器）。需要处理图像？它可以调用图像处理工具。需要查询数据库？只要安装了相应的客户端并且有访问凭据，它就能使用。可能性是无限的。在其他 AI 代理框架中，这被称为工具使用，而 Gemini CLI 的设计充分信任其代理能够选择合适的[工具](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=The%20Gemini%20CLI%20%20is,understanding%20of%20the%20developer%20workflow)。

**当出错时：** 但另一方面，如果 AI 误解了某个工具或者对它产生了幻觉，就可能尝试调用不存在的命令，或者使用错误的参数，从而导致错误。不过这不是什么大问题——你会看到错误信息，可以及时纠正或澄清。实际上，Gemini CLI 的系统提示很可能指导它先进行"试运行"（只是提出命令建议）而不是盲目执行，所以你通常有机会及时发现这些问题。随着时间的推移，开发者们也在不断改进工具选择逻辑，以减少这些失误。

核心要点是：要把 Gemini CLI 想象成一把**超强的瑞士军刀**——不仅有自带的刀片，更包含了你操作系统中的每一个工具。对于标准程序，你不需要专门指导它如何使用；通常它自己就懂，或者能快速搞明白。这极大地扩展了你能完成的任务范围。就像拥有了一个熟悉如何运行你安装的几乎所有程序的初级开发者或运维工程师。

作为专业用户，你甚至可以安装额外的命令行工具来赋予 Gemini 更强大的能力。比如说，如果你安装了云服务的命令行工具（AWS CLI、GCloud CLI 等），理论上只要给出相应提示，Gemini 就能利用它们来管理云资源。当然，一定要确保你理解并信任执行的命令，特别是使用那些功能强大的工具时（你肯定不希望它意外启动庞大的云实例）。但只要使用得当，这个理念——**一切皆可为 Gemini 工具**——就是让它融入你的环境后能力呈指数级增长的关键所在。

## 技巧18：运用多模态功能 - 让 Gemini 识别图像等内容

**快速使用场景：** Gemini CLI 不仅限于文本处理，它还是多模态的。这意味着它可以分析图像、图表，甚至 PDF 文件（只要你提供）。充分利用这一点吧。比如说，你可以说："这是一个错误对话框的截图， @./error.png - 帮我排查一下这个问题。"AI 就会"看见"这张图片并给出相应的回应。

Google Gemini 模型（以及其前身 PaLM2 的 Codey 形式）的突出特性之一就是图像理解能力。在 Gemini CLI 中，当你用 @ 引用图像时，模型会接收到图像数据。它可以输出描述、分类，或对图像内容进行推理。我们已经讨论过根据内容重命名图像（技巧 14）和描述截图（技巧 7）。但让我们看看其他一些有创意的用法：

* **UI/UX 反馈：** 如果你是和设计师一起工作的开发者，你可以直接把UI设计图丢给Gemini，让它来反馈意见或者生成代码。比如，你可以说："看看这个UI原型 @mockup.png ，然后为它生成一个React组件结构。"Gemini能够识别图中的各种元素（比如标题栏、按钮等），然后为你规划出相应的代码结构。

* **图片整理：** 除了重命名，如果你有一堆杂乱的图片文件夹，想要按内容进行分类整理，可以说："把 ./photos/ 文件夹里的图片按主题分类到子文件夹中（比如日落、山脉、人物等）。"AI会逐一查看每张照片并对其进行分类（这和很多相册App用AI自动整理图片的功能类似 - 现在你可以通过Gemini用自己的脚本来实现了）。

* **OCR 和数据提取：** 如果你有错误信息的截图或者文档的照片，Gemini通常能从中读取文字。比如，"从 invoice.png 中提取文本并整理成结构化格式。"正如Google Cloud博客中的一个例子所示，Gemini CLI可以处理一组发票图片，[并输出包含这些发票信息的表格](https://medium.com/google-cloud/gemini-cli-tutorial-series-part-4-built-in-tools-c591befa59ba#:~:text=Press%20enter%20or%20click%20to,view%20image%20in%20full%20size)。它基本上完成了OCR识别+内容理解，从发票图片中提取出发票号、日期、金额等信息。这是一个高级用例，但在底层多模态模型的支持下是完全可以实现的。

* **理解图形或图表：** 如果你有图表截图，可以问："解读下这张图的关键信息 @chart.png 。"它可能会帮你解读坐标轴和数据趋势。虽然准确性可能会有差异，但这是一个很值得尝试的便捷功能。

为了能实际应用：当您使用 @image.png 引用图片时，请确保图片不要过大（尽管模型能够处理尺寸合理的图片）。CLI工具会将图片进行编码并发送给模型处理。响应结果可能会包含图片描述或相关操作建议。您还可以在同一个提示中混合使用文本和图片引用。

**非图像模态：** CLI工具和模型还可以处理PDF和音频文件，通过内部工具进行转换。例如，当您引用 @report.pdf 时，Gemini CLI会在后台使用PDF转文本工具来提取内容并生成摘要。如果您引用 @audio.mp3 并要求转录，它可能会调用音频转文本工具（如语音识别功能）。速查表显示支持引用PDF、音频和视频文件，这应该是通过调用相应的内部工具或API来实现的。因此，像"转录这段访谈音频： @interview.wav "这样的命令实际上是可行的（即使现在不行，很可能很快就会支持，因为底层的Google语音转文本API可以集成进来）。

**丰富输出格式**： 多模态也意味着AI可以在响应中返回图像内容（如果进行了相关集成）。不过在CLI环境中，通常不会直接显示图像，但可能会保存图片文件或输出ASCII艺术等形式。之前提到的MCP功能表明，工具能够返回图像数据。例如，AI绘图工具可以生成图像，然后Gemini CLI可以通过打开文件或提供链接的方式来呈现这张图片。

**重要提示**： CLI工具本身是基于文本的，所以您无法在终端中直接看到图像（除非支持ASCII预览功能）。您只会获得对图像的分析结果。因此，这主要是为了读取图像内容，而不是显示图像。如果您使用的是VS Code集成环境，那么图片可能会在聊天视图中显示出来。

总而言之，**使用Gemini CLI时不要忘记GUI中的"I"（Interface）** - 在很多情况下，它能很好地处理图像信息，就像处理文本一样轻松。这开启了许多工作流程，比如可视化调试、设计辅助、从截图中提取数据等，所有这些都可以在同一个工具中完成。这是其他一些CLI工具目前可能不具备的差异化优势。随着模型的不断改进，这种多模态支持将变得更加强大，因此掌握这项技能是一项面向未来的能力。

## 技巧19：自定义 `$PATH`（和工具可用性）以获得稳定性

**快速使用场景：** 如果您发现Gemini CLI出现混乱或调用了错误的程序，可以考虑使用定制的 $PATH 环境变量来运行它。通过限制或排序可执行文件的访问顺序，您可以防止AI调用到意料之外的同名脚本。从本质上说，您就是将其工具访问限制在一个可信的沙盒环境中，只允许使用已知安全的工具。

对大多数用户来说，这并不是什么问题，但对于拥有大量自定义脚本或多个工具版本的专业用户来说，这可能会很有帮助。开发者提到的一个原因是避免无限循环或[奇怪的行为](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=We%20built%20a%20CLI%20tool,trash%20folder%20for%20manual%20deletion)。例如，如果 gemini 命令本身就在 $PATH 中，失控的AI可能会在Gemini内部递归调用 gemini （虽然这是个奇怪的场景，但理论上是有可能的）。又或者您有一个名为 test 的命令与其他工具产生了冲突——AI可能会调用到错误的那个命令。

**如何为 Gemini 设置 PATH：** 最简单的方法是在启动时直接设置：

```bash
PATH=/usr/bin:/usr/local/bin gemini
```

这样运行Gemini CLI时，使用的 $PATH 就只限于指定的目录。您可以排除那些存放实验性或危险脚本的目录。另外，您也可以创建一个小的shell脚本包装器，先清理或调整 $PATH ，然后再执行 gemini 。

另一种方法是使用环境变量或配置来明确禁用某些工具。例如，如果您绝对不希望AI使用 rm 或某些破坏性工具，理论上可以在安全的 $PATH 中创建一个别名或空的 rm 命令（但这可能会干扰正常操作，所以不太推荐）。更好的方法是在设置中使用排除列表。在扩展或 settings.json 中，您可以排除特定的工具名称。例如：

```json
"excludeTools": ["run_shell_command"]
```

这个极端的例子会阻止所有shell命令的执行（使Gemini变为只读模式）。更细粒度的控制方式是跳过某些命令的确认提示；同样地，您也可以进行类似这样的配置：

```json
"tools": {
  "exclude": ["apt-get", "shutdown"]
}
```

*（此语法是说明性的；确切用法请参阅文档。）*

其原理是，通过控制环境来降低AI使用不当工具造成失误的风险。这就类似于给家里做儿童安全防护。

**防止无限循环：** 有个用户遇到过Gemini不断[读取自己的输出或重复读取文件的无限循环情况](https://support.google.com/gemini/thread/337650803/infinite-loops-with-tool-code-in-answers?hl=en#:~:text=Community%20support,screen%20with%20weird%20scrolling)。自定义 $PATH 无法直接修复逻辑循环，但其中一个原因可能是AI调用了某个命令而触发了自身调用。确保它不会意外启动另一个AI实例（比如AI决定调用 bard 或 gemini 命令）是很重要的做法。将那些命令从 $PATH 中移除（或者在当前会话中重命名它们）就能帮助避免这种情况。

**通过沙箱隔离：** 除了修改 $PATH 之外，另一个选择是使用 --sandbox 模式（它会通过Docker或Podman在隔离环境中运行工具）。在这种情况下，AI的所有操作都被限制在容器内，只能使用沙盒镜像中提供的工具。您可以提供一个经过精心挑选的Docker镜像，里面包含特定的工具集。这种方法虽然比较重量级，但安全性非常高。

**为特定任务自定义 PATH**： 你可以为不同项目设置不同的 $PATH 配置。例如，在一个项目中，你希望使用特定版本的 Node.js 或本地工具链。使用指向这些版本的 $PATH 来启动 gemini ，就能确保 AI 使用正确的版本。本质上，要把 Gemini CLI 当作普通用户看待——它使用你提供的任何环境。因此，如果你需要它选择 gcc-10 而不是 gcc-12 ，相应地调整 $PATH 或 CC 环境变量即可。

**总结来说**： 给Gemini CLI设置防护栏。 作为高级用户，你能够微调 AI 的运行环境。如果你发现某种不良行为模式与工具使用相关，调整 $PATH 就是快速解决方案。日常使用中你可能不需要这样做，但这是一个专业技巧，值得在将 Gemini CLI 集成到自动化或 CI 环境时记住：给它一个受控的环境。这样你就能确切知道它能做什么和不能做什么，从而提高可靠性。

## 技巧 20：通过令牌缓存和统计来跟踪和降低令牌消耗
如果你运行长对话或重复附加相同的大文件，可以通过启用令牌缓存和监控使用情况来降低成本和延迟。使用 API 密钥或 Vertex AI 认证时，Gemini CLI 会自动重用之前发送的系统指令和上下文，让后续请求更便宜。你可以在 CLI 中实时看到节省的效果。

**如何使用**

选择启用缓存的认证模式。使用 Gemini API 密钥或 Vertex AI 认证时可使用令牌缓存功能。目前 OAuth 登录暂不支持此功能。[参见文档:Google Gemini](https://google-gemini.github.io/gemini-cli/docs/cli/token-caching.html)

检查你的使用情况和缓存命中率。在会话期间运行 stats 命令。它会显示总令牌数，以及在缓存激活时的 cached 字段。
```bash
/stats
```
命令的描述和缓存报告行为在[命令参考和 FAQ 中有文档说明](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html?utm_source=chatgpt.com)

在脚本中捕获指标。无头运行时，输出 JSON 并解析 stats 块，其中包含每个模型的 tokens.cached:

```bash
gemini -p "Summarize README" --output-format json
```
无头指南记录了包含缓存令牌计数的 JSON 架构: [https://google-gemini.github.io/gemini-cli/docs/cli/headless.html](https://google-gemini.github.io/gemini-cli/docs/cli/headless.html)

将会话摘要保存到文件：对于 CI 或预算跟踪，将 JSON 会话摘要写入磁盘。
```bash
gemini -p "Analyze logs" --session-summary usage.json
```
使用 API 密钥或 Vertex 身份验证时，CLI 会自动重用之前发送的上下文，这样后续对话就能发送更少的令牌。保持 GEMINI.md 和大文件引用在多次对话中的稳定性可以提高缓存命中率；你会在统计信息中看到这一效果反映为缓存的令牌。

## 技巧21: 使用 /copy 命令快速复制到剪贴板
**快速使用场景**： 立即将 Gemini CLI 中的最新答案或代码片段复制到系统剪贴板，[不含任何多余格式或行号](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html#:~:text=,for%20easy%20sharing%20or%20reuse)。这非常适合将 AI 生成的代码快速粘贴到编辑器中，或与团队成员分享结果。

当 Gemini CLI 提供答案时（特别是多行代码块），你通常想在别处重用它。 /copy 斜杠命令让这一操作变得轻而易举——它直接将 CLI 最后产生的输出复制到剪贴板。与手动选择（可能会抓取行号或提示文本）不同， /copy 只获取原始响应内容。例如，如果 Gemini 刚刚生成了一个 50 行的 Python 脚本，只需输入 /copy 就会将整个脚本放入剪贴板，随时可以粘贴——无需滚动和选择文本。在底层，Gemini CLI 会使用适合你平台的剪贴板工具（例如 macOS 上的 pbcopy 、Windows 上的 clip ）。运行命令后，你通常会看到确认消息，然后就可以将复制的文本粘贴到任何需要的地方。
**工作原理**： /copy 命令要求你的系统有可用的[剪贴板工具](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html#:~:text=,for%20easy%20sharing%20or%20reuse)。在 macOS 和 Windows 上，所需的工具（分别为 pbcopy 和 clip ）通常预装。在 Linux 上，你可能需要安装 xclip 或 xsel 才能让 /copy 正常工作。确保后，你可以在 Gemini CLI 输出答案后的任何时候使用 /copy 。它会捕获整个最后的响应（即使很长）并省略 CLI 可能在屏幕上显示的任何内部编号或格式。这为你省去了传输内容时处理不必要杂质的麻烦。虽然这只是个小功能，但当你迭代代码或整理 AI 生成的报告时，能为你节省大量时间。
**提示**： 如果你发现 /copy 命令不工作，请检查剪贴板工具是否已安装并可访问。例如，Ubuntu 用户应该运行 sudo apt install xclip 来启用剪贴板复制功能。设置完成后， /copy 让你能零阻力地分享 Gemini 
## 技巧 22：掌握 Ctrl+C 用于 Shell 模式和退出

**快速使用场景**： 通过按键干净利落地中断 Gemini CLI 或退出 shell 模式——快速双击可完全退出 CLI——这都归功于万能的 Ctrl+C 

Gemini CLI 的操作类似于 REPL，了解如何中断操作至关重要。按一次 Ctrl+C 会取消当前操作或清除你已开始输入的任何内容，本质上充当"中止"命令。例如，如果 AI 正在生成冗长的答案而你已经看够了，按下 Ctrl+C ——生成会立即停止。如果你已开始输入提示但想放弃， Ctrl+C 会清除输入行，让你可以重新开始。此外，如果你处于 shell 模式（通过输入 ! 激活以运行 shell 命令），单次 Ctrl+C 将退出 shell 模式并返回到正常的 Gemini 提示符（它会向正在运行的 shell 进程发送中断信号）。如果 shell 命令卡住了或者你只是想回到 AI 模式，这极其方便。
连续按两次 Ctrl+C 是完全退出 Gemini CLI 的快捷键。可以这样理解："按一次 Ctrl+C 取消，再按一次 Ctrl+C 退出。" 这种双击操作会向 CLI 发出终止会话的信号（你会看到告别消息或程序关闭）。这比输入 /quit 或关闭终端窗口更快，让你能通过键盘优雅地关闭 CLI。请注意，如果有输入需要清除或有操作需要中断，单次 Ctrl+C 不会退出——它需要第二次按键（在提示符空闲时）才能完全退出。这种设计可以防止在你只想停止当前输出时意外关闭会话。

**提示**： 在 shell 模式下，你也可以按 Esc 键离开 shell 模式并返回到 Gemini 的聊天模式，而无需终止 CLI。如果你更喜欢正式的退出方式， /quit 命令始终可用，可以干净地结束会话。此外，Unix 用户可以在空提示符下使用 Ctrl+D（EOF）退出——如果需要，Gemini CLI 会提示确认。但对于大多数情况，掌握 Ctrl+C 的单击和双击是保持控制的最快方式。

## 技巧 23：使用 settings.json 自定义 Gemini CLI

**快速使用场景**： 通过编辑 settings.json 配置文件来调整 CLI 的行为和外观，以符合你的偏好或项目约定，而不是固守一刀切的默认设置。这让你能够在所有会话中强制执行主题、工具使用规则或编辑器模式等设置。

Gemini CLI 是高度可配置的。在用户主目录（ ~/.gemini/ ）或项目文件夹（仓库内的 .gemini/ ）中，你可以创建 settings.json 文件来覆盖默认设置。几乎 CLI 的每个方面都可以在这里调整——从视觉主题到工具权限。CLI 会合并多个级别的设置：系统范围的默认设置、用户设置和项目特定设置（项目设置会覆盖用户设置）。例如，你可能有一个全局的深色主题偏好，但某个特定项目可能需要更严格的工具沙盒限制；你可以通过在不同级别的 settings.json 文件来处理这种情况。

在 settings.json 内部，选项以 JSON 键值对的形式指定。以下是一个展示一些有用自定义配置的代码片段:
```json
{
"theme": "GitHub",
"autoAccept": false,
"vimMode": true,
"sandbox": "docker",
"includeDirectories": ["../shared-library", "~/common-utils"],
"usageStatisticsEnabled": true
}
```

在这个例子中，我们将主题设置为 "GitHub"（一种流行的配色方案），禁用 autoAccept （这样 CLI 在运行可能有潜在影响的工具前总是会询问），启用输入编辑器的 Vim 键绑定，并强制使用 Docker 进行工具沙盒化。我们还在工作区上下文中添加了一些目录（ includeDirectories ），这样 Gemini 默认就能看到共享路径中的代码。最后，我们将 usageStatisticsEnabled 保持为 true 来收集基本使用统计（如果启用了遥测，这会提供数据支持）。还有更多可用设置——比如定义自定义颜色主题、调整令牌限制，或列入白名单/黑名单特定工具——所有这些都记录在配置指南中。通过调整这些设置，你可以确保 Gemini CLI 为你的工作流程表现最佳（例如，一些开发者为了效率总是希望开启 vimMode ，而其他人可能更喜欢默认编辑器）。

编辑设置的一个便捷方式是通过内置的设置 UI。在 Gemini CLI 中运行命令 `/settings`，它会为你的配置打开一个交互式编辑器。这个界面让你可以浏览和搜索带描述的设置，并通过验证输入来防止 JSON 语法错误。你可以通过友好的菜单调整颜色、切换 `yolo`（自动批准）等功能、调整检查点（文件保存/恢复行为）等。更改会保存到你的 `settings.json`，有些更改会立即生效（其他可能需要重启 CLI）。

**提示：** 为不同需求维护单独的项目特定 `settings.json` 文件。例如，在团队项目中你可能设置 `"sandbox": "docker"` 和 `"excludeTools": ["run_shell_command"]` 来锁定危险操作，而个人项目可能允许直接 shell 命令。Gemini CLI 会自动在项目目录树中找到最近的 `.gemini/settings.json` 并将其与你的全局 `~/.gemini/settings.json` 合并。另外，别忘了你可以快速调整视觉偏好：尝试使用 `/theme` 交互式切换主题而无需编辑文件，这对于找到舒适的外观很棒。一旦找到喜欢的主题，就把它放入 `settings.json` 使其永久生效。


## 技巧 24：利用 IDE 集成（VS Code）获取上下文和差异比较

**快速使用场景：** 通过将 Gemini CLI 链接到 VS Code 来增强其功能——CLI 会自动知道你正在处理哪些文件，甚至会在 VS Code 的差异编辑器中为你打开 AI 提供的代码更改。这在 AI 助手和你的编码工作空间之间创建了无缝循环。

Gemini CLI 的强大功能之一是它与 Visual Studio Code 的 **IDE 集成**。通过在 VS Code 中安装官方的 *Gemini CLI Companion* 扩展并连接它，你允许 Gemini CLI 对你的编辑器变得"上下文感知"。这在实践中意味着什么？当连接时，Gemini 知道你打开的文件、当前光标位置，[以及你在 VS Code 中选择的任何文本](https://developers.googleblog.com/en/gemini-cli-vs-code-native-diffing-context-aware-workflows/?source=post_page-----26afd3422028---------------------------------------#:~:text=,working%20on%20at%20the%20moment)。所有这些信息都会输入到 AI 的上下文中。所以如果你问"解释这个函数"，Gemini CLI 可以看到你高亮的确切函数并给出相关答案，而无需你将代码复制粘贴到提示中。该集成共享最多你最近打开的 10 个文件，加上选择和光标信息，让模型对你的工作空间有丰富的理解。

另一个巨大优势是代码更改的**原生差异比较**。当 Gemini CLI 建议修改你的代码时（例如，"重构这个函数"并生成补丁），它可以自动在 VS Code 的差异查看器中打开这些更改。你会在 VS Code 中看到并排的差异视图，显示建议的编辑。然后你可以使用 VS Code 熟悉的界面来检查更改、进行任何手动调整，[甚至通过单击接受补丁](https://developers.googleblog.com/en/gemini-cli-vs-code-native-diffing-context-aware-workflows/?source=post_page-----26afd3422028---------------------------------------#:~:text=%2A%20Native%20in,the%20code%20right%20within%20this)。CLI 和编辑器保持同步——如果你在 VS Code 中接受差异，Gemini CLI 会知道并在应用这些更改后继续会话。这个紧密循环意味着你不再需要将代码从终端复制到编辑器；AI 的建议直接流入你的开发环境。

**如何设置：** 如果你在 VS Code 的集成终端中启动 Gemini CLI，它会检测到 VS Code 并通常会提示你自动安装/连接扩展。如果你同意，它将运行必要的 `/ide install` 步骤。如果你没有看到提示（或稍后启用），只需打开 Gemini CLI 并运行命令：`/ide install`。这将为你获取并安装"Gemini CLI Companion"扩展到 VS Code 中。接下来，运行 `/ide enable` 建立连接——CLI 然后会指示它已链接到 VS Code。你可以随时使用 `/ide status` 验证，它会显示是否已连接并列出正在跟踪的编辑器和文件。从那时起，Gemini CLI 将自动从 VS Code 接收上下文（打开的文件、选择），并在需要时在 VS Code 中打开差异。它本质上将 Gemini CLI 变成一个存在于你的终端中但完全了解你的 IDE 的 AI 结对程序员。

目前，VS Code 是此集成的主要支持编辑器。（其他支持 VS Code 扩展的编辑器，如 VSCodium 或通过插件的一些 JetBrains，可能通过相同的扩展工作，但目前官方支持的是 VS Code。）不过设计是开放的——有 IDE Companion 规范用于与其他编辑器开发类似的集成。所以未来我们可能会看到对像 IntelliJ 或 Vim 等 IDE 的一流支持，通过社区扩展实现。


**提示：** 连接后，你可以使用 VS Code 的命令面板来控制 Gemini CLI 而无需离开编辑器。例如，按 **Ctrl+Shift+P**（Mac 上为 Cmd+Shift+P）并尝试像 **"Gemini CLI: Run"**（在终端中启动新的 CLI 会话）、**"Gemini CLI: Accept Diff"**（批准并应用打开的差异）或 **"Gemini CLI: Close Diff Editor"**（拒绝更改）等命令。这些快捷键可以进一步简化你的工作流程。而且记住，你不必总是手动启动 CLI——如果你启用了集成，Gemini CLI 本质上就成为了 VS Code 内部的 AI 协同开发者，观察上下文并在你处理代码时随时准备提供帮助。


## 技巧 25：使用 `Gemini CLI GitHub Action` 自动化仓库任务

**快速应用场景：** 让 Gemini 在 GitHub 上发挥作用 - 使用 **Gemini CLI GitHub Action** 来自动分类新 issue 和审查仓库中的拉取请求，就像一个 AI 团队成员一样处理日常开发[任务](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=1,write%20tests%20for%20this)。


Gemini CLI 不仅仅适用于交互式终端会话；它还可以通过 GitHub Actions 在 CI/CD 流水线中运行。Google 提供了一个现成的 **Gemini CLI GitHub Action**（目前处于测试阶段），可以集成到您仓库的[工作流](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=It%E2%80%99s%20now%20in%20beta%2C%20available,cli)中。这实际上是在 GitHub 上的项目中部署了一个 AI 代理。它在后台运行，由仓库[事件](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=Triggered%20by%20events%20like%20new,do%2C%20and%20gets%20it%20done)触发。

例如，当有人提交一个新的 **issue** 时，Gemini Action 会自动分析 issue 描述，应用相关标签，甚至确定其优先级或建议可能的重复项（这就是"智能 issue 分类"[工作流](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=1,attention%20on%20what%20matters%20most)）。当开启 **pull request** 时，Action 会启动并提供 **AI 代码审查** - 它会在 PR 上评论，提供关于代码质量、潜在错误或风格[改进](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=attention%20on%20what%20matters%20most,more%20complex%20tasks%20and%20decisions)的见解。这让维护者在任何人工查看之前就能获得对 PR 的即时反馈。

或许最酷的功能是 **on-demand collaboration(按需协作)**：团队成员可以在 issue 或 PR 评论中提及 `@gemini-cli` 并给出指令，比如"`@gemini-cli` 请为此编写单元测试"。Action 会接收到这个信息，Gemini CLI 会尝试完成请求（例如，通过添加包含新测试的提交）。这就像在您的仓库中有一个 AI 助手，随时准备在需要时处理杂务。

设置 Gemini CLI GitHub Action 非常简单。首先，确保您在本地安装了 **0.1.18 或更高版本**的 Gemini CLI（这确保了与 [Action](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=Gemini%20CLI%20GitHub%20Actions%20is,for%20individual%20users%20available%20soon)的兼容性）。然后，在 Gemini CLI 中运行特殊命令：[`/setup-github`](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:textTo%20get%20started%2C%20download%20Gemini,cli)。这个命令会在您的仓库中生成必要的工作流文件（如果需要，它会引导您完成身份验证）。具体来说，它会在 `.github/workflows/` 目录下添加 YAML 工作流文件（用于 issue 分类、PR 审查等）。

您需要将 Gemini API 密钥添加到仓库的机密信息中（作为 `GEMINI_API_KEY`），这样 Action 就可以使用 Gemini [API](https://github.com/google-github-actions/run-gemini-cli#:~:text=Store%20your%20API%20key%20as,in%20your%20repository)。一旦完成并提交了工作流，GitHub Action 就会启动 - 从那一刻起，Gemini CLI 将根据这些工作流自主响应新的 issue 和 PR。


由于这个 Action 本质上是以自动化方式运行 Gemini CLI，您可以像自定义 CLI 一样自定义它。默认设置包含三个工作流（issue 分类、PR 审查和通用的提及触发助手），这些工作流是**完全开源且可[编辑的](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=Think%20of%20these%20initial%20workflows,into%20Gemini%20CLI%20GitHub%20Actions)**。您可以调整 YAML 来修改 AI 的行为，甚至添加新的工作流。

例如，您可以创建一个夜间工作流，使用 Gemini CLI 扫描仓库中的过期依赖项，或基于最近的代码更改更新 README - 可能性是无穷的。这里的关键好处是将繁琐或耗时的任务交给 AI 代理处理，让人类开发者可以专注于更困难的问题。而且由于它在 GitHub 的基础设施上运行，不需要您的干预 - 它真正是一个"设置即忘记"的 AI 助手。


**提示：** 关注 GitHub Actions 日志中 Action 的输出以保持透明度。Gemini CLI Action 日志会显示它运行了什么提示以及做出了或建议了什么更改。这既能建立信任，也能帮助您优化其行为。此外，团队还在 Action 中内置了企业级安全保障 - 例如，您可以要求 AI 在工作流中尝试运行的所有 shell 命令都必须经过您的[允许列表](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=in%20your%20environment%2C%20drastically%20reducing,your%20preferred%20observability%20platform%2C%20like)批准。所以即使在严肃的项目中也不要犹豫使用它。

如果您使用 Gemini CLI 想出了一个很酷的自定义工作流，考虑将其贡献回社区 - 项目欢迎在他们的仓库中提出新想法！


## 技巧 26：启用Telemetry以获得指标和可观测性

**快速应用场景：** 通过启用内置的 **OpenTelemetry** 仪表化来深入了解 Gemini CLI 的使用情况和性能表现 - 监控 AI 会话的指标、日志和追踪，分析使用模式或排除[问题](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text:,across%20teams%2C%20track%20costs%2C%20ensure)。

对于喜欢测量和优化的开发者来说，Gemini CLI 提供了一个可观测性功能，可以揭示内部发生的情况。通过利用 **OpenTelemetry (OTEL)**，Gemini CLI 可以上报关于您[会话](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text:Built%20on%20OpenTelemetry%20%E2%80%94%20the,Gemini%20CLI%E2%80%99s%20observability%20system%20provides)的结构化遥测数据。这包括指标（如使用的令牌数量、响应延迟）、所采取操作的日志，甚至工具调用的追踪。

启用遥测后，您可以回答诸如：*我最常使用哪个自定义命令？本周 AI 在这个项目中编辑文件的次数是多少？当我要求 CLI 运行测试时的平均响应时间是多少？*这类数据对于理解使用模式和[性能](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text:,across%20teams%2C%20track%20costs%2C%20ensure)至关重要。团队可以用它来查看开发者如何与 AI 助手交互，以及可能存在的瓶颈在哪里。

默认情况下，遥测是**关闭**的（Gemini 尊重隐私和性能）。您可以通过在 `settings.json` 中设置 `"telemetry.enabled": true` 或使用标志 [`--telemetry`](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text:Setting%20Environment%20Variable%20CLI%20Flag,grpc) 启动 Gemini CLI 来选择启用。此外，您可以选择遥测数据的**目标**：可以在**本地**记录或发送到像 Google Cloud 这样的后端。

为了快速开始，您可以设置 `"telemetry.target": "local"` - 这样，Gemini 会简单地将遥测数据写入本地文件（默认情况下）或您通过 `["outfile"](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text:disable%20telemetry%20,file%20path)` 指定的自定义路径。本地遥测包括您可以解析或输入到工具中的 JSON 日志。

为了更强大的监控，设置 `"target": "gcp"`（Google Cloud）或甚至与其他 OpenTelemetry 兼容的系统（如 Jaeger 或 [Datadog](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text:,between%20backends%20without%20changing%20your)）集成。事实上，Gemini CLI 的 OTEL 支持是厂商中立的 - 您可以将数据导出到您偏好的几乎任何可观测性堆栈（Google Cloud Operations、Prometheus、[等等](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text:,between%20backends%20without%20changing%20your)）。

Google 为 Cloud 提供了简化的路径：如果您指向 GCP，CLI 可以直接将数据发送到您项目中的 Cloud Logging 和 Cloud Monitoring，在那里您可以使用通常的仪表板和警报[工具](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text:2,explorer%20%2A%20Traces%3A%20https%3A%2F%2Fconsole.cloud.google.com%2Ftraces%2Flist)。


您能获得什么样的洞察？遥测捕获诸如工具执行、错误和重要里程碑等事件。它还记录指标，如提示处理时间和每个[提示](https://medium.com/google-cloud/gemini-cli-tutorial-series-part-13-gemini-cli-observability-c410806bc112#:~:text:,integrate%20with%20existing%20monitoring%20infrastructure)的令牌计数。

对于使用分析，您可以聚合团队中每个斜杠命令的使用次数，或代码生成被调用的频率。对于性能监控，您可以跟踪响应是否变慢，这可能表明达到 API 速率限制或模型更改。对于调试，您可以看到工具抛出的错误或异常（例如，`run_shell_command` 失败）随上下文一起记录。

如果将这些数据发送到像 Google Cloud Monitoring 这样的平台，所有这些数据都可以可视化 - 例如，您可以创建"每天使用的令牌数"或"工具 X 的错误率"的仪表板。它本质上为您提供了窥视 AI "大脑"和您使用情况的窗口，这在企业环境中特别有帮助，以确保一切运行[顺畅](https://medium.com/google-cloud/gemini-cli-tutorial-series-part-13-gemini-cli-observability-c410806bc112#:~:text:resource%20utilization%20%2A%20%20Real,integrate%20with%20existing%20monitoring%20infrastructure)。启用遥测确实会带来一些开销（额外的数据处理），所以个人使用时可能不会一直保持开启状态。然而，它对于调试会话或间歇性健康检查非常有用。一种方法是在 CI 服务器或团队的共享环境中启用它来收集统计信息，而在本地则保持关闭状态，除非需要。请记住，您可以随时动态切换：更新设置，如果需要可以使用 `/memory refresh` 重新加载，或使用 `--telemetry` 标志重启 Gemini CLI。此外，所有遥测都在您的控制之下 - 它尊重您为端点和凭据设置的环境变量，因此数据只会到达您想要的地方。这个功能将 Gemini CLI 从黑盒变成了天文台，照亮了 AI 代理如何与您的世界互动，这样您就可以持续改进这种互动。


**提示：** 如果您只是想快速查看当前会话的统计信息（而不需要完整的遥测），使用 `/stats` 命令。它会在 [CLI](https://www.howtouselinux.com/post/the-complete-google-gemini-cli-cheat-sheet-and-guide#:~:text:Command%20Description%20,tag%3E%60Save%20the%20current%20conversation) 中直接输出令牌使用量和会话长度等指标。这是查看即时数据的轻量级方法。但对于长期或多会话分析，遥测是正确的选择。如果您将遥测发送到云项目，考虑设置仪表板或警报（例如，如果错误率激增或令牌使用量达到阈值则警报） - 这可以主动捕获 Gemini CLI 在您团队中使用时出现的问题。

## 技巧 27：关注路线图（后台代理等）

**快速使用场景：** 及时了解即将推出的 Gemini CLI 功能——通过关注公共的 **Gemini CLI 路线图**，你可以在主要计划的增强功能（如*用于长时间运行任务的后台代理*）到达之前就了解它们，让你能够进行规划并提供反馈。

Gemini CLI 正在快速发展，新版本频繁发布，因此关注其未来规划是明智的选择。Google 在 GitHub 上维护着 Gemini CLI 的[公开路线图](https://google-gemini.github.io/gemini-cli/ROADMAP.html#:~:text=quality.%20,related%20to%20security%20and%20privacy)，详细说明了近期重点关注的领域和目标功能。这基本上是一份活文档（以及相关 issue 集合），你可以从中看到开发者的工作重点和即将推出的[功能](https://google-gemini.github.io/gemini-cli/ROADMAP.html#:~:text=This%20document%20outlines%20our%20approach,live%20in%20our%20GitHub%20Issues)。例如，路线图中一个令人期待的项目是后台代理支持——即能够生成在后台运行的自主代理，持续处理任务或异步执行工作。根据路线图讨论，这些后台代理将让你把长时间运行的任务委托给 Gemini CLI，而不会占用你的交互会话。比如，你可以启动一个后台代理来监控项目的特定事件，或者定期执行任务，无论是在本地机器上还是通过部署到 Cloud Run 等服务来实现。该功能旨在直接从 CLI"启用长时间运行的自主任务和主动辅助"，实质上是将 Gemini CLI 的实用性扩展到即时查询之外的场景。

通过关注路线图，你还能了解到其他规划中的功能。这些可能包括新的工具集成、对其他 Gemini 模型版本的支持、UI/UX 改进等等。路线图通常按"领域"组织（例如*扩展性*、*模型*、*后台*等），并经常标注里程碑（如目标交付季度）。这并不能保证某功能的具体发布时间，但能让你很好地了解团队的优先事项。

由于该项目是开源的，你甚至可以深入查看每个路线图项目链接的 GitHub issue，了解设计提案和进展。对于依赖 Gemini CLI 的开发者来说，这种透明度意味着你可以预见变化——也许是某个 API 正在添加你需要的功能，或者即将有破坏性变更，让你能够提前做好准备。

关注路线图很简单，收藏 GitHub 项目看板或标记为"路线图"的 issue，并定期查看。一些重大更新（如扩展功能或 IDE 集成的引入）在正式发布前都在路线图中有所暗示，所以你能提前一窥究竟。此外，Gemini CLI 团队经常鼓励社区对这些未来功能提供反馈。如果你对后台代理等功能有想法或使用案例，通常可以在相关的 issue 或讨论线程中留言，从而影响其开发方向。

**提示：**由于 Gemini CLI 是开源项目（采用 Apache 2.0 许可证），你不仅可以关注路线图，还可以参与其中！维护者欢迎贡献，特别是与路线图一致的项目。如果你真的关心某个功能，可以考虑在预览阶段贡献代码或进行测试。如果你需要的功能尚未出现在路线图中，你可以提出功能请求。路线图页面本身就提供了如何提议变更的指导。参与项目不仅让你保持信息同步，还能让你塑造自己使用的工具。毕竟，Gemini CLI 在设计时就考虑了社区参与，许多最近的功能（如某些扩展和工具）都起源于社区建议。


## 技巧 28：使用插件增强 Gemini CLI

**快速用例：**通过安装即插即用的**插件**为 Gemini CLI 添加新功能——例如集成你喜爱的数据库或云服务——无需费心就能扩展 AI 的工具集。这就像为你的 CLI 安装应用程序，教它掌握新技能。

[插件](https://blog.google/technology/developers/gemini-cli-extensions/#:~:text=Gemini%20CLI%20is%20an%20open,design%20platforms%20to%20payment%20services)是 2025 年底推出的革命性功能：它们让你能够以模块化方式**自定义和扩展** Gemini CLI 的功能。插件本质上是一个配置包（可选包含代码），将 Gemini CLI 连接到外部工具或服务。例如，Google 发布了一套 Google Cloud 插件——有帮助部署应用到 Cloud Run 的，有管理 BigQuery 的，有分析应用安全的，还有更多。合作伙伴和社区开发者也为各种用途构建了插件：Dynatrace（监控）、Elastic（搜索分析）、Figma（设计资源）、Shopify、Snyk（安全扫描）、Stripe（支付）等等，而且这个名单还在不断增长。通过安装合适的插件，你立即赋予 Gemini CLI 使用新的领域特定工具的能力。其美妙之处在于，这些插件带有预定义的**"操作手册"**，教会 AI 如何有效使用这些新工具。这意味着一旦安装，你就可以让 Gemini CLI 执行与这些服务相关的任务，它会知道调用合适的 API 或命令，就像内置了这些知识一样。

使用插件非常简单。CLI 有专门的命令来管理它们：`gemini extensions install <URL>`。通常，你提供插件的 GitHub 仓库 URL 或本地路径，CLI 就会获取并安装它。例如，要安装官方插件，你可能运行：`gemini extensions install https://github.com/google-gemini/gemini-cli-extension-cloud-run`。几秒钟内，插件就会添加到你的环境中（存储在 `~/.gemini/extensions/` 或你项目的 `.gemini/extensions/` 文件夹下）。然后你可以通过在 CLI 中运行 `/extensions` 查看它，该命令会列出活跃的插件。从那时起，AI 就有了新的工具可用。如果是一个 Cloud Run 插件，你可以说"将我的应用部署到 Cloud Run"，Gemini CLI 实际上就能执行这个操作（通过插件的工具调用底层的 `gcloud` 命令）。本质上，插件作为 Gemini CLI 功能的一级扩展存在，但你只需要选择安装需要的那些。

围绕插件有一个**开放的生态系统**。Google 有一个官方插件页面列出了可用的插件，而且由于框架是开放的，任何人都可以创建和分享自己的插件。如果你有特定的内部 API 或工作流，可以为其构建插件，让 Gemini CLI 能够协助处理。编写插件比听起来更容易：通常你创建一个目录（比如 `my-extension/`），在其中创建 `gemini-extension.json` 文件，描述要添加的工具或上下文。你可以定义新的斜杠命令或指定 AI 可以调用的远程 API。无需修改 Gemini CLI 的核心——只需放入你的插件即可。CLI 被设计为在运行时加载这些插件。许多插件通过添加自定义 *MCP 工具*（模型上下文协议服务器或函数）来实现，AI 可以使用这些工具。例如，插件可以通过接入外部翻译 API 来添加 `/translate` 命令；一旦安装，AI 就知道如何使用 `/translate`。关键优势是**模块化**：你只安装需要的插件，保持 CLI 轻量，但可以选择集成几乎任何东西。

要管理插件，除了 `install` 命令外，你还可以通过类似的 CLI 命令更新或移除它们（`gemini extensions update` 或直接删除文件夹）。明智的做法是定期检查你使用的插件是否有更新，因为它们可能获得了改进。CLI 未来可能会引入"插件市场"风格的界面，但目前，探索 GitHub 仓库和官方目录是发现新插件的方式。发布时一些热门插件包括 GenAI **Genkit** 插件（用于构建生成式 AI 应用），以及各种 Google Cloud 插件，涵盖 CI/CD、数据库管理等领域。

**提示：**如果你要构建自己的插件，先从现有的插件中寻找示例。官方文档提供了**插件指南**，其中包含架构和功能说明。创建私有插件的一个简单方法是使用 `GEMINI.md` 中的 `@include` 功能注入脚本或上下文，但完整的插件给你更多能力（如打包工具）。此外，由于插件可以包含上下文文件，你可以用它们预加载领域知识。想象一个为你公司内部 API 创建的插件，包含 API 摘要和调用工具——AI 就会知道如何处理与该 API 相关的请求。简而言之，插件开启了一个新世界，Gemini CLI 可以与任何东西交互。关注插件市场的新增内容，不要犹豫与你创建的有用插件分享给社区——你可能帮助到成千上万的其他开发者。


## 附加：柯基模式彩蛋 🐕

最后，这不是一个提高生产力的技巧，但确实是一个有趣的彩蛋——在 Gemini CLI 中尝试 `*/corgi*` 命令。这会切换到**"柯基模式"**，让可爱的柯基动画在你的终端中奔跑！它虽然不能帮助你更好地编码，但肯定能在漫长的编码会话中轻松一下心情。你会看到一个 ASCII 艺术的柯基在 CLI 界面中飞奔。要关闭它，再次运行 `/corgi` 即可。

这是团队添加的纯娱乐功能（是的，甚至有关于在柯基模式上花费开发时间的调侃讨论）。这表明开发者在工具中隐藏了一些奇思妙想。所以当你需要快速休息一下或想开心一下时，试试 `/corgi` 吧。🐕🎉

*（传闻说可能还有其他彩蛋或模式——谁知道呢？也许是"/partyparrot"之类的。备忘单或帮助命令列出了 `/corgi`，所以这不是秘密，只是很少被使用。现在你也在这个笑话之中了！）*


---

**总结：**

我们已经全面介绍了 Gemini CLI 的专业技巧和功能。从使用 `GEMINI.md` 设置持久上下文，到编写自定义命令和使用 MCP 服务器等高级工具，再到利用多模态输入和自动化工作流，这个 AI 命令行助手能做的事情非常多。作为外部开发者，你可以将 Gemini CLI 整合到日常工作中——它就像你终端中的强大盟友，能够处理繁琐任务、提供见解，甚至排查环境问题。

Gemini CLI 正在快速发展（作为开源项目，有社区贡献），新功能和改进不断涌现。通过掌握本指南中的专业技巧，你将能够充分利用这个工具的全部潜力。这不仅仅是使用 AI 模型——而是将 AI 深度整合到你的软件开发和管理方式中。

祝你在使用 Gemini CLI 时编码愉快，探索一下你的"终端中的 AI 代理"能带你走多远。

**现在你手中拥有了 AI 瑞士军刀——明智地使用它，它将让你成为更高效（或许也更快乐）的开发者**！
