# Gemini CLI Tips & Tricks

**This guide covers \~30 pro-tips for effectively using Gemini CLI for agentic coding**

**Gemini CLI** is an open-source AI assistant that brings the power of Google’s Gemini model directly into your [terminal](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=The%20Gemini%20CLI%20is%20an,via%20a%20Gemini%20API%20key). It functions as a conversational, “agentic” command-line tool – meaning it can reason about your requests, choose tools (like running shell commands or editing files), and execute multi-step plans to help with your development [workflow](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=The%20Gemini%20CLI%20%20is,understanding%20of%20the%20developer%20workflow).

In practical terms, Gemini CLI acts like a supercharged pair programmer and command-line assistant. It excels at coding tasks, debugging, content generation, and even system automation, all through natural language prompts. Before diving into pro tips, let’s quickly recap how to set up Gemini CLI and get it running.

## Getting Started

**Installation:** You can install Gemini CLI via npm. For a global install, use:

> npm install \-g @google/gemini-cli

Or run it without installing using npx:

> npx @google/gemini-cli

Gemini CLI is available on all major platforms (it’s built with Node.js/TypeScript). Once installed, simply run the gemini command in your terminal to launch the interactive [CLI](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Interactive%20Mode%20,conversational%20session).

**Authentication:** On first use, you’ll need to authenticate with the Gemini service. You have two options: (1) **Google Account Login (free tier)** – this lets you use Gemini 2.5 Pro for free with generous usage limits (about 60 requests/minute and 1,000 requests per [day](https://blog.google/technology/developers/introducing-gemini-cli-open-source-ai-agent/#:~:text=Unmatched%20usage%20limits%20for%20individual,developers). On launch, Gemini CLI will prompt you to sign in with a Google account (no billing [required)](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=%2A%20Google,Google%20AI%20Studio%2C%20then%20run). (2) **API Key (paid or higher-tier access)** – you can get an API key from Google AI [Studio](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=1,key%20from%20Google%20AI%20Studio) and set the environment variable GEMINI\_API\_KEY to use [it](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Method%201%3A%20Shell%20Environment%20Variable,zshrc).

API key usage can offer higher quotas and enterprise data‑use protections; prompts aren’t used for training on paid/billed usage, though logs may be retained for [safety](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=responses%20may%20be%20logged%20for,Google%20AI%20Studio%2C%20then%20run).

For example, add to your shell profile:

> export GEMINI\_API\_KEY="YOUR\_KEY\_HERE"

**Basic Usage:** To start an interactive session, just run gemini with no arguments. You’ll get a gemini\> prompt where you can type requests or commands. For instance:

$ gemini  
gemini\> Create a React recipe management app using SQLite

You can then watch as Gemini CLI creates files, installs dependencies, runs tests, etc., to fulfill your request[\[9\]](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=to%20enter%20the%20interactive%20REPL,language%20prompts%20like)[\[10\]](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=and%20watch%20it%20create%20files%2C,and%20show%20you%20the%20results). If you prefer a one-shot invocation (non-interactive), use the \-p flag with a prompt, for example:

> gemini \-p "Summarize the main points of the attached file. @./report.txt"

This will output a single response and [exit](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=gemini). You can also pipe input into Gemini CLI: for example, echo "Count to 10" | gemini will feed the prompt via [stdin](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=gemini%20,txt).

**CLI Interface:** Gemini CLI provides a rich REPL-like interface. It supports **slash commands** (special commands prefixed with / for controlling the session, tools, and settings) and **bang commands** (prefixed with \! to execute shell commands directly). We’ll cover many of these in the pro tips below. By default, Gemini CLI operates in a safe mode where any action that modifies your system (writing files, running shell commands, etc.) will ask for confirmation. When a tool action is proposed, you’ll see a diff or command and be prompted (Y/n) to approve or reject it. This ensures the AI doesn’t make unwanted changes without your consent.

With the basics out of the way, let’s explore a series of pro tips and hidden features to help you get the most out of Gemini CLI. Each tip is presented with a simple example first, followed by deeper details and nuances. These tips incorporate advice and insights from the tool’s creators (e.g. Taylor Mullen) and the Google Developer Relations team, as well as the broader community, to serve as a **canonical guide for power users** of Gemini CLI.

## Tip 1: Use **GEMINI.md** for Persistent Context

**Quick use-case:** Stop repeating yourself in prompts. Provide project-specific context or instructions by creating a GEMINI.md file, so the AI always has important background knowledge without being told every [time](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Context%20Files%20%28).

When working on a project, you often have certain overarching details – e.g. coding style guidelines, project architecture, or important facts – that you want the AI to keep in mind. Gemini CLI allows you to encode these in one or more **GEMINI.md** files. Simply create a .gemini folder (if not already present) in your project, and add a Markdown file named GEMINI.md with whatever notes or instructions you want the AI to persist. For example:

\# Project Phoenix – AI Assistant

\- All Python code must follow PEP 8 style.  
\- Use 4 spaces for indentation.  
\- The user is building a data pipeline; prefer functional programming paradigms.

Place this file in your project root (or in subdirectories for more granular context). Now, whenever you run gemini in that project, it will automatically load these instructions into [context](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Context%20Files%20%28). This means the model will *always* be primed with them, avoiding the need to prepend the same guidance to every prompt.

**How it works:** Gemini CLI uses a hierarchical context loading [system](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Hierarchical%20Loading%3A%20The%20CLI%20combines,The%20loading%20order%20is). It will combine **global context** (from \~/.gemini/GEMINI.md, which you can use for cross-project defaults) with your **project-specific GEMINI.md**, and even context files in subfolders. More specific files override more general ones. You can inspect what context was loaded at any time by using the command:

\> /memory show

This will display the full combined context the AI [sees](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,current%20conversation%20with%20a%20tag). If you make changes to your GEMINI.md, use /memory refresh to reload the context without restarting the [session](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,current%20conversation%20with%20a%20tag).

**Pro Tip:** Use the /init slash command to quickly generate a starter GEMINI.md. Running /init in a new project creates a template context file with information like the tech stack detected, a summary of the project, [etc](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,directory%20workspace%20%28e.g.%2C%20%60add).. You can then edit and expand that file. For large projects, consider breaking the context into multiple files and **importing** them into GEMINI.md with @include syntax. For example, your main GEMINI.md could have lines like @./docs/prompt-guidelines.md to pull in additional context [files](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Modularizing%20Context%20with%20Imports%3A%20You,files). This keeps your instructions organized.

With a well-crafted GEMINI.md, you essentially give Gemini CLI a “memory” of the project’s requirements and conventions. This **persistent context** leads to more relevant responses and less back-and-forth prompt engineering.

## Tip 2: Create Custom Slash Commands

**Quick use-case:** Speed up repetitive tasks by defining your own slash commands. For example, you could make a command /test:gen that generates unit tests from a description, or /db:reset that drops and recreates a test database. This extends Gemini CLI’s functionality with one-liners tailored to your workflow.

Gemini CLI supports **custom slash commands** that you can define in simple configuration files. Under the hood, these are essentially pre-defined prompt templates. To create one, make a directory commands/ under either \~/.gemini/ for global commands or in your project’s .gemini/ folder for project-specific [commands](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Custom%20Commands). Inside commands/, create a TOML file for each new command. The file name format determines the command name: e.g. a file test/gen.toml defines a command /test:gen.

Let’s walk through an example. Say you want a command to generate a unit test from a requirement description. You could create \~/.gemini/commands/test/gen.toml with the following content:

\# Invoked as: /test:gen "Description of the test"  
description \= "Generates a unit test based on a requirement."  
prompt \= """  
You are an expert test engineer. Based on the following requirement, please write a comprehensive unit test using the Jest framework.

Requirement: {{args}}  
"""

Now, after reloading or restarting Gemini CLI, you can simply type:

\> /test:gen "Ensure the login button redirects to the dashboard upon success"

Gemini CLI will recognize /test:gen and substitute the {{args}} in your prompt template with the provided argument (in this case, the requirement). The AI will then proceed to generate a Jest unit test [accordingly](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Example%3A%20%60). The description field is optional but is used when you run /help or /tools to list available commands.

This mechanism is extremely powerful – effectively, you can script the AI with natural language. The community has created numerous useful custom commands. For instance, Google’s DevRel team shared a set of *10 practical workflow commands* (via an open-source repo) demonstrating how you can script common flows like creating API docs, cleaning data, or setting up boilerplate [code](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=,to%20generate%20a%20better%20output). By defining a custom command, you package a complex prompt (or series of prompts) into a reusable shortcut.

**Pro Tip:** Custom commands can also be used to enforce formatting or apply a “persona” to the AI for certain tasks. For example, you might have a /review:security command that always prefaces the prompt with “You are a security auditor…” to review code for vulnerabilities. This approach ensures consistency in how the AI responds to specific categories of tasks.

To share commands with your team, you can commit the TOML files in your project’s repo (under .gemini/commands directory). Team members who have Gemini CLI will automatically pick up those commands when working in the project. This is a great way to **standardize AI-assisted workflows** across a team.

## Tip 3: Extend Gemini with Your Own **MCP** Servers

**Quick use-case:** Suppose you want Gemini to interface with an external system or a custom tool that isn’t built-in – for example, query a proprietary database, or integrate with Figma designs. You can do this by running a custom **Model Context Protocol (MCP) server** and plugging it into Gemini [CLI](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Extend%20the%20CLI%20with%20your,add%7Clist%7Cremove%3E%60%20commands). MCP servers let you add new tools and abilities to Gemini, effectively **extending the agent**.

Gemini CLI comes with several MCP servers out-of-the-box (for instance, ones enabling Google Search, code execution sandboxes, etc.), and you can add your own. An MCP server is essentially an external process (it could be a local script, a microservice, or even a cloud endpoint) that speaks a simple protocol to handle tasks for Gemini. This architecture is what makes Gemini CLI so [extensible](https://blog.google/technology/developers/introducing-gemini-cli-open-source-ai-agent/#:~:text=,interactively%20within%20your%20scripts).

**Examples of MCP servers:** Some community and Google-provided MCP integrations include a **Figma MCP** (to fetch design details from Figma), a **Clipboard MCP** (to read/write from your system clipboard), and others. In fact, in an internal demo, the Gemini CLI team showcased a “Google Docs MCP” server that allowed saving content directly to Google [Docs](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=%2A%20Utilize%20the%20google,summary%20directly%20to%20Google%20Docs). The idea is that whenever Gemini needs to perform an action that the built-in tools can’t handle, it can delegate to your MCP server.

**How to add one:** You can configure MCP servers via your settings.json or using the CLI. For a quick setup, try the CLI command:

gemini mcp add myserver \--command "python3 my\_mcp\_server.py" \--port 8080

This would register a server named “myserver” that Gemini CLI will launch by running the given command (here a Python module) on port 8080\. In \~/.gemini/settings.json, it would add an entry under mcpServers. For example:

"mcpServers": {  
  "myserver": {  
    "command": "python3",  
    "args": \["-m", "my\_mcp\_server", "--port", "8080"\],  
    "cwd": "./mcp\_tools/python",  
    "timeout": 15000  
  }  
}

This configuration (based on the official docs) tells Gemini how to start the MCP server and [where](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Example%20). Once running, the tools provided by that server become available to Gemini CLI. You can list all MCP servers and their tools with the slash command:

\> /mcp

This will show any registered servers and what tool names they [expose](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Command%20Description%20,List%20active%20extensions).

**Power of MCP:** MCP servers can provide **rich, multi-modal results**. For instance, a tool served via MCP could return an image or a formatted table as part of the response to Gemini [CLI](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Capabilities%3A). They also support OAuth 2.0, so you can securely connect to APIs (like Google’s APIs, GitHub, etc.) via an MCP tool without exposing [credentials](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Extend%20the%20CLI%20with%20your,add%7Clist%7Cremove%3E%60%20commands). Essentially, if you can code it, you can wrap it as an MCP tool – turning Gemini CLI into a hub that orchestrates many services.

**Default vs. custom:** By default, Gemini CLI’s built-in tools cover a lot (reading files, web search, executing shell commands, etc.), but MCP lets you go beyond. Some advanced users have created MCP servers to interface with internal systems or to perform specialized data processing. For example, you could have a database-mcp that provides a /query\_db tool for running SQL queries on a company database, or a jira-mcp to create tickets via natural language.

When creating your own, be mindful of security: by default, custom MCP tools require confirmation unless you mark them as trusted. You can control safety with settings like trust: true for a server (which auto-approves its tool actions) or by whitelisting specific safe tools and blacklisting dangerous [ones](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,takes%20precedence).

In short, **MCP servers unlock limitless integration**. They’re a pro feature that lets Gemini CLI become a glue between your AI assistant and whatever system you need it to work with. If you’re interested in building one, check out the official [MCP guide](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Transport%20) and community examples.

## Tip 4: Leverage Memory Addition & Recall

**Quick use-case:** Keep important facts at your AI’s fingertips by adding them to its long-term memory. For example, after figuring out a database port or an API token, you can do:

\> /memory add "Our staging RabbitMQ is on port 5673"

This will store that fact so you (or the AI) don’t forget it [later](https://binaryverseai.com/gemini-cli-open-source-ai-tool/#:~:text=Gemini%20CLI%20Ultimate%20Agent%3A%2060,a%20branch%20of%20conversation). You can then recall everything in memory with /memory show at any time.

The **/memory** commands provide a simple but powerful mechanism for *persistent memory*. When you use /memory add \<text\>, the given text is appended to your project’s global context (technically, it’s saved into the global \~/.gemini/GEMINI.md file or the project’s [GEMINI.md)](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=,load%20memory%20from%20%60GEMINI.md). It’s a bit like taking a note and pinning it to the AI’s virtual bulletin board. Once added, the AI will always see that note in the prompt context for future interactions, across sessions.

Consider an example: you’re debugging an issue and discover a non-obvious insight (“The config flag X\_ENABLE must be set to true or the service fails to start”). If you add this to memory, later on if you or the AI are discussing a related problem, it won’t overlook this critical detail – it’s in the context.

**Using /memory:**

* \*/memory add "\<text\>"\* – Add a fact or note to memory (persistent context). This updates the GEMINI.md immediately with the new entry.

* \*/memory show\* – Display the full content of the memory (i.e. the combined context file that’s currently loaded).

* \*/memory refresh\* – Reload the context from disk (useful if you manually edited the GEMINI.md file outside of Gemini CLI, or if multiple people are collaborating on it).

Because the memory is stored in Markdown, you can also manually edit the GEMINI.md file to curate or organize the info. The /memory commands are there for convenience during conversation, so you don’t have to open an editor.

**Pro Tip:** This feature is great for “decision logs.” If you decide on an approach or rule during a chat (e.g., a certain library to use, or an agreed code style), add it to memory. The AI will then recall that decision and avoid contradicting it later. It’s especially useful in long sessions that might span hours or days – by saving key points, you mitigate the model’s tendency to forget earlier context when the conversation gets long.

Another use is personal notes. Because \~/.gemini/GEMINI.md (global memory) is loaded for all sessions, you could put general preferences or information there. For example, “The user’s name is Alice. Speak politely and avoid slang.” It’s like configuring the AI’s persona or global knowledge. Just be aware that global memory applies to *all* projects, so don’t clutter it with project-specific info.

In summary, **Memory Addition & Recall** helps Gemini CLI maintain state. Think of it as a knowledge base that grows with your project. Use it to avoid repeating yourself or to remind the AI of facts it would otherwise have to rediscover from scratch.

## Tip 5: Use Checkpointing and **/restore** as an Undo Button

**Quick use-case:** If Gemini CLI makes a series of changes to your files that you’re not happy with, you can *instantly roll back* to a prior state. Enable checkpointing when you start Gemini (or in settings), and use the /restore command to undo changes like a lightweight Git [revert](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,Exit%20the%20Gemini%20CLI). /restore rolls back your workspace to the saved checkpoint; conversation state may be affected depending on how the checkpoint was captured.

Gemini CLI’s **checkpointing** feature acts as a safety net. When enabled, the CLI takes a snapshot of your project’s files *before* each tool execution that modifies [files](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=When%20,snapshot%20before%20tools%20modify%20files). If something goes wrong, you can revert to the last known good state. It’s essentially version control for the AI’s actions, without you needing to manually commit to Git each time.

**How to use it:** You can turn on checkpointing by launching the CLI with the \--checkpointing flag:

gemini \--checkpointing

Alternatively, you can make it the default by adding to your config ("checkpointing": { "enabled": true } in [settings.json](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=%7B%20,true)). Once active, you’ll notice that each time Gemini is about to write to a file, it says something like “Checkpoint saved.”

If you then realize an AI-made edit is problematic, you have two options:

* Run \*/restore list\* (or just /restore with no arguments) to see a list of recent checkpoints with timestamps and descriptions.

* Run \*/restore \<id\>\* to rollback to a specific checkpoint. If you omit the id and there’s only one pending checkpoint, it will restore that by [default](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=Step).

For example:

\> /restore

Gemini CLI might output:

0: \[2025-09-22 10:30:15\] Before running 'apply\_patch'  
1: \[2025-09-22 10:45:02\] Before running 'write\_file'

You can then do \> /restore 0 to revert all file changes (and even the conversation context) back to how it was at that checkpoint. In this way, you can “undo” a mistaken code refactor or any other changes Gemini [made](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=1,point%20and%20roll%20back%20instantly).

**What gets restored:** The checkpoint captures the state of your working directory (all files that Gemini CLI is allowed to modify) and the workspace files (conversation state may also be rolled back depending on how the checkpoint was captured). When you restore, it overwrites files to the old version and resets the conversation memory to that snapshot. It’s like time-traveling the AI agent back to before it made the wrong turn. Note that it won’t undo external side effects (for example, if the AI ran a database migration, it can’t undo that), but anything in the file system and chat context is fair game.

**Best practices:** It’s a good idea to keep checkpointing on for non-trivial tasks. The overhead is small, and it provides peace of mind. If you find you don’t need a checkpoint (everything went well), you can always clear it or just let the next one overwrite it. The development team recommends using checkpointing especially before multi-step code [edits](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=Tips%20to%20avoid%20messy%20rollbacks). For mission-critical projects, though, you should still use a proper version control (git) as your primary safety [net](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=No,VS%20Code%20is%20already%20free) – consider checkpoints as a convenience for quick undo rather than a full VCS.

In essence, **/restore** lets you use Gemini CLI with confidence. You can let the AI attempt bold changes, knowing you have an *“OH NO” button* to rewind if needed.

## Tip 6: Read Google Docs, Sheets, and More. With a Workspace MCP server configured, you can paste a Docs/Sheets link and have the MCP fetch it, subject to permissions

**Quick use-case:** Imagine you have a Google Doc or Sheet with some specs or data that you want the AI to use. Instead of copy-pasting the content, you can provide the link, and with a configured Workspace MCP server Gemini CLI can fetch and read it.

For example:

\> Summarize the requirements from this design doc: [https://docs.google.com/document/d/\\](https://docs.google.com/document/d/\\)\<id\>

Gemini can pull in the content of that Doc and incorporate it into its response. Similarly, it can read Google Sheets or Drive files by link, and even perform internal searches on Google’s knowledge base (Moma) if you have access.

**How this works:** These capabilities are typically enabled via **MCP integrations**. Google’s Gemini CLI team has built (or is working on) connectors for Google Workspace. One approach is running a small MCP server that uses Google’s APIs (Docs API, Sheets API, etc.) to retrieve document content when given a URL or \[ID[\[41\]](https://github.com/google-gemini/gemini-cli/issues/7175#:~:text=Create%20a%20Google%20Docs%20MCP,to%20provide%20seamless%20document%20access)\]([https://github.com/google-gemini/gemini-cli/issues/7175\#:\~:text=%2A%20%60read\_google\_doc%28doc\_id\_or\_url%29%60%20,List%20accessible%20documents](https://github.com/google-gemini/gemini-cli/issues/7175#:~:text=%2A%20%60read_google_doc%28doc_id_or_url%29%60%20,List%20accessible%20documents)). When configured, you might have slash commands or tools like /read\_google\_doc or simply an auto-detection that sees a Google Docs link and invokes the appropriate tool to fetch it.

For example, in an Agent Factory podcast demo, the team used a **Google Docs MCP** to save a summary directly to a [doc](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=%2A%20Utilize%20the%20google,summary%20directly%20to%20Google%20Docs) – which implies they could also read the doc’s content in the first place. In practice, you might do something like:

\> @[https://docs.google.com/document/d/XYZ12345](https://docs.google.com/document/d/XYZ12345)

Including a URL with @ (the context reference syntax) signals Gemini CLI to fetch that resource. With a Google Doc integration in place, the content of that document would be pulled in as if it were a local file. From there, the AI can summarize it, answer questions about it, or otherwise use it in the conversation.

Similarly, if you paste a Google Drive **file link**, a properly configured Drive tool could download or open that file (assuming permissions and API access are set up). **Google Sheets** could be made available via an MCP that runs queries or reads cell ranges, enabling you to ask things like “What’s the sum of the budget column in this Sheet \[link\]?” and have the AI calculate it.

**Setting it up:** As of this writing, the Google Workspace integrations may require some tinkering (obtaining API credentials, running an MCP server such as the one described by [Kanshi Tanaike](https://medium.com/google-cloud/managing-google-docs-sheets-and-slides-by-natural-language-with-gemini-cli-and-mcp-62f4dfbef2d5#:~:text=To%20implement%20this%20approach%2C%20I,methods%20for%20each%20respective%20API), etc.). Keep an eye on the official Gemini CLI repository and community forums for ready-to-use extensions – for example, an official Google Docs MCP might become available as a plugin/extension. If you’re eager, you can write one following guides on how to use Google APIs within an MCP [server](https://github.com/google-gemini/gemini-cli/issues/7175#:~:text=). It typically involves handling OAuth (which Gemini CLI supports for MCP servers) and then exposing tools like read\_google\_doc.

**Usage tip:** When you have these tools, using them can be as simple as providing the link in your prompt (the AI might automatically invoke the tool to fetch it) or using a slash command like /doc open \<URL\>. Check /tools to see what commands are available – Gemini CLI lists all tools and custom commands [there](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=Gemini%20CLI%20includes%20dozens%20of,can%20supercharge%20your%20dev%20process).

In summary, **Gemini CLI can reach out beyond your local filesystem**. Whether it’s Google Docs, Sheets, Drive, or other external content, you can pull data in by reference. This pro tip saves you from manual copy-paste and keeps the context flow natural – just refer to the document or dataset you need, and let the AI grab what’s needed. It makes Gemini CLI a true **knowledge assistant** for all the information you have access to, not just the files on your disk.

*(Note: Accessing private documents of course requires the CLI to have the appropriate permissions. Always ensure any integration respects security and privacy. In corporate settings, setting up such integrations might involve additional auth steps.)*

## Tip 7: Reference Files and Images with @ for Explicit Context

**Quick use-case:** Instead of describing a file’s content or an image verbally, just point Gemini CLI directly to it. Using the @ syntax, you can attach files, directories, or images into your prompt. This guarantees the AI sees exactly what’s in those files as [context](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Reference%20files%20or%20directories%20in,PDFs%2C%20audio%2C%20and%20video%20files). For example:

\> Explain this code to me: @./src/main.js

This will include the contents of *src/main.js* in the prompt (up to Gemini’s context size limits), so the AI can read it and explain [it](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Include%20a%20single%20file%3A).

This @ *file reference* is one of Gemini CLI’s most powerful features for developers. It eliminates ambiguity – you’re not asking the model to rely on memory or guesswork about the file, you’re literally handing it the file to read. You can use this for source code, text documents, logs, etc. Similarly, you can reference **entire directories**:

\> Refactor the code in @./utils/ to use async/await.

By appending a path that ends in a slash, Gemini CLI will recursively include files from that [directory](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Include%20a%20whole%20directory%20) (within reason, respecting ignore files and size limits). This is great for multi-file refactors or analyses, as the AI can consider all relevant modules together.

Even more impressively, you can reference **binary files like images** in prompts. Gemini CLI (using the Gemini model’s multimodal capabilities) can understand images. For example:

\> Describe what you see in this screenshot: @./design/mockup.png

The image will be fed into the model, and the AI might respond with something like “This is a login page with a blue sign-in button and a header image,” [etc](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Include%20an%20image%3A).. You can imagine the uses: reviewing UI mockups, organizing photos (as we’ll see in a later tip), or extracting text from images (Gemini can do OCR as well).

A few notes on using @ references effectively:

* **File limits:** Gemini 2.5 Pro has a huge context window (up to 1 million [tokens](https://blog.google/technology/developers/introducing-gemini-cli-open-source-ai-agent/#:~:text=To%20use%20Gemini%20CLI%20free,per%20day%20at%20no%20charge)), so you can include quite large files or many files. However, extremely large files might be truncated. If a file is enormous (say, hundreds of thousands of lines), consider summarizing it or breaking it into parts. Gemini CLI will warn you if a reference is too large or if it skipped something due to size.

* **Automatic ignoring:** By default, Gemini CLI respects your .gitignore and .geminiignore files when pulling in directory [context](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Reference%20files%20or%20directories%20in,PDFs%2C%20audio%2C%20and%20video%20files). So if you @./ a project root, it will not dump huge ignored folders (like node\_modules) into the prompt. You can customize ignore patterns with .geminiignore similarly to how .gitignore works.

* **Explicit vs implicit context:** Taylor Mullen (the creator of Gemini CLI) emphasizes using @ for *explicit context injection* rather than relying on the model’s memory or summarizing things yourself. It’s more precise and ensures the AI isn’t hallucinating content. Whenever possible, point the AI to the source of truth (code, config files, documentation) with @ references. This practice can significantly improve accuracy.

* **Chaining references:** You can include multiple files in one prompt, like: \> Compare @./foo.py and @./bar.py and tell me differences. The CLI will include both files. Just be mindful of token limits; multiple large files might consume a lot of the context window.

Using @ is essentially how you **feed knowledge into Gemini CLI on the fly**. It turns the CLI into a multi-modal reader that can handle text and images. As a pro user, get into the habit of leveraging this – it’s often faster and more reliable than asking the AI something like “Open the file X and do Y” (which it may or may not do on its own). Instead, you explicitly give it X to work with.

## Tip 8: On-the-Fly Tool Creation (Have Gemini Build Helpers)

**Quick use-case:** If a task at hand would benefit from a small script or utility, you can ask Gemini CLI to create that tool for you – right within your session. For example, you might say, “Write a Python script to parse all JSON files in this folder and extract the error fields.” Gemini can generate the script, which you can then execute via the CLI. In essence, you can **dynamically extend the toolset** as you go.

Gemini CLI is not limited to its pre-existing tools; it can use its coding abilities to fabricate new ones when needed. This often happens implicitly: if you ask for something complex, the AI might propose writing a temporary file (with code) and then running it. As a user, you can also guide this process explicitly:

* **Creating scripts:** You can prompt Gemini to create a script or program in the language of your choice. It will likely use the write\_file tool to create the file. For instance:

\> Generate a Node.js script that reads all '.log' files in the current directory and reports the number of lines in each.

Gemini CLI will draft the code, and with your approval, write it to a file (e.g. script.js). You can then run it by either using the \! shell command (e.g. \!node script.js) or by asking Gemini CLI to execute it (the AI might automatically use run\_shell\_command to execute the script it just wrote, if it deems it part of the plan).

* **Temporary tools via MCP:** In advanced scenarios, the AI might even suggest launching an MCP server for some specialized tasks. For example, if your prompt involves some heavy text processing that might be better done in Python, Gemini could generate a simple MCP server in Python and run it. While this is more rare, it demonstrates that the AI can set up a new “agent” on the fly. (One of the slides from the Gemini CLI team humorously referred to “MCP servers for everything, even one called LROwn” – suggesting you can have Gemini run an instance of itself or another model, though that’s more of a trick than a practical use\!).

The key benefit here is **automation**. Instead of you manually stopping to write a helper script, you can let the AI do it as part of the flow. It’s like having an assistant who can create tools on-demand. This is especially useful for data transformation tasks, batch operations, or one-off computations that the built-in tools don’t directly provide.

**Nuances and safety:** When Gemini CLI writes code for a new tool, you should still review it before running. The /diff view (Gemini will show you the file diff before you approve writing it) is your chance to inspect the [code](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=Nobody%20enjoys%20switching%20between%20windows,track%20changes%20line%20by%20line). Ensure it does what you expect and nothing malicious or destructive (the AI shouldn’t produce something harmful unless your prompt explicitly asks, but just like any code from an AI, double-check logic, especially for scripts that delete or modify lots of data).

**Example scenario:** Let’s say you have a CSV file and you want to filter it in a complex way. You ask Gemini CLI to do it, and it might say: “I will write a Python script to parse the CSV and apply the filter.” It then creates filter\_data.py. After you approve and it runs, you get your result, and you might never need that script again. This ephemeral creation of tools is a pro move – it shows the AI effectively extending its capabilities autonomously.

**Pro Tip:** If you find the script useful beyond the immediate context, you can promote it into a permanent tool or command. For instance, if the AI generated a great log-processing script, you might later turn it into a custom slash command (Tip \#2) for easy reuse. The combination of Gemini’s generative power and the extension hooks means your toolkit can continuously evolve as you use the CLI.

In summary, **don’t restrict Gemini to what it comes with**. Treat it as a junior developer who can whip up new programs or even mini-servers to help solve the problem. This approach embodies the agentic philosophy of Gemini CLI – it will figure out what tools it needs, even if it has to code them on the spot.

## Tip 9: Use Gemini CLI for System Troubleshooting & Configuration

**Quick use-case:** You can run Gemini CLI outside of a code project to help with general system tasks – think of it as an intelligent assistant for your OS. For example, if your shell is misbehaving, you could open Gemini in your home directory and ask: “Fix my .bashrc file, it has an error.” Gemini can then open and edit your config file for you.

This tip highlights that **Gemini CLI isn’t just for coding projects – it’s your AI helper for your whole development environment**. Many users (especially Googlers) have used Gemini to customize their dev setup or fix issues on their machine:

* **Editing dotfiles:** You can load your shell configuration (.bashrc or .zshrc) by referencing it (@\~/.bashrc) and then ask Gemini CLI to optimize or troubleshoot it. For instance, “My PATH isn’t picking up Go binaries, can you edit my .bashrc to fix that?” The AI can insert the correct export line. It will show you the diff for confirmation before saving changes.

* **Diagnosing errors:** If you encounter a cryptic error in your terminal or an application log, you can copy it and feed it to Gemini CLI. It will analyze the error message and often suggest steps to resolve it. This is similar to how one might use StackOverflow or Google, but with the AI directly examining your scenario. For example: “When I run npm install, I get an EACCES permission error – how do I fix this?” Gemini might detect it’s a permissions issue in node\_modules and guide you to change directory ownership or use a proper node version manager.

* **Running outside a project:** By default, if you run gemini in a directory without a .gemini context, it just means no project-specific context is loaded – but you can still use the CLI fully. This is great for ad-hoc tasks like system troubleshooting. You might not have any code files for it to consider, but you can still run shell commands through it or let it fetch web info. Essentially, you’re treating Gemini CLI as an AI-powered terminal that can *do* things for you, not just chat.

* **Workstation customization:** Want to change a setting or install a new tool? You can ask Gemini CLI, “Install Docker on my system” or “Configure my Git to sign commits with GPG.” The CLI will attempt to execute the steps. It might fetch instructions from the web (using the search tool) and then run the appropriate shell commands. Of course, always watch what it’s doing and approve the commands – but it can save time by automating multi-step setup processes. One real example: a user asked Gemini CLI to “set my macOS Dock preferences to auto-hide and remove the delay,” and the AI was able to execute the necessary defaults write commands.

Think of this mode as using Gemini CLI as a **smart shell**. In fact, you can combine this with Tip 16 (shell passthrough mode) – sometimes you might drop into \! shell mode to verify something, then go back to AI mode to have it analyze output.

**Caveat:** When doing system-level tasks, be cautious with commands that have widespread impact (like rm \-rf or system config changes). Gemini CLI will usually ask for confirmation, and it doesn’t run anything without you seeing it. But as a power user, you should have a sense of what changes are being made. If unsure, ask Gemini to explain a command before running (e.g., “Explain what defaults write com.apple.dock autohide-delay \-float 0 does” – it will gladly explain rather than just execute if you prompt it in that way).

**Troubleshooting bonus:** Another neat use is using Gemini CLI to parse logs or config files looking for issues. For instance, “Scan this Apache config for mistakes” (with @httpd.conf), or “Look through syslog for errors around 2 PM yesterday” (with an @/var/log/syslog if accessible). It’s like having a co-administrator. It can even suggest likely causes for crashes or propose fixes for common error patterns.

In summary, **don’t hesitate to fire up Gemini CLI as your assistant for environment issues**. It’s there to accelerate all your workflows – not just writing code, but maintaining the system that you write code on. Many users report that customizing their dev environment with Gemini’s help feels like having a tech buddy always on call to handle the tedious or complex setup steps.

## Tip 10: YOLO Mode – Auto-Approve Tool Actions (Use with Caution)

**Quick use-case:** If you’re feeling confident (or adventurous), you can let Gemini CLI run tool actions without asking for your confirmation each time. This is **YOLO mode** (You Only Live Once). It’s enabled by the \--yolo flag or by pressing Ctrl+Y during a [session](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,prompt%20in%20an%20external%20editor). In YOLO mode, as soon as the AI decides on a tool (like running a shell command or writing to a file), it executes it immediately, without that “Approve? (y/n)” prompt.

**Why use YOLO mode?** Primarily for speed and convenience **when you trust the AI’s actions**. Experienced users might toggle YOLO on if they’re doing a lot of repetitive safe operations. For example, if you ask Gemini to generate 10 different files one after another, approving each can slow down the flow; YOLO mode would just let them all be written automatically. Another scenario is using Gemini CLI in a completely automated script or CI pipeline – you might run it headless with \--yolo so it doesn’t pause for confirmation.

To start in YOLO mode from the get-go, launch the CLI with:

gemini \--yolo

Or the short form gemini \-y. You’ll see some indication in the CLI (like a different prompt or a notice) that auto-approve is [on](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=initial%20prompt.%20%2A%20%60,to%20revert%20changes). During an interactive session, you can toggle it by pressing **Ctrl+Y** at any [time](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,prompt%20in%20an%20external%20editor) – the CLI will usually display a message like “YOLO mode enabled (all actions auto-approved)” in the footer.

**Big warning:** YOLO mode is powerful but **risky**. The Gemini team themselves labels it for “daring users” – meaning you should be aware that the AI could potentially execute a dangerous command without asking. In normal mode, if the AI decided to run rm \-rf / (worst-case scenario), you’d obviously decline. In YOLO mode, that command would run immediately (and likely ruin your day). While such extreme mistakes are unlikely (the AI’s system prompt includes safety guidelines), the whole point of confirmations is to catch any unwanted action. YOLO removes that safety net.

**Best practices for YOLO:** If you want some of the convenience without full risk, consider *allow-listing* specific commands. For example, you can configure in settings that certain tools or command patterns don’t require confirmation (like allowing all git commands, or read-only actions). In fact, Gemini CLI supports a config for skipping confirmation on specific commands: e.g., you can set something like "tools.shell.autoApprove": \["git ", "npm test"\] to always run [those](https://google-gemini.github.io/gemini-cli/docs/cli/configuration.html#:~:text=match%20at%20L247%20%60%5B,Default%3A%20%60undefined). This way, you might not need YOLO mode globally – you selectively YOLO only safe commands. Another approach: run Gemini in a sandbox or container when using YOLO, so even if it does something wild, your system is insulated (Gemini has a \--sandbox flag to run tools in a Docker [container](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=echo%20,gemini)).

Many advanced users toggle YOLO on and off frequently – turning it on when doing a string of minor file edits or queries, and off when about to do something critical. You can do the same, using the keyboard shortcut as a quick toggle.

In summary, **YOLO mode eliminates friction at the cost of oversight**. It’s a pro feature to use sparingly and wisely. It truly demonstrates trust in the AI (or recklessness\!). If you’re new to Gemini CLI, you should probably avoid YOLO until you clearly understand the patterns of what it tends to do. If you do use it, double down on having version control or backups – just in case.

*(If it’s any consolation, you’re not alone – many in the community joke about “I YOLO’ed and Gemini did something crazy.” So use it, but… well, you only live once.)*

## Tip 11: Headless & Scripting Mode (Run Gemini CLI in the Background)

**Quick use-case:** You can use Gemini CLI in scripts or automation by running it in **headless mode**. This means you provide a prompt (or even a full conversation) via command-line arguments or environment variables, and Gemini CLI produces an output and exits. It’s great for integrating with other tools or triggering AI tasks on a schedule.

For instance, to get a one-off answer without opening the REPL, you’ve seen you can use gemini \-p "...prompt...". This is already headless usage: it prints the model’s response and returns to the [shell](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Non,and%20get%20a%20single%20response). But there’s more you can do:

* **System prompt override:** If you want to run Gemini CLI with a custom system persona or instruction set (different from the default), you can use the environment variable GEMINI\_SYSTEM\_MD. By setting this, you tell Gemini CLI to ignore its built-in system prompt and use your provided file [instead](https://medium.com/google-cloud/practical-gemini-cli-bring-your-own-system-instruction-19ea7f07faa2#:~:text=The%20,rather%20than%20its%20hardcoded%20defaults). For example:

export GEMINI\_SYSTEM\_MD="/path/to/custom\_system.md"  
gemini \-p "Perform task X with high caution"

This would load your custom\_system.md as the system prompt (the “role” and rules the AI follows) before executing the [prompt](https://medium.com/google-cloud/practical-gemini-cli-bring-your-own-system-instruction-19ea7f07faa2#:~:text=The%20feature%20is%20enabled%20by,specific%20configurations). Alternatively, if you set GEMINI\_SYSTEM\_MD=true, the CLI will look for a file named system.md in the current project’s .gemini [directory](https://medium.com/google-cloud/practical-gemini-cli-bring-your-own-system-instruction-19ea7f07faa2#:~:text=The%20feature%20is%20enabled%20by,specific%20configurations). This feature is very advanced – it essentially allows you to *replace the built-in brain* of the CLI with your own instructions, which some users do for specialized workflows (like simulating a specific persona or enforcing ultra-strict policies). Use it carefully, as replacing the core prompt can affect tool usage (the core prompt contains important directions for how the AI selects and uses [tools](https://medium.com/google-cloud/practical-gemini-cli-bring-your-own-system-instruction-19ea7f07faa2#:~:text=If%20you%20read%20my%20previous,proper%20functioning%20of%20Gemini%20CLI)).

* **Direct prompt via CLI:** Aside from \-p, there’s also \-i (interactive prompt) which starts a session with an initial prompt, and then keeps it open. For example: gemini \-i "Hello, let's debug something" will open the REPL and already have said hello to the model. This is useful if you want the first question to be asked immediately when starting.

* **Scripting with shell pipes:** You can pipe not just text but also files or command outputs into Gemini. For example: gemini \-p "Summarize this log:" \< big\_log.txt will feed the content of big\_log.txt into the prompt (after the phrase "Summarize this log:"). Or you might do some\_command | gemini \-p "Given the above output, what went wrong?". This technique allows you to compose Unix tools with AI analysis. It’s headless in the sense that it’s a single-pass operation.

* **Running in CI/CD:** You could incorporate Gemini CLI into build processes. For instance, a CI pipeline might run a test and then use Gemini CLI to automatically analyze failing test output and post a comment. Using the \-p flag and environment auth, this can be scripted. (Of course, ensure the environment has the API key or auth needed.)

One more headless trick: **the \--format=json flag** (or config setting). Gemini CLI can output responses in JSON format instead of the human-readable text if you configure [it](https://google-gemini.github.io/gemini-cli/docs/cli/configuration.html#:~:text=). This is useful for programmatic consumption – your script can parse the JSON to get the answer or any tool actions details.

**Why headless mode matters:** It transforms Gemini CLI from an interactive assistant into a **backend service** or utility that other programs can call. You could schedule a cronjob that runs a Gemini CLI prompt nightly (imagine generating a report or cleaning up something with AI logic). You could wire up a button in an IDE that triggers a headless Gemini run for a specific task.

**Example:** Let’s say you want a daily summary of a news website. You could have a script:

gemini \-p "Web-fetch \\"[https://news.site/top-stories\\\\](https://news.site/top-stories\\\\)" and extract the headlines, then write them to headlines.txt"

With \--yolo perhaps, so it won’t ask confirmation to write the file. This would use the web fetch tool to get the page and the file write tool to save the headlines. All automatically, no human in the loop. The possibilities are endless once you treat Gemini CLI as a scriptable component.

In summary, **Headless Mode** enables automation. It’s the bridge between Gemini CLI and other systems. Mastering it means you can scale up your AI usage – not just when you’re typing in the terminal, but even when you aren’t around, your AI agent can do work for you.

*(Tip: For truly long-running non-interactive tasks, you might also look into Gemini CLI’s “Plan” mode or how it can generate multi-step plans without intervention. However, those are advanced topics beyond this scope. In most cases, a well-crafted single prompt via headless mode can achieve a lot.)*

## Tip 12: Save and Resume Chat Sessions

**Quick use-case:** If you’ve been debugging an issue with Gemini CLI for an hour and need to stop, you don’t have to lose the conversation context. Use /chat save \<name\> to save the session. Later (even after restarting the CLI), you can use /chat resume \<name\> to pick up where you left [off](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,help%20information%20and%20available%20commands). This way, long-running conversations can be paused and continued seamlessly.

Gemini CLI essentially has a built-in chat session manager. The commands to know are:

* \*/chat save \<tag\>\* – Saves the current conversation state under a tag/name you [provide](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,help%20information%20and%20available%20commands). The tag is like a filename or key for that session. Save often if you want, it will overwrite the tag if it exists. (Using a descriptive name is helpful – e.g., chat save fix-docker-issue.)

* \*/chat list\* – Lists all your saved sessions (the tags you’ve [used)](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,help%20information%20and%20available%20commands). This helps you remember what you named previous saves.

* \*/chat resume \<tag\>\* – Resumes the session with that tag, restoring the entire conversation context and history to how it was when [saved](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,help%20information%20and%20available%20commands). It’s like you never left. You can then continue chatting from that point.

* \*/chat share\* \- (saves to file) This is useful as you can share the entire chat with someone else who can continue the session. Almost collaboration-like.

Under the hood, these sessions are stored likely in \~/.gemini/chats/ or a similar location. They include the conversation messages and any relevant state. This feature is super useful for cases such as:

* **Long debugging sessions:** Sometimes debugging with an AI can be a long back-and-forth. If you can’t solve it in one go, save it and come back later (maybe with a fresh mind). The AI will still “remember” everything from before, because the whole context is reloaded.

* **Multi-day tasks:** If you’re using Gemini CLI as an assistant for a project, you might have one chat session for “Refactor module X” that spans multiple days. You can resume that specific chat each day so the context doesn’t reset daily. Meanwhile, you might have another session for “Write documentation” saved separately. Switching contexts is just a matter of saving one and resuming the other.

* **Team hand-off:** This is more experimental, but in theory, you could share the content of a saved chat with a colleague (the saved files are likely portable). If they put it in their .gemini directory and resume, they could see the same context. The **practical simpler approach** for collaboration is just copying the relevant Q\&A from the log and using a shared GEMINI.md or prompt, but it’s interesting to note that the session data is yours to keep.

**Usage example:**

\> /chat save api-upgrade

*(Session saved as “api-upgrade”)*

\> /quit

*(Later, reopen CLI)*

$ gemini  
gemini\> /chat list

*(Shows: api-upgrade)*

gemini\> /chat resume api-upgrade

Now the model greets you with the last exchange’s state ready. You can confirm by scrolling up that all your previous messages are present.

**Pro Tip:** Use meaningful tags when saving [chats](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=Naming%20conventions%20to%20keep%20projects,organized). Instead of /chat save session1, give it a name related to the topic (e.g. /chat save memory-leak-bug). This will help you find the right one later via /chat list. There is no strict limit announced on how many sessions you can save, but cleaning up old ones occasionally might be wise just for organization.

This feature turns Gemini CLI into a persistent advisor. You don’t lose knowledge gained in a conversation; you can always pause and resume. It’s a differentiator compared to some other AI interfaces that forget context when closed. For power users, it means **you can maintain parallel threads of work** with the AI. Just like you’d have multiple terminal tabs for different tasks, you can have multiple chat sessions saved and resume the one you need at any given time.

## Tip 13: Multi-Directory Workspace – One Gemini, Many Folders

**Quick use-case:** Do you have a project split across multiple repositories or directories? You can launch Gemini CLI with access to *all of them* at once, so it sees a unified workspace. For example, if your frontend and backend are separate folders, you can include both so that Gemini can edit or reference files in both.

There are two ways to use **multi-directory mode**:

* **Launch flag:** Use the \--include-directories (or \-I) flag when starting Gemini CLI. For example:

gemini \--include-directories "../backend:../frontend"

This assumes you run the command from, say, a scripts directory and want to include two sibling folders. You provide a colon-separated list of paths. Gemini CLI will then treat all those directories as part of one big workspace.

* **Persistent setting:** In your settings.json, you can define "includeDirectories": \["path1", "path2", [...\]](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,61AFEF%22%2C%20%22AccentPurple). This is useful if you always want certain common directories loaded (e.g., a shared library folder that multiple projects use). The paths can be relative or absolute. Environment variables in the paths (like "\~/common-utils") are [allowed](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,61AFEF%22%2C%20%22AccentPurple).

When multi-dir mode is active, the CLI’s context and tools consider files across all included locations. The \> /directory show command will list which directories are in the current [workspace](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=How%20to%20add%20multiple%20directories,step). You can also dynamically add directories during a session with /directory add [\<path\>](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=How%20to%20add%20multiple%20directories,step) – it will then load that on the fly (potentially scanning it for context like it does on startup).

**Why use multi-directory mode?** In microservice architectures or modular codebases, it’s common that one piece of code lives in one repo and another piece in a different repo. If you only ran Gemini in one, it wouldn’t “see” the others. By combining them, you enable cross-project reasoning. For example, you could ask, “Update the API client in the frontend to match the backend’s new API endpoints” – Gemini can open the backend folder to see the API definitions and simultaneously open the frontend code to modify it accordingly. Without multi-dir, you’d have to do one side at a time and manually carry info over.

**Example:** Let’s say you have client/ and server/. You start:

cd client  
gemini \--include-directories "../server"

Now at the gemini\> prompt, if you do \> \!ls, you’ll see it can list files in both client and server (it might show them as separate paths). You could do:

\> Open server/routes/api.py and client/src/api.js side by side to compare function names.

The AI will have access to both files. Or you might say:

\> The API changed: the endpoint "/users/create" is now "/users/register". Update both backend and frontend accordingly.

It can simultaneously create a patch in the backend route and adjust the frontend fetch call.

Under the hood, Gemini merges the file index of those directories. There might be some performance considerations if each directory is huge, but generally it handles multiple small-medium projects fine. The cheat sheet notes that this effectively creates one workspace with multiple [roots](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=%22includeDirectories%22%3A%20%5B%22..%2Fshared,98C379%22%2C%20%22AccentYellow).

**Tip within a tip:** Even if you don’t use multi-dir all the time, know that you can still reference files across the filesystem by absolute path in prompts (@/path/to/file). However, without multi-dir, Gemini might not have permission to edit those or know to load context from them proactively. Multi-dir formally includes them in scope so it’s aware of all files for tasks like search or code generation across the whole set.

**Remove directories:** If needed, /directory remove \<path\> (or a similar command) can drop a directory from the workspace. This is less common, but maybe if you included something accidentally, you can remove it.

In summary, **multi-directory mode unifies your context**. It’s a must-have for polyrepo projects or any situation where code is split up. It makes Gemini CLI act more like an IDE that has your entire solution open. As a pro user, this means no part of your project is out of the AI’s reach.

## Tip 14: Organize and Clean Up Your Files with AI Assistance

**Quick use-case:** Tired of a messy Downloads folder or disorganized project assets? You can enlist Gemini CLI to act as a smart organizer. By providing it an overview of a directory, it can classify files and even move them into subfolders (with your approval). For instance, “Clean up my Downloads: move images to an Images folder, PDFs to Documents, and delete temporary files.”

Because Gemini CLI can read file names, sizes, and even peek into file contents, it can make informed decisions about file [organization](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=We%20built%20a%20CLI%20tool,trash%20folder%20for%20manual%20deletion). One community-created tool dubbed **“Janitor AI”** showcases this: it runs via Gemini CLI to categorize files as important vs junk, and groups them [accordingly](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=We%20built%20a%20CLI%20tool,trash%20folder%20for%20manual%20deletion). The process involved scanning the directory, using Gemini’s reasoning on filenames and metadata (and content if needed), then moving files into categories. Notably, it didn’t automatically delete junk – rather, it moved them to a Trash folder for [review](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=organize%20files,trash%20folder%20for%20manual%20deletion).

Here’s how you might replicate such a workflow with Gemini CLI manually:

1. **Survey the directory:** Use a prompt to have Gemini list and categorize. For example:

\> List all files in the current directory and categorize them as "images", "videos", "documents", "archives", or "others".

Gemini might use \!ls or similar to get the file list, then analyze the names/extensions to produce categories.

1. **Plan the organization:** Ask Gemini how it would like to reorganize. For example:

\> Propose a new folder structure for these files. I want to separate by type (Images, Videos, Documents, etc.). Also identify any files that seem like duplicates or unnecessary.

The AI might respond with a plan: e.g., *“Create folders: Images/, Videos/, Documents/, Archives/. Move X.png, Y.jpg to Images/; move A.mp4 to Videos/; etc. The file temp.txt looks unnecessary (maybe a temp file).”*

1. **Execute moves with confirmation:** You can then instruct it to carry out the plan. It may use shell commands like mv for each file. Since this modifies your filesystem, you’ll get confirmation prompts for each (unless you YOLO it). Carefully approve the moves. After completion, your directory will be neatly organized as suggested.

Throughout, Gemini’s natural language understanding is key. It can reason, for instance, that IMG\_001.png is an image or that presentation.pdf is a document, even if not explicitly stated. It can even open an image (using its vision capability) to see what’s in it – e.g., differentiating between a screenshot vs a photo vs an icon – and name or sort it [accordingly](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=If%20your%20project%20folder%20is,using%20relevant%20and%20descriptive%20terms).

**Renaming files by content:** A particularly magical use is having Gemini rename files to be more descriptive. The Dev Community article “7 Insane Gemini CLI Tips” describes how Gemini can **scan images and automatically rename them** based on their [content](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=If%20your%20project%20folder%20is,using%20relevant%20and%20descriptive%20terms). For example, a file named IMG\_1234.jpg might be renamed to login\_screen.jpg if the AI sees it’s a screenshot of a login [screen](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=If%20your%20project%20folder%20is,using%20relevant%20and%20descriptive%20terms). To do this, you could prompt:

\> For each .png image here, look at its content and rename it to something descriptive.

Gemini will open each image (via vision tool), get a description, then propose a mv IMG\_1234.png login\_screen.png [action](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=If%20your%20project%20folder%20is,using%20relevant%20and%20descriptive%20terms). This can dramatically improve the organization of assets, especially in design or photo folders.

**Two-pass approach:** The Janitor AI discussion noted a two-step process: first broad categorization (important vs junk vs other), then refining [groups](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=organize%20files,trash%20folder%20for%20manual%20deletion). You can emulate this: first separate files that likely can be deleted (maybe large installer .dmg files or duplicates) from those to keep. Then focus on organizing the keepers. Always double-check what the AI flags as junk; its guess might not always be right, so manual oversight is needed.

**Safety tip:** When letting the AI loose on file moves or deletions, have backups or at least be ready to undo (with /restore or your own backup). It’s wise to do a dry-run: ask Gemini to print the commands it *would* run to organize, without executing them, so you can review. For instance: “List the mv and mkdir commands needed for this plan, but don’t execute them yet.” Once you review the list, you can either copy-paste execute them, or instruct Gemini to proceed.

This is a prime example of using Gemini CLI for “non-obvious” tasks – it’s not just writing code, it’s doing **system housekeeping with AI smarts**. It can save time and bring a bit of order to chaos. After all, as developers we accumulate clutter (logs, old scripts, downloads), and an AI janitor can be quite handy.