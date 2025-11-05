# Instance Room and Worlds Demo 

## Youtube Tutorial
[![Youtube Tutorial](https://img.youtube.com/vi/yjMLMl5KotY/0.jpg)](https://youtube.com/playlist?list=PLYnmZFABLxZzFWegbGAriHwqYU4WFFVno&si=V_Iu8BNT14kQUAvP "Let’s Explore Instance Worlds and Rooms")

## Mod Installation Instructions
[Mod File Installation Guide](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects?tab=readme-ov-file#project-import-instructions)

## Project File
[Download Latest Project File](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/blob/8a5f8acb5b51b595adf2175bad948005b8be92db/Instance_Maps/Room_World_Instance_Demo.mod)

---

## Table of Contents
- [Overview](#overview)
- [Script Documentation](#script-documentation)
  - [SceneManagement](#scenemanagement)
  - [RoomInfo](#roominfo)
  - [CustomPortalComponent](#customportalcomponent)
  - [WorldManagement](#worldmanagement)
  - [MoveToMap](#movetomap)
  - [GameSystem](#gamesystem)
  - [RoomEnteredEvent](#roomenteredevent)
- [Changelog](#changelog)
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

---

## Script Documentation

---

## SceneManagement

**Purpose**  
Coordinates room and scene transitions across static and instance maps.  
Manages instance initialization, creation, and player movement between rooms while supporting shared-memory operations for global numeric values.  

---

### Properties

| Property  | Type                      | Purpose |
|------------|---------------------------|----------|
| `rooms`    | `table<string, RoomInfo>` | Stores configuration data for all instance rooms keyed by `instanceName`. Contains properties such as map lists and capacity. |
| `roomIdx`  | `table`                   | Tracks the number of created rooms per `instanceName` to ensure each new instance receives a unique identifier. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|----------|
| `ReturnUsersToStatic(users, mapName)` | Server | Moves a list of users from an instance room back to its static version. |
| `MoveToMap(userId, mapName, customPortalEntity)` | Server | Moves a single player between static or instance maps. Only works for same-space transitions. |
| `ReturnToStatic(userId, mapName)` | Server | Returns a single user from an instance to the target static map. |
| `CreateAndEnterInstanceMap(userId, instanceName, mapName)` | Server | Creates or fetches an instance room and moves a player to the specified map. |
| `InitInstanceRoom(maps, instanceName, maxCapacity)` | Server | Initializes metadata for an instance room, defining maps and capacity. |
| `CreateInstanceRoom(instanceName)` | Server | Creates a new instance room using previously initialized data. |
| `GetOpenInstance(instanceName, usersToMove)` | Server | Returns the ID of an available instance room that can accommodate additional users. |
| `MoveUsersToInstance(users, instanceName, mapName, withEvent)` | Server | Moves multiple users into an instance room and optionally triggers a post-move event. |
| `CreateSharedNumber(space, variableName, variableValue)` | Client/Server | Creates a shared-memory numeric variable. |
| `GetSharedNumber(space, variableName)` | Client/Server | Retrieves the numeric value of a shared variable from memory. |
| `UpdateSharedNumber(space, variableName, variableValue)` | Client/Server | Atomically updates a shared-memory numeric variable with concurrency safety. |

---

### Methods

<details>
<summary>ReturnUsersToStatic</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves multiple users from an instance back to its corresponding static map.

**Parameters**  
- `users` *(table<string>)* – List of player IDs.  
- `mapName` *(string)* – Name of the static map to return to.

**Behavior**  
1. Calls `_RoomService:MoveUsersToStaticRoom(users, mapName)`.  
2. Should only be executed within an instance room context.

</details>

---

<details>
<summary>MoveToMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Transfers a player between maps within the same environment type (static → static or instance → instance).

**Parameters**  
- `userId` *(string)* – The player’s ID.  
- `mapName` *(string)* – The name of the destination map.  
- `customPortalEntity` *(Entity)* – Optional portal entity; if `nil`, a map path entity is used.

**Behavior**  
1. Resolves a target entity using `_EntityService:GetEntityByPath("/maps/" .. mapName)` if not provided.  
2. Validates that the target exists.  
3. Calls `_TeleportService:TeleportToEntity(user, targetEntity)`.  
4. Logs a warning if teleportation fails.

</details>

---

<details>
<summary>ReturnToStatic</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves an individual player from an instance room back to a static map.

**Parameters**  
- `userId` *(string)* – ID of the player.  
- `mapName` *(string)* – Target static map name.

**Behavior**  
1. Logs the action.  
2. Calls `_RoomService:MoveUserToStaticRoom(userId, mapName)`.

</details>

---

<details>
<summary>CreateAndEnterInstanceMap</summary>

**Execution Scope**: `Server`  

**Purpose**  
Creates or reuses an instance room for a player and moves them into it.

**Parameters**  
- `userId` *(string)* – Player ID.  
- `instanceName` *(string)* – Instance name or key.  
- `mapName` *(string)* – Map to move into.

**Behavior**  
1. Gets or creates the instance room via `_RoomService:GetOrCreateInstanceRoom(instanceName)`.  
2. Calls `instanceRoom:MoveUser(userId)`.

</details>

---

<details>
<summary>InitInstanceRoom</summary>

**Execution Scope**: `Server`  

**Purpose**  
Initializes metadata for an instance room type.

**Parameters**  
- `maps` *(table)* – Maps associated with the instance.  
- `instanceName` *(string)* – Key name for the instance.  
- `maxCapacity` *(integer)* – Maximum player count per instance.

**Behavior**  
1. Ensures the instance hasn’t been initialized before.  
2. Creates and stores a `RoomInfo` object with capacity and map data in `self.rooms[instanceName]`.  
3. Prepares the instance for future creation calls.

</details>

---

<details>
<summary>CreateInstanceRoom</summary>

**Execution Scope**: `Server`  

**Purpose**  
Creates a new instance room using stored configuration data.

**Parameters**  
- `instanceName` *(string)* – The instance type to create.

**Behavior**  
1. Validates that the instance has been initialized.  
2. Creates a unique instance using `_RoomService:CreateInstanceRoom()`.  
3. Updates internal index tracking (`self.roomIdx`).  
4. Returns the created instance room.

</details>

---

<details>
<summary>GetOpenInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Finds and returns an open instance room that has enough capacity for additional players.

**Parameters**  
- `instanceName` *(string)* – Instance name.  
- `usersToMove` *(integer)* – Number of users to move.

**Behavior**  
1. Iterates through `_RoomService.InstanceRooms`.  
2. Selects the first available room with enough capacity.  
3. Creates a new room if none are available.  
4. Returns the instance key of the selected room.

</details>

---

<details>
<summary>MoveUsersToInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves a group of players into a new or existing instance.

**Parameters**  
- `users` *(table<string>)* – Player IDs to move.  
- `instanceName` *(string)* – Instance to move to.  
- `mapName` *(string)* – Destination map within that instance.  
- `withEvent` *(any)* – Optional event triggered after movement.

**Behavior**  
1. Calls `GetOpenInstance()` to find a valid room.  
2. Moves players using `_RoomService:MoveUsersToInstanceRoom()`.  
3. Sends `withEvent` if provided.

</details>

---

<details>
<summary>CreateSharedNumber</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Creates a numeric variable in shared memory.

**Parameters**  
- `space` *(string)* – Shared memory space.  
- `variableName` *(string)* – Variable identifier.  
- `variableValue` *(number)* – Initial value.

**Behavior**  
1. Retrieves shared memory via `_RoomService:GetSharedMemory(space)`.  
2. Calls `mem:CreateVariableAndWait(variableName, tostring(variableValue))`.  
3. Logs any creation failures.

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
2. Reads variable; if missing, calls `CreateSharedNumber()`.  
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

- **RoomInfo** – Stores configuration and capacity data for instance rooms.  
- **_RoomService** – Handles instance creation, user movement, and shared-memory access.  
- **_EntityService** – Used to find destination entities or maps by path.  
- **_TeleportService** – Teleports players to target entities.  
- **SharedMemoryResultCode** – Return codes for shared-memory operations.  
- **SharedVariableKeyInfo** – Used for concurrency-safe variable updates.  

---

## RoomInfo

**Purpose**  
Lightweight data container (struct) that describes an instance room configuration.  
Holds a unique room identifier, capacity, and a list of related map names used by `SceneManagement` to initialize, create, and route players into instance rooms.

---

### Properties

| Property       | Type              | Purpose |
|----------------|-------------------|---------|
| `roomId`       | `string`          | Unique identifier for the room/instance family. Used as a key to group and manage a set of instance rooms. |
| `maxCapacity`  | `number`          | Maximum number of players allowed in a single instance of this room. Defaults to `10`. |
| `maps`         | `table<string>`   | Collection of map names associated with this room (e.g., lobby/static map, destination/instance map). Used by scene/room creation logic to resolve teleport targets. |

---

### Method Summary

_No methods defined._

---

### Methods

_No methods to document for this script._

---

### Related Types & Services
- **SceneManagement** – Consumes `RoomInfo` to initialize and create instance rooms, assign capacity, and resolve map names.
- **_RoomService** – Uses room identifiers/capacity (from `RoomInfo`) when creating and managing instance rooms.

## CustomPortalComponent

**Purpose**  
Extends a standard portal with custom logic for **static** and **instance** room routing.  
On startup it can initialize instance-room metadata, and on use it decides whether to send the user to an **instance** (creating/entering as needed) or to a **static** map via the central `SceneManagement` logic.

---

### Properties

| Property              | Type         | Purpose |
|----------------------|--------------|---------|
| `isInstanceRoom`     | `boolean`    | When `true`, this portal treats the destination as part of an **instance** workflow (create or enter an instance). When `false`, it performs a regular static-room move. Defaults to `false`. |
| `roomName`           | `string`     | Name of the destination map/room. Used by `SceneManagement` to locate `/maps/<roomName>` or to target an instance’s map. |
| `customPortalEntity` | `EntityRef`  | Optional explicit destination entity. When `nil`, the component resolves `/maps/<roomName>` automatically. |

---

### Method Summary

| Method                 | Execution Scope | Purpose |
|------------------------|-----------------|---------|
| `OnBeginPlay()`        | Client/Server   | Initializes instance-room metadata (e.g., registers `roomName` with `SceneManagement`) and sets up capacity defaults when needed. |
| `HandlePortalUseEvent(event)` | Client/Server (handler) | Responds to a `PortalUseEvent`. If `isInstanceRoom` is set, moves the user into (or creates and enters) an instance; otherwise moves the user to a static destination. Honors `customPortalEntity` if provided. |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Prepares this portal for use by registering instance configuration when `isInstanceRoom` is enabled.

**Behavior**  
- Calls `_SceneManagement:InitInstanceRoom({ self.roomName }, self.Entity.Id, 2)` (from the script body preview), which:
  - Registers the instance family using the portal’s entity ID as the `instanceName`.
  - Seeds map configuration with `roomName`.
  - Uses a default capacity of `2` (adjust this in the script if your design needs a different number).

</details>

---

<details>
<summary>HandlePortalUseEvent</summary>

**Execution Scope**: `Client/Server` (event handler)  

**Purpose**  
Routes the **local user** when this portal is used, choosing between **instance** and **static** flows. Also supports an explicit `customPortalEntity` anchor when present.

**Parameters**  
- `event` *(PortalUseEvent)* – Fired when the portal is interacted with/entered.

**Behavior**  
1. If a valid `customPortalEntity` is set, it becomes the target; otherwise the component resolves `/maps/<roomName>`.  
2. If `isInstanceRoom` is `true`:
   - Uses `_SceneManagement:CreateAndEnterInstanceMap(localUserId, self.Entity.Id, self.roomName)` to create (or fetch) an instance and move the user into its destination map.  
3. If `isInstanceRoom` is `false`:
   - Uses `_SceneManagement:MoveToMap(localUserId, self.roomName, self.customPortalEntity)` to move the user to the static destination.  
4. May include safety checks (e.g., `isvalid(self.PortalEntityRef)`) before attempting movement.

</details>

---

### Related Types & Services
- **`SceneManagement`** – Performs all map/instance routing (`InitInstanceRoom`, `MoveToMap`, `CreateAndEnterInstanceMap`).  
- **`PortalUseEvent`** – Event raised when a portal is used.  

## WorldManagement

**Purpose**  
Provides centralized control over **world-level instance management** and **shared data synchronization**.  
Handles discovery of open world instances, warping users to available ones, and managing shared numeric data using the `_WorldInstanceService`.

---

### Properties

_No properties defined in this script._

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|----------|
| `FindOpenInstance(usersToMove)` | Server | Finds the next open world instance capable of accepting additional users. |
| `WarpToOpenInstance(users)` | Server | Warps all provided users to the next available world instance. |
| `CreateSharedNumber(space, variableName, variableValue)` | Client/Server | Creates a numeric variable in world-shared memory. |
| `GetSharedNumber(space, variableName)` | Client/Server | Retrieves a numeric variable from shared memory. Creates one if missing. |
| `UpdateSharedNumber(space, variableName, variableValue)` | Client/Server | Safely updates or increments a shared numeric variable with concurrency checks. |

---

### Methods

<details>
<summary>FindOpenInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Finds the next available open world instance with sufficient capacity for the specified number of players.

**Parameters**  
- `usersToMove` *(integer)* – Number of users that must fit in the next instance.

**Behavior**  
1. Calls `_WorldInstanceService:GetWorldInstanceInfoPagesAndWait()`.  
2. Iterates through returned instance info pages.  
3. Selects the first open instance that can accommodate the users.  
4. Returns that instance’s metadata; returns `nil` if none is found.

</details>

---

<details>
<summary>WarpToOpenInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves a set of users to the next available world instance discovered by `FindOpenInstance`.

**Parameters**  
- `users` *(table<string>)* – List of user IDs to warp.

**Behavior**  
1. Prepares optional data payloads (`warpData`) and serializes them with `_UtilLogic:TableToString()`.  
2. Calls `self:FindOpenInstance(#users)` to retrieve a target instance.  
3. If an instance is found, performs the warp using `_WorldInstanceService:WarpUsersToWorldInstanceAndWait(users, instanceKey, serializedData)`.  
4. Logs failures or invalid instance results.

</details>

---

<details>
<summary>CreateSharedNumber</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Creates a numeric variable in shared memory space accessible across all world instances.

**Parameters**  
- `space` *(string)* – Shared memory namespace.  
- `variableName` *(string)* – Name of the variable to create.  
- `variableValue` *(number)* – Initial value.

**Behavior**  
1. Calls `_WorldInstanceService:GetSharedMemory(space)`.  
2. Uses `mem:CreateVariableAndWait(variableName, tostring(variableValue))` to register it.  
3. Logs an error if creation fails or the shared memory call returns a non-OK code.

</details>

---

<details>
<summary>GetSharedNumber</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Retrieves the value of a shared numeric variable from world memory, creating it if it doesn’t exist.

**Parameters**  
- `space` *(string)* – Shared memory space.  
- `variableName` *(string)* – Target variable name.

**Returns**  
- *(number)* – Current or default numeric value.

**Behavior**  
1. Gets shared memory using `_WorldInstanceService:GetSharedMemory(space)`.  
2. Fetches the variable via `mem:GetVariableAndWait(variableName)`.  
3. If the variable doesn’t exist, initializes it via `CreateSharedNumber()`.  
4. Converts and returns the numeric value, logging conversion errors if needed.

</details>

---

<details>
<summary>UpdateSharedNumber</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Safely updates a numeric shared-memory variable in world space using ETag validation.

**Parameters**  
- `space` *(string)* – Shared memory namespace.  
- `variableName` *(string)* – Variable key name.  
- `variableValue` *(number)* – Value to add or set.

**Behavior**  
1. Retrieves shared memory (`_WorldInstanceService:GetSharedMemory(space)`).  
2. Reads current variable info with `mem:GetVariableAndWait(variableName)`.  
3. If missing, creates a new variable.  
4. Updates the variable using `mem:UpdateVariableAndWait(keyInfo, tostring(value + 1))` or equivalent logic.  
5. Handles precondition failures (`PreconditionFailed`) by logging ETag mismatches.

</details>

---

### Related Types & Services
- **_WorldInstanceService** – Provides APIs for discovering and managing world instances, teleportation, and shared-memory access.  
- **_UtilLogic** – Utility functions (e.g., serialization, table conversion).  
- **SharedMemoryResultCode** – Enum for shared-memory operation outcomes.  
- **SharedVariableKeyInfo** – Encapsulates shared variable metadata for safe updates.  

## MoveToMap

**Purpose**  
Provides convenient developer/test utilities to move the local player to either a **new/existing instance** or a **static** map.  
Includes a helper to get-or-create an instance room (incrementing an internal index) and simple handlers that can be tied to key input for quick teleports during development.

---

### Properties

| Property  | Type     | Purpose |
|-----------|----------|---------|
| `roomIdx` | `integer`| Internal counter used when creating successive instance rooms so each is unique. Starts at `0`. |
| `mapName` | `string` | Default destination map name (e.g., `"map02"`). Used when moving to static or instance maps if no other target is provided. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|---------|
| `GetOrCreateInstanceRoom()` | Server | Finds or creates a new instance room, incrementing `roomIdx` to ensure uniqueness, and returns the room handle/info. |
| `MoveToInstance()` | Server | Moves the local (or targeted) user into the specified `mapName` inside a created/found instance room. |
| `MoveToStatic()` | Server | Teleports the local (or targeted) user to the **static** version of `mapName`. |
| `HandleKeyDownEvent(event)` | Client/Server | Keyboard event handler intended for dev/testing; triggers instance or static movement based on key input. |

---

### Methods

<details>
<summary>GetOrCreateInstanceRoom</summary>

**Execution Scope**: `Server`  

**Purpose**  
Creates a **brand-new instance** (or returns an existing one as implemented) and advances `roomIdx` so each call yields a unique room when desired.

**Behavior**  
1. Uses room services to **create** an instance room (e.g., `_RoomService:GetOrCreateInstanceRoom(...)`).  
2. Increments `self.roomIdx` (`self.roomIdx = self.roomIdx + 1`).  
3. Returns the newly created or resolved `instanceRoom` reference.

**Returns**  
- *(any)* – Handle or info object representing the instance room.

</details>

---

<details>
<summary>MoveToInstance</summary>

**Execution Scope**: `Server`  

**Purpose**  
Moves a user into the configured `mapName` located within an instance room.

**Behavior**  
1. Calls `self:GetOrCreateInstanceRoom()` to obtain an instance context.  
2. Resolves a destination target (e.g., instance map anchor for `/maps/<mapName>`).  
3. Invokes the appropriate service API to move the player to the instance’s `mapName` (e.g., `_RoomService` / teleport service).

</details>

---

<details>
<summary>MoveToStatic</summary>

**Execution Scope**: `Server`  

**Purpose**  
Sends a user to the **static** map named by `self.mapName`.

**Behavior**  
1. Resolves the static map entity (e.g., `_EntityService:GetEntityByPath("/maps/" .. self.mapName)`).  
2. Teleports the player to that static destination (e.g., via `_TeleportService` or room service).

</details>

---

<details>
<summary>HandleKeyDownEvent</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Development convenience handler: reacts to keyboard input to trigger quick teleports to **instance** or **static** destinations.

**Parameters**  
- `event` *(KeyDownEvent)* – Keyboard event with a key code/name.

**Behavior**  
- Depending on your key mapping in the script (e.g., a specific key), invokes `MoveToInstance()` or `MoveToStatic()` for rapid testing.

</details>

---

### Related Types & Services
- **_RoomService** – Creating/finding instance rooms and moving users between rooms.  
- **_EntityService** – Resolving `/maps/<mapName>` anchors for static teleports.  
- **_TeleportService** – Executing actual player teleports to target entities.  
- **KeyDownEvent** – Input event used to trigger dev/test teleports.

## GameSystem

**Purpose**  
Top-level coordinator for lobby/game flow and quick testing actions.  
Initializes instance configuration, wires UI (start button) interactions, moves players into **instance** rooms or back to **starting/static** rooms, reacts to **RoomEnteredEvent** toasts, and exposes developer shortcuts (e.g., F1 to warp to an open world instance). Also demonstrates reading warp payload data and showing a popup.

---

### Properties

| Property       | Type     | Purpose |
|----------------|----------|---------|
| `startButton`  | `Entity` | UI button entity used to start a run or return players to the starting area. The script connects a click handler on the client. *(Default GUID in file: `"7da83c1a-b787-499a-b1e4-4a23c40fdadf"`)* |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|-----------------|---------|
| `OnBeginPlay()` | Client/Server | Client: connects `startButton` click to trigger either **move to instance** or **return to starting** flows. Server: can be used for any boot-time setup if needed. |
| `InitInstance()` | Client/Server | Prepares instance configuration (maps list, instance key, capacity) and registers it in `SceneManagement`. |
| `MovePlayersToInstance()` | Client/Server | Groups target users (e.g., local player / party) and moves them to the configured **instance** destination. |
| `MovePlayersToStarting()` | Client/Server | Returns the same set of users back to the **starting/static** map. |
| `HandleRoomEnteredEvent(event)` | Client/Server | Handles a custom `RoomEnteredEvent` by reading `event.message` and displaying a toast. |
| `ShowPopup()` | Client/Server | Opens a modal popup showing a value (example of `_UIPopup` usage). |
| `UserEnterEvent(event)` | Client/Server | Runs when a user joins. Demonstrates reading **last warp record** data and decoding it for logging/logic. |
| `OnKeyDown(event)` | Client/Server | Developer shortcut: on **F1**, warps the local user to the next **open world instance** via `WorldManagement`. |

---

### Methods

<details>
<summary>OnBeginPlay</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Sets up UI and initial state; on the **client**, binds `startButton` click to drive either instance entry or return to starting.

**Behavior**  
- If `self:IsClient()`:
  1. Finds/uses `self.startButton`.
  2. `ConnectEvent(ButtonClickEvent, function() ... end)` to react to clicks.
  3. In the click handler, decides between `self:MovePlayersToStarting()` **or** `self:MovePlayersToInstance()` (e.g., based on your UI/flag), then invokes it.

</details>

---

<details>
<summary>InitInstance</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Registers an instance “family” with maps and capacity so future calls can create/find instances.

**Behavior**  
1. Builds a map list (e.g., `{"Instance_Room1", "Instance_Room2", ...}`).
2. Chooses an `instanceName` and `maxCapacity`.
3. Calls `_SceneManagement:InitInstanceRoom(maps, instanceName, maxCapacity)` to register.

</details>

---

<details>
<summary>MovePlayersToInstance</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Moves the selected users (commonly the local player, or a small group) into the configured **instance** map.

**Behavior**  
1. Gathers user IDs (e.g., `_UserService.LocalPlayer.PlayerComponent.UserId` or a group).
2. Invokes `SceneManagement` to place users into a suitable open instance (e.g., `CreateAndEnterInstanceMap` / `MoveUsersToInstance` depending on your flow).
3. Optionally passes an event/payload so clients can show UI after arrival.

</details>

---

<details>
<summary>MovePlayersToStarting</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Returns the same set of users back to the **static** starting area.

**Behavior**  
1. Gathers the relevant user IDs.
2. Calls `SceneManagement` to return them to a static map (e.g., `ReturnUsersToStatic(users, mapName)`).

</details>

---

<details>
<summary>HandleRoomEnteredEvent</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Shows feedback when room entry completes.

**Parameters**  
- `event` *(RoomEnteredEvent)* – Contains a `message` string to show.

**Behavior**  
- Reads `event.message`.
- Calls `_UIToast:ShowMessage(msg)` to display a toast.

</details>

---

<details>
<summary>ShowPopup</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Demonstrates `_UIPopup` usage to show a value in a modal dialog.

**Behavior**  
- Invokes `_UIPopup:Open("The value is " .. value, nil, nil)` (OK/Cancel callbacks can be supplied as needed).

</details>

---

<details>
<summary>UserEnterEvent</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Runs when a user enters the world; demonstrates reading data that might have been sent during a previous warp.

**Parameters**  
- `event` *(UserEnterEvent)* – Contains `UserId` for the joining user.

**Behavior**  
1. Reads `event.UserId`.  
2. Retrieves the last warp record for that user via `_WorldInstanceService` (e.g., `GetLastWarpRecordAndWait`).  
3. Deserializes payload: `_UtilLogic:StringToTable(warpRecord.Data)`.  
4. Example: logs a field (e.g., `userData.coins`) for analytics or conditional UI.

</details>

---

<details>
<summary>OnKeyDown</summary>

**Execution Scope**: `Client/Server`  

**Purpose**  
Developer/testing shortcut to try multi-instance flows.

**Parameters**  
- `event` *(KeyDownEvent)* – Keyboard event (contains key code/name).

**Behavior**  
- If `event.key == KeyboardKey.F1`:
  1. Builds a list with the local user ID: `{ _UserService.LocalPlayer.PlayerComponent.UserId }`.
  2. Calls `_WorldManagement:WarpToOpenInstance(users)` to hop to a discovered open world instance.

</details>

---

### Related Types & Services
- **`SceneManagement`** – Instance/static routing (`InitInstanceRoom`, move/return helpers).  
- **`WorldManagement`** – Cross-world instance discovery and warping (`WarpToOpenInstance`).  
- **`_WorldInstanceService`** – Access to world instances and warp records (e.g., `GetLastWarpRecordAndWait`).  
- **`_UIPopup`, `_UIToast`** – UI helpers for modal dialogs and transient messages.  
- **Events** – `ButtonClickEvent`, `RoomEnteredEvent`, `UserEnterEvent`, `KeyDownEvent`.  

## RoomEnteredEvent

**Purpose**  
Represents a simple event triggered when a player enters a new room or map.  
Carries a string message payload that can be displayed to the player or used for logging.  
Commonly consumed by `GameSystem` to show a toast message (`_UIToast:ShowMessage(event.message)`).

---

### Properties

| Property | Type    | Purpose |
|-----------|---------|---------|
| `message` | `string` | Optional text message passed along with the event. Used to display feedback or contextual information when a player enters a room. |

---

### Method Summary

_No methods defined in this script._

---

### Methods

_No methods to document for this script._

---

### Related Types & Services
- **`EventType`** – Base class for all custom event scripts in MapleStory Worlds.  
- **`GameSystem`** – Listens for this event and displays the contained `message` to players via UI toast notifications.  


## Changelog

| Version | Notes |
|---------|-------|
| v1.0 | Initial release with scene, portal, and room coordination systems. |

---


