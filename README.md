# SYNC

**Calm, rhythm-cued motor practice for children who struggle with coordination.**

▶ **Live demo:** https://sync-proj-demo.netlify.app (best on an iPad. Motion sensors don't work on a desktop browser.)

SYNC is a tablet game for motor-planning practice. It was designed first for autistic children with dyspraxia (developmental coordination disorder, DCD), but it is meant to work for any child who finds coordination hard. The child tilts the tablet to steer an indicator toward a target, then taps to commit, in time with a calm rhythmic cue. Behind the game, SYNC records the raw motion signal so that a therapist or researcher can look at *how* the child moved, not only whether they hit the target.

> **Status:** research prototype. No data from real children has been collected, and none will be until ethics (REB) approval is in place.

---

## Why it exists

My scoping review of sensory-motor difficulties in autistic children with DCD found two gaps. Very few interventions are built for this population, and very few tools capture movement quality objectively outside a lab. SYNC tries to address both: practice that a child will tolerate and want to repeat, and data a clinician can use.

## Design rules

- **Errorless, error-tolerant feedback.** Successes are gently reinforced. Near-misses and misses are never punished: no red, no sad sounds, no "try again." The child never sees raw scores.
- **Calm and predictable.** Muted palette, no sudden motion, and an indicator that can never leave the play area ("lost the ball" would read as failure).
- **Tilt to aim, tap to commit.** This splits each attempt into planning and execution, which mirrors real motor planning.
- **Separate child and therapist modes.** The child sees only the game. Setup and data live in a therapist panel.
- **Difficulty is fixed within a round.** The therapist or parent raises it between rounds, so each round's data is comparable.

## How the tilt input works

Turning raw iPad orientation events into a steady, fair cursor was the hardest engineering problem in the project. The pipeline:

1. **Gravity projection.** Instead of reading the `gamma` angle as "left/right" (it jumps and flips near vertical), SYNC computes the direction of gravity in the device's frame. This is smooth at every angle, and compass heading drops out, so the game plays the same whichever way the child faces.
2. **Tilt measured from the child's resting pose.** At calibration, SYNC records how the child is naturally holding the tablet and measures tilt relative to that pose. On a real iPad held near vertical, the naive approach made one axis about 5× more sensitive than the other. The fix makes both axes equally sensitive in any posture: flat, upright, or on a stand.
3. **Gesture calibration** to learn which way is "right" for this child and this grip.
4. **One Euro filter** (Casiez, Roussel & Vogel, CHI 2012). It smooths heavily when the hand is still and lightly when it moves, so the cursor is steady without lagging behind the beat.
5. **Radial dead zone, rescaled, plus a clamp to the play area.** The cursor eases out of center instead of snapping.

**Raw sensor values are logged before any of this processing.** The smoothing is for what the child sees. Analysis gets the untouched signal.

## Data (planned research use)

Every attempt logs the timestamped tilt stream, from which movement smoothness (jerk), endpoint error, path efficiency, timing variability, reaction time, and hit rate can be derived. Data stays on the device, and the therapist exports it. There are no accounts and nothing is sent anywhere. The quantitative analysis is being developed with Dr. Philippe Dixon (McGill Kinesiology).

## Tech

One self-contained HTML/JavaScript file with no framework and no build step, hosted on Netlify. It uses the browser's `DeviceOrientation` API, so iOS asks the user for motion permission at the start of each session.

## Roadmap

- On-device testing with adult hands to tune the filter
- Data export format for analysis
- A native (Expo / React Native) version for steadier sensor rates and no repeated permission prompts
- Ethics approval, then a small pilot

---

Designed and built by **Alyza Abdullah** (McGill Kinesiology), with AI-assisted coding.
