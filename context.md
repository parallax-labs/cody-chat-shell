⠋ Traversing directory and building tree..  [0s] [ℹ️] Top 15 largest files (by estimated token count):
- 1. /Users/pjones/dev/cody-chat-shell/cody-chat (1,248 tokens)
- 2. /Users/pjones/dev/cody-chat-shell/readme.md (595 tokens)
- 3. /Users/pjones/dev/cody-chat-shell/context.md (4 tokens)


Source Trees:

/Users/pjones/dev/cody-chat-shell:
(Files/directories marked with ❌ are excluded or not included here)

cody-chat-shell/
├── .git/ ❌
├── LICENSE ❌
├── cody-chat
├── context.md
└── readme.md


Excluded Content:

Files:

- /Users/pjones/dev/cody-chat-shell/LICENSE







`/Users/pjones/dev/cody-chat-shell/context.md:`

```md

```




`/Users/pjones/dev/cody-chat-shell/cody-chat:`

```
#!/bin/bash

################################################################################
# chat.sh — A wrapper around the Cody CLI to log chat sessions.
#
# Setup:
#   1. Install the Cody CLI via npm:
#        npm install -g @cody/cli
#   2. Authenticate through your company portal:
#        Visit your MyApps portal and complete the login flow for Cody.
#   3. Generate a new API token in the portal and copy it.
#   4. Export the token as an environment variable:
#        export CODY_AUTH_TOKEN="<your_token>"
#      Or pass it directly to the CLI:
#        cody chat --token "$CODY_AUTH_TOKEN" ...
#
# Usage:
#   chat.sh [options] [initial_message]
#
# Options:
#   -C, --dir <directory>   Change to <directory> before starting chat.
#   --session <name>        Use <name> (sanitized) as the session logfile.
#   initial_message         Send this as the first message to Cody.
#   --token <token>         Pass your API token explicitly for this session.
#
# If no session name is provided, a timestamped file will be created under:
#   $HOME/dev/chat/log/chat_<YYYYMMDD_HHMMSS>.md
#
# In piped mode (echo "hello" | chat.sh), stdin is read as the initial message
# and the script exits after one interaction. Otherwise it launches an
# interactive loop; type `exit` to quit.
################################################################################

# Define log directory and ensure it exists
LOG_DIR="$HOME/dev/chat/log"
mkdir -p "$LOG_DIR"

# Initialize variables
WORKING_DIR=""       # Optional: Change to this directory before chat
SESSION_NAME=""      # Optional: Custom session name (sanitized)
INITIAL_MESSAGE=""   # Optional: First message to send to Cody
TOKEN_PARAM=()         # Optional: --token flag for CLI
CONTEXT_ARGS=()        # Will hold --context-file if context.md exists

# -------------------------------------------------------------------------------
# sanitize_filename: strip out all but alphanumeric, dash, underscore; lower-case
# -------------------------------------------------------------------------------
sanitize_filename() {
    echo "$1" | tr -cd '[:alnum:]_-' | tr '[:upper:]' '[:lower:]'
}

# -------------------------------------------------------------------------------
# Parse command-line arguments
# -------------------------------------------------------------------------------
while [[ $# -gt 0 ]]; do
    case "$1" in
        -C|--dir)
            # Change working directory before chat
            shift
            if [[ -d "$1" ]]; then
                WORKING_DIR="$1"
            else
                echo "Error: Directory '$1' does not exist." >&2
                exit 1
            fi
            ;;
        --session)
            # Provide a custom session name for logfile
            shift
            SESSION_NAME=$(sanitize_filename "$1")
            ;;
        --token)
            # Pass API token explicitly for this run
            shift
            TOKEN_PARAM=(--token "$1")
            ;;
        *)
            # Anything else is treated as (part of) the initial message
            if [[ -z "$INITIAL_MESSAGE" ]]; then
                INITIAL_MESSAGE="$1"
            else
                INITIAL_MESSAGE="$INITIAL_MESSAGE $1"
            fi
            ;;
    esac
    shift
done

# -------------------------------------------------------------------------------
# Change to the specified working directory, if requested
# -------------------------------------------------------------------------------
if [[ -n "$WORKING_DIR" ]]; then
    cd "$WORKING_DIR" || { echo "Failed to cd to '$WORKING_DIR'" >&2; exit 1; }
fi

# -------------------------------------------------------------------------------
# Determine the logfile path
# -------------------------------------------------------------------------------
if [[ -n "$SESSION_NAME" ]]; then
    # Use sanitized session name
    LOG_FILE="$LOG_DIR/${SESSION_NAME}.md"
else
    # Fallback to timestamped name
    TIMESTAMP=$(date +"%Y%m%d_%H%M%S")
    LOG_FILE="$LOG_DIR/chat_$TIMESTAMP.md"
fi

# -------------------------------------------------------------------------------
# If there's a context.md file, pass it to Cody for continuity
# -------------------------------------------------------------------------------
CONTEXT_FILE="./context.md"
if [[ -f "$CONTEXT_FILE" ]]; then
    CONTEXT_ARGS=(--context-file "$CONTEXT_FILE")
fi

# -------------------------------------------------------------------------------
# process_message: send USER_INPUT to Cody, log both sides, and display response
# -------------------------------------------------------------------------------
process_message() {
    local USER_INPUT="$1"

    # Log user's message
    {
        echo "### You"
        echo "$USER_INPUT"
        echo ""
    } >> "$LOG_FILE"

    # Invoke Cody CLI
    CODY_RESPONSE=$(cody chat "${CONTEXT_ARGS[@]}" "${TOKEN_PARAM[@]}" -m "$USER_INPUT")

    # Log Cody's reply
    {
        echo "### Cody"
        echo "$CODY_RESPONSE"
        echo ""
    } >> "$LOG_FILE"

    # Print Cody's reply to the console
    echo -e "Cody: $CODY_RESPONSE
"
}

# -------------------------------------------------------------------------------
# Handle piped input: read stdin as the initial message and exit
# -------------------------------------------------------------------------------
if [ ! -t 0 ]; then
    PIPE_INPUT=$(cat)
    if [[ -n "$PIPE_INPUT" ]]; then
        # Merge piped text with any positional initial message
        INITIAL_MESSAGE="${INITIAL_MESSAGE:+$INITIAL_MESSAGE }$PIPE_INPUT"
        process_message "$INITIAL_MESSAGE"
    fi
    exit 0
fi

# -------------------------------------------------------------------------------
# If an initial message was provided via args, send it now
# -------------------------------------------------------------------------------
if [[ -n "$INITIAL_MESSAGE" ]]; then
    process_message "$INITIAL_MESSAGE"
fi

# -------------------------------------------------------------------------------
# Launch interactive chat loop
# -------------------------------------------------------------------------------
echo "Starting Cody chat session. Type 'exit' to end."
while true; do
    read -rp "You: " USER_INPUT
    if [[ "$USER_INPUT" == "exit" ]]; then
        echo "Chat session ended."
        break
    fi
    process_message "$USER_INPUT"
done

```




`/Users/pjones/dev/cody-chat-shell/readme.md:`

```md
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


```







[ℹ️] Tokens (Approximate): 1,986
