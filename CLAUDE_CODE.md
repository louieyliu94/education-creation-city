# Claude Code

Claude Code is Anthropic's agentic coding tool that lives in your terminal, understands your codebase, and helps you code faster through natural language commands. It reads files, edits code, runs commands, and integrates with your development tools — working across your entire project autonomously or with your guidance.

## Key Features

### Codebase Understanding
- Searches directories to build context and understand how modules connect
- Creates and edits files across a codebase
- Handles ambitious tasks like building new features or executing multi-file refactors

### Git Integration
- Works directly with git: stages changes, writes commit messages, creates branches, and opens pull requests
- Integrates with GitHub and GitLab to handle entire workflows — reading issues, writing code, running tests, and submitting PRs

### Safety and Control
- Default behavior is cautious: asks before making changes to files or running commands
- Developers control how much autonomy Claude Code has, from approving every action to letting built-in classifiers distinguish safe from risky actions automatically
- Checkpoints let you review and roll back before committing to changes

### Autonomous Features
- **Subagents**: Delegate specialized tasks and enable parallel development workflows (e.g., spin up a backend API while the main agent builds the frontend)
- **Hooks**: Automatically trigger actions at specific points, such as running test suites after code changes or linting before commits
- **Background tasks**: Keep long-running processes active without blocking progress
- **Scheduled automation**: Run Claude on a schedule for morning PR reviews, overnight CI failure analysis, weekly dependency audits, or syncing docs after PRs merge — on Anthropic-managed infrastructure even when your computer is off

### MCP Integration
Integrates with the Model Context Protocol (MCP) to connect with external data sources:
- Read design docs in Google Drive
- Update tickets in Jira
- Pull data from Slack
- Use custom internal tooling

### Skills
Package repeatable workflows your team can share, such as `/review-pr` or `/deploy-staging`.

## Interfaces

| Interface | Description |
|-----------|-------------|
| CLI (Terminal) | Full-featured command-line interface for working directly in your terminal |
| VS Code Extension | Inline diffs, @-mentions, plan review, and conversation history directly in the editor |
| Desktop App | Available on Mac and Windows |
| Web App | Available at [claude.ai/code](https://claude.ai/code) |
| JetBrains Extension | IDE integration for JetBrains IDEs |

## Default Model

Claude Sonnet 4.5 is the default model powering Claude Code.

## Getting Started

Install Claude Code via npm:

```bash
npm install -g @anthropic-ai/claude-code
```

Then run it in any project directory:

```bash
claude
```

## Resources

- [Claude Code Overview](https://docs.anthropic.com/en/docs/claude-code/overview)
- [Anthropic Claude Code Page](https://www.anthropic.com/claude-code)
- [Enabling Claude Code to Work More Autonomously](https://www.anthropic.com/news/enabling-claude-code-to-work-more-autonomously)
- [Claude Code Plugins](https://www.anthropic.com/news/claude-code-plugins)
