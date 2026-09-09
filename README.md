# Volleyball Luminous Field — interactive prototype

A single self-contained file: [index.html](index.html). No build step, no dependencies, no network calls at runtime.

## Run it (with a real camera)

Browsers only grant camera access on a "secure context" (`https://` or `localhost`), so open it through a tiny local server rather than double-clicking the file:

```bash
cd "/Users/taylorlynnhaigler/Documents/Professional/CIID/AI/volleyball-prototype"
python3 -m http.server 8080
```

Then open **http://localhost:8080** in your own Chrome/Safari (not this sandboxed preview — it has no access to your physical webcam) and click **Begin Experience**, which triggers the audio + camera permission prompt.

You can also just double-click `index.html` — most browsers still allow the camera on `file://`, but if the permission prompt is blocked, use the server method above instead.

## How it's built

- **Visuals**: a full-screen `<canvas>` painted every frame — an organic background gradient that flows toward the active player's hue, and a soft pulsating glow (the "ball energy") whose lightness/saturation is driven by ball height and whose pulse rate is driven by ball speed. Contacts, wall-hits, and drops each spawn a transient ripple/flash/collapse effect.
- **Audio**: Web Audio API, three always-blended layers (a calm drone, a filtered-noise "energy bed", and a slow triangle pad) whose gains/filter cutoffs are continuously ramped (`setTargetAtTime`) toward a single `activityLevel` value derived from speed, player count, interaction frequency, and rally duration — so the soundscape morphs rather than switches. Contacts/wall-hits/drops each also fire a short synthesized one-shot (pluck / thud).
- **Camera + CV (best-effort, real)**: the video feed is downsampled to a 96×72 analysis canvas; pixels that are very bright (white) or highly saturated (neon) are averaged into a centroid, tracked frame-to-frame for position/velocity. When "Use camera tracking" is on, that feeds height/speed directly, and simple motion-reversal heuristics attempt to infer contact/wall-hit/drop. This is intentionally simple (per the brief, real detection is hard to make robust) — the **developer panel is the reliable, primary way to demonstrate the full interaction**.
- **Simulated IR gate**: a toggle representing a non-contact boundary sensor concept (e.g. an IR beam at net height) — illustrative only, occasionally nudges "activity" to show how a second sensing modality would combine with the camera.
- **Dev panel** (gear icon, bottom-right): height/speed sliders, player selection (1–4, distinct hues), Start/Stop Rally (an organic auto-demo generator), Contact/Drop/Wall-Hit trigger buttons, and Reset to Idle.

## Notes on the physical constraint

Nothing here assumes any modification to the volleyball itself — no embedded sensor, marker, or electronics. All tracking is either camera-based (color/brightness blob tracking) or a non-contact environmental sensor placed around the space (the IR-gate concept). Where detection can't be made reliable with just a webcam, the dev-panel simulation stands in, as the brief allows.
