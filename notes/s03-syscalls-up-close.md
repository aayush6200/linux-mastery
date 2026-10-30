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

## Lab 1 closed
One-liner delivered (his words, polished): strace photographs only syscalls, and 2+3
never makes one — the CPU grinds the pan's own steps in user mode, start to finish.

## ANALOGY CANON CORRECTED (important for every future session)
The handoff PDF (Downloads/Aayush_Linux_Learning_Context.pdf, Oct 30) is the canonical
cast, and it differs from THE_STORY.md's dining-room framing:
- Pans live IN the kitchen, on the counter (each pan's counter section = its memory).
  There is no dining room. The only outsider is the USER at the serving hatch.
- The chef is a MODE, not a place/person: "the same CPU switches into kernel mode."
  A syscall = the pan's own burner flipping into chef mode. Avoid "door" language —
  it confused him twice; he pushed back hard until the cast was fixed.
- He now owns the state model in this cast: R = on a burner or in line for one;
  S = set aside on the counter waiting for the chef (event, not turn).

## Lab 2 — process states (core done; bonus deferred)
- Derived (after one detour via "runnable"): sleep 100 waits on an EVENT not a turn
  → sleeping. Verified: `ps -o pid,stat,comm` → `178265 S sleep`.
- Predicted and verified busy loop: `while True: pass` → R, 99.3%CPU in top.
- Read top himself: whole box is S at ~0% (incl. his own FRR pans — ospfd, watchfrr,
  asleep until the dock bell rings); TIME+ = accumulated burner time; %CPU scale is
  per-core (600 total on 6 vCPU); I rows = kernel idle workers, parked.
- Walked sleep's full life: seated → burner for an instant (place the timer ticket) →
  set aside 100s → timer fires → runnable → burner → exits, bash prints Done.

## 5-line summary
1. Kernel = I/O and shared stuff, never computation; memory USE is free, ACQUISITION
   (mmap/brk) is a ticket.
2. A process's life is overwhelmingly setup: 394 syscalls for print(2+3), of which
   393 are seating/cookbooks/counter and exactly 1 is the work (write). HFT keeps
   processes pre-warmed for this reason.
3. Partial-write gap DEAD after 4 misses: kernel reports honestly and forgets;
   program keeps the bookmark in its own memory and re-orders ONLY the remainder
   (write(1,"router\n",7)=3 → write(1,"ter\n",4) — cold, correct).
4. States seen live: R eats a burner (99.3%), S eats nothing; a healthy box is a
   kitchen full of set-aside pans.
5. Canon fixed: one kitchen, pans inside, user at the hatch, chef = kernel mode on
   the same burner.

## Next session opener
- No-notes quiz on: 394-vs-1 ratio, partial-write retry (one cold rep to confirm
  burial), the three states + letters.
- Lab 2 bonus: 3 loops pinned to burner 0 (`taskset -c 0`), predict per-pan %CPU.
- Then Lab 3: first file descriptors — the claim-ticket numbers (openat = 3 was
  already sighted on the Lab 1 tape), /proc/<PID>/fd, slots 0/1/2.
