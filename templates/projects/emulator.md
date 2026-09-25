# Project-Type Template: Emulator

See `templates/TEMPLATE_RULES.md` for shared design principles (question-bank shape, using web
research mid-dialog — especially relevant here for target-hardware specs and conformance
test suite discovery).

A question bank for hardware emulation projects (console/computer/arcade emulators), covering
the questions any such project needs answered regardless of which system is being emulated.

Unlike the language template, there are almost no static rules here — "what an emulator needs"
varies enormously by target hardware. This file exists to make sure `workflow-init` asks the
right questions, not to assert answers.

## Static Rules (the few things true for any emulator)

- Separate the CPU(s), memory/bus, and each peripheral subsystem into clear module boundaries —
  emulators are unusually prone to accidental coupling (a CPU handler reaching directly into
  video RAM, timing logic leaking into instruction decode) because the real hardware doesn't
  enforce any boundary at all. The project's own module list (Q3 below) is what actually goes
  into `CLAUDE.md`; this rule is *why* that question gets asked, not a substitute for it.

## Questions

### Target Hardware
1. **What system/hardware are you emulating?** (free text — console/computer/arcade name)
2. **Which processor(s) does it have, and what architecture is each?** (e.g. one CPU, or
   multiple like a main CPU + audio co-processor) — becomes the target hardware table.
3. **Master/subsystem clock rates, if known?** (master clock + per-component divisors, or "not
   yet researched" — this is exactly the kind of spec detail the AI should own and look up
   once implementation starts, not something the user needs to already know at init time)
4. **Memory map / bus architecture?** (unified address space vs. segmented/banked; any bank
   switching, mirroring, or memory-mapped I/O quirks worth flagging up front)

### Subsystems
5. **Which peripherals need emulating, beyond the CPU(s)?** (video/graphics chip, audio chip,
   input, cartridge/media loading, DMA, timers, interrupt controller — free text, becomes the
   module list the static rule above references)
6. **Rendering/output approach?** (software framebuffer, hardware-accelerated via a library
   like SDL/OpenGL/Vulkan, or undecided/deferred)

### Accuracy & Testing Philosophy
7. **Accuracy model — this is two separate, orthogonal questions, not one:**
   - **Execution granularity:** does the CPU execute a full instruction atomically and just
     advance its cycle counter by the instruction's cost, or does it actually step through
     sub-instruction/microcode cycles? These have real architectural consequences (an atomic
     model can't model mid-instruction bus contention/interrupts the way a stepped one can).
   - **Timing precision:** are cycle counts exact per addressing mode/bus-cycle quirk (hardware-
     verified against real timing references), or approximate/"close enough"? A project can
     be instruction-atomic *and* cycle-exact at the same time — that's a legitimate, common
     combination (execution is atomic; the cycle count charged for that atomic step is still
     precise), not a contradiction. Don't force this into a single 3-way "cycle-accurate /
     instruction-accurate / approximate" bucket.
8. **Is there a community-maintained conformance test suite for the target CPU?** (e.g.
   Tom Harte-style per-instruction JSON test vectors, or similar for other architectures) —
   if one exists, it's worth building the test harness around it from the start; if none
   exists, ask whether hand-written unit tests per instruction/opcode are the fallback.
9. **Any existing reference documentation for the target hardware?** (official manuals,
   community wikis, hardware test suites, prior open-source emulators used as reference) —
   feeds directly into the PRD's reference-material question too; no need to re-ask there.
11. **Who owns spec lookups during implementation — the AI or you?** (default: the AI — timing
    tables, cycle costs, encodings and masks looked up and stated directly, never asked of the
    user; "me" if the user has the manuals and wants to do the lookups as part of learning)

### Scope
10. **Full system, or a specific subsystem first?** (e.g. "just get the CPU executing
    correctly before touching video/audio" vs. "need a playable loop from day one") — shapes
    the project's own milestone/roadmap structure in the PRD.

## Memories

Seeded into the project's memory by `workflow-init` (Step 8). Bodies live in `templates/memory/`.

| File | Index line | Condition |
|------|-----------|-----------|
| `feedback_hardware_spec.md` | `- [Hardware spec ownership](feedback_hardware_spec.md) — AI owns all target-hardware timing/spec numbers; never ask the user to look them up` | `Q11 = AI` |
