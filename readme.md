## Overview

Cody Chat Shell is a Bash script that enhances the Cody CLI experience by:

- Automatically logging all conversations to Markdown files
- Supporting context files for better continuity
- Providing both interactive and non-interactive modes
- Allowing directory-specific chats
- Supporting custom session naming

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

## License

[MIT License](LICENSE)

