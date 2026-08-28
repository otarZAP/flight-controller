# Non-negotiable safety mechanisms

Each of these guards a specific failure mode. None ships unverified on the bench, props off,
before it flies.

| Mechanism | Failure it guards | Bench test |
|---|---|---|
| **Signal-loss failsafe** | RX link drops mid-flight | Power off TX. Motors must cut / enter safe descent. **Never hold-last-command.** |
| **Arm/disarm state machine** | Spin-up on boot, on reconnect, or on a glitched throttle channel | Boot with throttle high — must refuse to arm. Disarm, re-power RX — must stay disarmed. |
| **Hardware watchdog** | MCU hang | Force a hang (blocking loop behind a debug command). The chip must reset and motors must cut, not freeze at last throttle. On the Teensy 4.1 this is a real hardware WDOG with its own clock, not a software task watchdog — it fires even if the CPU is wedged. Feed it once per loop, never from an interrupt. |
| **IMU sanity checks** | Spikes / stuck values poisoning the control loop | Reject out-of-range and unchanging-sample conditions before fusion. Unplug the IMU mid-run. |
| **Brownout / battery monitoring** | Sag under load → MCU reset with ESCs still armed | Clean cutoff at threshold. Verify behavior *through* a reset, not just at it. |

## Standing rules

- Props off for every first run of anything that touches motor output.
- Meter every signal line before it touches a pin. The Teensy 4.x is **not 5 V tolerant** and has no protection diode.
- No armed state reachable from a fresh boot without an explicit disarm→arm transition.
- Any failsafe that "holds last known good" is wrong for throttle. Cut, don't hold.
