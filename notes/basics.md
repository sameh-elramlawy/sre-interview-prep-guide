# Basics — Notes

Reference: see the [`Basics` section](../README.md#basics) in README.md

## Key takeaways

- **URL to page**: input → parse URL → DNS resolve → TCP connect (ARP for MAC) → TLS handshake → HTTP req/res → parse HTML into DOM → parse CSS → layout/layers → GPU composite to screen.
- **Process**: isolated memory, own instance of a program; OS time-slices CPU across processes.
- **Thread**: lightweight execution unit *inside* a process; shares memory/code with sibling threads.
- Threads are ~5x cheaper to spawn and ~5x faster to context-switch (~1μs) than processes (~5μs), but lose crash isolation.
- Postgres = process-per-connection (isolation); MySQL = thread-per-connection (efficiency). Explains why Postgres connection pooling (pgbouncer) matters more in practice.
- Connection pooling exists because you can't afford a process/thread per client connection at scale — pool funnels many clients through ~5-50 real DB connections.

## Questions I got stuck on / want to revisit

-

## My own summary (explain it like I'd explain it in an interview)

When you hit enter on a URL, the browser first has to figure out where to send the request (DNS), then how to reach it securely (TCP + TLS), then what to ask for (HTTP), and finally how to turn the response into pixels (HTML/CSS parsing → render tree → GPU paint).

A process is a sandboxed running program; a thread is a cheaper, shared-memory worker within that sandbox. The tradeoff is isolation vs. cost — which is exactly why Postgres (process-per-connection) needs external pooling like pgbouncer, while MySQL's thread model handles more connections natively before needing a pooler.
