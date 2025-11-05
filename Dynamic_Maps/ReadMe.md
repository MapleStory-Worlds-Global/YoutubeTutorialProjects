# Dynamic Maps Demo 

## Youtube Tutorial
[![Youtube Tutorial](https://img.youtube.com/vi/RY1q4Pv7_B8/0.jpg)](https://youtu.be/RY1q4Pv7_B8?si=B_jZJq-9vyWQ4Z3U "Working With Dynamic Maps")
[![Youtube Tutorial](https://img.youtube.com/vi/LNVUPFfZM7k/0.jpg)](https://youtu.be/LNVUPFfZM7k?si=AOT4SU4U9lXotoKO "Let’s create a PQ Dungeon with Dynamic Maps! Part 2")
[![Youtube Tutorial](https://img.youtube.com/vi/wnDo6Bn17Ek/0.jpg)](https://youtu.be/wnDo6Bn17Ek?si=Y9EGrOUec15plb-c "Let’s create a PQ Dungeon with Dynamic Maps! Part 2")

## Mod Installation Instructions
[Mod File Installation Guide](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects?tab=readme-ov-file#project-import-instructions)

## Project File
[Download Latest Project File](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/blob/8a5f8acb5b51b595adf2175bad948005b8be92db/Instance_Maps/Room_World_Instance_Demo.mod)

---

## Table of Contents
- [Overview](#overview)
- [Script Documentation](#script-documentation)
  - [SceneManager](#scenemanager)
  - [UIManager](#uimanager)
  - [Utils](#utils)
  - [DynamicMapInfo](#dynamicmapinfo)
  - [DynamicMapPortal](#dynamicmapportal)
  - [DynamicMapReservedEvent](#dynamicmapreservedevent)
  - [DynamicPortalUseEvent](#dynamicportaluseevent)
  - [EnterDungeonEvent](#enterdungeonevent)
  - [InstanceDungeonReservationUI](#instancedungeonreservationui)
  - [InstanceDungeonReservationUIItem](#instancedungeonreservationuiitem)
  - [InstanceDungeonReservationUIItem (duplicate section)](#instancedungeonreservationuiitem-1)
  - [PlayerRegisteredEvent](#playerregisteredevent)
  - [InstanceDungeonMonster](#instancedungeonmonster)
- [Changelog](#changelog)

---
## Overview
This project is provided as supplemental resource for the "Let’s create a PQ Dungeon with Dynamic Maps!" tutorial series. 

Dynamic Maps open the door to infinite possibilities — from dungeons and event rooms to boss raids that change every run. Whether you’re a creator or a player, this is the feature that takes your world-building to the next level. Let's learn!

---

## Script Documentation

---
## SceneManager

**Purpose**  
Manages **dynamic map instances** by tracking user reservations, initializing maps with capacity limits, moving users into dynamically created instances, and handling clean-up when users leave or maps are closed.  
Supports multiplayer scenes where unique instance names are generated and associated with reserved users.

---

### Properties

| Property          | Type   | Purpose |
|-------------------|--------|---------|
| `dynamicMapInfos` | `table` | Holds `DynamicMapInfo` objects for each map type (indexed by base map name). Stores state like current reservations, user slots, and active instances. Initialized as an empty table `{}`. |

---

### Method Summary

| Method                    | Execution Scope | Purpose |
|---------------------------|-----------------|---------|
| `CreateDynamicMap(mapName)` | Server | Returns a new dynamic instance name if the map is initialized. Otherwise logs a warning and returns `nil`. |
| `ReserveDynamicMap(userId, mapName)` | Server | Reserves a spot for the user in a dynamic instance of the specified map. |
| `InitDynamicMap(mapName, maxPlayers)` | Server | Initializes internal tracking for the given map type, setting up a new `DynamicMapInfo`. |
| `MoveReservationsToMap(mapInstanceName)` | Server | Moves all users currently reserved for a given map instance into that instance. |
| `SendReservationEvent(mapInstanceName, users)` | Client | Sends a `DynamicMapReservedEvent` to users once their map reservation is confirmed. |
| `GetReservedUsers(mapInstanceName)` | Server | Returns the list of currently reserved users for a given dynamic map instance. |
| `UnregisterDynamicMap(mapInstanceName, userId)` | Server | Removes a user's reservation for the specified instance. |
| `FindInstanceName(mapName, userId)` | Server | Retrieves the instance name where the given user is reserved. |
| `CloseDynamicMap(mapInstanceName, targetMap)` | Server | Ends a dynamic map instance and optionally moves players to a fallback/target map. |
| `HandleUserLeaveEvent(event)` | Client/Server | Handles native leave events to remove users from reservation lists. |

---

### Methods

<details>
<summary>CreateDynamicMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Generates a unique map instance name if the map has been previously initialized via `InitDynamicMap`.

**Parameters**  
- `mapName` *(string)* – The base name of the map to instantiate.

**Returns**  
- *(string)* – Instance name if successful, or `nil` if the map is uninitialized.

</details>

---

<details>
<summary>ReserveDynamicMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Reserves a spot for the given user in a dynamic map instance. Must be initialized beforehand.

**Parameters**  
- `userId` *(string)* – The user's ID to reserve a space for.  
- `mapName` *(string)* – The base map name.

**Returns**  
- *(string)* – Name of the reserved instance or `nil` on failure.

</details>

---

<details>
<summary>InitDynamicMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Initializes a dynamic map by associating it with a `DynamicMapInfo` object and defining max player capacity.

**Parameters**  
- `mapName` *(string)* – Name of the base map to initialize.  
- `maxPlayers` *(integer)* – Maximum number of players for this dynamic instance.

</details>

---

<details>
<summary>MoveReservationsToMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Transfers all users with active reservations into the specified dynamic instance.

**Parameters**  
- `mapInstanceName` *(string)* – The full dynamic name of the instance (e.g., `"DungeonMap_001"`).

</details>

---

<details>
<summary>SendReservationEvent</summary>

**Execution Scope**: `Client`  

**Purpose**  
Sends a `DynamicMapReservedEvent` to a list of users to confirm their reservation and destination instance.

**Parameters**  
- `mapInstanceName` *(string)* – Instance name the users were assigned.  
- `users` *(table)* – List of user IDs to notify.

</details>

---

<details>
<summary>GetReservedUsers</summary>

**Execution Scope**: `Server`  

**Purpose**  
Returns all users who have active reservations in the given dynamic instance.

**Parameters**  
- `mapInstanceName` *(string)* – Dynamic map name.

**Returns**  
- *(table)* – List of reserved user IDs.

</details>

---

<details>
<summary>UnregisterDynamicMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Removes a user's reservation entry from the internal dynamic map tracking structure.

**Parameters**  
- `mapInstanceName` *(string)* – Full name of the instance.  
- `userId` *(string)* – User ID to remove.

</details>

---

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

---

<details>
<summary>CloseDynamicMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Closes the specified instance and can optionally warp all users to a fallback static map.

**Parameters**  
- `mapInstanceName` *(string)* – Dynamic instance to shut down.  
- `targetMap` *(string)* – Optional static fallback map name.

</details>

---

<details>
<summary>HandleUserLeaveEvent</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Native system callback when users leave the game; cleans up any active reservations for that user.

**Parameters**  
- `event` *(UserLeaveEvent)* – Contains the leaving user’s ID.

</details>

---

### Related Types & Services
- **`DynamicMapInfo`** – Custom type holding instance capacity, user list, and allocation logic.  
- **`DynamicMapReservedEvent`** – Event emitted when reservation is complete.  
- **`_UtilLogic`** – Used for various utility functions including string manipulation functions like `Split()`.  
- **`UserLeaveEvent`** – Native user departure event.

## UIManager

**Purpose**  
Central controller for showing and hiding **UI elements** related to dynamic dungeon instance reservations.  
Includes popup messages and a dedicated dungeon reservation UI component that displays users scheduled to enter a dungeon.

---

### Properties

| Property        | Type                         | Purpose |
|-----------------|------------------------------|---------|
| `instanceDungeon` | `InstanceDungeonReservationUI` | Reference to the UI component that displays reserved dungeon users. Default entity ID: `"a869fc03-e701-48a3-8758-333f4c28a086"`. |

---

### Method Summary

| Method                    | Execution Scope | Purpose |
|---------------------------|-----------------|---------|
| `ShowPopup(message)`      | Client          | Displays a popup dialog showing the given message string. |
| `ShowDungeonReservation(users)` | Client   | Displays the dungeon reservation UI with a list of user names or IDs. |
| `HideDungeonReservation()` | Client         | Hides the reservation UI. |

---

### Methods

<details>
<summary>ShowPopup</summary>

**Execution Scope**: `Client`  

**Purpose**  
Displays a generic popup message to the user.

**Parameters**  
- `message` *(string)* – Text to show in the popup.

**Behavior**  
- Likely uses `_UIPopup:Open()` or similar internal utility (inferred from naming convention).

</details>

---

<details>
<summary>ShowDungeonReservation</summary>

**Execution Scope**: `Client`  

**Purpose**  
Displays the reservation UI and populates it with a list of reserved users for an upcoming instance.

**Parameters**  
- `users` *(table)* – List of user names or IDs to show in the UI.

**Behavior**  
- Activates the `instanceDungeon` component and injects the `users` list into it for display.

</details>

---

<details>
<summary>HideDungeonReservation</summary>

**Execution Scope**: `Client`  

**Purpose**  
Closes or hides the dungeon reservation UI from view.

**Behavior**  
- Calls `self.instanceDungeon:Hide()` or disables visibility (assumed based on standard practice).

</details>

---

### Related Types & Services
- **`InstanceDungeonReservationUI`** – Custom UI component responsible for displaying the user reservation list for dungeon entry.
- **`_UIPopup`** - Modal popup system for alerts, confirmations, or messages.

## Utils

**Purpose**  
Provides utility logic functions for use across the game logic layer.  
Currently includes basic list-checking functionality.

---

### Properties

_No properties defined in this script._

---

### Method Summary

| Method        | Execution Scope | Purpose |
|---------------|-----------------|---------|
| `Contains(targetList, toCheck)` | Client/Server | Returns whether the `targetList` contains the specified value. |

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
- Iterates through the `targetList`.  
- If `value == toCheck`, returns `true` immediately.  
- If loop completes without a match, returns `false`.

</details>

---

### Related Types & Services
- **Logic Script** – This is a generic logic script (`extends Logic`), intended to be reused by other scripts for common operations.

## DynamicMapInfo

**Purpose**  
Coordinates creation and lifecycle of dynamic (instanced) maps derived from a base map.  
Manages instance generation, capacity, reservations, and player movement while cleaning up instances when empty.  

---

### Properties

| Property  | Type                      | Purpose |
|------------|---------------------------|----------|
| `users`    | `table<string, table<string>>` | Maps `instanceName -> { userId, ... }` for active players inside each dynamic instance. |
| `reservations`  | `table<string, table<string>>` | Maps `instanceName -> { userId, ... }` for players reserved to enter a given instance. |
| `mapName`  | `string`                   | Base/static map name used as a template for generating dynamic instances. |
| `maxPlayers` | `number`                | Maximum number of players allowed in a single dynamic instance. |
| `idx`      | `integer`                 | Monotonic counter for generating unique instance names (e.g., `map_1`, `map_2`). |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|----------|
| `Init(maxPlayers, mapName)` | Server | Initializes base map, capacity, and tracking tables for instances. |
| `GenerateMap()` | Server | Creates a new dynamic instance based on `mapName` and returns its `instanceName`. |
| `EnterMap(instanceName, userId)` | Server | Adds a user to an instance (capacity-checked) and teleports them into it. |
| `LeaveMap(userId, instanceName)` | Server | Removes a user from an instance; destroys the instance if it becomes empty. |
| `FindOpenMap()` | Server | Returns the first instance name with available capacity, considering reservations. |
| `ReserveMap(instanceName, userId)` | Server | Reserves a seat for a user in an instance (no immediate teleport). |
| `MoveReservations(instanceName)` | Server | Admits all reserved users into the instance and clears reservations. |
| `GetReservations(instanceName)` | Server | Retrieves the reservation list for an instance. |
| `UnregisterMap(instanceName, userId)` | Server | Removes a specific user’s reservation from an instance. |
| `GetInstance(userId)` | Server | Returns the instance containing or reserving the given user, or `nil`. |
| `CloseDynamicMap(instanceName, targetMap)` | Server | Teleports all users to `targetMap` and destroys the dynamic instance. |

---

### Methods

<details>
<summary>Init</summary>

**Execution Scope**: `Server`  

**Purpose**  
Initializes dynamic instance metadata and tracking.

**Parameters**  
- `maxPlayers` *(integer)* – Maximum players per instance.  
- `mapName` *(string)* – Base/static map used to spawn instances.

**Behavior**  
1. Stores `mapName` and `maxPlayers`.  
2. Resets `users` and `reservations` tables.  

</details>

---

<details>
<summary>GenerateMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Creates a new dynamic instance using the base map.

**Parameters**  
- *(none)*

**Behavior**  
1. Builds `instanceName = mapName .. "_" .. idx`, then increments `idx`.  
2. Calls `_DynamicMapService:CreateDynamicMap(mapName, instanceName)`.  
3. Initializes `users[instanceName] = {}`.  
4. Returns `instanceName`.  

</details>

---

<details>
<summary>EnterMap</summary>

**Execution Scope**: `Server`  

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

---

<details>
<summary>LeaveMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Removes a user from an instance; destroys it if no users remain.

**Parameters**  
- `userId` *(string)* – Player ID.  
- `instanceName` *(string)* – Instance name.

**Behavior**  
1. Removes `userId` from `users[instanceName]`.  
2. If the instance becomes empty, calls `_DynamicMapService:DestroyDynamicMap(instanceName)` and clears tracking.  

</details>

---

<details>
<summary>FindOpenMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Finds an instance with available capacity (users + reservations).

**Parameters**  
- *(none)*

**Behavior**  
1. Iterates known instances and computes `count = #users + #reservations`.  
2. Returns the first `instanceName` where `count < maxPlayers`, or `nil`.  

</details>

---

<details>
<summary>ReserveMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Queues a player for entry into an instance.

**Parameters**  
- `instanceName` *(string)* – Target instance name.  
- `userId` *(string)* – Player ID.

**Behavior**  
1. Ensures `reservations[instanceName]` exists.  
2. Inserts `userId` into the reservation list.  

</details>

---

<details>
<summary>MoveReservations</summary>

**Execution Scope**: `Server`  

**Purpose**  
Admits all reserved players into an instance.

**Parameters**  
- `instanceName` *(string)* – Instance name.

**Behavior**  
1. For each reserved `userId`, calls `EnterMap(instanceName, userId)`.  
2. Clears `reservations[instanceName]`.  

</details>

---

<details>
<summary>GetReservations</summary>

**Execution Scope**: `Server`  

**Purpose**  
Retrieves reservations for an instance.

**Parameters**  
- `instanceName` *(string)* – Instance name.

**Behavior**  
1. Returns `reservations[instanceName]` or `nil` if none.  

</details>

---

<details>
<summary>UnregisterMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Removes a specific user’s reservation from an instance.

**Parameters**  
- `instanceName` *(string)* – Instance name.  
- `userId` *(string)* – Player ID.

**Behavior**  
1. Finds and removes `userId` from `reservations[instanceName]` if present.  

</details>

---

<details>
<summary>GetInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Locates the instance that contains or reserves a given user.

**Parameters**  
- `userId` *(string)* – Player ID.

**Behavior**  
1. Scans `users` for membership.  
2. If not found, scans `reservations`.  
3. Returns the matching `instanceName` or `nil`.  

</details>

---

<details>
<summary>CloseDynamicMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Teleports all users out and destroys the instance.

**Parameters**  
- `instanceName` *(string)* – Instance name.  
- `targetMap` *(string)* – Static map to receive users.

**Behavior**  
1. Teleports each user in `users[instanceName]` to `targetMap` at `Vector3(0, 10, 0)`.  
2. Clears tracking and calls `_DynamicMapService:DestroyDynamicMap(instanceName)`.  

</details>

---

### Related Types & Services

- **_DynamicMapService** – Creates and destroys dynamic instances.  
- **_TeleportService** – Teleports players to map positions.  
- **_UserService** – Resolves `userId` to player entity.  
- **_Utils** – Helpers for membership checks and table ops.  

---

## DynamicMapPortal

**Purpose**  
Coordinates player interaction with portals that route into dynamic (instanced) maps.  
Manages instance discovery/creation, group reservation, and teleportation flow using DynamicMap services.  

---

### Properties

| Property  | Type                      | Purpose |
|------------|---------------------------|----------|
| `targetBaseMap`    | `string` | Base/static map used as the template for new dynamic instances. |
| `maxPlayers`  | `number`                   | Capacity hint used when selecting or creating target instances for this portal. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|----------|
| `OnUse(userId)` | Server | Handles a player activating the portal; ensures/open-finds an instance and teleports the user. |
| `TeleportToTarget(userId, mapName, customPortalEntity)` | Server | Teleports a single player to the portal’s target (static or instance) entry point. |
| `ReturnUserToHub(userId, mapName)` | Server | Returns a player from an instance to a designated hub/static map. |
| `CreateOrEnterInstance(userId, instanceName, mapName)` | Server | Ensures a dynamic instance exists and moves the user into its entry map. |
| `InitPortal(targetBaseMap, maxPlayers)` | Server | Initializes portal metadata, including base map and capacity hint. |
| `CreateInstance(instanceName)` | Server | Creates a new dynamic instance for the portal’s base map. |
| `FindOpenInstance(instanceName, usersToMove)` | Server | Locates an instance with space for additional users, considering capacity. |
| `MoveUsersIntoInstance(users, instanceName, mapName, withEvent)` | Server | Moves multiple users through the portal into an instance and optionally emits an event. |
| `CreateSharedNumber(space, variableName, variableValue)` | Client/Server | Creates a shared numeric counter (e.g., usage metrics) in shared memory. |
| `GetSharedNumber(space, variableName)` | Client/Server | Reads a shared numeric counter, creating it if missing. |
| `UpdateSharedNumber(space, variableName, variableValue)` | Client/Server | Atomically updates a shared numeric counter with concurrency safety. |

---

### Methods

<details>
<summary>OnUse</summary>

**Execution Scope**: `Server`  

**Purpose**  
Handles a player activating the portal and routes them to a suitable dynamic instance.

**Parameters**  
- `userId` *(string)* – The player’s ID.  

**Behavior**  
1. Calls `FindOpenInstance(targetBaseMap, 1)` to locate capacity.  
2. If none found, calls `CreateInstance(targetBaseMap)`.  
3. Calls `MoveUsersIntoInstance({userId}, instanceName, targetBaseMap, nil)`.

</details>

---

<details>
<summary>TeleportToTarget</summary>

**Execution Scope**: `Server`  

**Purpose**  
Transfers a player to a specific map or portal entry within the same space.

**Parameters**  
- `userId` *(string)* – The player’s ID.  
- `mapName` *(string)* – Destination map name.  
- `customPortalEntity` *(Entity)* – Optional portal entity; if `nil`, resolves a map path entity.

**Behavior**  
1. Resolves a target entity using `_EntityService:GetEntityByPath("/maps/" .. mapName)` if `customPortalEntity` is `nil`.  
2. Validates the target exists.  
3. Calls `_TeleportService:TeleportToEntity(user, targetEntity)`.  
4. Logs a warning if teleportation fails.

</details>

---

<details>
<summary>ReturnUserToHub</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves an individual player from a dynamic instance back to a designated hub/static map.

**Parameters**  
- `userId` *(string)* – Player ID.  
- `mapName` *(string)* – Hub/static map to return to.

**Behavior**  
1. Logs the action.  
2. Calls `_RoomService:MoveUserToStaticRoom(userId, mapName)`.

</details>

---

<details>
<summary>CreateOrEnterInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Creates or reuses a dynamic instance for the portal and moves the player into it.

**Parameters**  
- `userId` *(string)* – Player ID.  
- `instanceName` *(string)* – Instance key/prefix for grouping instances.  
- `mapName` *(string)* – Entry map within the instance.

**Behavior**  
1. Calls `_RoomService:GetOrCreateInstanceRoom(instanceName)` to ensure availability.  
2. Calls `instanceRoom:MoveUser(userId)`.

</details>

---

<details>
<summary>InitPortal</summary>

**Execution Scope**: `Server`  

**Purpose**  
Initializes portal metadata and capacity hint.

**Parameters**  
- `targetBaseMap` *(string)* – Base map used when creating dynamic instances.  
- `maxPlayers` *(integer)* – Maximum player count per instance (hint for selection).

**Behavior**  
1. Stores `targetBaseMap` and `maxPlayers`.  
2. Prepares the portal for instance creation and user movement.

</details>

---

<details>
<summary>CreateInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Creates a new dynamic instance derived from the portal’s base map.

**Parameters**  
- `instanceName` *(string)* – Instance type/prefix to create.

**Behavior**  
1. Validates that the portal has been initialized.  
2. Calls `_RoomService:CreateInstanceRoom()` or `_DynamicMapService:CreateDynamicMap(targetBaseMap, instanceName)` per environment.  
3. Tracks/returns the created instance identifier.

</details>

---

<details>
<summary>FindOpenInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Finds and returns an open instance with capacity for additional users.

**Parameters**  
- `instanceName` *(string)* – Instance key/prefix.  
- `usersToMove` *(integer)* – Number of users to accommodate.

**Behavior**  
1. Iterates through available instances for `instanceName`.  
2. Selects the first room with capacity `< maxPlayers`.  
3. Creates a new instance if none available.  
4. Returns the selected instance key.

</details>

---

<details>
<summary>MoveUsersIntoInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves a group of players into an instance through the portal.

**Parameters**  
- `users` *(table<string>)* – Player IDs to move.  
- `instanceName` *(string)* – Target instance.  
- `mapName` *(string)* – Destination map within that instance.  
- `withEvent` *(any)* – Optional event payload after movement.

**Behavior**  
1. Calls `FindOpenInstance(instanceName, #users)`.  
2. Moves players using `_RoomService:MoveUsersToInstanceRoom(users, instanceKey, mapName)`.  
3. Emits/sends `withEvent` if provided.

</details>

---

<details>
<summary>CreateSharedNumber</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Creates a numeric variable in shared memory (e.g., portal usage counters).

**Parameters**  
- `space` *(string)* – Shared memory space.  
- `variableName` *(string)* – Variable name.  
- `variableValue` *(number)* – Initial value.

**Behavior**  
1. Retrieves shared memory via `_RoomService:GetSharedMemory(space)`.  
2. Calls `mem:CreateVariableAndWait(variableName, tostring(variableValue))`.  
3. Logs creation failures.

</details>

---

<details>
<summary>GetSharedNumber</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Retrieves or creates a numeric shared variable if missing.

**Parameters**  
- `space` *(string)* – Shared memory namespace.  
- `variableName` *(string)* – Variable key.

**Behavior**  
1. Accesses memory via `_RoomService:GetSharedMemory(space)`.  
2. Reads the variable; if missing, calls `CreateSharedNumber()`.  
3. Converts value to `number` and returns it.

</details>

---

<details>
<summary>UpdateSharedNumber</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Safely updates a numeric shared-memory variable using ETag-based concurrency control.

**Parameters**  
- `space` *(string)* – Shared memory space.  
- `variableName` *(string)* – Variable name.  
- `variableValue` *(number)* – New value or increment.

**Behavior**  
1. Reads shared memory and fetches variable metadata (ETag).  
2. Updates the value atomically with `mem:UpdateVariableAndWait()`.  
3. Handles precondition failures by logging and recreating the variable if necessary.

</details>

---

### Related Types & Services

- **_RoomService / _DynamicMapService** – Instance discovery/creation and user movement into instances.  
- **_EntityService** – Resolves portal destination entities or map paths.  
- **_TeleportService** – Teleports players to portal targets.  
- **SharedMemoryResultCode** – Return codes for shared memory operations.  
- **SharedVariableKeyInfo** – Metadata for concurrency-safe updates.  

---

## DynamicMapReservedEvent

**Purpose**  
Custom event used to notify players when a reservation in a **dynamic map instance** is completed.  
Carries the destination instance ID and a list of users who are part of the reservation.

---

### Properties

| Property        | Type    | Purpose |
|-----------------|---------|---------|
| `mapInstanceId` | string  | The name or ID of the reserved dynamic map instance (e.g., `"DungeonMap_1"`). |
| `users`         | table   | A table of user IDs that are part of this reservation group. |

---

### Method Summary

_No methods defined in this script._

---

### Related Types & Services

- **SceneManager** – Sends this event to clients after dynamic instance reservation.
- **UIManager** – May listen to this event to show a reservation popup.
- **DynamicMapPortal** – Initiates the reservation and dispatches this event to users.

## DynamicPortalUseEvent

**Purpose**  
Custom event fired when a player interacts with a **dynamic map portal**.  
Acts as a signal for other systems to react to a portal activation (e.g., start reservation/teleport flow).

---

### Properties

_No properties defined in this script._

---

### Method Summary

_No methods defined in this script._

---

### Related Types & Services

- **DynamicMapPortal** – Emits this event when a portal is used.
- **SceneManager** – May listen and initiate routing to instances.
- **UIManager** – Can react to show loading/transition UI.

## EnterDungeonEvent

**Purpose**  
Custom event used to signal that a player (or party) is entering a **dungeon**—typically via a portal or matchmaking flow.  
Other systems listen for this event to begin transition, reservation, or teleportation into the target dungeon instance.

---

### Properties

_No properties defined in this script._

---

### Method Summary

_No methods defined in this script._

---

### Related Types & Services

- **DynamicMapPortal** – May dispatch this event when a dungeon portal is used.
- **SceneManager** – Can listen and route players to the appropriate dungeon map/instance.
- **UIManager** – May react to display loading or transition UI during dungeon entry.

## InstanceDungeonReservationUI

**Purpose**  
Client-side UI component that displays the **dungeon reservation** state (who’s in the party, whether the local player is registered, and when entry is available).  
Subscribes to reservation events and wires button interactions to register, close the panel, or enter the dungeon instance.

---

### Properties

| Property                   | Type    | Purpose |
|---------------------------|---------|---------|
| `reservationUIItemModel`  | string  | Model/Prefab ID used to spawn a single row/item representing a reserved user in the UI list. |
| `reservationListItems`    | table   | Runtime list tracking the spawned UI row entities for the reservation list. |
| `reservationScrollParent` | Entity  | UI container (e.g., a vertical layout/scroll view) under which spawned list items are parented. |
| `buttonRegister`          | Entity  | UI button entity the player clicks to toggle/register into the reservation. |
| `buttonClose`             | Entity  | UI button entity to close/hide the reservation panel. |
| `buttonEnter`             | Entity  | UI button entity to confirm entry into the reserved dungeon instance. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|---------|
| `Show(users)` | Client | Shows the reservation panel and renders the current `users` list. |
| `Hide()` | Client | Hides the reservation panel and clears transient UI state. |
| `PopulateList(users)` | Client | Spawns/updates UI rows for each user in `users`. |
| `ClearList()` | Client | Destroys all spawned list item entities and clears `reservationListItems`. |
| `UpdateRegistered(isRegistered)` | Client | Updates button states/labels based on whether the local user is currently registered. |
| `OnRegisterClicked()` | Client | Handles the **Register** button; toggles local user registration (emits a request/event). |
| `OnCloseClicked()` | Client | Handles the **Close** button; hides the reservation panel. |
| `OnEnterClicked()` | Client | Handles the **Enter** button; confirms entry (emits a request/event to proceed). |
| `HandleDynamicMapReservedEvent(event)` | Client | Processes a `DynamicMapReservedEvent`; updates registration state and refreshes the list UI. |

---

### Methods

<details>
<summary>Show</summary>

**Execution Scope**: `Client`  

**Purpose**  
Displays the reservation panel and renders the provided user list.

**Parameters**  
- `users` *(table<string>)* – User IDs currently reserved.

**Behavior**  
1. Enables the panel if it’s hidden.  
2. Calls `PopulateList(users)` to render rows.  
3. Ensures buttons reflect the latest registration state.
</details>

---

<details>
<summary>Hide</summary>

**Execution Scope**: `Client`  

**Purpose**  
Closes the reservation UI.

**Parameters**  
- *(none)*

**Behavior**  
1. Calls `ClearList()` to remove spawned rows.  
2. Disables the panel/root entity.
</details>

---

<details>
<summary>PopulateList</summary>

**Execution Scope**: `Client`  

**Purpose**  
Creates or updates UI list entries for each reserved user.

**Parameters**  
- `users` *(table<string>)* – Users to display.

**Behavior**  
1. Calls `ClearList()` to reset.  
2. For each `userId`, spawns `reservationUIItemModel` under `reservationScrollParent`.  
3. Pushes each spawned row into `reservationListItems` and binds any per-row data as needed.
</details>

---

<details>
<summary>ClearList</summary>

**Execution Scope**: `Client`  

**Purpose**  
Removes all currently spawned reservation row entities.

**Parameters**  
- *(none)*

**Behavior**  
1. Iterates `reservationListItems` and destroys each entity.  
2. Empties `reservationListItems`.
</details>

---

<details>
<summary>UpdateRegistered</summary>

**Execution Scope**: `Client`  

**Purpose**  
Reflects whether the local user is part of the reservation (e.g., toggles button states).

**Parameters**  
- `isRegistered` *(boolean)* – Whether the local user is currently in the reservation.

**Behavior**  
1. Enables/disables `buttonEnter` depending on `isRegistered`.  
2. Updates the **Register** button label/state accordingly.
</details>

---

<details>
<summary>OnRegisterClicked</summary>

**Execution Scope**: `Client`  

**Purpose**  
Handles click on the **Register** button.

**Parameters**  
- *(none)*

**Behavior**  
1. Emits a request/event to register or unregister the local user for the active reservation.  
2. Optionally disables the button briefly to avoid double clicks.
</details>

---

<details>
<summary>OnCloseClicked</summary>

**Execution Scope**: `Client`  

**Purpose**  
Handles click on the **Close** button.

**Parameters**  
- *(none)*

**Behavior**  
1. Calls `Hide()` to close the panel.  
2. Clears list state.
</details>

---

<details>
<summary>OnEnterClicked</summary>

**Execution Scope**: `Client`  

**Purpose**  
Handles click on the **Enter** button.

**Parameters**  
- *(none)*

**Behavior**  
1. Emits a request/event to proceed into the reserved instance for the group.  
2. Optionally disables the button until a response is received.
</details>

---

<details>
<summary>HandleDynamicMapReservedEvent</summary>

**Execution Scope**: `Client`  

**Purpose**  
Processes a `DynamicMapReservedEvent` for the local player’s space and updates UI.

**Parameters**  
- `event` *(`DynamicMapReservedEvent`)* – Contains `mapInstanceId` and `users`.

**Behavior**  
1. Logs the instance ID (`mapInstanceId`).  
2. Resolves local user ID and checks `_Utils:Contains(event.users, localUserId)`.  
3. Calls `UpdateRegistered(isRegistered)`.  
4. If the UI is visible and `isRegistered` is `true`, calls `Show(event.users)`; otherwise `Show({})`.
</details>

---

### Related Types & Services

- **DynamicMapReservedEvent** – Event payload this UI listens to (`mapInstanceId`, `users`).  
- **_UserService** – Used to fetch the local user ID.  
- **_Utils** – Provides `Contains(table, value)` helper used by the script.  
- **UI system** – Button entities (`buttonRegister`, `buttonClose`, `buttonEnter`) and list container (`reservationScrollParent`).  

---

## InstanceDungeonReservationUIItem

**Purpose**  
Represents a single row in the **dungeon reservation** UI list.  
Displays a player’s identity and registration state, and wires any per-row interactions (e.g., remove/unregister) back to the reservation UI flow.  

---

### Properties

| Property             | Type    | Purpose |
|---------------------|---------|---------|
| `userId`            | string  | The user ID this UI item represents. |
| `displayNameLabel`  | Entity  | UI text element used to show the player’s display name. |
| `statusLabel`       | Entity  | UI text element used to show reservation/ready status. |
| `avatarImage`       | Entity  | UI image element used to show the player’s avatar (if available). |
| `buttonRemove`      | Entity  | Optional UI button to remove/unregister this user from the reservation list. |
| `isLocalUser`       | boolean | Indicates whether this row represents the local player (used for styling/behavior). |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|---------|
| `Init(userId, displayName)` | Client | Initializes the row with the target user and sets initial UI text/images. |
| `BindUser(userId, displayName)` | Client | (Re)binds this item to a specific user and updates visuals. |
| `UpdateRegistered(isRegistered)` | Client | Updates the status label/state to reflect reservation/ready status. |
| `SetIsLocal(isLocal)` | Client | Marks this item as representing the local user (e.g., highlight). |
| `SetAvatar(avatarIdOrEntity)` | Client | Sets or refreshes the avatar image for this row, if supported. |
| `OnRemoveClicked()` | Client | Handles the remove/unregister action for this user. |
| `Destroy()` | Client | Cleans up spawned UI and detaches events for this row. |

---

### Methods

<details>
<summary>Init</summary>

**Execution Scope**: `Client`  

**Purpose**  
Initializes the UI item for a specific user and primes the visual elements.

**Parameters**  
- `userId` *(string)* – Player ID to show in this row.  
- `displayName` *(string)* – Player display name to render.

**Behavior**  
1. Stores `userId` and sets `displayNameLabel` text.  
2. Resets `statusLabel` (e.g., “Pending”).  
3. Optionally requests avatar and calls `SetAvatar(...)`.  
4. Binds `buttonRemove` click to `OnRemoveClicked()` if present.
</details>

---

<details>
<summary>BindUser</summary>

**Execution Scope**: `Client`  

**Purpose**  
Re-binds this UI item to a different user and refreshes visuals.

**Parameters**  
- `userId` *(string)* – New player ID.  
- `displayName` *(string)* – New display name.

**Behavior**  
1. Updates internal `userId`.  
2. Updates `displayNameLabel` and resets `statusLabel`.  
3. Optionally refreshes avatar via `SetAvatar(...)`.
</details>

---

<details>
<summary>UpdateRegistered</summary>

**Execution Scope**: `Client`  

**Purpose**  
Reflects whether the represented user is currently registered/ready.

**Parameters**  
- `isRegistered` *(boolean)* – `true` if registered/ready, otherwise `false`.

**Behavior**  
1. Sets `statusLabel` (e.g., “Ready” / “Pending”).  
2. Optionally toggles row styling to indicate readiness.
</details>

---

<details>
<summary>SetIsLocal</summary>

**Execution Scope**: `Client`  

**Purpose**  
Marks this row as the local player (for styling or button visibility).

**Parameters**  
- `isLocal` *(boolean)* – Whether the row is for the local user.

**Behavior**  
1. Stores `isLocalUser`.  
2. Optionally highlights the row or disables `buttonRemove` for non-local rows.
</details>

---

<details>
<summary>SetAvatar</summary>

**Execution Scope**: `Client`  

**Purpose**  
Sets the avatar image for the row.

**Parameters**  
- `avatarIdOrEntity` *(any)* – ID or entity handle used to resolve the avatar.

**Behavior**  
1. Resolves the avatar source.  
2. Applies it to `avatarImage` if available.
</details>

---

<details>
<summary>OnRemoveClicked</summary>

**Execution Scope**: `Client`  

**Purpose**  
Handles the remove/unregister action for this user from the reservation.

**Parameters**  
- *(none)*

**Behavior**  
1. Emits a UI/event signal to request removal/unregistration of `userId`.  
2. May disable the button to avoid double-submission until a response is received.
</details>

---

<details>
<summary>Destroy</summary>

**Execution Scope**: `Client`  

**Purpose**  
Cleans up the row UI and any bound listeners.

**Parameters**  
- *(none)*

**Behavior**  
1. Unbinds click handlers.  
2. Destroys the row entity or hides it and clears references.
</details>

---

### Related Types & Services

- **InstanceDungeonReservationUI** – Parent list/controller that creates and manages item rows.  
- **DynamicMapReservedEvent** – Source of reservation updates that drive item state.  
- **_UserService** – May supply display names/avatars for users.  
- **UI system** – Text/Image/Button entities used to render and interact with the row.  

---

## InstanceDungeonReservationUIItem

**Purpose**  
Represents a single **reservation list item** in the dungeon UI.  
Displays a user’s identifier in a text field and provides simple show/hide controls for the row.

---

### Properties

| Property    | Type          | Purpose |
|-------------|---------------|---------|
| `userText`  | `TextComponent` | UI text component used to display the user’s identifier in this row. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|----------|
| `Init(userId)` | Client | Binds this UI item to a specific `userId` and updates the text label. |
| `Show()` | Client | Enables the row entity, making the UI item visible. |
| `Hide()` | Client | Disables the row entity, hiding the UI item. |

---

### Methods

<details>
<summary>Init</summary>

**Execution Scope**: `Client`  

**Purpose**  
Initializes the UI row with the given user ID.

**Parameters**  
- `userId` *(string)* – Identifier to display in `userText`.

**Behavior**  
1. Sets `self.userText.Text = userId`.  
2. Leaves visibility unchanged (controlled by `Show`/`Hide`).
</details>

---

<details>
<summary>Show</summary>

**Execution Scope**: `Client`  

**Purpose**  
Makes this list item visible.

**Parameters**  
- *(none)*

**Behavior**  
1. Calls `self.Entity:SetEnable(true)`.
</details>

---

<details>
<summary>Hide</summary>

**Execution Scope**: `Client`  

**Purpose**  
Hides this list item.

**Parameters**  
- *(none)*

**Behavior**  
1. Calls `self.Entity:SetEnable(false)`.
</details>

---

### Related Types & Services

- **TextComponent** – Displays the bound `userId` string.  
- **UI system** – Provides the `Entity` and enable/disable behavior used by `Show`/`Hide`.  

---

## PlayerRegisteredEvent

**Purpose**  
Custom event used to notify systems when a player’s **reservation registration state** changes.  
Carries the player’s ID and whether they are currently registered for the reservation/group.

---

### Properties

| Property   | Type    | Purpose |
|------------|---------|---------|
| `userId`   | string  | The ID of the player whose registration state changed. |
| `registered` | boolean | `true` if the player is registered; `false` if they unregistered. |

---

### Method Summary

_No methods defined in this script._

---

### Related Types & Services

- **InstanceDungeonReservationUI** – Updates UI when a player registers/unregisters.
- **DynamicMapPortal** – May initiate or react to registration changes before entering an instance.
- **SceneManager** – Coordinates routing logic based on current party registration state.
- **DynamicMapReservedEvent** – Complementary event indicating a reservation group is ready/assigned.

## InstanceDungeonMonster

**Purpose**  
Server-authoritative boss/monster that orchestrates defeat flow in a **dynamic dungeon instance**.  
On death it plays client VFX, hides the entity, and requests the dynamic map to close—returning players to a configured fallback map.  

---

### Properties

| Property            | Type    | Purpose |
|---------------------|---------|---------|
| `returnMapOnDefeat` | string  | Static/hub map name to return players to when the boss is defeated and the dynamic map is closed. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|----------|
| `Dead()` | Server | Marks the monster as dead, switches to `DEAD` state, schedules hide/disable, and triggers the defeat sequence (`BossFinish`). |
| `OnBeginPlay()` | Server | Initializes the monster on spawn and starts the boss encounter (`BossStart`). |
| `BossStart()` | Client | Plays a client-side start VFX at the boss position to signal encounter start. |
| `BossFinish()` | Client | Plays a client-side finish VFX and, after a delay, closes the dynamic map and returns players to `returnMapOnDefeat`. |

---

### Methods

<details>
<summary>Dead</summary>

**Execution Scope**: `Server`  

**Purpose**  
Handles server-side death logic and kicks off the defeat sequence.

**Parameters**  
- *(none)*

**Behavior**  
1. Sets `self.IsDead = true`.  
2. Fetches `stateComponent = self.Entity.StateComponent`; if present, calls `stateComponent:ChangeState("DEAD")` and logs the transition.  
3. Defines `delayHide` that calls `self.Entity:SetVisible(false)` and `self.Entity:SetEnable(false)`.  
4. Schedules `delayHide` via `_TimerService:SetTimerOnce(delayHide, self.DestroyDelay)`.  
5. Calls `self:BossFinish()` to run the client finish flow.  
</details>

---

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `Server`  

**Purpose**  
Runs when the monster spawns; performs base initialization and starts the encounter.

**Parameters**  
- *(none)*

**Behavior**  
1. Calls `__base:OnBeginPlay()` (from `Monster`).  
2. Calls `self:BossStart()` to play the start VFX on clients.  
</details>

---

<details>
<summary>BossStart</summary>

**Execution Scope**: `Client`  

**Purpose**  
Signals the start of the boss encounter with a visual effect.

**Parameters**  
- *(none)*

**Behavior**  
1. Computes an effect position: `self.Entity.TransformComponent.Position + Vector3.up * 2`.  
2. Calls `_EffectService:PlayEffect("eee4571f66ff4acf8b7f3878860642fa", self.Entity, pos, 0, Vector3.one)`.  
</details>

---

<details>
<summary>BossFinish</summary>

**Execution Scope**: `Client`  

**Purpose**  
Signals the end of the encounter, then closes the dynamic map and returns players to a static map.

**Parameters**  
- *(none)*

**Behavior**  
1. Plays finish VFX at `self.Entity.TransformComponent.Position + Vector3.up * 2` using effect ID `"ad24ef5bcf8448fa9713a868aff14d6d"`.  
2. Schedules a timer with `_TimerService:SetTimerOnce(function() ... end, self.DestroyDelay * 2)` that:  
   - Reads `mapName = self.Entity.CurrentMap.Name`.  
   - Calls `_SceneManager:CloseDynamicMap(mapName, self.returnMapOnDefeat)`.  
</details>

---

### Related Types & Services

- **Monster** – Base class providing `__base:OnBeginPlay()`, `IsDead`, and common monster behavior.  
- **StateComponent** – Enables state transitions (e.g., `ChangeState("DEAD")`).  
- **_TimerService** – Schedules delayed hide and map-closing actions.  
- **_EffectService** – Plays start/finish VFX on clients.  
- **_SceneManager** – Closes the dynamic map and routes players to `returnMapOnDefeat`.  
- **Vector3** – Used to offset VFX spawn positions.  

---

## Changelog

| Version | Notes |
|---------|-------|
| v1.0 | Initial release with dynamic map and party reservation systems. |

---


