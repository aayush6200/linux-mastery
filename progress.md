# Linux Mastery — Progress

Target: Linux systems/network engineering at HFT level (SIG, Jane Street, IMC, HRT, Optiver).
Method: create → break → learn, on this box (Lima VM `netlab`, Ubuntu 26.04, kernel 7.0,
6 vCPU, 8GB) and the Containerlab/FRR lab in `/repo`.

Rules: one concept per session (30–60 min). Session opens with a no-notes quiz on the
last one, graded bluntly. Troubleshooting drills recur from session 2 on. Every session
ends with a 5-line summary in its note and a git commit.

Environment facts (inspected 2026-10-21):
- Host: macOS (terminal only). Linux box: Lima VM `netlab` — `~/dev` visible read-only
  at `/Users/aayushthapaliya/dev` inside the VM; `/repo` (network-mastery) is rw.
- Containerlab 0.78.2. Lab `ibgp-01` running (FRR 9.1.0), labs `abr-repro`/`asan` exited.
- No real NIC work possible (virtio NIC `eth0`); hardware-NIC topics (ring buffers,
  IRQ affinity, hw timestamps) get measured as far as virtio allows, concepts noted
  where the VM wall is hit.

## Syllabus

Order unchanged from the spec — it's correct: stack mechanics first, observability
second so every later topic is measured not described, then down the packet path
into tuning/latency, then the surrounding prod skills. Drills interleave from S2.

- [ ] 0. OS from zero: what a kernel is, programs vs processes, syscalls, files, /proc, the shell — taught against the lab, not in the abstract. (Inserted 2026-10-21: user calibrated "assume zero Linux/OS knowledge." Original session 1 stalled on "what is a process"; namespaces require process + file first.)
- [ ] 1. Linux networking stack: ip link/addr/route/neigh, namespaces, veth, bridges — what Containerlab builds
- [ ] 2. Observability: ss, tcpdump, /proc/net/*, ethtool -S, nstat, conntrack — finding drops
- [ ] 3. Packet path: NIC → driver → ring buffer → softirq/NAPI → socket; interrupts, RSS/RPS, IRQ affinity
- [ ] 4. Kernel tuning: sysctl net.*, socket buffers, TCP tuning — what HFT shops change
- [ ] 5. Low latency: CPU isolation, isolcpus/nohz_full, hugepages, NUMA, busy polling, kernel bypass (DPDK/Onload/AF_XDP)
- [ ] 6. Time: NTP vs PTP, hardware timestamping, nanoseconds in trading
- [ ] 7. Filtering and policy: nftables, tc, policy routing, multiple routing tables
- [ ] 8. eBPF: bpftrace basics, tracing the packet path, Cilium's use of it
- [ ] 9. Prod box skills: systemd, cgroups, journald, perf, strace, diagnosing a sick server fast
- [ ] 10. Troubleshooting drills (recurring from session 2)

## Session log

| # | Date | Topic | Quiz grade | Note |
|---|------|-------|-----------|------|
| 1 | 2026-10-21 | OS from zero: kernel, programs, processes | — (first) | notes/s01-os-from-zero.md |
| 2 | planned 2026-10-22 | What a process IS (fork/exec/PID), then file descriptors — 1 hr | s01 quiz: B/C+/B−/A | notes/s02-process-fds.md |
