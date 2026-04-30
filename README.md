# CueSystemTD

Frame-accurate cue/trigger system for TouchDesigner.

CueSystem allows you to trigger Python callbacks based on media playback position (video/audio) with high reliability, even under frame drops.

---

## Features

- Frame-accurate triggering
- Supports video (TOP) and audio (CHOP)
- Fraction / seconds / frames offsets
- Start / current / end anchors
- Callback-based
- Multiple tracks support
- JSON configuration

## Basic Usage

```
op.CueSystem.Add({
    "op": "/project1/moviefilein1",
    "offset": 0.5,
    "offset_type": "fraction",
    "anchor": "start",
    "on_trigger": lambda: print("Triggered!"),
    "once": False
})
```

## How it works

CueSystem checks the media playback position every frame and detects crossing events.

Instead of relying on exact frame matches, it triggers cues when the playback position crosses the target point.  
This ensures reliable triggering even if frames are skipped.

## Examples

Triggers 2 seconds before the end of the video, stops playback, and removes the cue after on_trigger is executed.

```
media = op('/project1/moviefilein1')

def stop_video():
    media.par.play = False

op.CueSystem.Add({
    "op": media,
    "offset": 2.0,
    "offset_type": "seconds",
    "anchor": "end",
    "on_trigger": stop_video,
    "once": True
})
```

Triggers 150 frames from the current playback position. On each loop, the trigger is executed at the same relative position as before.

```
op.CueSystem.Add({
    "op": "/project1/audiofilein1",
    "offset": 150,
    "offset_type": "frames",
    "anchor": "current",
    "on_trigger": "op.Media.AudioTriggered()",
    "once": False
})
```

## API Overview

| Parameter   | Type                        | Description     |
| ----------- | --------------------------- | --------------- |
| op          | str / TOP / CHOP            | Media source    |
| offset      | float                       | Trigger offset  |
| offset_type | fraction / seconds / frames | Offset type     |
| anchor      | start / current / end       | Reference point |
| on_trigger  | callable / str              | Callback        |
| once        | bool                        | Trigger once    |

