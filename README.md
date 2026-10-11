# F.R.I.D.A.Y.

A terminal-based personal AI assistant with persistent memory, inspired by Tony Stark's F.R.I.D.A.Y. It chats through Groq's OpenAI-compatible API, remembers facts you tell it across sessions, and answers in a calm, concise voice — addressing you as "Boss."

```
========================================
              F.R.I.D.A.Y.
          powered by Ganayo AI
========================================
Type /help for commands.
Type exit to shut down.

Ask: /remember I'm learning Python
FRIDAY: Memory saved, Boss.

Ask: what should I build next?
FRIDAY: ...
```

## Features

- **Conversational chat** in the terminal, with colored output.
- **Persistent memory** — facts saved with `/remember` are stored in `memory.json` and included in every request, so F.R.I.D.A.Y. knows them in future sessions.
- **Rolling context** — the last 20 messages (10 exchanges) of the current session are sent with each request.
- **Safe memory writes** — memory is written to a temp file first, then swapped in, to avoid corrupting `memory.json`.
- **Prompt-injection guard** — saved memories are treated as information, never as instructions; only the built-in commands can change memory.

## Requirements

- Python 3.8+
- A [Groq](https://console.groq.com/) API key
- Python packages: `openai`, `colorama`

## Setup

```bash
git clone https://github.com/godwinganayo/friday-ai.git
cd friday-ai

python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

pip install openai colorama
```

Set your API key. The script reads it from the `ZAI_API_KEY` environment variable:

```bash
# macOS / Linux
export ZAI_API_KEY="your-groq-api-key"

# Windows (PowerShell)
$env:ZAI_API_KEY="your-groq-api-key"
```

## Usage

```bash
python main.py
```

Type a message and press Enter to chat. Lines starting with `/` are handled locally and never sent to the API.

### Commands

| Command | Description |
|---|---|
| `/remember <fact>` | Save a fact to memory (duplicates are ignored, case-insensitive) |
| `/memory` | List all saved memories |
| `/forget <text>` | Find memories containing `<text>` and delete them after confirmation |
| `/clear-memory` | Delete all memories (type `YES` to confirm) |
| `/reset` | Clear the current conversation history (memories are kept) |
| `/help` | Show the command list |
| `exit` / `quit` / `bye` | Shut down |

`Ctrl+C` also ends the session.

## Configuration

These constants live at the top of `main.py`:

| Setting | Default | Purpose |
|---|---|---|
| `MODEL` | `openai/gpt-oss-120b` | Model used on Groq |
| `base_url` | `https://api.groq.com/openai/v1` | API endpoint (any OpenAI-compatible provider works) |
| `MAX_HISTORY_MESSAGES` | `20` | Recent messages sent with each request |
| `SYSTEM_PROMPT` | — | F.R.I.D.A.Y.'s personality and memory rules |

## How memory works

`memory.json` holds a single list of strings:

```json
{
    "facts": [
        "I'm learning Python"
    ]
}
```

On every message, the facts are appended to the system prompt under `SAVED MEMORIES`. If the file is missing or empty, F.R.I.D.A.Y. starts with an empty memory; if it's malformed, you'll see an error rather than silently losing data.

> **Note:** `memory.json` is tracked in git. If you save personal facts, consider adding it to `.gitignore` so they aren't pushed to GitHub.

## Project structure

```
friday-ai/
├── main.py        # Chat loop, commands, memory handling, API calls
├── memory.json    # Saved facts (persistent memory)
└── .gitignore
```
