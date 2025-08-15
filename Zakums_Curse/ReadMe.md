# Zakum's Curse 

## Youtube Tutorial
[![Youtube Tutorial](http://img.youtube.com/vi/7hNKAn9JmBQ/0.jpg)](https://youtu.be/7hNKAn9JmBQ?feature=shared "MapleStory Worlds Tutorial: Pro Mode Chapter 8 - Bringing Everything Together")

## Mod Installation Instructions
[Mod File Installation Guide](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects?tab=readme-ov-file#project-import-instructions)

## Project File
[Download Latest Project File](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/raw/refs/heads/main/Zakums_Curse/Zakums_Curse_V1_1.mod)

## Table of Contents
- [Overview](#overview)
- [Setup and Installation](#setup-and-installation)
- [Monster](#monster)
- [MonsterAttack](#monsterattack)
- [PlayerAttack](#playerattack)
- [PlayerHit](#playerhit)
- [UIPopup](#uipopup)
- [UIToast](#uitoast)
- [GameManager](#gamemanager)
- [GameEndEvent](#gameendevent)
- [TimerEvent](#timerevent)
- [ZakumChangedEvent](#zakumchangedevent)
- [RespawnComponent](#respawncomponent)
- [ZakumPlayerComponent](#zakumplayercomponent)
- [SoundManager](#soundmanager)
- [SFXList](#sfxlist)
- [ZakumUIComponent](#zakumuicomponent)
- [Changelog & Download](#changelog)
---

## Overview 
Time for the ultimate test—let’s build a world together from start to finish! 
In this video, we’ll put everything we’ve learned so far into practice. There’s a lot to cover, so don’t worry if it doesn’t all click right away! Think of this as your rite of passage—follow along, take it step by step, and soon you’ll be building your own worlds with confidence!

## Setup and Installation
* Ensure the ZakumPlayerComponent is attached to the DefaultPlayer entity.  
* Press Play to run the world
    * A second player must be added to trigger the gameplay. 
    * Add a second client, and move both to the portal to teleport to the game area. 
    * After the game begins, collide with the player with the zakum helmet to steal the helmet. 
    * Notice the zakum helmet owner has a slight speed boost. 
    * Onwer of the zakum helmet must run away and retain the helmet when the countdown timer ends. 

## Monster

**Purpose**  
Represents a monster entity in the game with health, death, and respawn mechanics. Handles taking damage and state changes, including optional automatic respawn.

---

### Properties

| Property         | Type    | Purpose |
|-----------------|---------|---------|
| `MaxHp`          | number  | Maximum health points of the monster. Synchronized across client/server. |
| `Hp`             | number  | Current health points. Synchronized across client/server. |
| `RespawnOn`      | boolean | Whether the monster should automatically respawn after death. Synchronized. |
| `IsDead`         | boolean | Tracks if the monster is dead. Synchronized and hidden from inspector. |
| `RespawnDelay`   | number  | Time in seconds before the monster respawns if `RespawnOn` is true. Synchronized. |
| `DestroyDelay`   | number  | Delay before the monster entity is hidden or destroyed after death. Synchronized. |

---

### Method Summary

| Method          | Execution Scope | Purpose |
|-----------------|----------------|---------|
| `OnBeginPlay`   | Client/Server  | Initializes monster HP to `MaxHp` when the entity spawns. |
| `Dead`          | ServerOnly     | Handles monster death: changes state, hides entity, and optionally destroys it. |
| `Respawn`       | ServerOnly     | Resets the monster to alive state and restores HP and visibility. |
| `HandleHitEvent`| ServerOnly     | Processes incoming hits, reduces HP, triggers death, and schedules respawn if enabled. |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Initializes the monster’s health when the game begins.

**Behavior**  
- Sets `Hp` to `MaxHp`.

</details>

<details>
<summary>Dead</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Handles the monster's death sequence.

**Behavior**  
1. Sets `IsDead` to `true`.  
2. Changes the entity’s state to `"DEAD"` if a `StateComponent` exists.  
3. Schedules a delayed function (`DestroyDelay`) to hide or disable the entity.  
4. If `RespawnOn` is `false`, the entity is destroyed after the delay.

</details>

<details>
<summary>Respawn</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Respawns the monster after death.

**Behavior**  
1. Sets `IsDead` to `false`.  
2. Makes the entity visible and enabled.  
3. Resets `Hp` to `MaxHp`.  
4. Changes the entity’s state to `"IDLE"` if a `StateComponent` exists.

</details>

<details>
<summary>HandleHitEvent</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Processes incoming hit events and applies damage to the monster.

**Parameters**  
- `event` *(HitEvent)* – Contains damage information and other optional metadata.  

**Behavior**  
1. Subtracts `event.TotalDamage` from `Hp`.  
2. Checks if the monster is still alive; returns early if `Hp` remains above 0 or it was already dead.  
3. Calls `Dead()` if HP drops to 0 or below.  
4. If `RespawnOn` is true, schedules `Respawn()` to be called after `RespawnDelay`.  

**Event Metadata**  
- **Event Sender**: `Self`  

</details>

---

### Related Types & Services
- **Component** – Base class for Unity-style entity components.  
- **_TimerService** – Schedules delayed and repeated callbacks.  
- **StateComponent** – Manages the entity's current state.  
- **HitEvent** – Event triggered when the monster receives damage.  
- **Entity** – Represents the monster in the game world.  
---

## MonsterAttack

**Purpose**  
Handles automatic melee attacks for monster entities based on their sprite size, creating a hitbox and repeatedly attacking nearby player entities.

---

### Properties

| Property         | Type      | Purpose |
|-----------------|-----------|---------|
| `AttackInterval` | number    | Time interval between repeated attacks (seconds). Must be ≥ 0. |
| `Shape`          | any       | The hitbox shape used for attacks. Hidden from inspector. |
| `SpriteSize`     | Vector2   | Size of the monster's sprite, used to define attack area. Hidden from inspector. |
| `PositionOffset` | Vector2   | Offset from the monster’s position to align the attack hitbox. Hidden from inspector. |

---

### Method Summary

| Method         | Execution Scope | Purpose |
|----------------|----------------|---------|
| `OnBeginPlay`  | ServerOnly     | Initializes attack shape and starts repeated attacks based on sprite size. |
| `AttackNear`   | Client/Server  | Updates attack hitbox position and executes attack on nearby players. |
| `IsAttackTarget` | Client/Server | Checks if a given entity is a valid player target for the monster attack. |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Initializes the monster’s attack system when the game begins.

**Behavior**  
1. Checks if the entity has a valid `Monster` component; returns if missing.  
2. Initializes `Shape` as a default `BoxShape`.  
3. Preloads the monster’s sprite and retrieves the first frame to calculate `SpriteSize` and `PositionOffset`.  
4. Starts a repeated timer that calls `AttackNear` every `AttackInterval` seconds if the monster is alive.

</details>

<details>
<summary>AttackNear</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Performs a melee attack on nearby players using a dynamically calculated hitbox.

**Behavior**  
1. Retrieves the entity’s `TransformComponent`.  
2. Calculates the world position, scale, rotation, and offset for the attack hitbox based on the sprite size.  
3. Updates `Shape` with calculated size, position, and rotation.  
4. Calls `AttackFast` with the hitbox and `CollisionGroups.Player` to deal damage.

</details>

<details>
<summary>IsAttackTarget</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Determines whether a given entity is a valid target for the monster’s attack.

**Parameters**  
- `defender` *(Entity)* – Potential target entity.  
- `attackInfo` *(string)* – Optional attack metadata.  

**Behavior**  
- Returns `false` if the entity does not have a `PlayerComponent`.  
- Otherwise, defers to the base `AttackComponent:IsAttackTarget` logic.

</details>

---

### Related Types & Services
- **AttackComponent** – Base class for handling attack logic and hit detection.  
- **_TimerService** – Schedules repeated or delayed callbacks.  
- **BoxShape** – Represents a rectangular hitbox with size, position, and rotation.  
- **CollisionGroups** – Defines groups for collision detection (`Player` in this case).  
- **TransformComponent** – Provides world position, rotation, and scale of the entity.  
- **_ResourceService** – Loads sprites and animation clips.  
- **Entity** – The monster entity executing attacks.  
---

## PlayerAttack

**Purpose**  
Handles a basic player attack system, defining a hitbox and executing attacks on nearby player targets. Supports critical hits and fixed damage calculation.

---

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `Shape`  | any  | Hitbox shape used for attacks. Hidden from inspector. |

---

### Method Summary

| Method               | Execution Scope  | Purpose |
|---------------------|-----------------|---------|
| `OnBeginPlay`        | ServerOnly      | Initializes the attack hitbox shape. |
| `AttackNormal`       | ServerOnly      | Performs a basic attack using the hitbox and applies damage to targets. |
| `CalcDamage`         | Client/Server   | Calculates damage dealt to a defender. Returns a fixed value. |
| `CalcCritical`       | Client/Server   | Determines if an attack is critical using a 30% chance. |
| `GetCriticalDamageRate` | Client/Server | Returns the multiplier for critical hits (2×). |
| `HandlePlayerActionEvent` | ServerOnly  | Listens for player action events and triggers attacks when appropriate. |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Initializes the player’s attack hitbox when the entity is spawned.

**Behavior**  
- Creates a `BoxShape` with position `(0,0)`, size `(1,1)`, and rotation `0`.  
- Stores the shape in `self.Shape`.

</details>

<details>
<summary>AttackNormal</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Executes a basic attack in front of the player.

**Behavior**  
1. Retrieves `PlayerControllerComponent` and `TransformComponent`.  
2. Calculates `attackOffset` in front of the player based on look direction.  
3. Updates `self.Shape.Position` to the calculated offset.  
4. Calls `AttackFast` with the hitbox and `CollisionGroups.Player`.

</details>

<details>
<summary>CalcDamage</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Calculates damage dealt by the attack.

**Behavior**  
- Returns a fixed damage value of `50`.

</details>

<details>
<summary>CalcCritical</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Determines whether an attack is critical.

**Behavior**  
- Uses a random double to generate a 30% chance of critical hit.

</details>

<details>
<summary>GetCriticalDamageRate</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Provides the multiplier applied to critical hits.

**Behavior**  
- Returns a fixed value of `2`.

</details>

<details>
<summary>HandlePlayerActionEvent</summary>

**Execution Scope**: `ServerOnly`  
**Event Sender**: `Self`  

**Purpose**  
Listens for player action events and triggers the normal attack.

**Parameters**  
- `event.ActionName` – Name of the player action.

**Behavior**  
- If `ActionName` equals `"Attack"`, calls `AttackNormal`.

</details>

---

### Related Types & Services
- **AttackComponent** – Base class for handling attacks and hit detection.  
- **BoxShape** – Represents rectangular attack hitboxes.  
- **CollisionGroups** – Defines collision layers (`Player` in this case).  
- **_UtilLogic** – Provides utility functions, including random number generation.  
- **PlayerControllerComponent** – Provides player orientation and control data.  
- **TransformComponent** – Provides world position and scale of the player entity.  
- **Entity** – The player entity executing attacks.  
- **_TimerService** – Can be used for scheduled repeated attacks (not used here).  
---

## PlayerHit

**Purpose**  
Handles the player's hit detection and damage immunity timing, ensuring the player cannot be hit repeatedly within a short interval.

---

### Properties

| Property           | Type    | Purpose |
|-------------------|---------|---------|
| `ImmuneCooldown`  | number  | Minimum time (in seconds) between consecutive hits. |
| `LastHitTime`     | number  | Timestamp of the last time the player was hit. Hidden from inspector. |

---

### Method Summary

| Method           | Execution Scope  | Purpose |
|-----------------|-----------------|---------|
| `IsHitTarget`    | Client/Server   | Determines whether the player can currently be hit based on cooldown. |

---

### Methods

<details>
<summary>IsHitTarget</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Checks if the player is eligible to receive damage at the current moment.

**Parameters**  
- `attackInfo` *(string)* – Optional information about the attack. Not used in this method.

**Behavior**  
1. Gets the current time from `_UtilLogic.ElapsedSeconds`.  
2. Compares `currentTime` with `LastHitTime + ImmuneCooldown`.  
3. If the cooldown period has passed:  
   - Updates `LastHitTime` to the current time.  
   - Returns `true` indicating the player can be hit.  
4. Otherwise, returns `false`.

</details>

---

### Related Types & Services
- **HitComponent** – Base class for entities that can be hit.  
- **_UtilLogic** – Provides utility functions, including the elapsed time in seconds.  
- **Entity** – The player entity receiving damage.  
---

## UIPopup

**Purpose**  
Manages popup windows in the UI with text messages, OK/Cancel buttons, and animated scale/fade transitions. Supports callbacks for user responses.

---

### Properties

| Property           | Type            | Purpose |
|-------------------|----------------|---------|
| `message`         | TextComponent   | UI text element to display the popup message. |
| `btnOk`           | ButtonComponent | OK button component. |
| `btnCancel`       | ButtonComponent | Cancel button component. |
| `popupGroup`      | Entity          | Parent group containing the popup for enabling/disabling. |
| `popup`           | Entity          | Popup entity with transform and UI components. |
| `onOk`            | any             | Callback executed when OK is clicked. |
| `onCancel`        | any             | Callback executed when Cancel is clicked. |
| `duration`        | number          | Duration of the open/close tween in seconds. |
| `from`            | number          | Initial scale for the tween. |
| `to`              | number          | Target scale for the tween. |
| `isOpen`          | boolean         | Whether the popup is currently open. |
| `isTweenPlaying`  | boolean         | Whether a tween animation is in progress. |
| `tweenEventId`    | number          | Timer ID for the active tween. |
| `okHandler`       | any             | Event connection handler for OK button. |
| `cancelHandler`   | any             | Event connection handler for Cancel button. |

---

### Method Summary

| Method         | Execution Scope  | Purpose |
|----------------|-----------------|---------|
| `Open`         | Client/Server   | Opens the popup with a message and optional OK/Cancel callbacks. |
| `Close`        | Client/Server   | Closes the popup and disconnects button events. |
| `OnClickOk`    | Client/Server   | Handles OK button click, executes callback, and closes the popup. |
| `OnClickCancel`| Client/Server   | Handles Cancel button click, executes callback, and closes the popup. |
| `StartTween`   | Client/Server   | Animates the popup scale and fade for opening or closing. |

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
1. Checks if the popup is already open; exits if true.  
2. Enables the popup group and sets the message text.  
3. Stores callbacks in `onOk` and `onCancel`.  
4. Connects OK and Cancel buttons to `OnClickOk` and `OnClickCancel`.  
5. Starts the opening tween animation.

</details>

<details>
<summary>Close</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Closes the popup and removes button event connections.

**Behavior**  
1. Disconnects OK and Cancel button events.  
2. Starts the closing tween animation.

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
<summary>StartTween</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Animates the popup's scale and transparency when opening or closing.

**Parameters**  
- `open` *(boolean)* – `true` for opening, `false` for closing.

**Behavior**  
1. Retrieves the popup transform and popup group canvas.  
2. Initializes scale and alpha based on `from` and `to` values.  
3. Sets `isTweenPlaying` to `true`.  
4. Uses `_TimerService:SetTimerRepeat` to repeatedly update scale and alpha each frame using `_TweenLogic:Ease`.  
5. Ends the tween when duration is reached, disables popup if closing, and clears the timer.

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

| Property          | Type            | Purpose |
|------------------|----------------|---------|
| `message`        | TextComponent   | Text component to display the toast message. |
| `toastGroup`     | Entity          | Parent entity for enabling/disabling the toast. |
| `duration`       | number          | Duration in seconds the toast stays visible before fading out. |
| `tweenDuration`  | number          | Duration in seconds for the fade-in and fade-out animation. |
| `tweenEventId`   | number          | Timer ID for the active tween animation. |
| `isTweenPlaying` | boolean         | Whether a tween animation is currently running. |
| `inited`         | boolean         | Indicates whether the toast has been initialized. |
| `offset`         | number          | Vertical offset used for tween animation. |

---

### Method Summary

| Method         | Execution Scope | Purpose |
|----------------|----------------|---------|
| `ShowMessage`  | Client         | Shows a toast message with automatic fade-in and fade-out. |
| `StartTween`   | Client/Server  | Animates the toast's alpha and vertical position. |

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
2. Clears any existing tween timer if active.  
3. Enables the toast group entity.  
4. Uses `_TimerService:SetTimerRepeat` to repeatedly update the toast each frame:  
   - Increases alpha during fade-in, decreases alpha during fade-out.  
   - Adjusts vertical position using `_TweenLogic:Ease` based on alpha.  
5. Disables the toast group and clears the timer when the total duration (visible + fade-out) is complete.

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

## GameManager

**Purpose**  
Handles core game flow, including player management, Zakum selection, teleportation, countdowns, and triggering game start and end events. Manages both server-side logic and client notifications.

---

### Properties

| Property             | Type                    | Purpose |
|---------------------|------------------------|---------|
| `currentZakum`       | ZakumPlayerComponent    | Tracks the current player designated as Zakum. |
| `startingAreaId`     | string                  | ID of the starting area for players. |
| `playerCount`        | number                  | Current number of players in the game. |
| `hasGameStarted`     | boolean                 | Indicates if the game has started. |
| `gameStartTimer`     | number                  | Timer ID for the game start countdown. |
| `playersInGame`      | table                   | Maps user IDs to player entities currently in the game. |
| `spawnAreaId`        | string                  | ID of the spawn area where players are sent after the game ends. |

---

### Method Summary

| Method                     | Execution Scope | Purpose |
|-----------------------------|----------------|---------|
| `ChangeZakum`               | Server         | Changes the current Zakum player to the specified user ID. |
| `SetRandomZakum`            | Server         | Randomly selects a new Zakum from players in the game. |
| `TeleportToStartingArea`    | Server         | Teleports a player to the starting area and adds them to the game. |
| `CheckPlayerCount`          | Server         | Checks if enough players are present to start the game and begins countdown. |
| `BeginCountdown`            | Server         | Starts a countdown timer and executes a callback when complete. |
| `GameStart`                 | Client/Server  | Starts the game, selects a Zakum, and sets the end-game countdown. |
| `GameEnd`                   | Client/Server  | Ends the game, resets players, and notifies clients of the winner. |
| `MovePlayersToSpawn`        | Client/Server  | Moves all players to the spawn area and resets the game state. |
| `CountEvent`                | Client         | Sends a countdown timer event to clients. |
| `GameStartEvent`            | Client         | Sends a game start event to clients. |
| `GameEndEvent`              | Client         | Sends a game end event with the winner's user ID to clients. |
| `OnBeginPlay`               | ServerOnly     | Plays background music when the game manager is initialized. |
| `AddPlayer`                 | Server         | Adds a player to the game and increments the player count. |
| `RemovePlayer`              | Server         | Removes a player from the game, adjusts player count, and handles Zakum reassignment if needed. |

---

### Methods

<details>
<summary>ChangeZakum</summary>

**Execution Scope**: `Server`  

**Purpose**  
Changes the current Zakum player.

**Parameters**  
- `userId` *(string)* – The user ID of the player to become Zakum.

**Behavior**  
1. Unsets the previous Zakum if one exists.  
2. Sets `currentZakum` to the new player's `ZakumPlayerComponent`.  
3. Calls `SetZakum()` on the new Zakum player.

</details>

<details>
<summary>SetRandomZakum</summary>

**Execution Scope**: `Server`  

**Purpose**  
Randomly assigns a Zakum among the current players.

**Behavior**  
1. Returns early if no players are present.  
2. Picks a random player from `playersInGame`.  
3. Calls `ChangeZakum` with the selected player's user ID.

</details>

<details>
<summary>TeleportToStartingArea</summary>

**Execution Scope**: `Server`  

**Purpose**  
Teleports a player to the starting area and adds them to the game.

**Parameters**  
- `userId` *(string)* – The user ID of the player to teleport.

**Behavior**  
1. Returns if the game has already started.  
2. Plays a portal sound effect at the player's position.  
3. Moves the player to the starting area entity.  
4. Adds the player to `playersInGame` and updates `playerCount`.  
5. Calls `CheckPlayerCount()` to potentially begin the game countdown.

</details>

<details>
<summary>BeginCountdown</summary>

**Execution Scope**: `Server`  

**Purpose**  
Starts a countdown timer and executes a callback when complete.

**Parameters**  
- `onComplete` *(function)* – Function to call when countdown finishes.

**Behavior**  
1. Clears any existing countdown timer.  
2. Starts a 10-second repeating timer.  
3. Each second, calls `CountEvent()` and plays a sound effect.  
4. Calls `onComplete()` when countdown reaches zero and stops the timer.

</details>

<details>
<summary>GameStart & GameEnd</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
- `GameStart`: Starts the game, selects a random Zakum, and schedules game end.  
- `GameEnd`: Ends the game, resets player positions, and triggers winner event.

</details>

<details>
<summary>MovePlayersToSpawn</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Moves all players back to the spawn area and resets game state.

</details>

<details>
<summary>CountEvent, GameStartEvent, GameEndEvent</summary>

**Execution Scope**: `Client`  

**Purpose**  
Sends relevant client events for countdown, game start, and game end with winner info.

</details>

<details>
<summary>AddPlayer & RemovePlayer</summary>

**Execution Scope**: `Server`  

**Purpose**  
- `AddPlayer`: Adds a player to the game state.  
- `RemovePlayer`: Removes a player, decrements player count, and handles Zakum reassignment if needed.

</details>

<details>
<summary>Event Handlers</summary>

**Execution Scope**: Varies  

- `HandleZakumChangedEvent` – Updates Zakum when a ZakumChangedEvent is received.  
- `HandlePortalUseEvent` – Teleports a player to the starting area when they use a portal.  
- `HandleUserLeaveEvent` – Removes a player from the game when they leave.

</details>

---

### Related Types & Services
- **Logic** – Base class for logic scripts.  
- **ZakumPlayerComponent** – Tracks Zakum-specific state on a player entity.  
- **_UserService** – Retrieves player entities by user ID.  
- **_TimerService** – Handles timers and repeating callbacks.  
- **_SoundManager** – Plays sound effects and background music.  
- **_SFXList** – Predefined sound effect identifiers.  
- **_UtilLogic** – Provides random numbers and other utility functions.  
- **Entity** – Represents a player or map entity.  
- **TextComponent** – UI component for displaying text.  
---

## GameEndEvent

**Purpose**  
Represents the event triggered when a game ends, notifying clients of the winning player.

---

### Properties

| Property           | Type   | Purpose |
|-------------------|--------|---------|
| `winnerUserId`     | string | The user ID of the player who won the game. |

---

### Usage

- Sent from the server (`GameManager`) to all clients when the game concludes.
- Clients can listen to this event to display winner information or trigger end-of-game UI.

---

## GameStartEvent

**Purpose**  
Represents the event triggered when a game starts, notifying clients to begin game-related logic or UI.

---

### Properties

This event has no custom properties.

---

### Usage

- Sent from the server (`GameManager`) to all clients when the game begins.
- Clients can listen to this event to initialize gameplay UI, timers, or other start-of-game behaviors.

---

## TimerEvent

**Purpose**  
Represents a timer update event, typically used to notify clients of countdowns or elapsed time during gameplay.

---

### Properties

| Property       | Type   | Purpose |
|----------------|--------|---------|
| `timerIndex`   | number | The current value of the timer or countdown. |

---

### Usage

- Sent from server or client logic to indicate the current timer value.
- Commonly used in countdowns, game start sequences, or UI timer updates.

---

## ZakumChangedEvent

**Purpose**  
Signals that the current Zakum player has changed, typically to update the game state or UI to reflect the new Zakum.

---

### Properties

| Property   | Type   | Purpose |
|------------|--------|---------|
| `userId`   | string | The UserId of the player who is now designated as Zakum. |

---

### Usage

- Sent when the Zakum role is assigned to a different player.
- Handlers for this event typically update the game state or notify clients about the new Zakum.

---

## RespawnComponent

**Purpose**  
Handles automatic player respawn when entering a designated trigger area, moving the player to a specified respawn location.

---

### Properties

| Property             | Type   | Purpose |
|----------------------|--------|---------|
| `respawnLocation`    | string | The Entity ID or path of the respawn location where players will be moved. |

---

### Method Summary

| Method                       | Execution Scope | Purpose |
|-------------------------------|----------------|---------|
| `HandleTriggerEnterEvent`     | Client/Server  | Triggered when an entity enters the attached trigger. Moves valid player entities to the respawn location. |

---

### Methods

<details>
<summary>HandleTriggerEnterEvent</summary>

**Execution Scope**: `Client/Server`  
**Event Sender**: `Entity` (TriggerComponent)

**Purpose**  
Moves players to the respawn location when they enter the trigger area.

**Parameters**  
- `event.TriggerBodyEntity` *(Entity)* – The entity that entered the trigger.

**Behavior**  
1. Checks if `TriggerBodyEntity` has a valid `PlayerComponent`.  
2. If valid, calls `MoveToEntity(respawnLocation)` on the player to move them to the respawn point.

</details>

---

### Related Types & Services
- **Component** – Base class for entity components.  
- **TriggerEnterEvent** – Event fired when an entity enters a trigger.  
- **PlayerComponent** – Handles player-specific movement and actions.  

---

## ZakumPlayerComponent

**Purpose**  
Handles the "Zakum" role mechanics for a player, including setting/unsetting Zakum status, immunity, speed adjustments, and triggering role change events when colliding with other players.

---

### Properties

| Property              | Type    | Purpose |
|-----------------------|---------|---------|
| `isZakum`             | boolean | Synced property indicating if the player is currently Zakum. |
| `zakumHatRUID`        | string  | Resource ID of the Zakum hat to equip. |
| `isImmune`            | boolean | Synced property indicating if the player is temporarily immune to role changes. |
| `immunityDuration`    | number  | Duration of temporary immunity in seconds. |
| `zakumEffectRUID`     | string  | Resource ID of the effect to play when becoming Zakum. |
| `zakumSpeed`          | number  | Movement speed while being Zakum. |
| `normalSpeed`         | number  | Default movement speed for the player. |

---

### Method Summary

| Method            | Execution Scope | Purpose |
|------------------|----------------|---------|
| `SetZakum`        | Server         | Assigns Zakum role to the player, equips the hat, sets speed, and plays effects. |
| `UnsetZakum`      | Server         | Removes Zakum role, resets speed, and unequips hat. |
| `Immunity`        | Server         | Grants temporary immunity from being tagged as Zakum. |
| `OnBeginPlay`     | ServerOnly     | Initializes player speed on start. |
| `SetSpeed`        | Server         | Adjusts the player’s movement speed. |
| `HandleTriggerEnterEvent` | Client/Server | Handles collisions with other players to trigger Zakum role changes. |

---

### Methods

<details>
<summary>SetZakum</summary>

**Execution Scope**: `Server`  

**Purpose**  
Assigns Zakum status to the player and applies related visual, audio, and movement effects.

**Behavior**  
1. Sets `isZakum` to `true`.  
2. Equips the Zakum hat using `CostumeManagerComponent`.  
3. Sets movement speed to `zakumSpeed`.  
4. Plays Zakum sound and effect attached to the player entity.

</details>

<details>
<summary>UnsetZakum</summary>

**Execution Scope**: `Server`  

**Purpose**  
Removes Zakum status from the player and restores default properties.

**Behavior**  
1. Sets `isZakum` to `false`.  
2. Unequips the Zakum hat.  
3. Resets speed to `normalSpeed`.

</details>

<details>
<summary>Immunity</summary>

**Execution Scope**: `Server`  

**Purpose**  
Temporarily prevents the player from being tagged as Zakum.

**Behavior**  
1. Sets `isImmune` to `true`.  
2. Schedules a timer to reset `isImmune` to `false` after `immunityDuration` seconds.

</details>

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Initializes the player’s speed to normal at the start of play.

</details>

<details>
<summary>SetSpeed</summary>

**Execution Scope**: `Server`  

**Purpose**  
Adjusts the player’s movement speed.

**Parameters**  
- `speed` *(number)* – Speed value to apply to `MovementComponent.InputSpeed`.

</details>

<details>
<summary>HandleTriggerEnterEvent</summary>

**Execution Scope**: `Client/Server`  
**Event Sender**: `Self`  

**Purpose**  
Handles collisions with other players to potentially transfer the Zakum role.

**Parameters**  
- `event.TriggerBodyEntity` *(Entity)* – Entity that entered the trigger.

**Behavior**  
1. Returns immediately if executed on client.  
2. Checks if the colliding entity has a valid `ZakumPlayerComponent`.  
3. Logs "Immunity" if the colliding player is currently immune.  
4. If the colliding player is Zakum and not immune:  
   - Triggers `ZakumChangedEvent` with this entity as the new Zakum.  
   - Grants temporary immunity using `Immunity()`.  
   - Sends the event to `_GameManager`.

</details>

---

### Related Types & Services
- **Component** – Base class for entity components.  
- **_TimerService** – Used to schedule immunity timers.  
- **_EffectService** – Plays visual effects.  
- **_SoundManager** – Plays audio effects.  
- **TriggerEnterEvent** – Event fired when an entity enters a trigger.  
- **ZakumChangedEvent** – Event triggered when Zakum role changes.  
- **MovementComponent** – Handles player movement speed and direction.  
- **CostumeManagerComponent** – Manages player avatar equipment.  

---

## SoundManager

**Purpose**  
Provides a centralized logic for playing background music (BGM) and sound effects (SFX) in the game, supporting both client-side positional audio and server-triggered global SFX.

---

### Properties

| Property | Type   | Purpose |
|----------|--------|---------|
| `bgmRUID` | string | Resource ID of the background music to play. |

---

### Method Summary

| Method               | Execution Scope | Purpose |
|---------------------|----------------|---------|
| `PlaySFXAtPos`      | Client         | Plays a sound effect at a specified world position for the local player. |
| `PlayBGM`            | Client         | Plays the background music (BGM) for the local player. |
| `PlaySFX`            | Server         | Plays a global sound effect for all players. |

---

### Methods

<details>
<summary>PlaySFXAtPos</summary>

**Execution Scope**: `Client`  

**Purpose**  
Plays a sound effect at a specific position in the game world for the local player.

**Parameters**  
- `sfx` *(string)* – Resource ID of the sound effect.  
- `pos` *(Vector3)* – World position where the sound should be played.

**Behavior**  
Uses `_SoundService:PlaySoundAtPos` with the local player as the listener and a volume of 1.

</details>

<details>
<summary>PlayBGM</summary>

**Execution Scope**: `Client`  

**Purpose**  
Plays the background music for the local player.

**Behavior**  
Uses `_SoundService:PlayBGM` with `bgmRUID` and a volume of 1.

</details>

<details>
<summary>PlaySFX</summary>

**Execution Scope**: `Server`  

**Purpose**  
Plays a sound effect globally for all players.

**Parameters**  
- `sfx` *(string)* – Resource ID of the sound effect.

**Behavior**  
Uses `_SoundService:PlaySound` with volume 1.

</details>

---

### Related Services
- **_SoundService** – Handles playback of sound effects and background music.  
- **_UserService.LocalPlayer** – Provides the local player entity for client-side positional audio.

---

## SFXList

**Purpose**  
Provides a centralized list of sound effect (SFX) resource IDs used throughout the game for consistency and easy reference.

---

### Properties

| Property   | Type   | Purpose |
|------------|--------|---------|
| `STEALZAKUM` | string | SFX for when the Zakum player is stolen or targeted. |
| `WINNER`     | string | SFX played when a player wins the game. |
| `START`      | string | SFX played when the game starts. |
| `COUNT`      | string | SFX for countdown ticks during game start or events. |
| `PORTAL`     | string | SFX played when a player uses a portal. |

## ZakumUIComponent

**Purpose**  
Manages the UI and visual effects for the Zakum game mode, including countdown timers, game start/end effects, and winner display.

---

### Properties

| Property                  | Type    | Purpose |
|---------------------------|---------|---------|
| `timerEffectRUIDs`        | table   | List of effect RUIDs for countdown timers. |
| `waitingForPlayersUI`     | Entity  | UI element displayed while waiting for players. |
| `gameTimerUI`             | Entity  | UI element showing the current game timer. |
| `gameStartEffectRUID`     | string  | Effect played at the start of the game. |
| `gameEndEffectRUID`       | string  | Effect played at the end of the game. |
| `gameWinnerUI`            | Entity  | UI element displaying the winner of the game. |

---

### Method Summary

| Method                   | Execution Scope | Purpose |
|--------------------------|----------------|---------|
| `TimerEffect`            | Client         | Plays a timer effect corresponding to a countdown number. |
| `GameStartEffect`        | Client         | Plays the visual effect when the game starts. |
| `GetScreenCenterPos`     | Client         | Returns the world position at the center of the screen. |
| `SetWaitingForPlayers`   | Client/Server  | Shows the waiting UI and hides the game timer. |
| `SetTimer`               | Client         | Updates the timer UI and triggers the timer effect. |
| `GameEndEffect`          | Client         | Plays the visual effect when the game ends. |
| `ShowWinner`             | Client/Server  | Displays the winner UI with the username. |
| `HideWinner`             | Client/Server  | Hides the winner UI. |
| `OnInitialize`           | ClientOnly     | Initializes the timer effect RUIDs. |

---

### Methods

<details>
<summary>TimerEffect</summary>

**Execution Scope**: `Client`  

**Purpose**  
Plays the visual effect corresponding to the current countdown timer index.

**Parameters**  
- `timerIndex` *(integer)* – The current countdown number.

**Behavior**  
1. Checks if `timerIndex` is valid within `timerEffectRUIDs`.  
2. Plays the effect at the screen center using `_EffectService:PlayEffect`.

</details>

<details>
<summary>GameStartEffect</summary>

**Execution Scope**: `Client`  

**Purpose**  
Triggers the game start effect at the screen center.

**Behavior**  
- Plays `gameStartEffectRUID` using `_EffectService:PlayEffect`.

</details>

<details>
<summary>GetScreenCenterPos</summary>

**Execution Scope**: `Client`  

**Purpose**  
Returns the world position at the center of the screen for UI effects.

**Returns**  
- `Vector3` – World position at the screen center.

**Behavior**  
- Converts the screen center `(ScreenWidth/2, ScreenHeight/2)` to world coordinates using `_UILogic:ScreenToWorldPosition`.

</details>

<details>
<summary>SetWaitingForPlayers</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Shows the "waiting for players" UI and hides the game timer UI.

**Behavior**  
- Enables `waitingForPlayersUI`.  
- Disables `gameTimerUI`.

</details>

<details>
<summary>SetTimer</summary>

**Execution Scope**: `Client`  

**Purpose**  
Updates the game timer display and triggers the corresponding countdown effect.

**Parameters**  
- `timer` *(integer)* – The current countdown number.

**Behavior**  
1. Enables `gameTimerUI` and disables `waitingForPlayersUI`.  
2. Updates the text of `gameTimerUI` to the current timer.  
3. Calls `TimerEffect(timer)` to play the corresponding visual effect.

</details>

<details>
<summary>GameEndEffect</summary>

**Execution Scope**: `Client`  

**Purpose**  
Plays the game end effect at the screen center.

**Behavior**  
- Uses `_EffectService:PlayEffect` with `gameEndEffectRUID`.

</details>

<details>
<summary>ShowWinner</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Displays the winner UI with the given username.

**Parameters**  
- `username` *(string)* – Name of the winning player.

**Behavior**  
1. Enables `gameWinnerUI`.  
2. Sets the winner text to `"<username> is the winner!"`.

</details>

<details>
<summary>HideWinner</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Hides the winner UI.

**Behavior**  
- Disables `gameWinnerUI`.

</details>

<details>
<summary>OnInitialize</summary>

**Execution Scope**: `ClientOnly`  

**Purpose**  
Initializes the `timerEffectRUIDs` list for the countdown timer.

**Behavior**  
- Assigns pre-defined RUIDs to `timerEffectRUIDs`.

</details>

---

### Event Handlers

| Handler                   | Event Type        | Sender / Scope      | Purpose |
|----------------------------|-----------------|-------------------|---------|
| `HandleTimerEvent`         | `TimerEvent`     | Logic / GameManager | Updates the timer UI for the current countdown number. |
| `HandleGameStartEvent`     | `GameStartEvent` | Logic / GameManager | Plays game start effects and hides the winner UI. |
| `HandleGameEndEvent`       | `GameEndEvent`   | Logic / GameManager | Plays game end effects, resets UI, and shows the winner. |

---

### Related Types & Services
- **Component** – Base class for entity components.  
- **Entity** – Represents UI elements or game entities.  
- **Vector2 / Vector3** – 2D and 3D position representations.  
- **_EffectService** – Plays visual effects in the world.  
- **_UILogic** – Provides screen-to-world position conversion.  
- **_UserService** – Access to player entities and information.


## Changelog
| Version | Changelog | Download |
|---------|-------------|-------------|
| V1_1 | Zakum's Curse Tutorial Project Files Released! | [Download](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/raw/refs/heads/main/Zakums_Curse/Zakums_Curse_V1_1.mod) |
