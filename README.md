# kirana voice agent

A voice assistant for a kirana shop, built on LiveKit Agents.
It takes grocery orders by voice and keeps a bill: add item, remove item, read bill.

## Run

```
uv sync
lk agent console
```

Needs `.env.local` with `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`.

Bugs found and how I fixed them: [LEARNINGS.md](LEARNINGS.md)
