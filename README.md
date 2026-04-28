# Modulove Screensaver

Animated WebGL logo screensaver. A single self-contained HTML file with a
debug panel for live tuning, four visual-effect knobs, audio reactivity
(microphone or audio file), WebMIDI control over every parameter.

## Files

| File | Purpose |
| --- | --- |
| `floaters.html` | Self-contained app. Both PNGs are embedded as base64. |
| `manifest.json` | PWA manifest (used when the folder is served over HTTP/HTTPS). |
| `logo1.png`, `logo2.png` | Logo textures, square-cropped with transparent margins. |
| `README.md` | This file. |

## Quick start

`floaters.html` works straight from `file://` for quick preview

For PWA install, microphone audio, or audio-file playback (all of which
need a secure context), load from here https://dl.modulove.de/screensaver/


## Keyboard

- **D** — toggle the debug panel.
- **F** — toggle browser fullscreen.

The cursor auto-hides three seconds after the last mouse move while the
debug panel is closed.

## Phone / tablet

- **Long-press** anywhere on the canvas (~600 ms) toggles the debug panel.
- Defaults for `Base size` and `Count per logo` auto-scale at boot:
  ×0.40 below 500 px min-dimension, ×0.65 between 500 and 800 px. Presets
  re-apply the same scale.
- For fullscreen, install as a PWA (Add to Home Screen).
- **Tilt physics**: tick *Use device tilt* in the debug panel and the
  logos will roll toward whichever physical edge is currently lower.
  Rotating the device makes them tumble around as the gravity vector
  swings. Strength is adjustable; iOS asks for permission on the first
  enable. Off by default.

## Debug panel

Sections, top to bottom:

- **Preset** — Clean / VHS / Cosmic / Storm / Disco. Picking one rewrites
  every slider below and respawns floaters. Touching any slider afterward
  drops the dropdown back to *(custom)*.
- **Count per logo** + **logo1 / logo2** checkboxes + **Respawn** —
  instances per logo type (1–8). The checkboxes hide a logo live (its
  floaters keep their physics state but stop drawing and stop colliding,
  so re-enabling restores them in place). On respawn, disabled logos get
  zero instances. Respawn re-randomises positions, velocities, depth
  phases, and per-instance hue offsets for the enabled logos.
- **Base size / Depth amp / Wobble amp** — visual size, depth pulse
  fraction, shader wobble amplitude.
- **Min speed / Max speed / Flow strength / Damping** — motion. Floaters
  drift on a per-instance flow field; speed is clamped each frame.
- **Collide tolerance / Spin on wall bounce / Spin on collision** —
  collision behaviour. Two floaters only collide when their pulsed sizes
  are within tolerance (i.e. on the same depth plane); otherwise they
  pass through.
- **Gravity** — 0–1500 px/s² slider, with quick-set buttons **None / Moon
  (162) / Earth (980)**.
- **Effects** — `Trail persistence` (frame-rate-independent fade in
  seconds), `Chromatic aberration`, `Hue intensity`, `Hue cycle (Hz)`.
- **Audio reactivity** — see below.
- **WebMIDI** — see below.

## Audio reactivity

**Source** dropdown:

- *Off* — no input, no effect.
- *Microphone* — prompts for permission, then drives the visualiser from
  your default input. Not routed to the speakers.
- *Audio file…* — opens a file picker, loops the file through your
  speakers and into the analyser.

Three sensitivity sliders (Bass / Mid / Treble, 0–2) control the
modulation:

- Bass → `flow_strength` + `wobble_amp`
- Mid → `hue_speed`
- Treble → `aberration`

Sensitivities at 0 = no effect even with a source connected. The thin
bars under the sliders show the current band levels.

Microphone and the audio-file path require a secure context (HTTPS or
`localhost`). Use the `python -m http.server` recipe above when running
from a local folder.

## WebMIDI

Chrome / Edge only — Safari and Firefox don't ship Web MIDI by default
and the section reports as much.

To map a MIDI controller:

1. Plug it in (or hot-plug; the device list refreshes automatically).
2. Click **MIDI Learn** — the panel border turns orange.
3. Click the slider you want to control — it gets an orange outline.
4. Twist a knob / move a fader. The mapping is saved.
5. Repeat for more sliders. Click **Stop Learn** when done.

Mappings persist to `localStorage` (`floater_midi` key). Remove individual
mappings with the small `×` next to each row, or wipe everything with
**Clear all**.

The device dropdown lets you accept messages from any input
(`All inputs`) or a specific one. CC values 0–127 are remapped linearly
to each slider's `min..max` range and dispatched as synthetic `input`
events, so all the existing slider hooks (preset reset, value display,
audio path) run as if you'd dragged the slider yourself.
