# Dynamic Maps Demo 

## Youtube Tutorial
[![Youtube Tutorial](https://img.youtube.com/vi/RY1q4Pv7_B8/0.jpg)](https://youtu.be/RY1q4Pv7_B8?si=B_jZJq-9vyWQ4Z3U "Working With Dynamic Maps")
[![Youtube Tutorial](https://img.youtube.com/vi/LNVUPFfZM7k/0.jpg)](https://youtu.be/LNVUPFfZM7k?si=AOT4SU4U9lXotoKO "Let’s create a PQ Dungeon with Dynamic Maps! Part 2")
[![Youtube Tutorial](https://img.youtube.com/vi/wnDo6Bn17Ek/0.jpg)](https://youtu.be/wnDo6Bn17Ek?si=Y9EGrOUec15plb-c "Let’s create a PQ Dungeon with Dynamic Maps! Part 2")

## Mod Installation Instructions
[Mod File Installation Guide](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects?tab=readme-ov-file#mod-file-import-instructions)

## Project File
[Download Latest Project File](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/raw/refs/heads/main/Dynamic_Maps/DynamicMapsTutorial.mod)

## Table of Contents
- [Overview](#overview)
- [Setup and Installation](#setup-and-installation)
- [Script Documentation](#script-documentation)
  - [SceneManager](#scenemanager)
  - [UIManager](#uimanager)
  - [Utils](#utils)
  - [DynamicMapInfo](#dynamicmapinfo)
  - [DynamicMapPortal](#dynamicmapportal)
  - [DynamicMapReservedEvent](#dynamicmapreservedevent)
  - [DynamicPortalUseEvent](#dynamicportaluseevent)
  - [EnterDungeonEvent](#enterdungeonevent)
  - [PlayerRegisteredEvent](#playerregisteredevent)
  - [InstanceDungeonReservationUI](#instancedungeonreservationui)
  - [InstanceDungeonReservationUIItem](#instancedungeonreservationuiitem)
  - [Monster](#monster)
  - [InstanceDungeonMonster](#instancedungeonmonster)
  - [MonsterAttack](#monsterattack)
  - [PlayerAttack](#playerattack)
  - [PlayerHit](#playerhit)
  - [Popupbutton](#popupbutton)
- [Changelog & Download](#changelog)
---

## Overview
This project is provided as supplemental resource for the "Let’s create a PQ Dungeon with Dynamic Maps!" tutorial series. 

Dynamic Maps open the door to infinite possibilities — from dungeons and event rooms to boss raids that change every run. Whether you’re a creator or a player, this is the feature that takes your world-building to the next level. Let's learn!

In this demo, a portal in a hub map opens a party-finder panel. Players register ("Search Party") to reserve a slot in a dynamic copy of a dungeon map, then press **Enter** to move the whole reserved party into that private instance. Defeating the boss inside closes the instance and returns everyone to a static map.

## Setup and Installation
* Import the mod file (see the installation guide above) and open the world in MSW Maker.
* Make sure the `SceneManager`, `UIManager` and `Utils` logic scripts are present (they are accessed globally as `_SceneManager`, `_UIManager` and `_Utils`).
* On the hub map, attach `DynamicMapPortal` to a portal entity:
    * Set `mapName` to the base dungeon map to copy. The name must not contain `_`, because instances are named `<mapName>_<n>` and the base name is recovered by splitting on `_`.
    * Set `maxPlayers` to the party size per instance.
    * Leave the portal's destination portal (`PortalEntityRef`) empty, otherwise the reservation flow is skipped.
* The UI must contain the reservation panel with `InstanceDungeonReservationUI` (referenced by `UIManager.instanceDungeon`) and its Register / Enter / Close buttons, plus the row model that carries `InstanceDungeonReservationUIItem`.
* In the dungeon map, give the boss `InstanceDungeonMonster` (and `MonsterAttack`) and set `returnMapOnDefeat` to the static map players should return to. Regular monsters use `Monster` and `MonsterAttack`.
* Attach `PlayerAttack` and `PlayerHit` to the DefaultPlayer so players can fight.
* Press Play to test:
    * Walk into the portal to open the party panel, then click **Search Party** to reserve a slot (add more clients to test a group).
    * Click **Enter** (or press **F**) to move the reserved party into a new dungeon instance.
    * Defeat the boss; after a short delay the instance closes and everyone is sent to `returnMapOnDefeat`.

## Script Documentation

## SceneManager

**Purpose**  
Manages **dynamic map instances** by tracking user reservations, initializing maps with capacity limits, moving users into dynamically created instances, and handling clean-up when users leave or maps are closed.  
Keeps one `DynamicMapInfo` per base map; instance names are `<mapName>_<n>`, and the base name is recovered by splitting the instance name on `_`.

---

### Properties

| Property          | Type   | Purpose |
|-------------------|--------|---------|
| `dynamicMapInfos` | `table` | Holds `DynamicMapInfo` objects for each map type (indexed by base map name). Initialized as an empty table `{}`. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|---------|
| `CreateDynamicMap` | Server | Creates a new dynamic instance of an initialized base map and returns its name; otherwise logs a warning and returns `nil`. |
| `ReserveDynamicMap` | Server | Reserves a spot for the user in an open (or newly created) instance and notifies everyone reserved for it. |
| `InitDynamicMap` | Server | Creates and stores a `DynamicMapInfo` for the given base map and capacity. |
| `MoveReservationsToMap` | Server | Moves all users reserved for a given instance into that instance. |
| `SendReservationEvent` | Client | Sends a `DynamicMapReservedEvent` with the instance name and current reservation list. |
| `GetReservedUsers` | Server | Returns the reservation list for a given instance. |
| `UnregisterDynamicMap` | Server | Removes a user's reservation and notifies the remaining users and the removed user. |
| `FindInstanceName` | Server | Returns the instance a user is in or reserved for. |
| `CloseDynamicMap` | Server | Teleports all users in an instance to a target map and destroys the instance. |

---

### Methods

<details>
<summary>CreateDynamicMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Generates a new dynamic instance if the map has been previously initialized via `InitDynamicMap`.

**Parameters**  
- `mapName` *(string)* – The base name of the map to instantiate.

**Returns**  
- *(string)* – Instance name from `DynamicMapInfo:GenerateMap()`, or `nil` if the map is uninitialized.

</details>

<details>
<summary>ReserveDynamicMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Reserves a spot for the given user in a dynamic map instance. The base map must be initialized beforehand.

**Parameters**  
- `userId` *(string)* – The user's ID to reserve a space for.  
- `mapName` *(string)* – The base map name.

**Behavior**  
1. Calls `FindOpenMap()`; if no instance has space, creates one with `CreateDynamicMap`.  
2. If an existing instance was found and the user is already in its reservation list, logs a warning and returns.  
3. If the user entity is valid, calls `ReserveMap` and sends `SendReservationEvent` to every reserved user.  
4. Returns the instance name.

</details>

<details>
<summary>InitDynamicMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Initializes a dynamic map by associating it with a `DynamicMapInfo` object and defining max player capacity. Called by `DynamicMapPortal.OnBeginPlay`.

**Parameters**  
- `mapName` *(string)* – Name of the base map to initialize.  
- `maxPlayers` *(integer)* – Maximum number of players for each dynamic instance.

</details>

<details>
<summary>MoveReservationsToMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Transfers all users with active reservations into the specified dynamic instance (`DynamicMapInfo:MoveReservations`).

**Parameters**  
- `mapInstanceName` *(string)* – The full dynamic name of the instance (e.g., `"DungeonMap_1"`).

</details>

<details>
<summary>SendReservationEvent</summary>

**Execution Scope**: `Client`  

**Purpose**  
Sends a `DynamicMapReservedEvent` to confirm the current reservation list. Server code calls it with a trailing target `userId`, so it runs on that user's client.

**Parameters**  
- `mapInstanceName` *(string)* – Instance name the users were assigned.  
- `users` *(table)* – List of reserved user IDs.

</details>

<details>
<summary>GetReservedUsers</summary>

**Execution Scope**: `Server`  

**Purpose**  
Returns all users who have active reservations in the given dynamic instance.

**Parameters**  
- `mapInstanceName` *(string)* – Dynamic map name.

**Returns**  
- *(table)* – List of reserved user IDs, or `nil` if there are none or the base map is uninitialized.

</details>

<details>
<summary>UnregisterDynamicMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Removes a user's reservation entry and broadcasts the updated list.

**Parameters**  
- `mapInstanceName` *(string)* – Full name of the instance.  
- `userId` *(string)* – User ID to remove.

**Behavior**  
1. Calls `DynamicMapInfo:UnregisterMap`.  
2. Sends `SendReservationEvent` to every remaining reserved user, and also to the removed user so their UI updates.

</details>

<details>
<summary>FindInstanceName</summary>

**Execution Scope**: `Server`  

**Purpose**  
Finds the dynamic instance name associated with a specific user for a particular map type.

**Parameters**  
- `mapName` *(string)* – Base map name.  
- `userId` *(string)* – User ID to query.

**Returns**  
- *(string)* – Full instance name or `nil`.

</details>

<details>
<summary>CloseDynamicMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Closes the specified instance and warps all users inside it to a static map. Called by `InstanceDungeonMonster` when the boss is defeated.

**Parameters**  
- `mapInstanceName` *(string)* – Dynamic instance to shut down.  
- `targetMap` *(string)* – Static map name to send users to.

</details>

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandleUserLeaveEvent` | `UserLeaveEvent` | UserService / Server | Removes the leaving user from the dynamic instance they are in (`DynamicMapInfo:LeaveMap`), destroying the instance if it becomes empty. |

---

### Related Types & Services
- **`DynamicMapInfo`** – Custom struct holding instance capacity, user lists, and reservations.  
- **`DynamicMapReservedEvent`** – Event emitted when the reservation list changes.  
- **`_UtilLogic`** – Used for `Split()` to recover the base map name from an instance name.  
- **`_Utils`** – `Contains()` helper for duplicate-reservation checks.  
- **`UserLeaveEvent`** – Native user departure event.

---

## UIManager

**Purpose**  
Central controller for showing and hiding **UI elements** related to dynamic dungeon instance reservations.  
Server scripts call its Client methods with a trailing target `userId`, so each call runs on that player's client.

---

### Properties

| Property        | Type                         | Purpose |
|-----------------|------------------------------|---------|
| `instanceDungeon` | `InstanceDungeonReservationUI` | Reference to the UI component that displays reserved dungeon users. Default entity ID: `"a869fc03-e701-48a3-8758-333f4c28a086"`. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|---------|
| `ShowPopup` | Client | Placeholder popup: currently only logs the message. |
| `ShowDungeonReservation` | Client | Displays the dungeon reservation UI with a list of user IDs. |
| `HideDungeonReservation` | Client | Hides the reservation UI. |

---

### Methods

<details>
<summary>ShowPopup</summary>

**Execution Scope**: `Client`  

**Purpose**  
Shows a message to the user. No popup UI is wired up yet.

**Parameters**  
- `message` *(string)* – Text to show.

**Behavior**  
- Calls `log(message)`.

</details>

<details>
<summary>ShowDungeonReservation</summary>

**Execution Scope**: `Client`  

**Purpose**  
Displays the reservation UI and populates it with a list of reserved users.

**Parameters**  
- `users` *(table)* – List of user IDs to show (an empty table opens an empty list).

**Behavior**  
- Calls `self.instanceDungeon:Show(users)`.

</details>

<details>
<summary>HideDungeonReservation</summary>

**Execution Scope**: `Client`  

**Purpose**  
Closes the dungeon reservation UI.

**Behavior**  
- Calls `self.instanceDungeon:Hide()`, which also unregisters the local player if they are still registered.

</details>

---

### Related Types & Services
- **`InstanceDungeonReservationUI`** – Custom UI component responsible for displaying the user reservation list for dungeon entry.

---

## Utils

**Purpose**  
Provides utility logic functions for use across the game logic layer (accessed as `_Utils`).  
Currently includes basic list-checking functionality.

---

### Properties

This script has no custom properties.

---

### Method Summary

| Method        | Execution Scope | Purpose |
|---------------|-----------------|---------|
| `Contains` | Client/Server | Returns whether the `targetList` contains the specified value. |

---

### Methods

<details>
<summary>Contains</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Checks if a value exists in a given list or table.

**Parameters**  
- `targetList` *(table)* – The table or list to search through.  
- `toCheck` *(any)* – The value to search for.

**Returns**  
- *(boolean)* – `true` if `toCheck` is found in `targetList`; otherwise `false`.

**Behavior**  
- Iterates through `targetList` with `pairs`.  
- If `value == toCheck`, stops and returns `true`.  
- If the loop completes without a match, returns `false`.

</details>

---

### Related Types & Services
- **Logic** – This is a generic logic script (`extends Logic`), reused by other scripts for common operations.

---

## DynamicMapInfo

**Purpose**  
Struct (one per base map, owned by `SceneManager`) that coordinates creation and lifecycle of dynamic (instanced) maps derived from a base map.  
Manages instance generation, capacity, reservations, and player movement while cleaning up instances when empty.  

---

### Properties

| Property  | Type                      | Purpose |
|------------|---------------------------|----------|
| `maxPlayers` | `number`                | Maximum number of players allowed in a single dynamic instance (users + reservations). Default `10`. |
| `mapName`  | `string`                   | Base/static map name used as a template for generating dynamic instances. |
| `idx`      | `integer`                 | Counter for generating unique instance names (e.g., `map_1`, `map_2`). |
| `users`    | `table` | Maps `instanceName -> { userId, ... }` for active players inside each dynamic instance. |
| `reservations`  | `table` | Maps `instanceName -> { userId, ... }` for players reserved to enter a given instance. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|----------|
| `Init` | ServerOnly | Initializes base map, capacity, and tracking tables. |
| `GenerateMap` | Client/Server | Creates a new dynamic instance based on `mapName` and returns its name. |
| `EnterMap` | Client/Server | Adds a user to an instance (capacity-checked) and teleports them into it. |
| `LeaveMap` | Client/Server | Removes a user from an instance; destroys the instance if it becomes empty. |
| `FindOpenMap` | Client/Server | Returns an instance with available capacity, counting reservations. |
| `ReserveMap` | Client/Server | Reserves a seat for a user in an instance (no immediate teleport). |
| `MoveReservations` | Client/Server | Admits all reserved users into the instance and clears reservations. |
| `GetReservations` | Client/Server | Retrieves the reservation list for an instance. |
| `UnregisterMap` | Client/Server | Removes a specific user’s reservation from an instance. |
| `GetInstance` | Client/Server | Returns the instance containing or reserving the given user, or `nil`. |
| `CloseDynamicMap` | Client/Server | Teleports all users to `targetMap` and destroys the dynamic instance. |

> All methods except `Init` have no `@ExecSpace`; in practice they are only called from `SceneManager`'s Server methods, so they run on the server.

---

### Methods

<details>
<summary>Init</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Initializes dynamic instance metadata and tracking.

**Parameters**  
- `maxPlayers` *(integer)* – Maximum players per instance.  
- `mapName` *(string)* – Base/static map used to spawn instances.

**Behavior**  
1. Stores `mapName` and `maxPlayers`.  
2. Resets `users` and `reservations` tables.  

</details>

<details>
<summary>GenerateMap</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Creates a new dynamic instance using the base map.

**Behavior**  
1. Builds `instanceName = "<mapName>_<idx>"`, then increments `idx`.  
2. Calls `_DynamicMapService:CreateDynamicMap(mapName, instanceName)` and logs the result code on failure.  
3. Initializes `users[instanceName] = {}`.  
4. Returns `instanceName`.  

</details>

<details>
<summary>EnterMap</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Moves a player into an instance if it exists and has capacity.

**Parameters**  
- `instanceName` *(string)* – Target instance name.  
- `userId` *(string)* – Player ID.

**Behavior**  
1. Verifies `users[instanceName]` exists and is below `maxPlayers`.  
2. Adds `userId` to `users[instanceName]`.  
3. Teleports the user via `_TeleportService:TeleportToMapPosition(userEntity, Vector3(0, 10, 0), instanceName)`.  

</details>

<details>
<summary>LeaveMap</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Removes a user from an instance; destroys it if no users remain.

**Parameters**  
- `userId` *(string)* – Player ID.  
- `instanceName` *(string)* – Instance name.

**Behavior**  
1. Removes `userId` from `users[instanceName]`.  
2. If the instance becomes empty, calls `_DynamicMapService:DestroyDynamicMap(instanceName)` and clears its entry.  

</details>

<details>
<summary>FindOpenMap</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Finds an instance with available capacity (users + reservations).

**Behavior**  
1. Iterates known instances and computes `count = #users + #reservations`.  
2. Returns the first `instanceName` where `count < maxPlayers`, or `nil`.  

</details>

<details>
<summary>ReserveMap</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Queues a player for entry into an existing instance.

**Parameters**  
- `instanceName` *(string)* – Target instance name.  
- `userId` *(string)* – Player ID.

**Behavior**  
1. Returns with a warning if the instance does not exist.  
2. Ensures `reservations[instanceName]` exists.  
3. Inserts `userId` into the reservation list.  

</details>

<details>
<summary>MoveReservations</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Admits all reserved players into an instance.

**Parameters**  
- `instanceName` *(string)* – Instance name.

**Behavior**  
1. For each reserved `userId`, calls `EnterMap(instanceName, userId)`.  
2. Clears `reservations[instanceName]`.  

</details>

<details>
<summary>GetReservations</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Retrieves reservations for an instance.

**Parameters**  
- `instanceName` *(string)* – Instance name.

**Returns**  
- *(table)* – `reservations[instanceName]`, or `nil` (with a warning) if none.  

</details>

<details>
<summary>UnregisterMap</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Removes a specific user’s reservation from an instance.

**Parameters**  
- `instanceName` *(string)* – Instance name.  
- `userId` *(string)* – Player ID.

**Behavior**  
1. Finds and removes `userId` from `reservations[instanceName]` if present.  

</details>

<details>
<summary>GetInstance</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Locates the instance that contains or reserves a given user (linear search; the code notes it should not be overused).

**Parameters**  
- `userId` *(string)* – Player ID.

**Returns**  
- *(string)* – The matching `instanceName`, or `nil`.  

</details>

<details>
<summary>CloseDynamicMap</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Teleports all users out and destroys the instance.

**Parameters**  
- `instanceName` *(string)* – Instance name.  
- `targetMap` *(string)* – Static map to receive users.

**Behavior**  
1. Teleports each user in `users[instanceName]` to `targetMap` at `Vector3(0, 10, 0)`.  
2. Clears `users[instanceName]` and calls `_DynamicMapService:DestroyDynamicMap(instanceName)`.  

</details>

---

### Related Types & Services

- **_DynamicMapService** – Creates and destroys dynamic instances.  
- **_TeleportService** – Teleports players to map positions.  
- **_UserService** – Resolves `userId` to player entity.  
- **_Utils** – Helper for membership checks.  

---

## DynamicMapPortal

**Purpose**  
Portal component (extends `PortalComponent`) that routes players into dynamic (instanced) maps through a party reservation flow.  
Using the portal opens the reservation UI; the UI's Register and Enter buttons are handled here and forwarded to `SceneManager`.

---

### Properties

| Property  | Type                      | Purpose |
|------------|---------------------------|----------|
| `mapName`    | `string` | Base/static map used as the template for new dynamic instances. |
| `maxPlayers`  | `number`                   | Maximum players per instance, passed to `SceneManager:InitDynamicMap`. Default `5`. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|----------|
| `OnBeginPlay` | ServerOnly | Registers `mapName` / `maxPlayers` with `SceneManager:InitDynamicMap`. |
| `EnterMap` | Server | Earlier direct-entry version: creates a fresh dynamic map and teleports a single user into it. Currently unused. |
| `UsePortal` | Server | Opens the dungeon reservation UI for the user. |
| `EnterReservation` | Server | Moves the reserved party into the instance if the requesting user is registered. |
| `ReserveMap` | Server | Reserves a slot for the user and stores the instance name on their client. |
| `UpdateLocalReservation` | Client | Stores the reserved instance name in the client-local `_T.reservedMap`. |
| `UnReserveMap` | Client | Cancels the local player's reservation (if any) via `SceneManager:UnregisterDynamicMap`. |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Initializes the dynamic map tracking for this portal's target map.

**Behavior**  
- Calls `_SceneManager:InitDynamicMap(self.mapName, self.maxPlayers)`.

</details>

<details>
<summary>EnterMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Creates a new dynamic map and teleports one user straight into it (from the first part of the tutorial; its call in `HandlePortalUseEvent` is commented out).

**Parameters**  
- `userId` *(string)* – The player's ID.

**Behavior**  
1. Uses a counter in `_T.idx` to build the name `mapName .. idx`.  
2. Calls `_DynamicMapService:CreateDynamicMap`; logs and returns on failure.  
3. Teleports the user to `Vector3(0, 10, 0)` in the new map and increments the counter.

</details>

<details>
<summary>UsePortal</summary>

**Execution Scope**: `Server`  

**Purpose**  
Opens the reservation panel for the player who used the portal.

**Parameters**  
- `userId` *(string)* – The player's ID.

**Behavior**  
- Calls `_UIManager:ShowDungeonReservation({}, userId)` (empty list, shown on that user's client).

</details>

<details>
<summary>EnterReservation</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves the whole reserved party into their dynamic instance.

**Parameters**  
- `reservedMap` *(string)* – Instance name stored on the requesting client.

**Behavior**  
1. If `reservedMap` is `nil`, shows "No Registered Users" to the sender and returns.  
2. Gets the reserved users; if the sender (`senderUserId`) is not among them, shows "Please Register First".  
3. Clears the sender's local reservation, calls `_SceneManager:MoveReservationsToMap(reservedMap)`, and hides the reservation UI for every moved user.

</details>

<details>
<summary>ReserveMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Reserves a slot for the user in an open instance.

**Parameters**  
- `userId` *(string)* – The player's ID.

**Behavior**  
1. Calls `_SceneManager:ReserveDynamicMap(userId, self.mapName)`.  
2. Calls `UpdateLocalReservation(mapName, userId)` to store the instance name on that user's client.

</details>

<details>
<summary>UpdateLocalReservation</summary>

**Execution Scope**: `Client`  

**Purpose**  
Remembers which instance the local player is reserved for.

**Parameters**  
- `reservedMap` *(string)* – Instance name, or `nil` to clear.

</details>

<details>
<summary>UnReserveMap</summary>

**Execution Scope**: `Client`  

**Purpose**  
Cancels the local player's reservation.

**Parameters**  
- `userId` *(string)* – The player's ID.

**Behavior**  
1. Returns if `_T.reservedMap` is `nil`.  
2. Calls `_SceneManager:UnregisterDynamicMap(reservedMap, userId)` and clears the local reservation.

</details>

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandlePortalUseEvent` | `PortalUseEvent` | Self (PortalComponent) / Client | If the portal has no destination (`PortalEntityRef`), calls `UsePortal` for the player. |
| `HandleKeyDownEvent` | `KeyDownEvent` | InputService / Client | Temporary shortcut: pressing **F** calls `EnterReservation`. |
| `HandlePlayerRegisteredEvent` | `PlayerRegisteredEvent` | LocalPlayer / ClientOnly | Toggles the reservation: unreserves if `registered` is true, otherwise reserves. |
| `HandleEnterDungeonEvent` | `EnterDungeonEvent` | LocalPlayer / ClientOnly | Calls `EnterReservation` with the local reserved map. |
| `HandleUserLeaveEvent` | `UserLeaveEvent` | UserService / Server | Looks up the leaving user's instance and calls `UnReserveMap`. |

---

### Related Types & Services

- **PortalComponent** – Native base class providing `PortalEntityRef` and `PortalUseEvent`.  
- **_SceneManager** – Instance creation, reservations, and movement.  
- **_UIManager** – Shows/hides the reservation UI and popups.  
- **_DynamicMapService / _TeleportService** – Used by the legacy `EnterMap`.  
- **PlayerRegisteredEvent / EnterDungeonEvent** – Sent by the reservation UI.  

---

## DynamicMapReservedEvent

**Purpose**  
Custom event used to notify players when the reservation list of a **dynamic map instance** changes.  
Carries the destination instance ID and the list of users who are part of the reservation.

---

### Properties

| Property        | Type    | Purpose |
|-----------------|---------|---------|
| `mapInstanceId` | string  | The name of the reserved dynamic map instance (e.g., `"DungeonMap_1"`). |
| `users`         | table   | A table of user IDs that are part of this reservation group. |

---

### Usage

- Sent by `SceneManager.SendReservationEvent` (on each affected user's client) from `ReserveDynamicMap` and `UnregisterDynamicMap`.
- Handled by `InstanceDungeonReservationUI` to update the registered state and refresh the party list.

---

## DynamicPortalUseEvent

**Purpose**  
Custom event intended to signal that a player interacted with a **dynamic map portal**.

---

### Properties

This event has no custom properties.

---

### Usage

- Not currently sent or handled by any script in this project; `DynamicMapPortal` reacts to the native `PortalUseEvent` instead.

---

## EnterDungeonEvent

**Purpose**  
Custom event used to signal that the local player wants to move the reserved party into the **dungeon** instance.

---

### Properties

This event has no custom properties.

---

### Usage

- Sent to the local player entity by `InstanceDungeonReservationUI.EnterDungeon` when the **Enter** button is clicked.
- Handled by `DynamicMapPortal.HandleEnterDungeonEvent`, which calls `EnterReservation`.

---

## PlayerRegisteredEvent

**Purpose**  
Custom event sent when the player toggles their **reservation registration**.  
Carries the player’s ID and whether they were registered at the time of the click.

---

### Properties

| Property   | Type    | Purpose |
|------------|---------|---------|
| `userId`   | string  | The ID of the local player. |
| `registered` | boolean | The registration state before the click: `true` means the player should be unregistered, `false` means they should be reserved. |

---

### Usage

- Sent to the local player entity by `InstanceDungeonReservationUI.PlayerRegistered` (Register button, or closing the panel while registered).
- Handled by `DynamicMapPortal.HandlePlayerRegisteredEvent`, which calls `UnReserveMap` or `ReserveMap`.

---

## InstanceDungeonReservationUI

**Purpose**  
Client-side UI component that displays the **dungeon reservation** state (who’s in the party and whether the local player is registered).  
Wires the Register, Enter and Close buttons to events handled by `DynamicMapPortal`, and refreshes itself from `DynamicMapReservedEvent`.

---

### Properties

| Property                   | Type    | Purpose |
|---------------------------|---------|---------|
| `reservationUIItemModel`  | string  | Model ID used to spawn a row (`InstanceDungeonReservationUIItem`) for a reserved user. |
| `reservationListItems`    | table   | Pool of spawned row components; reused between refreshes. |
| `reservationScrollParent` | Entity  | UI container under which spawned rows are parented. |
| `buttonRegister`          | Entity  | "Search Party" / "Leave Party" toggle button. |
| `buttonClose`             | Entity  | Closes the panel. |
| `buttonEnter`             | Entity  | Moves the reserved party into the dungeon instance. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|---------|
| `Show` | Client | Shows the panel and renders one row per user ID (by nickname). |
| `GetOrCreateReservationItem` | Client/Server | Returns the next pooled row, spawning one if needed. |
| `OnBeginPlay` | ClientOnly | Connects the three buttons and resets the registered state. |
| `PlayerRegistered` | Client/Server | Sends a `PlayerRegisteredEvent` with the current registered state. |
| `EnterDungeon` | Client | Sends an `EnterDungeonEvent` and resets the registered state. |
| `Hide` | Client | Hides the panel, unregistering the player if registered. |
| `UpdateRegistered` | Client | Stores the registered state and updates the Register button label. |

---

### Methods

<details>
<summary>Show</summary>

**Execution Scope**: `Client`  

**Purpose**  
Displays the reservation panel and renders the provided user list.

**Parameters**  
- `userIds` *(table)* – User IDs currently reserved.

**Behavior**  
1. Resets the row pointer, then for each user gets a row via `GetOrCreateReservationItem`, calls `Init` with the user's nickname (`_UserService.Users[userId].Nickname`) and `Show`.  
2. Hides any leftover pooled rows.  
3. Enables the panel entity.

</details>

<details>
<summary>GetOrCreateReservationItem</summary>

**Execution Scope**: `Client/Server` (called from `Show` on the client)  

**Purpose**  
Returns the pooled row at the current pointer, spawning a new one under `reservationScrollParent` with `_SpawnService:SpawnByModelId` if needed, and advances the pointer.

**Returns**  
- *(InstanceDungeonReservationUIItem)* – The row component.

</details>

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `ClientOnly`  

**Purpose**  
Clears the row pool, connects `buttonRegister` → `PlayerRegistered`, `buttonEnter` → `EnterDungeon`, `buttonClose` → `Hide`, and calls `UpdateRegistered(false)`.

</details>

<details>
<summary>PlayerRegistered</summary>

**Execution Scope**: `Client/Server` (button handler on the client)  

**Purpose**  
Sends a `PlayerRegisteredEvent` (local user ID + current `_T.isRegistered`) to the local player entity. The button label updates later when the server's `DynamicMapReservedEvent` arrives.

</details>

<details>
<summary>EnterDungeon</summary>

**Execution Scope**: `Client`  

**Purpose**  
Sends an `EnterDungeonEvent` to the local player entity and calls `UpdateRegistered(false)`.

</details>

<details>
<summary>Hide</summary>

**Execution Scope**: `Client`  

**Purpose**  
Disables the panel; if the player is still registered, calls `PlayerRegistered` to unregister them.

</details>

<details>
<summary>UpdateRegistered</summary>

**Execution Scope**: `Client`  

**Purpose**  
Sets the Register button text to "Leave Party" or "Search Party" and stores the state in `_T.isRegistered`.

**Parameters**  
- `isRegistered` *(boolean)* – Whether the local user is currently in the reservation.

</details>

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandleDynamicMapReservedEvent` | `DynamicMapReservedEvent` | Logic / SceneManager | Updates the registered state; if the panel is open, shows the full party when the local player is registered, otherwise an empty list. |

---

### Related Types & Services

- **InstanceDungeonReservationUIItem** – Row component spawned per reserved user.  
- **DynamicMapReservedEvent** – Event payload this UI listens to (`mapInstanceId`, `users`).  
- **PlayerRegisteredEvent / EnterDungeonEvent** – Events this UI sends to the local player.  
- **_UserService** – Local user ID and nicknames.  
- **_Utils** – Provides `Contains(table, value)`.  
- **_SpawnService** – Spawns row entities.  

---

## InstanceDungeonReservationUIItem

**Purpose**  
Represents a single **reservation list item** in the dungeon UI.  
Displays a player’s name in a text field and provides simple show/hide controls for the row.

---

### Properties

| Property    | Type          | Purpose |
|-------------|---------------|---------|
| `userText`  | `TextComponent` | UI text component used to display the player's name in this row. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|----------|
| `Init` | Client/Server | Sets the row's text. |
| `Show` | Client/Server | Enables the row entity, making the UI item visible. |
| `Hide` | Client/Server | Disables the row entity, hiding the UI item. |

---

### Methods

<details>
<summary>Init</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Initializes the UI row with the given text.

**Parameters**  
- `userId` *(string)* – Text to display in `userText` (the caller passes the player's nickname).

**Behavior**  
1. Sets `self.userText.Text = userId`.

</details>

<details>
<summary>Show</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Makes this list item visible by calling `self.Entity:SetEnable(true)`.

</details>

<details>
<summary>Hide</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Hides this list item by calling `self.Entity:SetEnable(false)`.

</details>

---

### Related Types & Services

- **InstanceDungeonReservationUI** – Parent list that spawns, pools and drives these rows.  
- **TextComponent** – Displays the player's name.  

---

## Monster

**Purpose**  
Represents a monster entity with health, death, and respawn mechanics. Handles taking damage and state changes, including optional automatic respawn. Base class of `InstanceDungeonMonster`.

---

### Properties

| Property         | Type    | Purpose |
|-----------------|---------|---------|
| `MaxHp`          | number  | Maximum health points of the monster. Sync. |
| `Hp`             | number  | Current health points. Sync. |
| `RespawnOn`      | boolean | Whether the monster should automatically respawn after death. Sync. |
| `IsDead`         | boolean | Tracks if the monster is dead. Sync, HideFromInspector. |
| `RespawnDelay`   | number  | Time in seconds before the monster respawns if `RespawnOn` is true. Sync. |
| `DestroyDelay`   | number  | Delay before the monster entity is hidden (and destroyed if not respawning) after death. Sync. |

---

### Method Summary

| Method          | Execution Scope | Purpose |
|-----------------|----------------|---------|
| `OnBeginPlay`   | Client/Server  | Initializes `Hp` to `MaxHp` when the entity spawns. |
| `Dead`          | ServerOnly     | Handles monster death: changes state, hides the entity, and optionally destroys it. |
| `Respawn`       | ServerOnly     | Resets the monster to alive state and restores HP and visibility. |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Sets `Hp` to `MaxHp`.

</details>

<details>
<summary>Dead</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Handles the monster's death sequence.

**Behavior**  
1. Sets `IsDead` to `true`.  
2. Changes the entity’s state to `"DEAD"` if a `StateComponent` exists.  
3. After `DestroyDelay`, hides and disables the entity.  
4. If `RespawnOn` is `false`, the entity is destroyed at that point.

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

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandleHitEvent` | `HitEvent` | Self / ServerOnly | Subtracts `TotalDamage` from `Hp`; on the hit that drops HP to 0 or below, calls `Dead()` and schedules `Respawn()` after `RespawnDelay` if `RespawnOn`. |

---

### Related Types & Services
- **_TimerService** – Schedules delayed hide and respawn.  
- **StateComponent** – Manages the entity's current state.  
- **HitEvent** – Event triggered when the monster receives damage.  

---

## InstanceDungeonMonster

**Purpose**  
Boss monster for a **dynamic dungeon instance** (extends `Monster`).  
Plays start/finish effects and, on death, closes the dynamic map and returns players to a configured map.  

---

### Properties

| Property            | Type    | Purpose |
|---------------------|---------|---------|
| `returnMapOnDefeat` | string  | Static map name to return players to when the boss is defeated and the dynamic map is closed. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|----------|
| `Dead` | ServerOnly | Marks the monster as dead, switches to `DEAD` state, schedules hide/disable, and triggers `BossFinish`. |
| `OnBeginPlay` | Client/Server | Runs `Monster.OnBeginPlay` and plays the start effect (`BossStart`). |
| `BossStart` | Client | Plays the encounter-start effect above the boss. |
| `BossFinish` | Client | Plays the finish effect and, after a delay, closes the dynamic map via `SceneManager`. |

---

### Methods

<details>
<summary>Dead</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Handles server-side death logic and kicks off the defeat sequence. Overrides `Monster.Dead`; the entity is never destroyed here because the whole instance is destroyed instead.

**Behavior**  
1. Sets `self.IsDead = true`.  
2. If a `StateComponent` exists, calls `ChangeState("DEAD")`.  
3. Schedules hide/disable after `DestroyDelay`.  
4. Calls `self:BossFinish()` (runs on clients).  

</details>

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Runs when the monster spawns; performs base initialization and starts the encounter.

**Behavior**  
1. Calls `__base:OnBeginPlay()` (from `Monster`).  
2. Calls `self:BossStart()`.  

</details>

<details>
<summary>BossStart</summary>

**Execution Scope**: `Client`  

**Purpose**  
Calls `_EffectService:PlayEffect` with effect `"eee4571f66ff4acf8b7f3878860642fa"` at `TransformComponent.Position + Vector3.up * 2`.

</details>

<details>
<summary>BossFinish</summary>

**Execution Scope**: `Client`  

**Purpose**  
Signals the end of the encounter, then closes the dynamic map and returns players to a static map.

**Behavior**  
1. Plays finish effect `"ad24ef5bcf8448fa9713a868aff14d6d"` two units above the boss.  
2. After `DestroyDelay * 2` seconds, calls `_SceneManager:CloseDynamicMap(self.Entity.CurrentMap.Name, self.returnMapOnDefeat)`.  

</details>

---

### Related Types & Services

- **Monster** – Base class providing HP, `IsDead`, `DestroyDelay`, and hit handling.  
- **StateComponent** – State transitions (`ChangeState("DEAD")`).  
- **_TimerService** – Schedules delayed hide and map closing.  
- **_EffectService** – Plays start/finish effects.  
- **_SceneManager** – Closes the dynamic map and routes players to `returnMapOnDefeat`.  

---

## MonsterAttack

**Purpose**  
Handles automatic contact attacks for monster entities based on their sprite size, creating a hitbox and repeatedly attacking overlapping player entities.

---

### Properties

| Property         | Type      | Purpose |
|-----------------|-----------|---------|
| `AttackInterval` | number    | Time interval between repeated attacks (seconds). Must be ≥ 0. |
| `Shape`          | any       | The hitbox shape used for attacks. HideFromInspector. |
| `SpriteSize`     | Vector2   | Size of the monster's sprite, used to define attack area. HideFromInspector. |
| `PositionOffset` | Vector2   | Offset from the monster’s position to align the attack hitbox. HideFromInspector. |

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
1. Checks if the entity has a `Monster` component; returns if missing.  
2. Initializes `Shape` as a default `BoxShape`.  
3. Preloads the monster’s sprite and uses the first animation frame to calculate `SpriteSize` and `PositionOffset`.  
4. Starts a repeated timer that calls `AttackNear` every `AttackInterval` seconds while the monster is alive.

</details>

<details>
<summary>AttackNear</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Performs an attack on nearby players using a dynamically calculated hitbox.

**Behavior**  
1. Reads the entity’s `TransformComponent` (world position, scale, rotation).  
2. Rotates and scales `PositionOffset`, then updates `Shape` size, position, and angle.  
3. Calls `AttackFast` with the hitbox and `CollisionGroups.Player`.

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
- **AttackComponent** – Base class for attack logic and hit detection.  
- **_ResourceService** – Loads the sprite's animation clip.  
- **_TimerService** – Schedules repeated attacks.  
- **BoxShape** – Rectangular hitbox with size, position, and rotation.  
- **CollisionGroups** – Collision groups (`Player` here).  

---

## PlayerAttack

**Purpose**  
Handles a basic player attack, placing a hitbox in front of the player and attacking monsters when the Attack action is used. Supports critical hits and fixed damage.

---

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `Shape`  | any  | Hitbox shape used for attacks. HideFromInspector. |

---

### Method Summary

| Method               | Execution Scope  | Purpose |
|---------------------|-----------------|---------|
| `OnBeginPlay`        | ServerOnly      | Initializes the attack hitbox shape. |
| `AttackNormal`       | ServerOnly      | Performs a basic attack in front of the player against monsters. |
| `CalcDamage`         | Client/Server   | Returns a fixed damage value of `50`. |
| `CalcCritical`       | Client/Server   | 30% chance of a critical hit. |
| `GetCriticalDamageRate` | Client/Server | Returns the critical damage multiplier (`2`). |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Creates a `BoxShape` at `(0,0)` with size `(1,1)` and rotation `0`, stored in `Shape`.

</details>

<details>
<summary>AttackNormal</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Executes a basic attack in front of the player.

**Behavior**  
1. Retrieves `PlayerControllerComponent` and `TransformComponent`.  
2. Positions `Shape` 0.5 units in the look direction and 0.5 units up from the player.  
3. Calls `AttackFast` with the hitbox and `CollisionGroups.Monster`.

</details>

<details>
<summary>CalcDamage</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Returns a fixed damage value of `50`.

</details>

<details>
<summary>CalcCritical</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Returns `_UtilLogic:RandomDouble() < 0.3` (30% critical chance).

</details>

<details>
<summary>GetCriticalDamageRate</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Returns `2` (critical hits deal double damage).

</details>

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandlePlayerActionEvent` | `PlayerActionEvent` | Self / ServerOnly | Calls `AttackNormal` when `ActionName` is `"Attack"`. |

---

### Related Types & Services
- **AttackComponent** – Base class for attacks and hit detection.  
- **BoxShape** – Rectangular attack hitbox.  
- **CollisionGroups** – Collision layers (`Monster` here).  
- **_UtilLogic** – Random number generation.  
- **PlayerControllerComponent** – Provides the look direction.  

---

## PlayerHit

**Purpose**  
Handles the player's hit detection and damage immunity timing, ensuring the player cannot be hit repeatedly within a short interval.

---

### Properties

| Property           | Type    | Purpose |
|-------------------|---------|---------|
| `ImmuneCooldown`  | number  | Minimum time (in seconds) between consecutive hits. Must be ≥ 0. |
| `LastHitTime`     | number  | Timestamp of the last time the player was hit. HideFromInspector. |

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
- `attackInfo` *(string)* – Optional information about the attack. Not used.

**Behavior**  
1. Gets the current time from `_UtilLogic.ElapsedSeconds`.  
2. If `LastHitTime + ImmuneCooldown` is earlier than the current time, updates `LastHitTime` and returns `true`.  
3. Otherwise returns `false`.

</details>

---

### Related Types & Services
- **HitComponent** – Base class for entities that can be hit.  
- **_UtilLogic** – Provides elapsed time in seconds.  

---

## Popupbutton

**Purpose**  
Sample UI script that opens one of four popup panels under `/ui/Popup` when its matching button is clicked, and wires the panel's close button(s) to hide it again. Not used by the dynamic map flow.

---

### Properties

This script has no custom properties.

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandleButtonClickEvent` | `ButtonClickEvent` | Entity `a721af74-...` / ClientOnly | Button named `"1"`: opens `GameResultPanel`; `Btn_Ok_1` closes it. |
| `HandleButtonClickEvent2` | `ButtonClickEvent` | Entity `02210bfa-...` / ClientOnly | Button named `"2"`: opens `InventoryUI`; `CloseButton` closes it. |
| `HandleButtonClickEvent3` | `ButtonClickEvent` | Entity `5ec12c2a-...` / ClientOnly | Button named `"3"`: opens `BasicPopup2`; `CloseButton` closes it. |
| `HandleButtonClickEvent4` | `ButtonClickEvent` | Entity `e07d430e-...` / ClientOnly | Button named `"4"`: opens `BasicPopup`; `Btn_Ok` and `Btn_No` close it. |

---

### Related Types & Services
- **_EntityService** – Looks up popup entities by path.  
- **ButtonClickEvent** – Native UI button click event.  

---

## Changelog
| Version | Changelog | Download |
|---------|-------------|-------------|
| V1_0 | Initial release with dynamic map and party reservation systems. | [Download](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/raw/refs/heads/main/Dynamic_Maps/DynamicMapsTutorial.mod) |
