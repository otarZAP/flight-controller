# Open / TBD

Revision B. Much shorter than Revision A — the Teensy 4.1 reversal closed most of it.

## Blocking

- [ ] **Which IMU is physically on hand?** The only genuine blocker left. An MPU6050 breakout
      already in a drawer decides stage one and needs no driver work.

## Decided, with a default set

- [ ] **SBUS vs CRSF on the ELRS link** — default is Path A (SBUS) for first flight. Revisit
      only once flying and telemetry is worth the integration cost.

## Verify, don't decide

- [ ] **What the 250's ESCs accept** — downgraded from blocking. OneShot125 auto-detect is
      near-universal on BLHeli_S/AM32. Confirm at bench step 7, not up front.

## Later

- [ ] **DShot output on Teensy 4.1** — needed when the custom AM32 ESCs exist, since dRehmFlight
      emits OneShot125. FlexIO or DMA; bounded, but plan it *with* the ESC boards.
- [ ] **ESC MOSFET / gate-driver selection** — against current stock and the AM32 supported-target
      list, at schematic capture.
- [ ] **How far to adapt dRehmFlight vs. run near-stock** — decide after first bench loop-rate
      measurement, not before.

## Closed in Revision B

- ~~Which ESP32-S3 board~~ — moot, board is a Teensy 4.1.
- ~~dRehmFlight port strategy~~ — no port required.
- ~~Full DSMX receiver or satellite~~ — RC link is ExpressLRS.
