# Kirana Voice Agent

A voice assistant for a kirana (Indian grocery) shop. You speak your order, it adds items to a bill, and reads the bill back when you ask.

Built with [LiveKit Agents](https://docs.livekit.io/agents/) for Python, starting from the official Python starter.

## Example

> **You:** 2 kilo rice and 1 kilo dal
> **Agent:** Added 2 kg rice and 1 kg dal.
> **You:** Remove 1 kilo rice
> **Agent:** Done, 1 kg rice left.
> **You:** What's my bill?
> **Agent:** 1 kg rice, 1 kg dal.

## What it can do

| Tool | What it does |
|---|---|
| `add_item_to_bill` | Adds an item and its quantity. Asks again if the item is unclear. |
| `remove_item_from_bill` | Removes an item, or part of it (e.g. 2 kg out of 5 kg rice). |
| `read_bill` | Reads back everything on the bill. |

## How it works

```
mic -> speech to text -> LLM -> text to speech -> speaker
                          |
                     bill tools
```

| Part | Model |
|---|---|
| Speech to text | AssemblyAI Universal (via LiveKit Inference) |
| LLM | Gemma 4 31B (via LiveKit Inference) |
| Text to speech | Fish Audio (via LiveKit Inference) |
| Turn detection | LiveKit turn detector |

All the agent code is in `src/agent.py`.

## Run it

1. Install [uv](https://docs.astral.sh/uv/) and the [LiveKit CLI](https://docs.livekit.io/home/cli/).
2. Create `.env.local` with your LiveKit Cloud keys:
   ```
   LIVEKIT_URL=...
   LIVEKIT_API_KEY=...
   LIVEKIT_API_SECRET=...
   ```
3. Install and start talking:
   ```bash
   uv sync
   lk agent console
   ```

Press `Ctrl+T` in the console to type instead of speak.

## Tests

```bash
uv run pytest
```

## What I learned

Three bugs I hit while building this, and how I fixed them:

| Bug | Fix |
|---|---|
| The agent made an extra tool call with `"unknown"` as the item. | A guardrail inside `add_item_to_bill` refuses empty or unknown items, so the agent asks again instead. |
| It said "removed 1 kg", but the bill never changed. The logs showed `read_bill` returning the old items while the LLM confidently claimed the removal. | Switched the bill from a list to a dictionary (item to quantity), so part of an item can be removed, like 2 kg out of 5 kg rice. |
| The LLM answered from memory and claimed tools worked when they hadn't. | Instructions now say: always call `read_bill`, never read the bill from memory, never pretend a tool worked. |

The main lesson: **never trust what the LLM says it did; check the tool calls in the logs.**

Tool calls also didn't always come in the order I said things. That one is still open.

The full story is in [LEARNINGS.md](LEARNINGS.md).
