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

