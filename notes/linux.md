# Linux — Notes

Reference: see the [`Linux` section](../README.md#linux) in README.md

## Key takeaways

### Shell internals (FDs, pipes, terminals, sessions, daemons)
- File descriptor = integer handle to a kernel "open file description" (file/socket/pipe + offset/flags); shared across fork(), can be duplicated with dup2.
- Pipes wire one process's stdout FD to another's stdin FD — literal mechanism behind `cmd1 | cmd2`.
- Session (tied to terminal login) contains process groups (jobs); Ctrl+C sends SIGINT to the *foreground process group*, not a single PID.
- PTYs turn keystrokes into input and handle terminal resize via SIGWINCH (why vim redraws on resize).

### Linux Page Cache
- Kernel caches file contents in free RAM; reclaimable, so "high memory used" from `free`/`top` is often misleading.
- Critical for capacity planning and container memory limits — cgroups can OOM-kill if page cache isn't accounted for correctly.
- Key syscalls: mmap(), fsync(), posix_fadvise(). Key tools: vmtouch, /proc/PID/smaps, /proc/kpageflags.
- Misreading page cache behavior causes false "memory leak" alerts and unpredictable I/O latency during backups/rsync/DB workloads.

## Questions I got stuck on / want to revisit

-

## My own summary (explain it like I'd explain it in an interview)

Everything a Linux process touches — files, sockets, pipes — is addressed through file descriptors, small integers the kernel maps to real resources. Pipes are just two processes sharing FDs so one's stdout becomes another's stdin. Signals like Ctrl+C don't target a single process; they hit the whole foreground process group of your session, which is why job control (bg/fg, process groups) matters for building resilient daemons.

The page cache is the kernel using "free" RAM to cache file I/O — it's not wasted memory, it's reclaimable. As an SRE, if I don't account for this I'll misdiagnose "memory pressure" that's actually harmless cache, or worse, hit surprise OOM kills in a cgroup-limited container that doesn't reclaim cache fast enough under memory pressure.
