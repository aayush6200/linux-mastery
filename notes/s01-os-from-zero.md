# Session 1 — OS from zero: kernel, user space, syscalls (2026-10-21)

## Concept
Hardware (CPU/RAM/disk/NIC) is dumb. The kernel is the only code allowed to touch it —
enforced by a CPU privilege mode, not convention. Everything else (ping, bash, tcpdump,
FRR) runs in user space and can only get work done by handing the kernel an order via a
system call (~350 exist: read, write, open, socket, sendto, ...). The program is frozen
while the kernel executes the call, then gets a result back.

Analogy used: kitchen (hardware) / head chef (kernel) / dining room (user space) /
order ticket (syscall). Program = recipe on disk; process = one live run of it, with a
PID (only touched on — processes are still ahead).

## Commands
- `limactl shell netlab` — enter the Linux VM (prompt changes Mac → lima-netlab)
- `strace -e trace=write echo hello` — wiretap on the syscall doorway
- Shell grammar: `program options arguments`; options start with `-`

## What normal looks like
`write(1, "hello\n", 6) = 6` then `+++ exited with 0 +++`.
- `1` = file descriptor for the screen (fd deep-dive is session 2)
- `"hello\n", 6` = the bytes (\n is a real 6th byte)
- `= 6` = kernel's receipt: "wrote 6 of 6"
- exit 0 = process ended successfully
Interleaved/mangled strace output is normal: strace and the traced program share the
terminal; you can see order → execution → receipt in one line.

## What broken looks like
- `= 3` on a 6-byte write: partial write — kernel did some, caller must retry the rest
- `= -1` : syscall refused/failed
- `sudo ip netns list` printing nothing while 23 routers run: `/run` is tmpfs, wiped at
  boot; `ip netns` lists *names*, not namespaces. (Met accidentally; session on it later.)

## My mistakes / gaps hit
- Didn't know what a process was → syllabus got a Session 0 track inserted.
- "= 6" first read as "6 chars echoed back" — it's a receipt (bytes written), not an echo.
- Reasoned correctly, unprompted: tcpdump gets packet *copies* via syscalls; permission
  checks must live in the kernel because user-space checks are editable = suggestions.

## 5-line summary
1. The kernel alone touches hardware; the CPU's privilege mode enforces the wall.
2. User-space programs get everything done via syscalls — the only doorway.
3. strace shows every syscall live: `write(1, "hello\n", 6) = 6` is order, work, receipt.
4. Security checks live kernel-side; user-space rules can be edited away.
5. Next: file descriptors — what that `1` really is, and why "everything is a file."

## Addendum — the syscall loop, visualized (same night)
The atom: **ticket → freeze → chef works → receipt → unfreeze.**
1. Program fills the ticket (write: where=fd 1, what="hello\n", how much=6)
2. Hands it over and FREEZES — the program does nothing during a syscall
3. Kernel does the physical work (only the kernel ever touches the screen)
4. Waiter returns the receipt (= 6); program unfreezes holding it
strace = inspector at the kitchen door: photographs the ticket (before step 3) and the
receipt (after step 3). The "mangled" output is chronological: ticket photo, real
output, receipt photo. strace's own photo lines are printed via its own write syscalls —
no back door exists; everything on screen goes through the kernel.

## Quiz result (graded at s02 open, 2026-10-21)
Q1 B (kernel ✓; say "CPU privilege mode", not "silicon coating")
Q2 C+ (loop ✓; MISSED: program is FROZEN during the syscall — re-quiz)
Q3 B− (parts ✓; fd 1 is a PLACE not a request; MISSED partial write `= 1` — re-quiz)
Q4 A (kernel-side checks, user space is editable)
