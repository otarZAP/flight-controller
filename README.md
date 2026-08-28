# FlightController

Ground-up modular flight controller: Teensy 4.1 running dRehmFlight native, custom per-motor
AM32 ESC boards, ExpressLRS link. Proven on a 250mm quad first, VTOL after.

- [docs/guide.html](docs/guide.html) — **the full illustrated build manual** (schematics, board layout, pin map, bring-up sequence)
- [docs/HANDOFF.md](docs/HANDOFF.md) — scope, decisions, and the reasoning behind them
- [docs/SAFETY.md](docs/SAFETY.md) — the five non-negotiables and how each gets bench-tested
- [docs/OPEN-QUESTIONS.md](docs/OPEN-QUESTIONS.md) — what's still undecided

Status: **planning, revision B.** Board settled (Teensy 4.1, no port required). Only genuine
blocker left is which IMU is physically on hand.
