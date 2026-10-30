# Session 3 — Syscalls up close with strace (2026-10-30)

Context: resumed via handoff PDF (Downloads/Aayush_Linux_Learning_Context.pdf) after
lost chat history. Learning order from here: syscalls → file descriptors → pipes →
sockets. Labs run in netlab VM (strace/python3/procps verified present).

## Quiz (processes & syscalls)
- "2+3 needs kernel?" — initially unsure, derived: NO. Math = unprivileged CPU
  instructions on own memory. Kernel = I/O and shared stuff, never computation.
- MISS then fixed: predicted 3 chef visits for `a=2+3; b=a*10; print(b)` (counted
  stores as syscalls). Corrected: memory USE is free; memory ACQUISITION (mmap/brk)
  is a ticket. "x=5 a million times = 0 syscalls" — passed.

## Lab 1 — strace python3 -c "print(2+3)"
- Prediction: 1 syscall. Reality: **394** (strace -c). The predicted write WAS there,
  exactly once. Lesson: a process's life is overwhelmingly setup — 393 tickets of
  seating (execve ×1), counter claims (mmap 25 / brk 4), cookbook fetches
  (openat 36 / read 31 / close 29) vs 1 ticket of actual work.
- Confusion caught & fixed: "chef called only once" — no: ONE seating (execve),
  MANY tickets (394). The chef is rung for every physical thing, forever.
- errors column (34): honest "no" receipts (e.g. faccessat ENOENT ld.so.preload),
  not crashes.
- Follow-up verified: `print(1);print(2);print(3)` → predicted & confirmed 1 execve,
  3 writes. Read the jammed strace lines himself (append-only terminal chronology,
  output photobombing the strace line — callback to s02).
- openat = 3 receipt noted as a CLAIM TICKET NUMBER (fd) — parked for next session.
- HFT tie-in: 393:1 overhead-to-work is why low-latency shops keep processes
  long-running/pre-warmed; kernel bypass = never ring the chef on the hot path.

## PARTIAL-WRITE GAP: DEAD (was 4 misses)
Miss #4 ("kernel hands it back and exits with 1" — also tangled exit status in).
Buried via concrete drills:
1. Kernel does NOTHING about missing bytes — honest receipt, keeps no IOU, stateless
   per ticket. 2. PROGRAM advances a bookmark by the receipt and re-orders ONLY the
   remainder (resending all = double-print bug: "hellhello").
- `write(1,"hello\n",6) = 4` → next call `write(1, "o\n", 2)` — got it after one miss
  (tried resending all 6).
- `write(1,"router\n",7) = 3` → `write(1, "ter\n", 4)` — **cold, correct.**
- Exit status is unrelated to mid-life receipts: retry succeeds → still exits 0.

## Still open (end of session / next)
- Lab 1 closing question: why "add" has no strace row (he knows it; needs the one-liner).
- Lab 2: process states & scheduler (sleep/ps/top/taskset).
- Lab 3: first FDs — /proc/<PID>/fd, slots 0/1/2.
- THE_STORY beats pending: births (two-step ticket), zombies, ps/pstree, fds, sockets.
