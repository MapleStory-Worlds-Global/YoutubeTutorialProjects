# Zakum's Curse 

## Youtube Tutorial
[![Youtube Tutorial](http://img.youtube.com/vi/7hNKAn9JmBQ/0.jpg)](https://youtu.be/7hNKAn9JmBQ?feature=shared "MapleStory Worlds Tutorial: Pro Mode Chapter 8 - Bringing Everything Together")

## Mod Installation Instructions
[Mod File Installation Guide](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects?tab=readme-ov-file#project-import-instructions)

## Project File
[Download Latest Project File](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/raw/refs/heads/main/Zakums_Curse/Zakums_Curse_V1_1.mod)

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

## Components

### RespawnComponent

**Purpose:**  
Handles automatic respawning of players who fall off the gameplay area. When a player enters the designated trigger volume (usually placed off-map), they are teleported back to a safe spawn location.

**Key Property:**

- `respawnLocation` (string): Entity ID of the safe respawn point.

**Core Logic:**

- Listens for trigger enter events.
- When triggered by a player entity, calls their movement component to teleport them to the respawn location.

---

### ZakumPlayerComponent

**Purpose:**  
Manages the player’s Zakum helmet state, including applying the helmet costume, speed modification, immunity timing after stealing, and detecting collisions with other players to handle stealing mechanics.

**Key Properties:**

- `isZakum` (boolean, synced): Whether this player currently holds the Zakum helmet.
- `zakumHatRUID` (string): Resource ID for the Zakum helmet costume.
- `isImmune` (boolean, synced): Temporary immunity status after stealing the helmet.
- `immunityDuration` (number): Duration (in seconds) of immunity.
- `zakumEffectRUID` (string): Visual effect played when a player becomes Zakum.
- `zakumSpeed` (number): Movement speed multiplier when holding the helmet.
- `normalSpeed` (number): Normal movement speed.

**Important Methods:**

- `SetZakum()`: Assigns the helmet to the player, applies costume and speed boost, plays sound and effect.
- `UnsetZakum()`: Removes the helmet state and resets speed.
- `Immunity()`: Grants temporary immunity to stealing after a helmet change.
- `SetSpeed(speed)`: Adjusts player movement speed.
- Event handler listens for trigger collisions with other players to trigger helmet stealing.

---

### ZakumUIComponent

**Purpose:**  
Manages all client-side UI updates and visual effects related to the game state, including countdown timers, game start/end effects, and displaying the winner announcement.

**Key Properties:**

- `timerEffectRUIDs` (table): List of effect resource IDs mapped to countdown seconds.
- `waitingForPlayersUI` (Entity): UI element displayed while waiting for players.
- `gameTimerUI` (Entity): UI element displaying the ongoing game timer.
- `gameWinnerUI` (Entity): UI element used to display the winner’s name.

**Important Methods:**

- `SetTimer(timer)`: Updates timer UI and triggers corresponding countdown effects.
- `SetWaitingForPlayers()`: Switches UI to “waiting” mode.
- `ShowWinner(username)`: Displays the winner’s name on screen.
- `GameStartEffect()` / `GameEndEffect()`: Play respective visual effects.

**Event Handling:**

- Responds to timer ticks, game start, and game end events from the `GameManager` to update UI accordingly.

---

## Logic Scripts

### GameManager

**Purpose:**  
Central game controller managing player states, game flow, and event broadcasting. Handles player registration, random Zakum assignment, countdown timers, teleportation, and notifying clients about game state changes.

**Key Properties:**

- `currentZakum` (ZakumPlayerComponent): Reference to the current helmet holder.
- `startingAreaId` (string): Entity ID of the game start location.
- `playerCount` (number): Number of players currently in the game.
- `hasGameStarted` (boolean): Flag indicating whether the game is running.
- `gameStartTimer` (number): Timer ID for the game start countdown.
- `playersInGame` (table): Mapping of user IDs to player entities.
- `spawnAreaId` (string): Entity ID where players are teleported after the game ends.

**Important Methods:**

- `ChangeZakum(userId)`: Assigns the helmet to a specific player.
- `SetRandomZakum()`: Randomly picks a player to be Zakum.
- `TeleportToStartingArea(userId)`: Moves a player to the starting zone if the game hasn’t started.
- `CheckPlayerCount()`: Starts the countdown if enough players are present.
- `BeginCountdown(onComplete)`: Manages countdown timer with repeated ticks and callback.
- `GameStart()`: Starts the game and timer for game end.
- `GameEnd()`: Ends the game, resets states, teleports players back to spawn.
- `MovePlayersToSpawn()`: Teleports all players out and clears player lists.
- `AddPlayer(userId)`: Adds a player to the game.
- `RemovePlayer(userId)`: Removes a player, handles Zakum reassignment if needed.

**Event Handlers:**

- Listens for player portal use, user leave, and Zakum changed events to update game state accordingly.

---

### SFXList

**Purpose:**  
Acts as a centralized registry of sound effect identifiers (RUIDs). Using this script helps avoid magic strings and ensures consistent use of sound assets across the project.

**Sound Keys Include:**

- `STEALZAKUM`: Played when a player steals the Zakum helmet.
- `WINNER`: Played when the game ends.
- `START`: Played at game start.
- `COUNT`: Played during countdown ticks.
- `PORTAL`: Played when a player uses the portal.

---

## Event Types

### GameEndEvent

**Purpose:**  
Notifies listeners when the game ends. Contains the winning player's user ID.

### GameStartEvent

**Purpose:**  
Indicates the start of a game round. No extra data needed.

### TimerEvent

**Purpose:**  
Communicates the current timer tick to clients, usually for UI countdown updates.

### ZakumChangedEvent

**Purpose:**  
Broadcasts when the Zakum helmet changes owners, specifying the new holder’s user ID.

---

## Summary Notes

- Most components rely on globally accessible engine services such as `_SoundService`, `_EffectService`, and `_UILogic`.
- Resource IDs (RUIDs) represent linked assets such as sound effects and visual effects.
- Event-driven architecture connects game logic (`GameManager`) to UI updates (`ZakumUIComponent`) and player state management (`ZakumPlayerComponent`).

---


## Changelog
| Version | Changelog | Download |
|---------|-------------|-------------|
| V1_1 | Zakum's Curse Tutorial Project Files Released! | [Download](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/raw/refs/heads/main/Zakums_Curse/Zakums_Curse_V1_1.mod) |
