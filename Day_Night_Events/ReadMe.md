# Day night Cycles with Events

## Youtube Tutorial
[![Youtube Tutorial](https://img.youtube.com/vi/uF-DGHIF3jg/0.jpg)](https://youtu.be/uF-DGHIF3jg?si=KB6xS1eMN80AE9Du "Day night Cycles with Events")

## Mod Installation Instructions
[Mod File Installation Guide](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects?tab=readme-ov-file#mod-file-import-instructions)

## Project File
[Download Latest Project File](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/raw/refs/heads/main/Day_Night_Events/DayNightEvents.mod)

## Table of Contents
- [Overview](#overview)
- [Setup and Installation](#setup-and-installation)
- [Script Documentation](#script-documentation)
  - [WorldTimeManagement](#worldtimemanagement)
  - [PlayerHUD](#playerhud)
  - [OnSwitchActivated](#onswitchactivated)
  - [SwitchComponent](#switchcomponent)
  - [CustomPlayerComponent](#customplayercomponent)
  - [Popupbutton](#popupbutton)
  - [UIPopup](#uipopup)
  - [UIToast](#uitoast)
- [Changelog & Download](#changelog)
---

## Overview
This project is provided as supplemental resource for the "Day night Cycles with Events" tutorial. 

While playing MapleStory Worlds, you may have come across a scenario where a player attacks and enemy takes damage, interact with a lever and a trapdoor opens, etc. In all cases, something interacts with another, and an event is raised. Today, we’re going to use events to manipulate the time of day! Lets learn!

## Setup and Installation
* Ensure `CustomPlayerComponent` is attached to the DefaultPlayer entity so the player can receive `OnSwitchActivated` events.
* Place a switch entity in the map with a `SpriteRendererComponent`, a `TriggerComponent` and `SwitchComponent`; set `triggerOnRUID` / `triggerOffRUID` to the ON and OFF sprites.
* In `WorldTimeManagement`, point `bg` at the map's `BackgroundComponent` (using a material that exposes `BlendIntensity`, such as `NightMat` in `MyDesk/Common/Utils/TimeManagement`) and `tileMap` at the map's `TileMapComponent`; set `colorHex` to the desired night tint.
* In `PlayerHUD`, assign the Day and Night UI buttons to `buttonDay` / `buttonNight`.
* Press Play to run the world:
    * Click the Day / Night HUD buttons to blend the time of day on your own client.
    * Walk into the switch: it flips between ON and OFF and the world switches to day (ON) or night (OFF) for every player.

## Script Documentation

## WorldTimeManagement

**Purpose**  
Controls client-side world-time blending by tweening a background material property and tilemap tint.
Initializes color/material context and updates visual state over time using a linear tween callback.

---

### Properties

| Property        | Type                  | Purpose                                                                                     |
| --------------- | --------------------- | ------------------------------------------------------------------------------------------- |
| `colorHex`      | `string`              | Night tint hex color for the tilemap (converted to `Color` on init).                        |
| `bg`            | `BackgroundComponent` | Background component whose `MaterialId` is targeted for property blending.                  |
| `tileMap`       | `TileMapComponent`    | Tilemap to be tinted between `Color.white` and the configured color.                        |
| `materialProps` | `table`               | Material property bag (e.g., `{ BlendIntensity = number }`) applied via `_MaterialService`. |

---

### Method Summary

| Method           | Execution Scope | Purpose                                                                                   |
| ---------------- | --------------- | ----------------------------------------------------------------------------------------- |
| `OnBeginPlay`    | ClientOnly      | Initializes color/material context and resets material properties (`BlendIntensity = 0`). |
| `SetTime`        | Client          | Tweens `BlendIntensity` to `time` over 2 seconds (linear), storing the active tween.      |
| `OnTween`        | Client/Server   | Tween callback that applies material property changes and updates the tilemap tint.       |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `ClientOnly`  

**Purpose**  
Initializes runtime color and material identifiers, and seeds material properties.

**Behavior**  
1. Converts `self.colorHex` to a `Color` via `Color.FromHexCode`, forces alpha to `1`, and stores it in `self._T.color` for later interpolation.
2. Caches `self.bg.MaterialId` into a transient field (`self._T.matId`).
3. Initializes `self.materialProps = { ["BlendIntensity"] = 0 }`.

</details>

<details>
<summary>SetTime</summary>

**Execution Scope**: `Client`  

**Purpose**  
Starts (or restarts) a linear tween to drive material/tint blending toward the target time value. When called from the server (e.g. by `CustomPlayerComponent`), the call is forwarded to clients so all players see the change.

**Parameters**  
- `time` *(number)* – Target blend value (`0` = day, `1` = night).

**Behavior**  
1. If a previous tween exists (`isvalid(self._T.currentTween)`), stops it.
2. Creates a tween from `self.materialProps.BlendIntensity` to `time` over `2.0s` using `EaseType.Linear`, with callback `self.OnTween`.
3. Plays the tween and stores it in `self._T.currentTween`.

</details>

<details>
<summary>OnTween</summary>

**Execution Scope**: `Client/Server` (invoked by the client-side tween)  

**Purpose**  
Applies the current tween value to material properties and the tilemap color.

**Parameters**  
- `value` *(number)* – Current interpolated value emitted by the tween.

**Behavior**  
1. Updates `self.materialProps.BlendIntensity = value`.
2. Calls `_MaterialService:ChangeMaterialProperty(self._T.matId, self.materialProps)` to apply the blend.
3. Sets `self.tileMap.Color = Color.Lerp(Color.white, self._T.color, value)` to tint the world.

</details>

---

### Related Types & Services
- **BackgroundComponent** – Provides the `MaterialId` used for material property updates.
- **TileMapComponent** – Receives color tint updates based on tween progress.
- **_TweenLogic** – Creates and manages tweens (`MakeTween`, `Play`, `Stop`).
- **EaseType** – Easing modes for tweens (`Linear` used here).
- **_MaterialService** – Applies material property changes.
- **Color** – Utilities for hex conversion and color interpolation (`FromHexCode`, `Lerp`).

---

## PlayerHUD

**Purpose**  
Provides client-side HUD controls to switch world time between **day** and **night**.
Wires UI button clicks to the `WorldTimeManagement` system to tween environment visuals on the local client only.

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
| `OnBeginPlay`    | ClientOnly      | Subscribes day/night buttons to send time values to the world-time system. |
| `SetTime`        | Client/Server   | Forwards a numeric time/blend value to `WorldTimeManagement`.              |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `ClientOnly`  

**Purpose**  
Initializes HUD button behaviors to control world time.

**Behavior**  
1. Subscribes `buttonDay` to `ButtonClickEvent`; handler calls `self:SetTime(0.0)`.
2. Subscribes `buttonNight` to `ButtonClickEvent`; handler calls `self:SetTime(1.0)`.

</details>

<details>
<summary>SetTime</summary>

**Execution Scope**: `Client/Server` (called from the client button handlers)  

**Purpose**  
Sends the requested time/blend value to the world-time manager.

**Parameters**  
- `value` *(number)* – Desired blend target (`0.0` = day, `1.0` = night).

**Behavior**  
1. Calls `_WorldTimeManagement:SetTime(value)`.

</details>

---

### Related Types & Services
- **_WorldTimeManagement** – Applies tweened material and color blending for time-of-day.
- **ButtonClickEvent** – UI event emitted by `buttonDay` / `buttonNight`.
- **Entity** – UI button entities used for event subscriptions.
- **Logic** – Base script type for HUD behavior lifecycle.

---

## OnSwitchActivated

**Purpose**  
Custom event used to notify systems when an in-world **switch/toggle** changes state.  
Carries the resulting boolean `state` indicating whether the switch is now active (on) or inactive (off).

---

### Properties

| Property | Type    | Purpose |
|----------|---------|---------|
| `state`  | boolean | `true` when the switch is activated/on (day); `false` when deactivated/off (night). Defaults to `false`. |

---

### Usage
- Sent by `SwitchComponent:SendTriggerEvent` (server) to the entity that entered the switch's trigger.
- Handled by `CustomPlayerComponent:HandleOnSwitchActivated` on the player, which toggles day/night.

---

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

| Method             | Execution Scope | Purpose                                                                       |
| ------------------ | --------------- | ----------------------------------------------------------------------------- |
| `OnInitialize`     | ServerOnly      | Sets the initial switch state to active (`true`).                             |
| `OnBeginPlay`      | ServerOnly      | Applies the initial **ON** sprite to the entity.                              |
| `ToggleTrigger`    | Server          | Flips `triggerState` and updates the entity’s sprite to match.                |
| `SendTriggerEvent` | Server          | Emits `OnSwitchActivated` to a target entity with the current `triggerState`. |

---

### Methods

<details>
<summary>OnInitialize</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Establishes the default state for the switch at initialization.

**Behavior**  
1. Sets `self.triggerState = true` to start in the **ON** state.

</details>

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Sets the visible sprite to match the initial ON state.

**Behavior**  
1. Assigns `self.Entity.SpriteRendererComponent.SpriteRUID = self.triggerOnRUID`.

</details>

<details>
<summary>ToggleTrigger</summary>

**Execution Scope**: `Server`  

**Purpose**  
Toggles the switch state and updates its sprite accordingly.

**Behavior**  
1. Computes `state = not self.triggerState`.
2. Selects `triggerRUID = state and self.triggerOnRUID or self.triggerOffRUID`.
3. Sets `self.Entity.SpriteRendererComponent.SpriteRUID = triggerRUID`.
4. Writes `self.triggerState = state`.

</details>

<details>
<summary>SendTriggerEvent</summary>

**Execution Scope**: `Server`  

**Purpose**  
Sends an activation event to a specific entity with the current state.

**Parameters**  
- `entity` *(Entity)* – Recipient of the event.

**Behavior**  
1. Creates `evt = OnSwitchActivated()`.
2. Sets `evt.state = self.triggerState`.
3. Calls `entity:SendEvent(evt)`.

</details>

<details>
<summary>HandleTriggerEnterEvent</summary>

**Execution Scope**: `Client/Server` (returns immediately on the client)  
**Event Sender**: `Self` (`TriggerComponent`)  

**Purpose**  
Handles a `TriggerEnterEvent` from the entity’s own `TriggerComponent` by toggling and notifying the entering body.

**Parameters**  
- `event` *(TriggerEnterEvent)* – Contains `TriggerBodyEntity`.

**Behavior**  
1. Reads `TriggerBodyEntity = event.TriggerBodyEntity`.
2. Early return on client: `if self:IsClient() then return end`.
3. Calls `self:ToggleTrigger()` to flip state and sprite.
4. Calls `self:SendTriggerEvent(TriggerBodyEntity)` to notify the entering entity.

</details>

---

### Event Handlers

| Handler                   | Event Type          | Sender / Scope                     | Purpose |
|---------------------------|---------------------|------------------------------------|---------|
| `HandleTriggerEnterEvent` | `TriggerEnterEvent` | Self (`TriggerComponent`) / Server | Toggles the switch and sends `OnSwitchActivated` to the entering entity. |

---

### Related Types & Services
- **OnSwitchActivated** – Event carrying the resulting boolean `state`.
- **TriggerEnterEvent** – Native event fired when a body enters the trigger volume.
- **TriggerComponent** – Emits `TriggerEnterEvent` (sender is the same entity via `@EventSender("Self")`).
- **SpriteRendererComponent** – Used to swap the ON/OFF sprite (`SpriteRUID`).

---

## CustomPlayerComponent

**Purpose**  
Server-side player component that reacts to switch activation events and toggles the world time between **day** and **night** by delegating to `_WorldTimeManagement`.

---

### Properties

This script has no custom properties.

---

### Method Summary

| Method      | Execution Scope | Purpose                                                                         |
| ----------- | --------------- | ------------------------------------------------------------------------------- |
| `ToggleDay` | Server          | Sets time-of-day based on a boolean: `false` → night (1.0), `true` → day (0.0). |

---

### Methods

<details>
<summary>ToggleDay</summary>

**Execution Scope**: `Server`  

**Purpose**  
Switches the global time-of-day via `_WorldTimeManagement` based on the provided state.

**Parameters**  
- `state` *(boolean)* – If `false`, set night; if `true`, set day.

**Behavior**  
1. If `state == false`, calls `_WorldTimeManagement:SetTime(1.0)` (night).
2. Else, calls `_WorldTimeManagement:SetTime(0.0)` (day).
3. Because `SetTime` is a `Client` method called from the server, the change is applied on all clients.

</details>

<details>
<summary>HandleOnSwitchActivated</summary>

**Execution Scope**: `ServerOnly`  
**Event Sender**: `Self`  

**Purpose**  
Responds to an `OnSwitchActivated` event and toggles day/night accordingly.

**Parameters**  
- `event` *(OnSwitchActivated)* – Provides boolean `state`.

**Behavior**  
1. Reads `state = event.state`.
2. Calls `self:ToggleDay(state)` to apply the corresponding time-of-day.

</details>

---

### Event Handlers

| Handler                   | Event Type          | Sender / Scope                          | Purpose |
|---------------------------|---------------------|-----------------------------------------|---------|
| `HandleOnSwitchActivated` | `OnSwitchActivated` | Self (sent by `SwitchComponent`) / ServerOnly | Forwards `event.state` to `ToggleDay`. |

---

### Related Types & Services
- **OnSwitchActivated** – Event carrying the switch `state` consumed by this component.
- **_WorldTimeManagement** – Applies tweened material/color blending via `SetTime(number)`.
- **SwitchComponent** – Emits `OnSwitchActivated` when a trigger is entered.

---

## Popupbutton

**Purpose**  
Client-side UI helper that opens one of several popup panels under `/ui/PopupGroup` when its menu button is clicked, and closes the panel when that panel's exit (or OK/Cancel) button is clicked. It is part of the bundled UI template and is not used by the day/night logic.

---

### Properties

This script has no custom properties.

---

### Method Summary

This script has no methods; all logic lives in its event handlers.

---

### Event Handlers

| Handler                  | Event Type         | Sender / Scope                     | Purpose |
|--------------------------|--------------------|------------------------------------|---------|
| `HandleButtonClickEvent`  | `ButtonClickEvent` | Entity button named `"1"` / ClientOnly | Opens `/ui/PopupGroup/AchieveUI`; its `TitlePanel/ExitButton` closes it. |
| `HandleButtonClickEvent2` | `ButtonClickEvent` | Entity button named `"2"` / ClientOnly | Opens `/ui/PopupGroup/MonsterDexUI`; its `TitlePanel/ExitButton` closes it. |
| `HandleButtonClickEvent3` | `ButtonClickEvent` | Entity button named `"3"` / ClientOnly | Opens `/ui/PopupGroup/QuestUI`; its `QuestUITitle/ExitButton` closes it. |
| `HandleButtonClickEvent4` | `ButtonClickEvent` | Entity button named `"4"` / ClientOnly | Opens `/ui/PopupGroup/InventoryUI`; its `TopPanel/ExitButton` closes it. |
| `HandleButtonClickEvent5` | `ButtonClickEvent` | Entity button named `"5"` / ClientOnly | Opens `/ui/PopupGroup/ShopUI`; its `TopPanel/ExitButton` closes it. |
| `HandleButtonClickEvent6` | `ButtonClickEvent` | Entity button named `"6"` / ClientOnly | Opens `/ui/PopupGroup/BasicPopup`; `PopupBtnOK` and `PopupBtnCancel` close it. |

Each handler returns early unless the clicked entity's `Name` matches its button number, enables the panel, then connects a `ButtonClickEvent` on the close button(s) that sets the panel's `Enable` to `false`.

---

### Related Types & Services
- **ButtonClickEvent** – UI click event that drives each handler.
- **_EntityService** – Looks up popup panels and close buttons by path (`GetEntityByPath`).

---

## UIPopup

**Purpose**  
Manages popup windows in the UI with text messages, OK/Cancel buttons, and animated scale/fade transitions. Supports callbacks for user responses.

---

### Properties

| Property         | Type            | Purpose |
|------------------|-----------------|---------|
| `message`        | TextComponent   | UI text element to display the popup message. |
| `btnOk`          | ButtonComponent | OK button component. |
| `btnCancel`      | ButtonComponent | Cancel button component. |
| `popupGroup`     | Entity          | Parent group containing the popup for enabling/disabling and fading (`CanvasGroupComponent`). |
| `popup`          | Entity          | Popup entity whose `UITransformComponent` is scaled. |
| `onOk`           | any             | Callback executed when OK is clicked. |
| `onCancel`       | any             | Callback executed when Cancel is clicked. |
| `duration`       | number          | Duration of the open/close tween in seconds (default `0.15`). |
| `from`           | number          | Initial scale for the tween (default `0.5`). |
| `to`             | number          | Target scale for the tween (default `1`). |
| `isOpen`         | boolean         | Whether the popup is currently open. |
| `isTweenPlaying` | boolean         | Whether a tween animation is in progress. |
| `tweenEventId`   | number          | Timer ID for the active tween. |
| `okHandler`      | any             | Event connection handler for OK button. |
| `cancelHandler`  | any             | Event connection handler for Cancel button. |

---

### Method Summary

| Method          | Execution Scope | Purpose |
|-----------------|-----------------|---------|
| `Open`          | Client/Server   | Opens the popup with a message and optional OK/Cancel callbacks. |
| `OnClickOk`     | Client/Server   | Handles OK button click, executes callback, and closes the popup. |
| `OnClickCancel` | Client/Server   | Handles Cancel button click, executes callback, and closes the popup. |
| `Close`         | Client/Server   | Disconnects button events and plays the closing tween. |
| `StartTween`    | Client/Server   | Animates the popup scale and fade for opening or closing. |

---

### Methods

<details>
<summary>Open</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Displays the popup with the given message and sets up OK/Cancel callbacks.

**Parameters**  
- `message` *(string)* – The message to display in the popup.  
- `onOk` *(any)* – Optional callback executed when OK is clicked.  
- `onCancel` *(any)* – Optional callback executed when Cancel is clicked.

**Behavior**  
1. Checks if the popup is already open; exits if true. Otherwise sets `isOpen` to `true`.  
2. Enables the popup group and sets the message text.  
3. Stores callbacks in `onOk` and `onCancel`.  
4. Connects OK and Cancel buttons to `OnClickOk` and `OnClickCancel`.  
5. Starts the opening tween animation.

</details>

<details>
<summary>OnClickOk</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Handles OK button click and triggers the assigned callback.

**Behavior**  
1. Ignores click if a tween is playing.  
2. Executes the `onOk` callback if defined and sets it to `nil`.  
3. Calls `Close()`.

</details>

<details>
<summary>OnClickCancel</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Handles Cancel button click and triggers the assigned callback.

**Behavior**  
1. Ignores click if a tween is playing.  
2. Executes the `onCancel` callback if defined and sets it to `nil`.  
3. Calls `Close()`.

</details>

<details>
<summary>Close</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Closes the popup and removes button event connections.

**Behavior**  
1. Disconnects OK and Cancel button events.  
2. Starts the closing tween animation (which disables the group and clears `isOpen` when finished).

</details>

<details>
<summary>StartTween</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Animates the popup's scale and transparency when opening or closing.

**Parameters**  
- `open` *(boolean)* – `true` for opening, `false` for closing.

**Behavior**  
1. Retrieves the popup transform and popup group canvas.  
2. Initializes scale (and alpha `0` when opening) based on `from` and `to` values.  
3. Sets `isTweenPlaying` to `true`.  
4. Uses `_TimerService:SetTimerRepeat` (every 1/60 s) to update scale with `_TweenLogic:Ease` (`SineEaseIn`) and alpha linearly, running time backwards when closing.  
5. Ends the tween when duration is reached, disables the popup group and clears `isOpen` if closing, and clears the timer.

</details>

---

### Related Types & Services
- **Logic** – Base class for logic scripts.  
- **TextComponent** – Handles displaying text in UI.  
- **ButtonComponent** – Handles button input and click events.  
- **_TweenLogic** – Provides easing functions for animations.  
- **_TimerService** – Executes timed and repeating callbacks.  
- **_UtilLogic** – Provides elapsed time in seconds.  
- **Entity** – Represents UI elements and groups.

---

## UIToast

**Purpose**  
Displays temporary toast messages in the UI with fade-in and fade-out animations. Automatically handles positioning and alpha transitions.

---

### Properties

| Property         | Type          | Purpose |
|------------------|---------------|---------|
| `message`        | TextComponent | Text component to display the toast message. |
| `toastGroup`     | Entity        | Parent entity for enabling/disabling the toast. |
| `duration`       | number        | Duration in seconds the toast stays visible before fading out (default `2`). |
| `tweenDuration`  | number        | Duration in seconds for the fade-in and fade-out animation (default `0.1`). |
| `tweenEventId`   | number        | Timer ID for the active tween animation. |
| `isTweenPlaying` | boolean       | Declared but not used by the current code. |
| `inited`         | boolean       | Indicates whether `offset` has been initialized. |
| `offset`         | number        | Vertical offset used for the slide animation (negated anchored Y of the message). |

---

### Method Summary

| Method        | Execution Scope | Purpose |
|---------------|-----------------|---------|
| `ShowMessage` | Client          | Shows a toast message with automatic fade-in and fade-out. |
| `StartTween`  | Client/Server   | Animates the toast's alpha and vertical position. |

---

### Methods

<details>
<summary>ShowMessage</summary>

**Execution Scope**: `Client`  

**Purpose**  
Displays a toast message with fade-in and fade-out animation.

**Parameters**  
- `message` *(string)* – The message text to show in the toast.

**Behavior**  
1. Initializes `offset` based on the text's anchored Y position if not already initialized.  
2. Sets the text component to the provided `message`.  
3. Calls `StartTween()` to animate the toast.

</details>

<details>
<summary>StartTween</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Animates the toast message by controlling its alpha transparency and vertical position.

**Behavior**  
1. Retrieves the `CanvasGroupComponent` and `UITransformComponent` from the message entity.  
2. Clears any existing tween timer if active (keeping the current alpha); otherwise starts from alpha `0`.  
3. Enables the toast group entity.  
4. Uses `_TimerService:SetTimerRepeat` (every 1/60 s) to update the toast:  
   - Increases alpha during the visible period, decreases it after `duration`.  
   - Adjusts vertical position using `_TweenLogic:Ease` based on alpha.  
5. Disables the toast group and clears the timer when `duration + tweenDuration` has elapsed.

</details>

---

### Related Types & Services
- **Logic** – Base class for logic scripts.  
- **TextComponent** – UI component for displaying text.  
- **Entity** – Represents UI elements.  
- **_TweenLogic** – Provides easing functions for animations.  
- **_TimerService** – Handles repeating or delayed callbacks.  
- **_UtilLogic** – Provides elapsed time in seconds.

---

## Changelog
| Version | Changelog | Download |
|---------|-------------|-------------|
| V1_0 | Initial release with day night cycle demo. | [Download](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/raw/refs/heads/main/Day_Night_Events/DayNightEvents.mod) |
