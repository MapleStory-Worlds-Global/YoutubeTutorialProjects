# Instance Room and Worlds Demo 

## Youtube Tutorial
[![Youtube Tutorial](https://img.youtube.com/vi/yjMLMl5KotY/0.jpg)](https://youtube.com/playlist?list=PLYnmZFABLxZzFWegbGAriHwqYU4WFFVno&si=V_Iu8BNT14kQUAvP "Let’s Explore Instance Worlds and Rooms")

## Mod Installation Instructions
[Mod File Installation Guide](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects?tab=readme-ov-file#mod-file-import-instructions)

## Project File
[Download Latest Project File](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/raw/refs/heads/main/Instance_Maps/Room_World_Instance_Demo.mod)

## Table of Contents
- [Overview](#overview)
- [Setup and Installation](#setup-and-installation)
- [Script Documentation](#script-documentation)
  - [SceneManagement](#scenemanagement)
  - [RoomInfo](#roominfo)
  - [CustomPortalComponent](#customportalcomponent)
  - [WorldManagement](#worldmanagement)
  - [GameSystem](#gamesystem)
  - [RoomEnteredEvent](#roomenteredevent)
  - [PlayerMapComponent](#playermapcomponent)
  - [MoveToMap (Scripts/InstanceMapTest)](#movetomap-scriptsinstancemaptest)
  - [MoveToMap (InstanceMapTest)](#movetomap-instancemaptest)
  - [UIPopup (Scripts/UI)](#uipopup-scriptsui)
  - [UIPopup (MyDesk root)](#uipopup-mydesk-root)
  - [UIToast (Scripts/UI)](#uitoast-scriptsui)
  - [UIToast (MyDesk root)](#uitoast-mydesk-root)
  - [Popupbutton (Scripts/UI)](#popupbutton-scriptsui)
  - [Popupbutton (MyDesk root)](#popupbutton-mydesk-root)
- [Changelog & Download](#changelog)
---

## Overview
This project is provided as supplemental resource for the "Let’s Explore Instance Worlds and Rooms" tutorial series. 

Often times you’ll want to be able to separate players into their own separate map instances or even world instances. By the end of this series, you’ll be a master of everything instance room and world!

What You’ll Learn in this tutorial:

- Chapter 1: Overview of the difference between static and instance rooms.
- Chapter 2: Creating our own custom static portals
- Chapter 3: Creating our own custom instance portals
- Chapter 4: Communicating data between instance rooms
- Chapter 5: Overview of Instance Worlds
- Chapter 6: Shared memory between both instance rooms and worlds

## Setup and Installation
* Import the project file (see the installation guide above) and open it in MapleStory Worlds Maker.
* `SceneManagement`, `WorldManagement`, `GameSystem`, `UIPopup` and `UIToast` are Logic scripts and run automatically; no component setup is needed for them.
* `GameSystem` expects static map `Static_Starting` and instance maps `Instance_Room1` / `Instance_Room2`, plus the start button referenced by its `startButton` property.
* Custom portals use the `CustomPortal` model (`MyDesk/Common/SceneManagement/Portal/Model`). On each portal set `roomName` to the destination map, tick `isInstanceRoom` if that map is an instance map, and optionally set `customPortalEntity` as an explicit landing point. Leave the native portal target empty so the custom routing runs.
* Press Play:
    * Walk into a custom portal to move between static maps, or from a static map into an instance room (and back).
    * Click the start button on `Static_Starting` to move everyone on that map into a `GameInstance` instance room; a toast appears on arrival and a popup shows the shared counter value. Click it again inside the instance to return everyone to `Static_Starting`.
    * Press **F1** to warp the local player to another open world instance (requires more than one world instance, e.g. a published world).
    * If a `MoveToMap` component is attached to an entity, press **M** / **N** to move all players to an instance of `mapName` / back to static `map01`.

## Script Documentation

## SceneManagement

**Purpose**  
Global Logic (`_SceneManagement`) that coordinates room transitions across static and instance maps.  
Registers instance-room "families", creates instance rooms, moves players between rooms and reads/writes numbers in room shared memory.

---

### Properties

| Property  | Type                      | Purpose |
|------------|---------------------------|----------|
| `rooms`    | `table<string, RoomInfo>` | Registered instance-room families keyed by `instanceName` (maps list and capacity). Filled by `InitInstanceRoom`. |
| `roomIdx`  | `table`                   | Next index per `instanceName`, appended to the room key so each `CreateInstanceRoom` call creates a brand-new room. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|----------|
| `ReturnUsersToStatic(users, mapName)` | Server | Moves a list of users from an instance room back to a static map. |
| `MoveToMap(userId, mapName, customPortalEntity)` | Server | Teleports a single player within the same room (static -> static or instance -> instance). |
| `ReturnToStatic(userId, mapName)` | Server | Returns a single user from an instance room to the target static map. |
| `CreateAndEnterInstanceMap(userId, instanceName, mapName)` | Server | Gets or creates the instance room keyed by `instanceName` and moves the player into it. |
| `InitInstanceRoom(maps, instanceName, maxCapacity)` | Server | Registers an instance-room family (maps and capacity). |
| `CreateInstanceRoom(instanceName)` | Server | Creates a new instance room from a registered family. |
| `GetOpenInstance(instanceName, usersToMove)` | Server | Returns the key of an instance room with free capacity, creating one if needed. |
| `MoveUsersToInstance(users, instanceName, mapName, withEvent)` | Server | Moves multiple users into an instance room and optionally sends an event to that room. |
| `CreateSharedNumber(space, variableName, variableValue)` | Client/Server | Creates a numeric variable in room shared memory. |
| `GetSharedNumber(space, variableName)` | Client/Server | Reads a numeric shared variable, creating it with `0` if missing. |
| `UpdateSharedNumber(space, variableName, variableValue)` | Client/Server | Increments a numeric shared variable by 1 using ETag concurrency control. |

---

### Methods

<details>
<summary>ReturnUsersToStatic</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves multiple users from an instance back to a static map.

**Parameters**  
- `users` *(table<string>)* – List of user IDs.  
- `mapName` *(string)* – Name of the static map to return to.

**Behavior**  
1. Calls `_RoomService:MoveUsersToStaticRoom(users, mapName)`.  
2. Only works when called from an instance room.

</details>

<details>
<summary>MoveToMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Transfers a player between maps within the same room type (static -> static or instance -> instance).

**Parameters**  
- `userId` *(string)* – The player’s user ID.  
- `mapName` *(string)* – The name of the destination map.  
- `customPortalEntity` *(Entity)* – Optional teleport target; if `nil`, the map entity is used.

**Behavior**  
1. If no target is given, resolves `_EntityService:GetEntityByPath("/maps/" .. mapName)`.  
2. Logs a warning and returns if the target is invalid.  
3. Gets the user entity with `_UserService:GetUserEntityByUserId(userId)`.  
4. Calls `_TeleportService:TeleportToEntity(user, targetEntity)`.

</details>

<details>
<summary>ReturnToStatic</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves an individual player from an instance room back to a static map.

**Parameters**  
- `userId` *(string)* – User ID of the player.  
- `mapName` *(string)* – Target static map name.

**Behavior**  
1. Logs the action.  
2. Calls `_RoomService:MoveUserToStaticRoom(userId, mapName)`.

</details>

<details>
<summary>CreateAndEnterInstanceMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Creates or reuses an instance room for a player and moves them into it.

**Parameters**  
- `userId` *(string)* – User ID.  
- `instanceName` *(string)* – Instance room key.  
- `mapName` *(string)* – Destination map name (currently not used by the method body).

**Behavior**  
1. Gets or creates the instance room via `_RoomService:GetOrCreateInstanceRoom(instanceName)`.  
2. Calls `instanceRoom:MoveUser(userId)`.  
3. Does not consult the `RoomInfo` registered by `InitInstanceRoom` (no capacity check).

</details>

<details>
<summary>InitInstanceRoom</summary>

**Execution Scope**: `Server`  

**Purpose**  
Registers metadata for an instance-room family.

**Parameters**  
- `maps` *(table)* – Map names included in each instance room.  
- `instanceName` *(string)* – Key name for the family.  
- `maxCapacity` *(integer)* – Maximum player count per instance room.

**Behavior**  
1. Logs a warning and returns if `instanceName` is already registered.  
2. Creates a `RoomInfo` with `roomId`, `maxCapacity` and `maps` and stores it in `self.rooms[instanceName]`.

</details>

<details>
<summary>CreateInstanceRoom</summary>

**Execution Scope**: `Server`  

**Purpose**  
Creates a new instance room using a registered family.

**Parameters**  
- `instanceName` *(string)* – The family to create a room for.

**Returns**  
- *(InstanceRoom)* – The created room, or `nil` if the family is not registered.

**Behavior**  
1. Logs a warning and returns if the family has not been initialized.  
2. Calls `_RoomService:CreateInstanceRoom("<instanceName>_<idx>", roomInfo.maps)`.  
3. Increments `self.roomIdx[instanceName]` so the next call creates a different room.

</details>

<details>
<summary>GetOpenInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Finds an instance room with enough capacity for additional players.

**Parameters**  
- `instanceName` *(string)* – Family name.  
- `usersToMove` *(integer)* – Number of users to move.

**Returns**  
- *(string)* – `InstanceKey` of the selected room, or `nil`.

**Behavior**  
1. Iterates `_RoomService.InstanceRooms`.  
2. Selects the first room where `GetUserCount() + usersToMove <= maxCapacity`. (The key is not actually matched against `instanceName`, so rooms of any family can be selected.)  
3. If none fits, creates a new room with `CreateInstanceRoom`.  
4. Returns the room's `InstanceKey`.

</details>

<details>
<summary>MoveUsersToInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves a group of players into a new or existing instance room.

**Parameters**  
- `users` *(table<string>)* – User IDs to move.  
- `instanceName` *(string)* – Family to move to.  
- `mapName` *(string)* – Destination map within that instance room.  
- `withEvent` *(any)* – Optional event sent to the room after the move.

**Behavior**  
1. Calls `GetOpenInstance(instanceName, #users)` to find a room key.  
2. Moves the users with `_RoomService:MoveUsersToInstanceRoom(key, users, mapName)`.  
3. If `withEvent` is provided, sends it with `_RoomService:RequestSendEventToRoomAndWait(withEvent, key)`.

</details>

<details>
<summary>CreateSharedNumber</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Creates a numeric variable in room shared memory.

**Parameters**  
- `space` *(string)* – Shared memory space.  
- `variableName` *(string)* – Variable identifier.  
- `variableValue` *(number)* – Initial value.

**Behavior**  
1. Retrieves shared memory via `_RoomService:GetSharedMemory(space)`.  
2. Calls `mem:CreateVariableAndWait(variableName, tostring(variableValue))`.  
3. Logs an error on failure.

</details>

<details>
<summary>GetSharedNumber</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Reads a numeric shared variable, creating it if missing.

**Parameters**  
- `space` *(string)* – Shared memory space.  
- `variableName` *(string)* – Variable key.

**Returns**  
- *(number)* – The stored value, `0` if it was just created, or `nil` on failure.

**Behavior**  
1. Accesses memory via `_RoomService:GetSharedMemory(space)`.  
2. Reads the variable; if missing, creates it with `0` via `CreateSharedNumber()`.  
3. Converts the value to `number` and returns it.

</details>

<details>
<summary>UpdateSharedNumber</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Increments a numeric shared-memory variable using ETag-based concurrency control.

**Parameters**  
- `space` *(string)* – Shared memory space.  
- `variableName` *(string)* – Variable name.  
- `variableValue` *(number)* – Initial value used only when the variable does not exist yet.

**Behavior**  
1. Reads the variable; if missing, creates it with `variableValue`.  
2. Otherwise builds a `SharedVariableKeyInfo(Name, ETag)` and writes `value + 1` with `mem:UpdateVariableAndWait()`.  
3. Logs an error on `PreconditionFailed` (ETag mismatch). The update is not retried.

</details>

---

### Related Types & Services

- **RoomInfo** – Stores configuration and capacity data for instance-room families.  
- **_RoomService** – Handles instance creation, user movement, room events and shared-memory access.  
- **_EntityService** – Used to find destination map entities by path.  
- **_TeleportService** – Teleports players to target entities.  
- **_UserService** – Resolves user entities from user IDs.  
- **SharedMemoryResultCode** – Return codes for shared-memory operations.  
- **SharedVariableKeyInfo** – Used for concurrency-safe variable updates.  

---

## RoomInfo

**Purpose**  
Lightweight data container (struct) that describes an instance-room family.  
Holds the family identifier, capacity and list of map names used by `SceneManagement` to create and fill instance rooms.

---

### Properties

| Property       | Type              | Purpose |
|----------------|-------------------|---------|
| `roomId`       | `string`          | Family name (same as the key in `SceneManagement.rooms`). |
| `maxCapacity`  | `number`          | Maximum number of players allowed in a single instance room of this family. Defaults to `10`. |
| `maps`         | `table<string>`   | Map names included when an instance room of this family is created. |

---

### Usage
- Created and stored by `SceneManagement:InitInstanceRoom`.
- Read by `SceneManagement:CreateInstanceRoom` (maps) and `SceneManagement:GetOpenInstance` (capacity).

---

## CustomPortalComponent

**Purpose**  
Extends `PortalComponent` with custom routing between **static** and **instance** rooms.  
On startup it registers an instance-room family for the portal; on use it moves the player within the same room type, back to a static map, or into an instance room via `_SceneManagement`.

---

### Properties

| Property              | Type         | Purpose |
|----------------------|--------------|---------|
| `isInstanceRoom`     | `boolean`    | `true` if the destination map is in an instance room. Defaults to `false`. |
| `roomName`           | `string`     | Name of the destination map (`/maps/<roomName>`). |
| `customPortalEntity` | `EntityRef`  | Optional explicit landing entity for same-room-type moves. When `nil`, `/maps/<roomName>` is used. |

---

### Method Summary

| Method                 | Execution Scope | Purpose |
|------------------------|-----------------|---------|
| `OnBeginPlay`          | ServerOnly      | Registers an instance-room family for this portal with `SceneManagement`. |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Prepares this portal for instance routing.

**Behavior**  
- Calls `_SceneManagement:InitInstanceRoom({ self.roomName }, self.Entity.Id, 2)`:
  - Uses the portal’s entity ID as the `instanceName`.
  - Includes `roomName` as the family's only map.
  - Uses a capacity of `2`.
- Runs for every portal, whether or not `isInstanceRoom` is set.

</details>

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandlePortalUseEvent` | `PortalUseEvent` | Self / ClientOnly | If the native `PortalEntityRef` is valid, only logs (the base portal teleports). Otherwise compares `_RoomService:IsInstanceRoom()` with `isInstanceRoom`: same type -> `_SceneManagement:MoveToMap(userId, roomName, customPortalEntity)`; in an instance going to static -> `ReturnToStatic(userId, roomName)`; in static going to an instance -> `CreateAndEnterInstanceMap(userId, self.Entity.Id, roomName)`. |

---

### Related Types & Services
- **PortalComponent** – Base native portal component.  
- **SceneManagement** – Performs all map/instance routing (`InitInstanceRoom`, `MoveToMap`, `ReturnToStatic`, `CreateAndEnterInstanceMap`).  
- **_RoomService** – `IsInstanceRoom()` tells which room type the player is currently in.  
- **PortalUseEvent** – Event raised when a portal is used.  

---

## WorldManagement

**Purpose**  
Global Logic (`_WorldManagement`) for **world-instance** operations.  
Finds open world instances, warps users to them with a data payload, and manages numeric values in world shared memory using `_WorldInstanceService`.

---

### Properties

This script has no custom properties.

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|----------|
| `FindOpenInstance(usersToMove)` | Server | Finds a world instance with enough free capacity. |
| `WarpToOpenInstance(users)` | Server | Warps the given users to an open world instance with a serialized payload. |
| `CreateSharedNumber(space, variableName, variableValue)` | Client/Server | Creates a numeric variable in world shared memory. |
| `GetSharedNumber(space, variableName)` | Client/Server | Reads a numeric variable from world shared memory, creating it if missing. |
| `UpdateSharedNumber(space, variableName, variableValue)` | Client/Server | Increments a world shared numeric variable by 1 with ETag checks. |

---

### Methods

<details>
<summary>FindOpenInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Finds a world instance with sufficient capacity for the specified number of players.

**Parameters**  
- `usersToMove` *(integer)* – Number of users that must fit.

**Returns**  
- *(WorldInstanceInfo)* – Matching instance info, or `nil`.

**Behavior**  
1. Calls `_WorldInstanceService:GetWorldInstanceInfoPagesAndWait()`; returns `nil` if no pages.  
2. For each page, checks `CurrentUserCount + usersToMove <= MaxUserCount`.  
3. Pages with `MoveToNextPageAndWait()` until `IsLastPage`; a match on a later page replaces an earlier one.  
4. The current world instance is not excluded (the check is commented out).

</details>

<details>
<summary>WarpToOpenInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves a set of users to an open world instance found by `FindOpenInstance`.

**Parameters**  
- `users` *(table)* – List of user IDs to warp.

**Behavior**  
1. Builds a sample payload `{coins = 100}` and serializes it with `_UtilLogic:TableToString()`.  
2. Calls `self:FindOpenInstance(#users)`; logs a warning and returns if none is found.  
3. Warps with `_TeleportService:WarpUsersToWorldAndWait(users, info.Id, serializedData)`.

</details>

<details>
<summary>CreateSharedNumber</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Creates a numeric variable in shared memory accessible across world instances.

**Parameters**  
- `space` *(string)* – Shared memory namespace.  
- `variableName` *(string)* – Name of the variable to create.  
- `variableValue` *(number)* – Initial value.

**Behavior**  
1. Calls `_WorldInstanceService:GetSharedMemory(space)`.  
2. Uses `mem:CreateVariableAndWait(variableName, tostring(variableValue))`.  
3. Logs an error if either call fails.

</details>

<details>
<summary>GetSharedNumber</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Reads a numeric variable from world shared memory, creating it with `0` if it doesn’t exist.

**Parameters**  
- `space` *(string)* – Shared memory space.  
- `variableName` *(string)* – Target variable name.

**Returns**  
- *(number)* – Current value, `0` if just created, or `nil` on failure.

**Behavior**  
1. Gets shared memory using `_WorldInstanceService:GetSharedMemory(space)`.  
2. Fetches the variable via `mem:GetVariableAndWait(variableName)`; creates it if missing.  
3. Converts and returns the numeric value, logging conversion errors.

</details>

<details>
<summary>UpdateSharedNumber</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Increments a numeric variable in world shared memory using ETag validation.

**Parameters**  
- `space` *(string)* – Shared memory namespace.  
- `variableName` *(string)* – Variable key name.  
- `variableValue` *(number)* – Initial value used only if the variable does not exist yet.

**Behavior**  
1. Reads the variable; if missing, creates it with `variableValue`.  
2. Otherwise writes `value + 1` with `mem:UpdateVariableAndWait(SharedVariableKeyInfo(Name, ETag), ...)`.  
3. Logs an error on `PreconditionFailed` (ETag mismatch).

</details>

---

### Related Types & Services
- **_WorldInstanceService** – Lists world instances and provides world shared memory.  
- **_TeleportService** – Warps users to another world instance (`WarpUsersToWorldAndWait`).  
- **_UtilLogic** – Serializes the warp payload (`TableToString`).  
- **SharedMemoryResultCode** – Enum for shared-memory operation outcomes.  
- **SharedVariableKeyInfo** – Encapsulates variable name + ETag for safe updates.  

---

## GameSystem

**Purpose**  
Top-level Logic for the lobby/game flow.  
Registers the `GameInstance` instance family, wires the start button to move players into an instance room or back to `Static_Starting`, shows a toast on `RoomEnteredEvent`, shows the shared counter in a popup on entry, reads world-warp payloads, and warps to another world instance on **F1**.

---

### Properties

| Property       | Type     | Purpose |
|----------------|----------|---------|
| `startButton`  | `Entity` | UI button that moves players to the instance room (or back to the starting map when pressed inside an instance). |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|---------|
| `OnBeginPlay` | Client/Server | Client: connects the start button. Server: calls `InitInstance`. |
| `InitInstance` | Client/Server | Registers the `GameInstance` family (Instance_Room1, Instance_Room2; capacity 10). |
| `MovePlayersToInstance` | Server | Moves all users on `Static_Starting` into `Instance_Room1` of an open `GameInstance` room. |
| `MovePlayersToStarting` | Server | Returns all users in the current room to `Static_Starting`. |
| `ShowPopup` | Client | Opens `_UIPopup` with "The value is <value>". |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Sets up the start button on the client and registers the instance family on the server.

**Behavior**  
- Client: connects `ButtonClickEvent` on `startButton`. If `_RoomService:IsInstanceRoom()` calls `MovePlayersToStarting()`, otherwise `MovePlayersToInstance()` (both forwarded to the server).  
- Server: calls `InitInstance()`.

</details>

<details>
<summary>InitInstance</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Registers the instance family used by the start button.

**Behavior**  
- Calls `_SceneManagement:InitInstanceRoom({"Instance_Room1", "Instance_Room2"}, "GameInstance", 10)`.

</details>

<details>
<summary>MovePlayersToInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves every player on the starting map into an instance room.

**Behavior**  
1. Collects user IDs from `_UserService:GetUsersByMapName("Static_Starting")`.  
2. Reads `GlobalCounters/MyCounter` with `_SceneManagement:GetSharedNumber` and increments it with `UpdateSharedNumber`.  
3. Creates a `RoomEnteredEvent` with message "New competitors have entered the room!".  
4. Calls `_SceneManagement:MoveUsersToInstance(users, "GameInstance", "Instance_Room1", evt)`.

</details>

<details>
<summary>MovePlayersToStarting</summary>

**Execution Scope**: `Server`  

**Purpose**  
Returns all players in the current room to the static starting map.

**Behavior**  
1. Collects user IDs from `_UserService.UserEntities`.  
2. Calls `_SceneManagement:ReturnUsersToStatic(users, "Static_Starting")`.

</details>

<details>
<summary>ShowPopup</summary>

**Execution Scope**: `Client`  

**Purpose**  
Shows a value in the modal popup.

**Parameters**  
- `value` *(string)* – Text appended to "The value is ".

**Behavior**  
- Calls `_UIPopup:Open("The value is " .. value, nil, nil)`. When called from the server with a trailing user ID (as in `UserEnterEvent`), only that user’s client runs it.

</details>

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandleRoomEnteredEvent` | `RoomEnteredEvent` | Service: RoomService / Client/Server | Shows `event.message` with `_UIToast:ShowMessage`. |
| `UserEnterEvent` | `UserEnterEvent` | Service: UserService / ServerOnly | Logs `coins` from `_TeleportService:GetWarpRecord(UserId)` if a warp payload exists; unless `_RoomService.RoomKey` contains "main", reads `GlobalCounters/MyCounter` and calls `ShowPopup(value, UserId)` for the entering user. |
| `OnKeyDown` | `KeyDownEvent` | Service: InputService / ClientOnly | On **F1**, calls `_WorldManagement:WarpToOpenInstance({localUserId})`. |

---

### Related Types & Services
- **SceneManagement** – Instance/static routing and room shared memory.  
- **WorldManagement** – World instance discovery and warping.  
- **_TeleportService** – `GetWarpRecord` returns the payload sent with a world warp.  
- **_UtilLogic** – `StringToTable` deserializes the warp payload; `Contains` checks the room key.  
- **_UIPopup, _UIToast** – UI helpers for modal dialogs and transient messages.  
- **Events** – `ButtonClickEvent`, `RoomEnteredEvent`, `UserEnterEvent`, `KeyDownEvent`.  

---

## RoomEnteredEvent

**Purpose**  
Custom event sent to an instance room after players are moved into it.  
Carries a message that `GameSystem` displays as a toast.

---

### Properties

| Property | Type    | Purpose |
|-----------|---------|---------|
| `message` | `string` | Text shown to players when the event arrives. |

---

### Usage
- Created in `GameSystem:MovePlayersToInstance` and passed as `withEvent` to `SceneManagement:MoveUsersToInstance`, which sends it with `_RoomService:RequestSendEventToRoomAndWait`.
- Handled by `GameSystem.HandleRoomEnteredEvent` (`_UIToast:ShowMessage(event.message)`).

---

## PlayerMapComponent

**Purpose**  
Empty placeholder component with no properties or logic. It is not referenced by any other script in this project.

---

### Properties

This script has no custom properties.

---

### Related Types & Services
- **Component** – Base class for entity components.

---

## MoveToMap (Scripts/InstanceMapTest)

**Purpose**  
Developer/test component that moves **all** players into a brand-new instance room or back to a static map using keyboard shortcuts.

---

### Properties

| Property  | Type     | Purpose |
|-----------|----------|---------|
| `roomIdx` | `integer`| Counter appended to the instance key (`"Map"..roomIdx`) so each call creates a new instance room. Starts at `0`. |
| `mapName` | `string` | Destination map inside the instance room. Defaults to `"map02"`. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|---------|
| `GetOrCreateInstanceRoom` | Server | Gets or creates instance room `"Map"..roomIdx`, then increments `roomIdx`. |
| `MoveToInstance` | Server | Moves all users to `mapName` inside a new instance room. |
| `MoveToStatic` | Server | Moves all users to the static map `map01`. |

---

### Methods

<details>
<summary>GetOrCreateInstanceRoom</summary>

**Execution Scope**: `Server`  

**Purpose**  
Creates a fresh instance room each call.

**Returns**  
- *(InstanceRoom)* – The room from `_RoomService:GetOrCreateInstanceRoom("Map"..tostring(roomIdx))`.

**Behavior**  
1. Gets or creates the room keyed by the current `roomIdx`.  
2. Increments `self.roomIdx`.

</details>

<details>
<summary>MoveToInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves every user into `mapName` inside an instance room.

**Behavior**  
1. Calls `GetOrCreateInstanceRoom()`.  
2. Collects all user IDs from `_UserService.UserEntities`.  
3. Calls `_RoomService:MoveUsersToInstanceRoom(room.InstanceKey, users, self.mapName)`. Assumes an instance-enabled map named `mapName` exists.

</details>

<details>
<summary>MoveToStatic</summary>

**Execution Scope**: `Server`  

**Purpose**  
Sends every user to the static map `map01`.

**Behavior**  
1. Collects all user IDs from `_UserService.UserEntities`.  
2. Calls `_RoomService:MoveUsersToStaticRoom(users, "map01")` (hard-coded; `mapName` is not used).

</details>

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandleKeyDownEvent` | `KeyDownEvent` | Service: InputService / Client | **M** calls `MoveToInstance()`, **N** calls `MoveToStatic()` (both forwarded to the server). |

---

### Related Types & Services
- **_RoomService** – Creates instance rooms and moves users between rooms.  
- **_UserService** – Lists all user entities.  
- **KeyDownEvent** – Input event used to trigger the test moves.

---

## MoveToMap (InstanceMapTest)

**Purpose**  
Identical copy of [MoveToMap (Scripts/InstanceMapTest)](#movetomap-scriptsinstancemaptest) located in `MyDesk/InstanceMapTest/`. See that section for properties, methods and handlers.

---

## UIPopup (Scripts/UI)

**Purpose**  
Global Logic (`_UIPopup`) that shows a modal popup with a message and OK/Cancel buttons, with a scale/fade tween and optional callbacks. Used by `GameSystem:ShowPopup`.

---

### Properties

| Property           | Type            | Purpose |
|-------------------|----------------|---------|
| `message`         | TextComponent   | Text element that displays the popup message. |
| `btnOk`           | ButtonComponent | OK button. |
| `btnCancel`       | ButtonComponent | Cancel button. |
| `popupGroup`      | Entity          | Parent group enabled/disabled with the popup; its `CanvasGroupComponent` is faded. |
| `popup`           | Entity          | Popup panel whose `UITransformComponent.UIScale` is tweened. |
| `onOk`            | any             | Callback executed when OK is clicked. |
| `onCancel`        | any             | Callback executed when Cancel is clicked. |
| `duration`        | number          | Duration of the open/close tween in seconds (`0.15`). |
| `from`            | number          | Starting scale for the tween (`0.5`). |
| `to`              | number          | Target scale for the tween (`1`). |
| `isOpen`          | boolean         | Whether the popup is currently open. |
| `isTweenPlaying`  | boolean         | Whether a tween is in progress (clicks are ignored meanwhile). |
| `tweenEventId`    | number          | Timer ID of the active tween. |
| `okHandler`       | any             | Event handler returned by connecting the OK button. |
| `cancelHandler`   | any             | Event handler returned by connecting the Cancel button. |

---

### Method Summary

| Method         | Execution Scope  | Purpose |
|----------------|-----------------|---------|
| `Open`         | Client/Server   | Opens the popup with a message and optional OK/Cancel callbacks. |
| `OnClickOk`    | Client/Server   | Runs the OK callback and closes the popup. |
| `OnClickCancel`| Client/Server   | Runs the Cancel callback and closes the popup. |
| `Close`        | Client/Server   | Disconnects the buttons and plays the closing tween. |
| `StartTween`   | Client/Server   | Animates the popup scale and alpha for opening or closing. |

---

### Methods

<details>
<summary>Open</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Displays the popup with the given message and callbacks.

**Parameters**  
- `message` *(string)* – Message to display.  
- `onOk` *(any)* – Optional callback for OK.  
- `onCancel` *(any)* – Optional callback for Cancel.

**Behavior**  
1. Returns if the popup is already open; otherwise sets `isOpen`.  
2. Enables `popupGroup` and sets the message text.  
3. Stores the callbacks and connects the buttons to `OnClickOk` / `OnClickCancel`.  
4. Calls `StartTween(true)`.

</details>

<details>
<summary>OnClickOk</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Handles the OK button.

**Behavior**  
1. Ignores the click while a tween is playing.  
2. Runs `onOk` if set, then clears it.  
3. Calls `Close()`.

</details>

<details>
<summary>OnClickCancel</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Handles the Cancel button.

**Behavior**  
1. Ignores the click while a tween is playing.  
2. Runs `onCancel` if set, then clears it.  
3. Calls `Close()`.

</details>

<details>
<summary>Close</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Closes the popup.

**Behavior**  
1. Disconnects the OK and Cancel button handlers.  
2. Calls `StartTween(false)`; `isOpen` is cleared when the tween finishes.

</details>

<details>
<summary>StartTween</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Animates the popup's scale and transparency.

**Parameters**  
- `open` *(boolean)* – `true` for opening, `false` for closing.

**Behavior**  
1. Sets the initial scale (and alpha 0 when opening) and marks `isTweenPlaying`.  
2. Starts a 1/60 s repeating timer that accumulates elapsed time from `_UtilLogic.ElapsedSeconds`.  
3. Each tick sets `UIScale` from `_TweenLogic:Ease(from, to, duration, EaseType.SineEaseIn, t)` and `GroupAlpha = t / duration` (time runs backwards when closing).  
4. When `duration` is reached, clears the timer and `isTweenPlaying`; when closing also disables `popupGroup` and clears `isOpen`.

</details>

---

### Related Types & Services
- **Logic** – Base class for global logic scripts.  
- **TextComponent / ButtonComponent** – Message text and buttons.  
- **CanvasGroupComponent / UITransformComponent** – Fade and scale targets.  
- **_TweenLogic** – Easing function for the animation.  
- **_TimerService** – Repeating timer that drives the tween.  
- **_UtilLogic** – Elapsed time in seconds.

---

## UIPopup (MyDesk root)

**Purpose**  
Identical copy of [UIPopup (Scripts/UI)](#uipopup-scriptsui) located directly in `MyDesk/`. See that section for details.

---

## UIToast (Scripts/UI)

**Purpose**  
Global Logic (`_UIToast`) that displays a temporary toast message with fade-in/fade-out and a slide animation. Used by `GameSystem.HandleRoomEnteredEvent`.

---

### Properties

| Property          | Type            | Purpose |
|------------------|----------------|---------|
| `message`        | TextComponent   | Text component that displays the toast message. |
| `toastGroup`     | Entity          | Parent entity enabled while the toast is visible. |
| `duration`       | number          | Seconds the toast stays visible before fading out (`2`). |
| `tweenDuration`  | number          | Seconds for the fade-in and fade-out (`0.1`). |
| `tweenEventId`   | number          | Timer ID of the active tween (`0` when idle). |
| `isTweenPlaying` | boolean         | Declared but not used by the current code. |
| `inited`         | boolean         | Whether `offset` has been captured. |
| `offset`         | number          | Vertical slide distance, captured from the text's initial anchored Y position. |

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
Displays a toast message.

**Parameters**  
- `message` *(string)* – The message text.

**Behavior**  
1. On first use, stores `offset` from the text's anchored Y position.  
2. Sets the text and calls `StartTween()`.

</details>

<details>
<summary>StartTween</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Animates the toast's alpha and vertical position.

**Behavior**  
1. If a tween is running, clears its timer (keeping the current alpha); otherwise starts from alpha 0.  
2. Enables `toastGroup` and starts a 1/60 s repeating timer.  
3. Fades in for `duration` seconds, then fades out over `tweenDuration`; position follows `_TweenLogic:Ease` of the alpha.  
4. After `duration + tweenDuration`, disables `toastGroup` and clears the timer.

</details>

---

### Related Types & Services
- **Logic** – Base class for global logic scripts.  
- **TextComponent** – UI text element.  
- **CanvasGroupComponent / UITransformComponent** – Fade and position targets.  
- **_TweenLogic** – Easing function.  
- **_TimerService** – Repeating timer that drives the tween.  
- **_UtilLogic** – Elapsed time in seconds.

---

## UIToast (MyDesk root)

**Purpose**  
Identical copy of [UIToast (Scripts/UI)](#uitoast-scriptsui) located directly in `MyDesk/`. See that section for details.

---

## Popupbutton (Scripts/UI)

**Purpose**  
UI template component that opens and closes sample popups (Basic, Shop, ShopResult, Achievement, DailyReward, Inventory, Result) when specific UI buttons are clicked, and toggles the shop button group.

---

### Properties

This script has no custom properties.

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandleButtonClickEvent` | `ButtonClickEvent` | Entity a99c2783-... / ClientOnly | Enables `/ui/PopupGroup/BasicPopup`; its OK and Cancel buttons disable it. |
| `HandleButtonClickEvent2` | `ButtonClickEvent` | Entity 241ee38a-... / ClientOnly | Enables `ShopPopup`; `Panel/CloseButton` disables it. |
| `HandleButtonClickEvent3` | `ButtonClickEvent` | Entity 57602223-... / ClientOnly | Enables `ShopResult`; `PopupBtnOK` disables it. |
| `HandleButtonClickEvent4` | `ButtonClickEvent` | Entity 076c41ef-... / ClientOnly | Enables `AchievementPopup`; `Panel/CloseButton` disables it. |
| `HandleButtonClickEvent5` | `ButtonClickEvent` | Entity 5fbdaa56-... / ClientOnly | Enables `DailyRewardPopup`; `Panel/CloseButton` disables it. |
| `HandleButtonClickEvent6` | `ButtonClickEvent` | Entity 872aef39-... / ClientOnly | Enables `InventoryPopup`; `Panel/CloseButton` disables it. |
| `HandleButtonClickEvent7` | `ButtonClickEvent` | Entity 0cfa965e-... / ClientOnly | Enables `ResultPanel`; `ReturnToLobby` disables it. |
| `HandleButtonClickEvent8` | `ButtonClickEvent` | Entity c760d7e3-... / ClientOnly | Toggles `/ui/ButtonGroup/Btn_Shop` and `Btn_ShopResult` and flips the clicked button's `UISprite`. |

Note: each open click connects a new close handler without disconnecting the previous one, so handlers accumulate on repeated use.

---

### Related Types & Services
- **_EntityService** – Finds the popup entities by path.  
- **ButtonClickEvent** – Raised by the UI buttons.  
- **SpriteGUIRendererComponent** – Flipped to indicate the toggle state.

---

## Popupbutton (MyDesk root)

**Purpose**  
Identical copy of [Popupbutton (Scripts/UI)](#popupbutton-scriptsui) located directly in `MyDesk/`. See that section for details.

---

## Changelog
| Version | Changelog | Download |
|---------|-------------|-------------|
| V1_0 | Initial release with scene, portal, and room coordination systems. | [Download](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/raw/refs/heads/main/Instance_Maps/Room_World_Instance_Demo.mod) |
