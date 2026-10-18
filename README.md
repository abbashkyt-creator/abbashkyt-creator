### Abbas Bidhan: networking & cybersecurity student who builds with AI agents

I'm not a traditional programmer: AI coding agents (Claude Code, Codex) write the code. I decide what to build, how the parts fit together, what to test and when it is right, and I run it live.

That is how I built and run [GhostNode](https://ghostnode.co) on my own: a system on one machine (Docker, PostgreSQL, Valkey, a Cloudflare tunnel) that tracks 8 auction marketplaces (1.7M+ listings tracked since March 2026) and bids automatically on 4 of them.

**[Engineering notes](https://github.com/abbashkyt-creator/engineering-notes)** has five problems the agents and I found and fixed on the live system:

- a dashboard bid reaches our server at most once: a request is retried only before a connection exists, and a test checks that nothing is replayed after a reset;
- listing updates were 99% of Postgres WAL; five changes took it from 3.8 GB/h (a few-minute sample, 3 Oct 2026) to 1.5 GB/h (19-hour average). Most of what is left is text-search index pages copied whole after each checkpoint;
- the engine froze 100 s after every start: a regex held Python's global lock, so every thread waited, its watchdogs included ([runnable repro](https://github.com/abbashkyt-creator/engineering-notes/blob/main/repro/gil_regex_freeze.py)). Lesson: a watchdog inside a process freezes with it, so the supervisor now lives outside it;
- one shared refresh loop became one lane per source, so a listing's price in its final 5 minutes went from about 3 days old to under 1 minute at a check on 3 Oct 2026, for every source but one (median 3 minutes there; whether "under 1 minute" was the median or the oldest was not recorded);
- vacuum keeping up with the main table again: dead tuples 77% → 0.1% after the fixes and a manual VACUUM, and 3.7% on 17 Oct 2026.

Two small open-source libraries, written by AI coding agents from ideas in that work, with tests on CI. Bugs are found by AI code review, and each one is reproduced as a failing test before it is fixed. Four reviews in a row found data loss in one feature of quiet-updates (carrying skipped values into a later write), so 0.4.0 deletes that feature instead of patching it again. An oracle test checks the library against plain SQLAlchemy applying the same rule by hand:
- **[quiet-updates](https://github.com/abbashkyt-creator/quiet-updates)**: a SQLAlchemy extension that skips UPDATEs touching only bookkeeping columns, such as last-checked timestamps.
- **[honest-retry](https://github.com/abbashkyt-creator/honest-retry)**: retries HTTP requests only when it is safe, and never replays a POST that may already have been received.

Other things I built the same way:
- **VEU**: a Node.js server that lets AI agents drive a real Chrome browser over the DevTools Protocol, through MCP, CLI or HTTP.
- **[Clone Anyone](https://cloneanyone.ghostnode.co/vx)**: ask a creator a question and get the answer from their own public videos, in Arabic, English or German, with the exact passage cited.
- **TG Sentinel**: a local service that lets an AI assistant search and summarise my Telegram; it sends nothing without my approval.
- **[Tip Tap Games](https://github.com/abbashkyt-creator/Hackathon)**: a hackathon project (July–August 2026), a swipe feed of third-party web games with live leaderboards and server-side score validation. The Codex agent wrote the first version.

Third-year Networking & Cybersecurity Engineering student, Al-Iraqia University (2024–2028). Looking for a remote internship or junior role in IT infrastructure, networking or security; open to relocation with visa sponsorship.

[LinkedIn](https://www.linkedin.com/in/abbas-bidhan)
