# Jazzpunk VR Mod

Experimental VR support for **Jazzpunk** using an IPA/IllusionPlugin C# plugin, OpenVR/SteamVR submission, and Unity camera overrides.

> Status: **experimental / work in progress**  
> This is not an official VR port and is not affiliated with Necrophone Games or Adult Swim Games.

## What this does

This mod attempts to make Jazzpunk playable in VR by:

- submitting the game camera to SteamVR through OpenVR;
- using true stereo rendering with OpenVR projection matrices;
- mapping HMD rotation to the in-game camera;
- supporting limited room-scale head/body movement;
- adding controller-based locomotion;
- adding snap turn and recenter controls;
- handling some cutscene or seated cameras by following the active game camera;
- adding an optional VR cursor for scenes where the original 2D cursor/hand overlay is not visible in VR.

The goal is to preserve Jazzpunk's normal gameplay while making it playable inside a headset.

## Current features

- SteamVR/OpenVR output
- DirectX texture submission
- True stereo rendering
- OpenVR projection matrix support
- HMD pitch/yaw/roll tracking
- Positional head tracking
- Configurable room-scale axis mapping
- Xbox/XInput controller support
- Left stick movement
- Right stick snap turning
- Right stick vertical camera pitch
- Controller recenter button
- Optional VR gaze cursor
- Automatic recenter on level load
- Automatic handling for some cutscene/seated cameras
- Manual fallback keyboard controls and diagnostics

## Current limitations

This is still experimental. Known limitations include:

- Some UI, overlays, and 2D cursor elements may not render correctly in VR.
- Some cutscenes may still need tuning.
- Room-scale translation may need per-headset/per-runtime axis configuration.
- Windows support is expected but still needs more testing than Proton/Linux.
- The mod currently targets the original 32-bit Unity build of Jazzpunk.
- This project does not add VR hands or motion-controller interactions.

## Requirements

### Game

- Jazzpunk on Steam
- Original Unity 4.x game build

### Runtime

- SteamVR / OpenVR-compatible runtime
- A VR headset supported by SteamVR

### Controller

Recommended:

- Xbox/XInput-compatible controller

Keyboard fallback exists for diagnostics and manual testing, but controller support is the intended mode.

### Build target

Jazzpunk is a 32-bit Unity game, so the plugin should be compiled as:

```text
Platform target: x86
```

Do not build as x64.

## IPA / IllusionPlugin setup

This mod requires **IPA / IllusionPlugin** support. Simply placing the DLL in the game folder is not enough unless Jazzpunk has already been patched to load IPA plugins.

Expected setup:

1. Install or extract IPA for Unity/IllusionPlugin-compatible games.
2. Patch the Jazzpunk executable with IPA.
3. Make sure the game has the required IPA/IllusionPlugin managed assemblies.
4. Put the mod DLL in the `Plugins` folder created/used by IPA.

After IPA is installed correctly, the game log should contain lines similar to:

```text
Platform assembly: .../Managed/IllusionInjector.dll
Platform assembly: .../Managed/IllusionPlugin.dll
Loading plugins from .../Jazzpunk/Plugins and found 1
Jazzpunk VR Test: ...
[JazzpunkVRTest] LOADED ...
```

If `IllusionInjector.dll` / `IllusionPlugin.dll` appear in the log but `JazzpunkVRTest` does not, the IPA loader is present but the mod DLL is probably in the wrong `Plugins` folder or was built for the wrong architecture.

If the IPA assemblies do not appear in the log at all, IPA has not been installed or the executable has not been patched correctly.

## Installation

Copy the compiled plugin DLL to:

```text
Jazzpunk/Plugins/JazzpunkVRTest.dll
```

Copy the config file to:

```text
Jazzpunk/Plugins/JazzpunkVRTest.ini
```

On Linux/Proton, this is usually:

```text
~/.local/share/Steam/steamapps/common/Jazzpunk/Plugins/JazzpunkVRTest.dll
~/.local/share/Steam/steamapps/common/Jazzpunk/Plugins/JazzpunkVRTest.ini
```

On Windows, this is usually:

```text
C:\Program Files (x86)\Steam\steamapps\common\Jazzpunk\Plugins\JazzpunkVRTest.dll
C:\Program Files (x86)\Steam\steamapps\common\Jazzpunk\Plugins\JazzpunkVRTest.ini
```

If the plugin does not load on Windows, also try placing the DLL and INI in:

```text
Jazzpunk/windows/Plugins/
```

## Steam launch options

Use DirectX 11:

```text
-force-d3d11
```

This is important because the mod submits DirectX textures to OpenVR.

## Verifying that the mod loaded

Open the game log and look for something like:

```text
Loading plugins from .../Jazzpunk/Plugins and found 1
Jazzpunk VR Test: 0.5.1-roomscale-recenter-button
[JazzpunkVRTest] LOADED 0.5.1-roomscale-recenter-button
```

If these lines are missing, the plugin is not loaded and none of the hotkeys will work.

## Basic startup sequence

After launching Jazzpunk:

```text
F4  = initialize OpenVR, compositor, textures, and DirectX diagnostics
F7  = force gameplay VR mode on
R   = recenter HMD
F12 = start/stop the SteamVR submit loop
```

Recommended startup sequence:

```text
F4
F7
R
F12
```

## Main controls

### Keyboard

```text
F4   Initialize OpenVR + compositor + DirectX diagnostics
F7   Force VR gameplay mode on
F12  Toggle submit loop
F1   Reload config
R    Recenter HMD
F11  Dump diagnostics
C    Toggle VR cursor
```

### Controller

Default XInput controls:

```text
Left stick              Move
Right stick left/right  Snap turn
Right stick up/down     Camera pitch offset
RB                      Recenter HMD
R3                      Toggle VR cursor
```

The recenter and cursor buttons are configurable.

## Configuration

Example `JazzpunkVRTest.ini`:

```ini
submit_fps=60
render_width=1280
render_height=720
fov=90
bounds_mode=1

true_stereo=true
submit_stereo_pair=true
use_openvr_projection=true
openvr_projection_api=0
eye_convergence=0.0
eye_separation=0.0

position_tracking=true
position_scale=1.0
invert_position_x=false
invert_position_y=false
invert_position_z=false
position_deadzone=0.006
position_smoothing=0.35

position_reference_mode=1
position_axis_mode=0
position_use_legacy_invert=false
position_x_sign=1
position_y_sign=1
position_z_sign=-1
position_x_scale=1.0
position_y_scale=1.0
position_z_scale=1.25

rotation_smoothing=0.75
pitch_scale=1.0
yaw_scale=1.0
roll_scale=1.0
invert_pitch=true
invert_roll=true

vr_locomotion=true
decouple_body_head_yaw=true
custom_movement=true
head_relative_movement=true
align_body_to_head_for_movement=true

move_speed=1.8
strafe_speed=1.4
move_deadzone=0.18

xinput_enabled=true
xinput_user_index=0
xinput_move_forward_sign=1
xinput_move_strafe_sign=1
xinput_turn_sign=1

xinput_recenter_enabled=true
xinput_recenter_button=512

snap_turn=true
snap_turn_degrees=30
snap_turn_cooldown=0.25
smooth_turn=false
smooth_turn_speed=90

cinema_mode=false
cinema_mono_submit=true

overlay_camera_composite=true
overlay_camera_min_depth_offset=0.01

cutscene_camera_follow=false
cutscene_disable_locomotion=true
cutscene_use_hmd_rotation=false
auto_follow_cutscene_camera=true

auto_recenter_on_attach=true
auto_recenter_on_teleport=true
player_teleport_threshold=1.25
camera_local_teleport_threshold=0.45

cutscene_allow_hmd_look=true
cutscene_hmd_yaw_scale=1.0
cutscene_hmd_pitch_scale=1.0

right_stick_pitch_enabled=true
right_stick_pitch_speed=75
right_stick_pitch_min=-55
right_stick_pitch_max=55

vr_cursor_enabled=true
vr_cursor_visible=false
vr_cursor_hold_mode=false
vr_cursor_toggle_button=128
vr_cursor_distance=1.35
vr_cursor_scale=0.018

max_submit_failures=3
```

## Room-scale axis tuning

If physical body movement is inverted, adjust these:

```ini
position_x_sign=1
position_z_sign=-1
```

Rules:

```text
Physical left/right inverted   -> change position_x_sign
Physical forward/back inverted -> change position_z_sign
```

Keep these disabled unless you specifically need the old behavior:

```ini
invert_position_x=false
invert_position_y=false
invert_position_z=false
position_use_legacy_invert=false
```

## XInput button values

Useful button values:

```text
64    L3 / left stick click
128   R3 / right stick click
256   LB
512   RB
4096  A
8192  B
16384 X
32768 Y
```

Recommended:

```ini
xinput_recenter_button=512
vr_cursor_toggle_button=128
```

This makes:

```text
RB = recenter
R3 = toggle VR cursor
```

## Troubleshooting

### F4 does nothing

The plugin probably did not load. Check the log for:

```text
[JazzpunkVRTest] LOADED
```

If it is missing:

- check that `JazzpunkVRTest.dll` is in `Jazzpunk/Plugins/`;
- make sure the DLL was compiled as x86;
- make sure IPA/IllusionPlugin is installed correctly and that the Jazzpunk executable has been patched by IPA;
- on Windows, try copying the DLL also to `Jazzpunk/windows/Plugins/`.

### The log shows Direct3D 9

Add this Steam launch option:

```text
-force-d3d11
```

The mod expects Direct3D 11 texture submission.

### The view is double or stereo is wrong

Make sure these are enabled:

```ini
true_stereo=true
submit_stereo_pair=true
use_openvr_projection=true
openvr_projection_api=0
eye_convergence=0.0
eye_separation=0.0
```

### Roll feels wrong

The currently tested value is:

```ini
invert_roll=true
```

### Controller recenter keeps triggering

Change the recenter button. For example:

```ini
xinput_recenter_button=512
```

Avoid using a button that the game repeatedly triggers or that you press during normal movement.

## Development notes

This project is built around Unity 4.x and IPA/IllusionPlugin.

Important implementation notes:

- Avoid `System.IO.Path`, `Path.Combine`, or helper methods named `GetPath` in the plugin source if building in the current project setup.
- The plugin should use manual string concatenation with `Application.dataPath` for config paths.
- The current OpenVR path expects 32-bit compatibility because Jazzpunk is a 32-bit game.
- The SteamVR submit loop uses throttling to avoid overwhelming the compositor.
- The plugin intentionally avoids automatically calling `VR_ShutdownInternal()` on quit, because that has caused shutdown instability in Proton/SteamVR testing.

## Project status

This is a prototype VR conversion layer, not a finished commercial-quality VR port.

The most stable current direction is:

- preserve Jazzpunk's normal gameplay camera;
- apply HMD pose for gameplay;
- use OpenVR projection for correct stereo;
- follow special cutscene cameras when needed;
- provide controller-based comfort tools such as snap turn, recenter, and a VR cursor.

## Disclaimer

This is an unofficial fan project.

Jazzpunk is owned by its respective rights holders. This repository does not include game assets, game binaries, or copyrighted Jazzpunk content.
