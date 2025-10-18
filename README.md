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

