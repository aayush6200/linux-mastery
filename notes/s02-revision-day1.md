# Session 2 — Full revision from zero (2026-10-27)

Context: 6-day gap after s01, retention ~zero. Restarting day-one material, taught fresh.
Tomorrow's session opens with a no-notes quiz on exactly the five items below.

## Today's syllabus (done when each box is checked)
- [x] 1. The kitchen: hardware is dumb; the kernel alone touches it; user space must ask.
       Pass = answer "who puts ping's packet on the wire?" correctly with reasoning.
- [x] 2. The syscall loop: ticket → freeze → chef works → receipt → unfreeze.
       Pass = recite the loop and state what the program does mid-syscall (nothing).
- [x] 3. Hands-on: rerun `strace -e trace=write echo hello`; read every part of
       `write(1, "hello\n", 6) = 6` cold (the 1, the bytes, the count, the receipt).
- [x] 4. Receipts: `= 6` success, `= 2` partial write (program must retry), exit 0.
- [x] 5. Program vs process: file on disk (inert bytes) vs private RAM copy + PID.
       Pass = answer "23 routers, one bgpd recipe → how many PIDs?"

## Parked for tomorrow (not today)
- The symlink twist: `/usr/bin/echo -> ../lib/cargo/bin/coreutils/echo` (what `l` and `->` mean)
- Process family tree (every process has a parent; PID 1), fork/exec
- File descriptors: slots 0/1/2, why errors get their own slot, `>` redirection

## Session log
(fills in as we go)
- Boxes 1–4 passed. Deep dive earned: full strace chronology (held tickets, strace's
  unfinished line, append-only terminal, microsecond timing). Self-corrected the
  "hello prints first" ordering.
- RECURRING GAP (3rd miss): partial-write receipts — always stops at "kernel wrote 2",
  keeps dropping "the PROGRAM must retry the rest." Re-quiz until dead.
- New today: exit status = final number left for the PARENT; 0 = success.

## 5-line summary (all 5 boxes passed)
1. Kernel alone touches hardware (CPU privilege mode); user space orders via syscalls.
2. The loop: ticket → freeze → chef works → receipt → unfreeze. strace photographs both door-crossings; jammed output = chronology on an append-only screen.
3. Receipts are honest, not guarantees: = 2 of 6 means THE PROGRAM retries the rest (3rd miss, flagged). Exit status = final number left for the parent; 0 = success.
4. Program = inert file; process = private RAM workspace + PID. 23 runs = 23 workspaces, 23 PIDs; walls contain crashes. The process is the atom — no sub-PIDs.
5. `echo $$` → own shell's PID (163410 today). A "router" = a team of processes; what groups them comes later (namespaces).
Next (2026-10-28): process deep dive — family tree, how processes are born, /proc, ps/pstree. Open with no-notes quiz on today's five.
