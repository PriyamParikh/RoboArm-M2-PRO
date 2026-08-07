# RoArm-M2-S Controller — User Guide

> **App file:** `app/index.html` · **Robot:** RoArm-M2-S via WiFi HTTP

---

## Table of Contents

1. [Starting the App](#1-starting-the-app)
2. [Connecting to the Arm](#2-connecting-to-the-arm)
3. [Motion Speed Config (Required First)](#3-motion-speed-config)
4. [Arm Visualizer](#4-arm-visualizer)
5. [Servo Control — Moving the Arm](#5-servo-control)
6. [Quick Controls](#6-quick-controls)
7. [Recording Studio — Creating Missions](#7-recording-studio)
8. [Mission Manager — Playback](#8-mission-manager)
9. [Editing Steps (Both Tabs)](#9-editing-steps)
10. [Joint Reference — Limits and Home Positions](#10-joint-reference)
11. [Mission File Format](#11-mission-file-format)
12. [Safety Rules](#12-safety-rules)
13. [Troubleshooting](#13-troubleshooting)

---

## 1. Starting the App

Open `index.html` in your browser (Chrome or Edge recommended).

1. Double-click `index.html` to open it.
2. The app loads — no installation required.
3. Proceed to connecting the arm.

---

## 2. Connecting to the Arm

The **header bar** at the top contains the connection controls.

| Element | Purpose |
|---------|---------|
| IP input field | Arm WiFi IP address (default: `192.168.4.1`) |
| **Connect** button | Starts the connection attempt |
| Status pill | Shows DISCONNECTED / CONNECTING... / CONNECTED |
| Battery indicator | Live voltage once connected |

**Steps:**
1. Power on the arm and connect your PC to its WiFi.
2. Enter the arm IP (leave default `192.168.4.1` for its own hotspot).
3. Click **Connect**.

The app polls the arm every ~1.8 seconds. If 5 consecutive polls fail, connection is considered lost.

---

## 3. Motion Speed Config

> **This must be configured before servo sliders will work.**

Located at the **top of the left panel** (amber border when unconfigured).

### Fields

| Field | Range | Unit | Meaning |
|-------|-------|------|---------|
| Speed | 1–200 | deg/second | How fast each joint moves |
| Accel | 1–100 | deg/s squared | How quickly joints ramp up/down |

### How to configure

**Quick — Use Defaults (10 / 10):**
Click the **Use Defaults (10 / 10)** button. Sets Speed=10, Accel=10 and applies in one click.

**Custom:**
1. Type Speed value (e.g. `20`)
2. Type Accel value (e.g. `20`)
3. Click **Apply**

Once applied, the card border turns green and badge shows your values.

### Speed Reference

| Speed / Accel | Use Case |
|--------------|----------|
| 10 / 10 | First tests — very slow and safe |
| 20 / 20 | Recording missions — moderate |
| 50 / 30 | Repeated playback — fast |
| 100+ | Only when workspace is fully clear |

> **Quick Controls (Home, Torque, LED, E-Stop) always work regardless of config** — they use safe internal defaults so you can always recover the arm.

---

## 4. Arm Visualizer

A **2D side-view diagram** of the arm updates in real time as you:
- Move sliders
- Receive live feedback from the arm

Colors match the servo row colors. The timestamp shows when the last reading arrived.

---

## 5. Servo Control — Moving the Arm

Each joint has its own row showing:
- Colored dot and name
- Live angle display (e.g. `45.0`)
- **+ Take** button (active only during recording)
- Slider with min/max labels

**To move a joint:**
1. Drag the slider to the desired angle.
2. **Release** the slider — the command sends at that moment.
3. The arm moves at your configured Speed and Acceleration.

> Commands send on **release only** — not while dragging. This prevents flooding the arm.

### Joint Limits

| Joint | Min | Max | Home |
|-------|-----|-----|------|
| Base | -180 | +180 | 0 |
| Shoulder | -90 | +90 | 0 |
| Elbow | 0 | +180 | +90 |
| Gripper | 62 (open) | 180 (closed) | 180 |

---

## 6. Quick Controls

Available whenever connected — no motion config needed:

| Button | Action |
|--------|--------|
| Home | All joints return to home position slowly. Sliders reset instantly. |
| Torque ON/OFF | Toggle servo lock. OFF lets you hand-pose the arm. |
| LED ON/OFF | Toggle the onboard LED. |
| E-STOP | Emergency stop. Halts motion and any active mission. |

---

## 7. Recording Studio — Creating Missions

**Workflow:**
```
Start Recording -> move sliders -> click "+ Take" -> repeat -> Stop -> Save
```

### Steps

1. **Name** your mission in the Mission Name field.
2. Click **Start Recording** (button turns red, Take buttons activate).
3. **Move** the arm to a pose using sliders.
4. Click **+ Take** (any joint row).
   - First Take: records the pose.
   - Subsequent Takes: records the elapsed time as a Delay step, then the pose.
5. **Repeat** for all poses.
6. Click **Stop Recording**.
7. Click **Save Mission** — a `.json` file downloads.

### How Delays Work

The elapsed time between two Take clicks becomes the delay step. Move quickly for short delays; pause intentionally if you need the arm to hold a position.

### Step Cards

Each step shows as a card. Cards have:
- Step number
- Type icon (clock for delay, pin for pose)
- Values
- **Edit** button
- **x** delete button

---

## 8. Mission Manager — Playback

1. Click **Choose .json file** and select your mission.
2. The preview box shows all steps.
3. Set **Repetitions** (1 = run once) or enable **Loop**.
4. Click **Play Mission**.

During playback:
- Active step highlights and auto-scrolls.
- Progress bar fills.
- Status shows current step and repetition.

Click **Stop** to abort at any time.

> Mission playback uses `spd` and `acc` values stored **inside the .json file**, not your current Motion Config. This ensures missions are reproducible.

---

## 9. Editing Steps

Works in **both** Recording Studio and Mission Manager.

### How to Edit

1. Click **Edit** on any step card.
2. An inline panel expands below the card.
3. **Pose step** — edit B, S, E, G angle fields.
4. **Delay step** — edit the millisecond value.
5. Click **Save** to apply, or **Discard** to cancel.

Only one step can be open for editing at a time.

### Saving Edited Missions (Mission Manager)

After saving any edits in Mission Manager, a **Save Edited .json** button appears. Click it to download the modified mission. The original file on disk is not changed.

---

## 10. Joint Reference

| Joint | Color | Home | Min | Max | Note |
|-------|-------|------|-----|-----|------|
| Base | Cyan | 0 | -180 | +180 | Full rotation |
| Shoulder | Purple | 0 | -90 | +90 | |
| Elbow | Green | +90 | 0 | +180 | |
| Gripper | Orange | +180 | 62 | +180 | 62=open, 180=closed |

---

## 11. Mission File Format

Missions are plain `.json` files editable in any text editor.

```json
{
  "name": "my_mission",
  "created": "2026-08-07T08:10:00.000Z",
  "app": "RoArm-M2-S Controller",
  "steps": [
    { "T": 122, "b": 0, "s": 0, "e": 90, "h": 180, "spd": 20, "acc": 20 },
    { "T": 111, "cmd": 3000 },
    { "T": 122, "b": 45, "s": 30, "e": 60, "h": 62, "spd": 20, "acc": 20 }
  ]
}
```

### Step Types

| T value | Type | Key fields | Meaning |
|---------|------|------------|---------|
| 122 | Pose | b s e h spd acc | Move all joints simultaneously |
| 111 | Delay | cmd | Wait for cmd milliseconds |

### Delay Calculation Formula

```
For large moves (angle > spd^2 / acc):
  t = angle / spd + spd / acc

For small moves:
  t = 2 * sqrt(angle / acc)

Required delay = MAX(t for each joint that changed) + 300ms buffer
```

**Example at spd:20, acc:20 — moving Base 60 degrees:**
```
t = 60/20 + 20/20 = 3 + 1 = 4.0s  ->  use cmd: 4300
```

---

## 12. Safety Rules

1. **Configure Motion Speed first.** Start with Use Defaults (10/10) for initial tests.
2. **Lift the arm up before long base swings.** Keep Elbow above 90 degrees during transit to avoid hitting the table.
3. **Use E-Stop** if anything moves unexpectedly.
4. **Test missions at slow speed first** before increasing to operating speed.
5. **Home button always uses slow speed** regardless of your config — use it as a safe recovery.
6. **Torque OFF** lets you hand-pose — remember to turn it ON before sending commands.
7. **Never set Speed above 50** until you have verified the pose sequence at slow speed.

---

## 13. Troubleshooting

### Sliders are disabled even though I am connected

Motion Speed Config has not been applied yet. Click **Use Defaults (10 / 10)**.

### Connection drops after a while

Momentary WiFi drop. Reconnect by clicking **Connect** again. Move closer to the arm's hotspot if it happens often.

### Arm moves too fast or slow

Change Speed/Accel in the Motion Config card and click **Apply**. Takes effect immediately for slider movements.

### Mission playback ends before arm finishes moving

Delays in the mission file are too short for the speed. Use the formula above to calculate correct delays, then edit the delay steps using the Edit button.

### Gripper moves unexpectedly when moving another joint

Check mission file steps — verify the `h` field (gripper) is correct in every `T:122` step. All joints are set simultaneously by T:122.

---

*Guide version: 2026-08-07 | App: RoArm-M2-S Controller*
