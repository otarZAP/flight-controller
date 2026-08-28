# FlightController — Project Handoff

Revision B, 2026-08-28. **Supersedes Revision A** — the FC moved from an ESP32-S3 port to a
native Teensy 4.1, and the RC link from Spektrum DSMX to ExpressLRS.

## What this is

A ground-up custom flight stack built as **separate modular boards**, targeting a VTOL
airframe, developed and flight-proven on a quad first.

## Scope — three modules, deliberately separated

| Module | Choice | Notes |
|---|---|---|
| **FC board** | Teensy 4.1 on perfboard | dRehmFlight run **native**, near-stock. No port. |
| **ESC board(s)** | Custom, one per motor | Hardware is yours; firmware is **AM32**. Failure isolation + per-motor redundancy. |
| **RC link** | ExpressLRS, external module | **Bought, not built** — deliberately. |
| **Motors** | Commodity brushless | Out of scope. Considered and dropped. |

## The Revision A → B reversal

Original plan was ESP32-S3 + Arduino, porting dRehmFlight from its native Teensy target.
**That porting path is abandoned.** dRehmFlight is written for and documented against the
Teensy 4.0/4.1; running it there deletes six categories of flight-critical work:

- `PulsePosition.h` (Teensy-only) — native
- `PWMServo` (Teensy-only, needed for the VTOL phase) — native
- OneShot125 bit-bang jitter under FreeRTOS preemption — deterministic on bare metal
- Core-pinning the control loop — one core, nothing to pin
- WiFi/BT disable to stop interrupt jitter — no radios on the part
- Flash-write stalls from NVS-emulated EEPROM — absent

A Teensy 4.1 was already on the bench. This is a straight risk reduction, not a compromise.

**ESP32-S3** is still a fine board — it just loses on the one axis that matters here.
**ESP32-P4** is ruled out entirely: Arduino support is explicitly immature (ESP-IDF is the
recommended path), which is the wrong foundation for a flight-critical loop.

## Sequencing

1. Bench bring-up, props off. Meter every signal line first — **the Teensy 4.x is NOT 5 V tolerant.**
2. Flight-test on the existing **250 mm carbon quad** (frame, motors, ESCs on hand).
3. Custom ESC boards.
4. VTOL airframe, only once (2) is proven stable.

Perfboard before PCB is deliberate: debugging a noise problem means moving a wire, not
respinning a board. The AIO/integrated board is **phase 2**.

## RC link — the one live decision

ELRS receivers speak **CRSF** natively; **SBUS** is a legacy output mode. dRehmFlight supports
PWM/PPM/SBUS/DSM — not CRSF.

- **Path A (default, first flight):** configure the RX to SBUS. Zero new code, drops into
  dRehmFlight's existing path. Loses return telemetry.
- **Path B (phase 2):** integrate `CRSFforArduino` (confirmed Teensy 4.x support) for full
  bidirectional telemetry — link quality, RSSI, battery voltage back to the transmitter.

Gotcha: ELRS can emit SBUS inverted or not, and mismatched inversion presents as *no channels
at all*. Meter the idle line before suspecting the parser.

## Standing design principles — carry forward

- **Two domains.** Logic/signal and pack/phase-current never share a return path.
- **Star ground** at a single physical point, marked on the board before soldering.
- **IMU soft-mounted**, treated as a mechanical low-pass filter with a designed corner.
- **Physical separation** of noise-sensitive from noisy — board level now, layout level later.

## Explicitly dropped — do not resurrect without new information

- **Hand-winding motors** — rotor imbalance manufactures the exact vibration the soft mount exists to reject.
- **Homebrew LoRa control link** — ELRS *is* LoRa (SX127x/SX1280) with adaptive rates, hopping
  and tuned failsafe detection already solved. Rolling your own reintroduces the unverified-link
  risk that buying the module removed. The spare S3 LoRa module's real job: telemetry downlink or
  a long-range failsafe position beacon, on its own channel, independent of control.
- **Writing ESC commutation** — bigger than the FC itself, and AM32 already does it better.
