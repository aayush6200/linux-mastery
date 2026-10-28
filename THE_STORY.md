# THE STORY — the one connected narrative (canonical; every revision starts here)

Each beat is a consequence of the one before. New topics get added as new beats of
this same story, never as disconnected facts.

1. **The machine is a dark kitchen.** CPU = furnace executing one dumb instruction at
   a time; RAM = counter (fast, wiped at power-off); disk = pantry (slow, permanent);
   NIC = loading dock for packets. The equipment knows nothing.
2. **Only the head chef touches equipment: the kernel.** Enforced by the CPU's
   privilege mode — in customer mode, equipment-touching instructions don't execute.
   Silicon, not rules.
3. **Everything else is a customer in the dining room.** Every program you run. They
   can think privately; anything physical is beyond their reach.
4. **One doorway: the order ticket — the syscall.** Fill blanks, hand over, FREEZE
   until the answer returns. ~350 kinds of ticket; all software is sequences of them.
5. **Every ticket returns a receipt.** `= 6` all done; `= 2` partial — the CUSTOMER
   re-orders the remaining 4. Exit status = final number left on the table; 0 = fine.
6. **A process = one seated customer:** the chef photocopies a recipe (program file,
   inert) onto a private walled table (own RAM: instruction copy + scratch paper) and
   pins an apron number (PID). File = permanent/shared; process = alive/private/temporary.
7. **Time-sharing:** 200 customers, 6 burners. A few ms each, bookmark mid-stride
   ("resume at step 4502"), rotate. Simultaneity is an illusion of speed. Frozen
   customers aren't in the rotation at all.
8. **Walls contain disasters.** Run #7 crashes → shred table 7; the other 22 runs of
   the same recipe never notice. Hence process ≠ program (one recipe, 23 aprons).
9. **Every customer was seated by an existing customer's ticket.** bash = customer
   factory. Optional second ticket: "wake me when my child leaves + hand me its
   number" (bash waiting); skipping it = `&`. All chains end at PID 1, seated at boot.
10. **The ledger is public: /proc.** One folder per apron number; appears at seating,
    vanishes at leaving; never on disk — a live window into the chef's bookkeeping.
    (Stood inside /proc/165267 on 2026-10-28 while its sleep was alive.)

Next beats to be written: births in detail (the two-step ticket), zombies, the family
tree commands (ps, pstree), file descriptors (slots 0/1/2), and — the bridge home —
sockets: a BGP session is just a slot number on an FRR customer's table.
