---
title: car-control — the SCA test field
date: 2026-11-14
tags: [project, sca, dummy_project, car-control, embedded, c]
status: draft
source: [[source-car-control-repo-2026-11-14]]
related: [[overview]], [[ground-truth-marker-convention]], [[embedded-c-defect-categories]], [[2026-11-11-free-tools-baseline-on-dummy-project]]
---

# car-control — the SCA test field

## Summary
`dummy_project` is a realistic embedded C99 program to run SCA on. It contains known bugs, so we can measure what SCA finds and what it misses.

## Details
- **Style:** embedded rules: no heap, fixed-width types, memory-mapped `volatile` registers, a 1 ms timer ISR, a cooperative scheduler.
- **Modules (15):** adc, battery (SOC), can (signals + dispatch), crc (CRC-8 J1850, CRC-16 CCITT), diag (DTCs, freeze frame), filter, motor_ctrl (state machine, PWM), nvm (calibration with CRC), pid (fixed point), ring_buffer, scheduler, util, watchdog, wheel_speed, main. On top of these, `hw_sim.c` is the PC-only plant model and is not ECU code.
- **Build:** `build.ps1` (CMake + Ninja, local toolchain) writes `build/compile_commands.json`, which is what SCA plan step 7 will consume. `-Clean` rebuilds from scratch.
- **Normal run:** a 12 s drive cycle (pedal 0 → 40 → 70 → 95 → 30 %) that prints one status line per second.
- **Fault injection:** `--fault 1..15` (`--list` shows them all) makes a planted defect actually happen at run time, for example a crash on divide by zero, silent memory corruption, or a wrong value. This proves the planted defects are real.
- **Use:** baseline for the free tools ([[2026-11-11-free-tools-baseline-on-dummy-project]]) and the benchmark in step 18 of [[implementation-plan]].

## Open questions
- Should the benchmark compare by file + line only, or by file + line + category? The categories in the CSV are our own names and need mapping to SCA rule IDs.
