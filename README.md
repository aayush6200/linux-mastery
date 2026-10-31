# linux-mastery

A from-zero, lab-driven Linux systems engineering track — aimed at the depth a
systems/production engineer needs at infrastructure-heavy shops (big tech NDE/PE
roles, and eventually low-latency/HFT infrastructure).

I come from networking (BGP/OSPF, two Azure Networking internships, incoming
Network Production Engineer intern @ Meta). Linux internals are the weak spot;
this repo is the fix. Everything here is learned against a real machine, not
from summaries.

## Method

- **Predict, then run.** Before every lab command I write down what I expect.
  A wrong prediction is the lesson (see session 3: predicted 1 syscall for
  `print(2+3)`, got 394).
- **One mental model, real terms attached.** A single "kitchen" analogy maps
  every concept to its real name (kernel = chef, process = tagged pan, CPU
  core = burner, RAM = counter, syscall = the burner flipping into chef mode).
  Interviews need the real terms, so both are always used.
- **Blunt grading, spaced re-quizzing.** Each session opens with a no-notes
  quiz on the previous one. Recurring gaps get drilled until dead (the
  partial-write retry took 4 sessions to bury — it's documented).
- **Session notes are the artifact.** Every session ends with a note in
  `notes/` and a commit. If it isn't written down, it didn't happen.

## Environment

Labs run inside a Lima VM (`netlab`, Ubuntu, 6 vCPU) on an Apple Silicon Mac —
macOS has no strace//proc/netns, so all Linux work happens in the VM. The VM
also hosts a Containerlab/FRR topology from my parallel
networking-mastery track, which this track increasingly straces and inspects
(the end goal: read a BGP daemon as a Linux process, not a black box).

## Learning order

1. **System calls up close** — strace, the user/kernel boundary, receipts,
   partial writes ✅ (sessions 1–3)
2. **Process model** — states (R/S), the scheduler, fork/exec, /proc ⏳
3. **File descriptors** — the per-process fd table, slots 0/1/2, redirection
4. **Pipes** — how `ls | grep` actually wires two processes together
5. **Sockets** — Unix then TCP/UDP; the bridge back to networking
6. Later: the packet path (NIC → ring → softirq → socket), kernel tuning,
   CPU isolation, hugepages, NUMA, kernel bypass, PTP/time, eBPF

## Repo layout

| Path | What it is |
|---|---|
| `notes/` | One note per session: what was predicted, what happened, what was missed |
| `progress.md` | Syllabus + session log with quiz grades |
| `THE_STORY.md` | The running narrative — every concept added as a beat of one connected story |
| `labs/`, `drills/` | Lab scripts and recurring troubleshooting drills (grow over time) |

## Highlights so far

- **394:1** — a `python3 -c "print(2+3)"` makes 394 syscalls; 393 are setup
  (execve, library loads, mmap), exactly 1 is the work. Why short-lived
  processes are expensive and why latency-sensitive systems pre-warm.
- **Receipts are honest, not guarantees** — `write(..., 7) = 3` means the
  kernel placed 3 bytes and forgot you; the *program* keeps the bookmark and
  re-orders only the remainder.
- **A healthy box is mostly asleep** — watched a busy-loop pin a core at 99.3%
  (state R) while every daemon on the machine, including my own FRR ospfd,
  sat at S/0% waiting on events.
