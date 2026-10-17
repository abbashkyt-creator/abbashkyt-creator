### Abbas Bidhan, backend engineer

**Hardest problem so far:** my engine went silent 100 s after every start, and it looked like a lost database reply. Two py-spy dumps showed a regex holding Python's GIL and silencing every in-process watchdog. I fixed the parser, and moved the watchdog out of process ([write-up and repro](https://github.com/abbashkyt-creator/engineering-notes/blob/main/03-gil-freeze.md)).

I built and run [GhostNode](https://ghostnode.co) alone: a Python and PostgreSQL system on one machine that tracks 8 auction marketplaces (1.7M+ listings tracked since March) and bids automatically on 4 of them.

**[Engineering notes](https://github.com/abbashkyt-creator/engineering-notes)** has five production problems from it:

- making the console-to-API hop for bids at-most-once: a request is retried only before a connection exists, and a test checks that nothing is replayed after a reset;
- finding that listing updates were 99% of Postgres WAL; five changes took it from 3.8 GB/h (a few-minute sample) to 1.5 GB/h (19-hour average), with 0 deadlocks in that window. Most of what is left is text-search index pages copied whole after each checkpoint;
- tracing a freeze 100 s after every start to a regex holding Python's GIL ([runnable repro](https://github.com/abbashkyt-creator/engineering-notes/blob/main/repro/gil_regex_freeze.py));
- replacing a shared refresh tick with per-source lanes, so a listing's price in its final 5 minutes went from about 3 days old to under 1 minute at a check the same evening, for every source but one, whose median was 3 minutes (I did not record whether "under 1 minute" was the median or the oldest);
- getting vacuum to keep up with the main table again: dead tuples 77% → 0.1% after the fixes and a manual VACUUM, and 3.7% on 17 Oct.

Open source, re-implemented from scratch from ideas in that work, with tests on CI. Bugs are found by AI code review, and each one is reproduced as a failing test before it is fixed. Four reviews in a row found data loss in one feature of quiet-updates: carrying skipped values into a later write, which is a cache that has to stay coherent across sessions and processes. So 0.4.0 deletes that feature instead of patching it again, and goes back to discarding skipped values. An oracle test checks the library against plain SQLAlchemy that applies the same rule by hand:
- **[quiet-updates](https://github.com/abbashkyt-creator/quiet-updates)**: a SQLAlchemy extension that skips UPDATEs touching only bookkeeping columns, such as last-checked timestamps.
- **[honest-retry](https://github.com/abbashkyt-creator/honest-retry)**: retries HTTP requests only when it is safe, and never replays a POST that may already have been received.

Other things I built:
- **VEU**: a Node.js server that lets AI agents drive a real Chrome browser over the DevTools Protocol, through MCP, CLI or HTTP.
- **[Clone Anyone](https://cloneanyone.ghostnode.co/vx)**: ask a creator a question and get the answer from their own public videos, in Arabic, English or German, with the exact passage cited.
- **TG Sentinel**: a local service that lets an AI assistant search and summarise a Telegram account; anything sent needs human approval.
- **[Tip Tap Games](https://github.com/abbashkyt-creator/Hackathon)**: a hackathon project (July–August 2026), a swipe feed of third-party web games with live leaderboards and server-side score validation. Built with AI coding agents that I directed; the Codex agent wrote the first version.

In the GhostNode repository, 231 of 409 commits are co-authored by Claude Code. I set the design, review every change, and do the debugging and operations.

Networking & Cybersecurity Engineering student, Al-Iraqia University (2024–2028). Open to full-time roles: remote, on-site in Baghdad, or relocation with visa sponsorship.

[LinkedIn](https://www.linkedin.com/in/abbas-bidhan)
