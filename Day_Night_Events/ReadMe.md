# Day night Cycles with Events

## Youtube Tutorial
[![Youtube Tutorial](https://img.youtube.com/vi/yjMLMl5KotY/0.jpg)](https://youtube.com/playlist?list=PLYnmZFABLxZzFWegbGAriHwqYU4WFFVno&si=V_Iu8BNT14kQUAvP "Day night Cycles with Events")

## Mod Installation Instructions
[Mod File Installation Guide](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects?tab=readme-ov-file#project-import-instructions)

## Project File
[Download Latest Project File](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/raw/refs/heads/main/Day_Night_Events/DayNightEvents.mod)

---

## Table of Contents
- [Overview](#overview)
- [Script Documentation](#script-documentation)
  - [WorldTimeManagement](#worldtimemanagement)
  - [PlayerHUD](#playerhud)
  - [SwitchComponent](#switchcomponent)
  - [OnSwitchActivated](#onswitchactivated)
  - [CustomPlayerComponent](#customplayercomponent)
- [Changelog](#changelog)

---

## Overview
This project is provided as supplemental resource for the "Day night Cycles with Events" tutorial. 

While playing MapleStory Worlds, you may have come across a scenario where a player attacks and enemy takes damage, interact with a lever and a trapdoor opens, etc. In all cases, something interacts with another, and an event is raised. Today, we’re going to use events to manipulate the time of day! Lets learn!

---

## Script Documentation

---

## WorldTimeManagement

**Purpose**
Controls client-side world-time blending by tweening a background material property and tilemap tint.
Initializes color/material context and updates visual state over time using a linear tween callback.

---

### Properties

| Property        | Type                  | Purpose                                                                                     |
| --------------- | --------------------- | ------------------------------------------------------------------------------------------- |
| `colorHex`      | `string`              | Base hex color used to tint the world visuals (converted to `Color` on init).               |
| `bg`            | `BackgroundComponent` | Background component whose `MaterialId` is targeted for property blending.                  |
| `tileMap`       | `TileMapComponent`    | Tilemap to be tinted between `Color.white` and the configured color.                        |
| `materialProps` | `table`               | Material property bag (e.g., `{ BlendIntensity = number }`) applied via `_MaterialService`. |

---

### Method Summary

| Method           | Execution Scope | Purpose                                                                                   |
| ---------------- | --------------- | ----------------------------------------------------------------------------------------- |
| `OnBeginPlay()`  | ClientOnly      | Initializes color/material context and resets material properties (`BlendIntensity = 0`). |
| `SetTime(time)`  | Client          | Tweens `BlendIntensity` to `time` over 2 seconds (linear), storing the active tween.      |
| `OnTween(value)` | Client          | Tween callback that applies material property changes and updates the tilemap tint.       |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `ClientOnly`

**Purpose**
Initializes runtime color and material identifiers, and seeds material properties.

**Parameters**

* *(none)*

**Behavior**

1. Converts `self.colorHex` to a `Color` via `Color.FromHexCode` and forces alpha to `1`.
2. Caches `self.bg.MaterialId` into a transient field (`self._T.matId`).
3. Initializes `self.materialProps = { ["BlendIntensity"] = 0 }`.
4. Stores the base tint color in `self._T.color` for later interpolation.

</details>

---

<details>
<summary>SetTime</summary>

**Execution Scope**: `Client`

**Purpose**
Starts (or restarts) a linear tween to drive material/tint blending toward the target time value.

**Parameters**

* `time` *(number)* – Target blend value (typically `0..1`) to reach.

**Behavior**

1. If a previous tween exists (`isvalid(self._T.currentTween)`), stops it.
2. Creates a tween from `self.materialProps.BlendIntensity` to `time` over `2.0s` using `EaseType.Linear`, with callback `self.OnTween`.
3. Plays the tween and stores it in `self._T.currentTween`.

</details>

---

<details>
<summary>OnTween</summary>

**Execution Scope**: `Client`

**Purpose**
Applies the current tween value to material properties and the tilemap color.

**Parameters**

* `value` *(number)* – Current interpolated value emitted by the tween.

**Behavior**

1. Updates `self.materialProps.BlendIntensity = value`.
2. Calls `_MaterialService:ChangeMaterialProperty(self._T.matId, self.materialProps)` to apply the blend.
3. Sets `self.tileMap.Color = Color.Lerp(Color.white, self._T.color, value)` to tint the world.

</details>

---

### Related Types & Services

* **BackgroundComponent** – Provides the `MaterialId` used for material property updates.
* **TileMapComponent** – Receives color tint updates based on tween progress.
* **_TweenLogic** – Creates and manages tweens (`MakeTween`, `Play`, `Stop`).
* **EaseType** – Easing modes for tweens (`Linear` used here).
* **_MaterialService** – Applies batched material property changes.
* **Color** – Utilities for hex conversion and color interpolation (`FromHexCode`, `Lerp`).

---

## PlayerHUD

**Purpose**
Provides client-side HUD controls to switch world time between **day** and **night**.
Wires UI button clicks to the `WorldTimeManagement` system to tween environment visuals.

---

### Properties

| Property      | Type     | Purpose                                                       |
| ------------- | -------- | ------------------------------------------------------------- |
| `buttonDay`   | `Entity` | UI button that, when clicked, sets time to daytime (`0.0`).   |
| `buttonNight` | `Entity` | UI button that, when clicked, sets time to nighttime (`1.0`). |

---

### Method Summary

| Method           | Execution Scope | Purpose                                                                    |
| ---------------- | --------------- | -------------------------------------------------------------------------- |
| `OnBeginPlay()`  | ClientOnly      | Subscribes day/night buttons to send time values to the world-time system. |
| `SetTime(value)` | Client          | Forwards a numeric time/blend value to `WorldTimeManagement`.              |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `ClientOnly`

**Purpose**
Initializes HUD button behaviors to control world time.

**Parameters**

* *(none)*

**Behavior**

1. Subscribes `buttonDay` to `ButtonClickEvent`; handler calls `self:SetTime(0.0)`.
2. Subscribes `buttonNight` to `ButtonClickEvent`; handler calls `self:SetTime(1.0)`.

</details>

---

<details>
<summary>SetTime</summary>

**Execution Scope**: `Client`

**Purpose**
Sends the requested time/blend value to the world-time manager.

**Parameters**

* `value` *(number)* – Desired blend target (e.g., `0.0` = day, `1.0` = night).

**Behavior**

1. Calls `_WorldTimeManagement:SetTime(value)`.

</details>

---

### Related Types & Services

* **_WorldTimeManagement** – Applies tweened material and color blending for time-of-day.
* **ButtonClickEvent** – UI event emitted by `buttonDay` / `buttonNight`.
* **Entity** – UI button entities used for event subscriptions.
* **Logic** – Base script type for HUD behavior lifecycle.

---

```markdown
## OnSwitchActivated

**Purpose**  
Custom event used to notify systems when an in-world **switch/toggle** changes state.  
Carries the resulting boolean `state` indicating whether the switch is now active (on) or inactive (off).

---

### Properties

| Property | Type    | Purpose |
|----------|---------|---------|
| `state`  | boolean | `true` when the switch is activated/on; `false` when deactivated/off. |

---

### Method Summary

_No methods defined in this script._

---

### Related Types & Services

- **World/Interaction Logic** – Listens to this event to enable/disable mechanics tied to the switch.  
- **UI/HUD** – May reflect the switch’s current state to the player.  
- **Scripting/Trigger System** – Emits this event whenever a switch is toggled.
```

## SwitchComponent

**Purpose**
Controls a world **switch/toggle** entity that flips visual state and dispatches an activation event when a trigger is entered.
Initializes default state, swaps the sprite between ON/OFF assets, and sends `OnSwitchActivated` to interacting entities.

---

### Properties

| Property         | Type                 | Purpose                                                                                         |
| ---------------- | -------------------- | ----------------------------------------------------------------------------------------------- |
| `triggerOnRUID`  | `string`             | Resource UID for the **ON** sprite shown when the switch is active.                             |
| `triggerOffRUID` | `string`             | Resource UID for the **OFF** sprite shown when the switch is inactive.                          |
| `triggerState`   | `boolean` *(Synced)* | Current switch state; `true` when active/ON, `false` when inactive/OFF. Replicated via `@Sync`. |

---

### Method Summary

| Method                                                    | Execution Scope | Purpose                                                                                    |
| --------------------------------------------------------- | --------------- | ------------------------------------------------------------------------------------------ |
| `OnInitialize()`                                          | ServerOnly      | Sets the initial switch state to active (`true`).                                          |
| `OnBeginPlay()`                                           | ServerOnly      | Applies the initial **ON** sprite to the entity.                                           |
| `ToggleTrigger()`                                         | Server          | Flips `triggerState` and updates the entity’s sprite to match.                             |
| `SendTriggerEvent(entity)`                                | Server          | Emits `OnSwitchActivated` to a target entity with the current `triggerState`.              |
| `HandleTriggerEnterEvent(event)` *(@EventSender("Self"))* | Server          | Responds to `TriggerEnterEvent`: toggles the switch and notifies the entering body entity. |

---

### Methods

<details>
<summary>OnInitialize</summary>

**Execution Scope**: `ServerOnly`

**Purpose**
Establishes the default state for the switch at initialization.

**Parameters**

* *(none)*

**Behavior**

1. Sets `self.triggerState = true` to start in the **ON** state.

</details>

---

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `ServerOnly`

**Purpose**
Sets the visible sprite to match the initial ON state.

**Parameters**

* *(none)*

**Behavior**

1. Assigns `self.Entity.SpriteRendererComponent.SpriteRUID = self.triggerOnRUID`.

</details>

---

<details>
<summary>ToggleTrigger</summary>

**Execution Scope**: `Server`

**Purpose**
Toggles the switch state and updates its sprite accordingly.

**Parameters**

* *(none)*

**Behavior**

1. Computes `state = not self.triggerState`.
2. Selects `triggerRUID = state and self.triggerOnRUID or self.triggerOffRUID`.
3. Sets `self.Entity.SpriteRendererComponent.SpriteRUID = triggerRUID`.
4. Writes `self.triggerState = state`.

</details>

---

<details>
<summary>SendTriggerEvent</summary>

**Execution Scope**: `Server`

**Purpose**
Sends an activation event to a specific entity with the current state.

**Parameters**

* `entity` *(Entity)* – Recipient of the event.

**Behavior**

1. Creates `evt = OnSwitchActivated()`.
2. Sets `evt.state = self.triggerState`.
3. Calls `entity:SendEvent(evt)`.

</details>

---

<details>
<summary>HandleTriggerEnterEvent</summary>

**Execution Scope**: `Server` *(@EventSender("Self"))*

**Purpose**
Handles a `TriggerEnterEvent` from the entity’s own `TriggerComponent` by toggling and notifying the entering body.

**Parameters**

* `event` *(`TriggerEnterEvent`)* – Contains `TriggerBodyEntity`.

**Behavior**

1. Early return on client: `if self:IsClient() then return end`.
2. Reads `TriggerBodyEntity = event.TriggerBodyEntity`.
3. Calls `self:ToggleTrigger()` to flip state and sprite.
4. Calls `self:SendTriggerEvent(TriggerBodyEntity)` to notify the entering entity.

**Native Event Sender Info**

* **Sender**: `TriggerComponent`
* **Space**: Server, Client

</details>

---

### Related Types & Services

* **OnSwitchActivated** – Event carrying the resulting boolean `state`.
* **TriggerEnterEvent** – Native event fired when a body enters the trigger volume.
* **TriggerComponent** – Emits `TriggerEnterEvent` (sender is the same entity via `@EventSender("Self")`).
* **SpriteRendererComponent** – Used to swap the ON/OFF sprite (`SpriteRUID`).

---

## CustomPlayerComponent

**Purpose**
Server-side player component that reacts to switch activation events and toggles the world time between **day** and **night** by delegating to `_WorldTimeManagement`.

---

### Properties

*No properties defined in this script.*

---

### Method Summary

| Method                                                    | Execution Scope | Purpose                                                                         |
| --------------------------------------------------------- | --------------- | ------------------------------------------------------------------------------- |
| `ToggleDay(state)`                                        | Server          | Sets time-of-day based on a boolean: `false` → night (1.0), `true` → day (0.0). |
| `HandleOnSwitchActivated(event)` *(@EventSender("Self"))* | ServerOnly      | Handles `OnSwitchActivated`; forwards `event.state` to `ToggleDay`.             |

---

### Methods

<details>
<summary>ToggleDay</summary>

**Execution Scope**: `Server`

**Purpose**
Switches the global time-of-day via `_WorldTimeManagement` based on the provided state.

**Parameters**

* `state` *(boolean)* – If `false`, set night; if `true`, set day.

**Behavior**

1. If `state == false`, calls `_WorldTimeManagement:SetTime(1.0)` (night).
2. Else, calls `_WorldTimeManagement:SetTime(0.0)` (day).

</details>

---

<details>
<summary>HandleOnSwitchActivated</summary>

**Execution Scope**: `ServerOnly` *(@EventSender("Self"))*

**Purpose**
Responds to an `OnSwitchActivated` event and toggles day/night accordingly.

**Parameters**

* `event` *(`OnSwitchActivated`)* – Provides boolean `state`.

**Behavior**

1. Reads `state = event.state`.
2. Calls `self:ToggleDay(state)` to apply the corresponding time-of-day.

</details>

---

### Related Types & Services

* **OnSwitchActivated** – Event carrying the switch `state` consumed by this component.
* **_WorldTimeManagement** – Applies tweened material/color blending via `SetTime(number)`.
* **SwitchComponent** – Emits `OnSwitchActivated` when a trigger is entered.

---


## Changelog

| Version | Notes |
|---------|-------|
| v1.0 | Initial release with day night cycle demo. |

---


