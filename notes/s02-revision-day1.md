# Session 2 — Full revision from zero (2026-10-27)

Context: 6-day gap after s01, retention ~zero. Restarting day-one material, taught fresh.
Tomorrow's session opens with a no-notes quiz on exactly the five items below.

## Today's syllabus (done when each box is checked)
- [x] 1. The kitchen: hardware is dumb; the kernel alone touches it; user space must ask.
       Pass = answer "who puts ping's packet on the wire?" correctly with reasoning.
- [x] 2. The syscall loop: ticket → freeze → chef works → receipt → unfreeze.
       Pass = recite the loop and state what the program does mid-syscall (nothing).
- [ ] 3. Hands-on: rerun `strace -e trace=write echo hello`; read every part of
       `write(1, "hello\n", 6) = 6` cold (the 1, the bytes, the count, the receipt).
- [ ] 4. Receipts: `= 6` success, `= 2` partial write (program must retry), exit 0.
- [ ] 5. Program vs process: file on disk (inert bytes) vs private RAM copy + PID.
       Pass = answer "23 routers, one bgpd recipe → how many PIDs?"

## Parked for tomorrow (not today)
- The symlink twist: `/usr/bin/echo -> ../lib/cargo/bin/coreutils/echo` (what `l` and `->` mean)
- Process family tree (every process has a parent; PID 1), fork/exec
- File descriptors: slots 0/1/2, why errors get their own slot, `>` redirection

## Session log
(fills in as we go)
