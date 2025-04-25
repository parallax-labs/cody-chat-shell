Cody: # Cody Chat Shell

## Overview

Cody Chat Shell is a Bash script that enhances the Cody CLI experience by creating a seamless interface between you and Sourcegraph's Cody AI assistant. This tool addresses several key needs for developers:

- **Persistent Knowledge Management**: Automatically logs all conversations to Markdown files, creating a searchable knowledge base of your AI interactions
- **Contextual Continuity**: Supports context files for better continuity between sessions
- **Workflow Integration**: Provides both interactive and non-interactive modes to fit into various development workflows
- **Project-Specific Conversations**: Allows directory-specific chats to maintain focus on relevant codebases
- **Organized Documentation**: Supports custom session naming for better organization of AI-assisted problem-solving

The primary purpose of this tool is to bridge the gap between ephemeral AI chat sessions and permanent documentation. By creating flat Markdown files for each conversation, you can:

1. Build a personal knowledge repository of solutions and explanations
2. Resume conversations by referencing previous chat logs
3. Share AI-generated solutions with team members
4. Track the evolution of your understanding and approach to problems
5. Maintain context across multiple development sessions

## Installation

1. Install the Cody CLI:
   ```bash
   npm install -g @cody/cli
   ```

2. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/cody-chat-shell.git
   ```

3. Make the script executable:
   ```bash
   chmod +x cody-chat-shell/cody-chat
   ```

4. Optionally, add the script to your PATH for easier access:
   ```bash
   ln -s "$(pwd)/cody-chat-shell/cody-chat" /usr/local/bin/chat
   ```

## Authentication

You'll need a Cody API token to use this tool:

1. Authenticate through your company portal and complete the login flow for Cody
2. Generate a new API token in the portal
3. Set up your token in one of these ways:
   - Export as an environment variable: `export CODY_AUTH_TOKEN="<your_token>"`
   - Pass directly with each command: `cody-chat --token "<your_token>" "Hello"`

## Usage

```
cody-chat [options] [initial_message]
```

### Options

- `-C, --dir <directory>` - Change to this directory before starting chat
- `--session <name>` - Use a custom name for the session logfile
- `--token <token>` - Pass your API token explicitly for this session
- `initial_message` - Send this as the first message to Cody

### Examples

Start an interactive chat session:
```bash
cody-chat
```

Ask a question and get a single response:
```bash
cody-chat "What is the capital of France?"
```

Use piped input:
```bash
echo "Explain this code:" | cody-chat
```

Chat about code in a specific directory:
```bash
cody-chat -C ~/projects/myapp "How can I optimize this code?"
```

Name your chat session:
```bash
cody-chat --session project-refactor "Let's discuss refactoring the auth module"
```

## Chat Logs

By default, all conversations are logged to Markdown files in:
```
$HOME/dev/chat/log/
```

Files are named either:
- Using your custom session name: `session-name.md`
- With a timestamp: `chat_YYYYMMDD_HHMMSS.md`

## Context Files

For better continuity, place a `context.md` file in your working directory. This file will be automatically passed to Cody to provide additional context for your conversation.

## Resuming Sessions

One of the key benefits of this tool is the ability to resume previous conversations:

1. Start a new session with a meaningful name:
   ```bash
   cody-chat --session auth-refactor "Let's improve our authentication system"
   ```

2. Later, continue the same conversation by using the same session name:
   ```bash
   cody-chat --session auth-refactor "Now let's implement those changes we discussed"
   ```

The tool will append to the existing log file, maintaining the full conversation history.

## Integration with Development Workflow

Cody Chat Shell is designed to integrate seamlessly with your development workflow:

- **Problem Exploration**: Use interactive mode to explore complex problems
- **Quick Answers**: Pipe in questions for immediate, non-interactive responses
- **Code Reviews**: Chat about specific directories to get contextual feedback
- **Documentation**: Generate explanations that are automatically saved for future reference
- **Knowledge Transfer**: Share chat logs with team members to transfer knowledge

## License

[MIT License](LICENSE)

