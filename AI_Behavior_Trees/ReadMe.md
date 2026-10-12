# AI Behavior Trees

## Youtube Tutorial
_Video coming soon._

## Mod Installation Instructions
[Mod File Installation Guide](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects?tab=readme-ov-file#mod-file-import-instructions)

## Project File
[Download Latest Project File](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/raw/refs/heads/main/AI_Behavior_Trees/AIBehaviorTrees.mod)

## Table of Contents
- [Overview](#overview)
- [Setup and Installation](#setup-and-installation)
- [Script Documentation](#script-documentation)
  - [BossAI](#bossai)
  - [AttackDuration](#attackduration)
  - [AttackFast](#attackfast)
  - [BehaviorTreeEvent](#behaviortreeevent)
  - [ExecuteMethod](#executemethod)
  - [OnMethodExecuted](#onmethodexecuted)
  - [PlayAnimationFrames](#playanimationframes)
  - [SetState](#setstate)
  - [ShowWarning](#showwarning)
  - [SpawnAtPosition](#spawnatposition)
  - [SpawnProjectileAttack](#spawnprojectileattack)
  - [WaitAnimationDuration](#waitanimationduration)
  - [AttackInfo](#attackinfo)
  - [BossAttackComponent](#bossattackcomponent)
  - [BossHitComponent](#bosshitcomponent)
  - [BossProjectile](#bossprojectile)
  - [ProjectileAttackComponent](#projectileattackcomponent)
  - [PatrolBehaviorTree](#patrolbehaviortree)
  - [ActionFollow](#actionfollow)
  - [ActionPatrol](#actionpatrol)
  - [HasTarget](#hastarget)
  - [TestDecorator](#testdecorator)
  - [CustomStateAnimationComponent](#customstateanimationcomponent)
  - [ColliderVisualizer](#collidervisualizer)
  - [Monster](#monster)
  - [MonsterAttack](#monsterattack)
  - [PlayerAttack](#playerattack)
  - [PlayerHit](#playerhit)
  - [UIMyInfo](#uimyinfo)
  - [UIMyInfoSimple](#uimyinfosimple)
  - [UIPopup](#uipopup)
  - [UIToast](#uitoast)
  - [ObjectPoolComponent](#objectpoolcomponent)
  - [OnPoolRelease](#onpoolrelease)
  - [PoolManager](#poolmanager)
  - [PooledObject](#pooledobject)
  - [ActionButtonBehavior](#actionbuttonbehavior)
  - [ActionButtonComponent](#actionbuttoncomponent)
  - [ToggleEnable](#toggleenable)
  - [HealthbarComponent](#healthbarcomponent)
  - [DestroyAfterFrames](#destroyafterframes)
  - [MathUtils](#mathutils)
  - [SpriteUtils](#spriteutils)
  - [TileUtils](#tileutils)
  - [Util](#util)
  - [AIChaseOrPatrol](#aichaseorpatrol)
  - [AddForce](#addforce)
  - [SetForce](#setforce)
  - [Chase](#chase)
  - [MoveTo](#moveto)
  - [MoveToDirection](#movetodirection)
  - [MoveToTarget](#movetotarget)
  - [Jump](#jump)
  - [DownJump](#downjump)
  - [SpawnEntity](#spawnentity)
  - [DestroyEntity](#destroyentity)
  - [SetEntityEnable](#setentityenable)
  - [PlayBasicParticle](#playbasicparticle)
  - [PlayAreaParticle](#playareaparticle)
  - [PlaySpriteParticle](#playspriteparticle)
  - [PlaySound](#playsound)
  - [Wait](#wait)
  - [WaitRandom](#waitrandom)
  - [WaitBlackboardTime](#waitblackboardtime)
  - [SetBlackboardValue_Bool](#setblackboardvalue_bool)
  - [SetBlackboardValue_Int](#setblackboardvalue_int)
  - [SetBlackboardValue_Number](#setblackboardvalue_number)
  - [SetBlackboardValue_String](#setblackboardvalue_string)
  - [SetBlackboardValue_Color](#setblackboardvalue_color)
  - [SetBlackboardValue_Vector2](#setblackboardvalue_vector2)
  - [SetBlackboardValue_Vector3](#setblackboardvalue_vector3)
  - [SetBlackboardValue_Vector4](#setblackboardvalue_vector4)
  - [SetBlackboardValue_Entity](#setblackboardvalue_entity)
  - [SetBlackboardValue_EntityRef](#setblackboardvalue_entityref)
  - [SetBlackboardValue_Component](#setblackboardvalue_component)
  - [SetBlackboardValue_ComponentRef](#setblackboardvalue_componentref)
  - [SetBlackboardValue_TargetPos](#setblackboardvalue_targetpos)
  - [RandomSelector](#randomselector)
  - [RandomSequence](#randomsequence)
  - [Inverter](#inverter)
  - [Succeeder](#succeeder)
  - [Loop](#loop)
  - [TimeLimit](#timelimit)
  - [Cooldown](#cooldown)
  - [CanMove](#canmove)
  - [IsOnGround](#isonground)
  - [IsJumping](#isjumping)
  - [BlackboardCondition_Bool](#blackboardcondition_bool)
  - [BlackboardCondition_Int](#blackboardcondition_int)
  - [BlackboardCondition_Number](#blackboardcondition_number)
  - [BlackboardCondition_String](#blackboardcondition_string)
  - [BlackboardCondition_Color](#blackboardcondition_color)
  - [BlackboardCondition_Vector2](#blackboardcondition_vector2)
  - [BlackboardCondition_Vector3](#blackboardcondition_vector3)
  - [BlackboardCondition_Vector4](#blackboardcondition_vector4)
  - [BlackboardCondition_Entity](#blackboardcondition_entity)
  - [BlackboardCondition_EntityRef](#blackboardcondition_entityref)
  - [BlackboardCondition_Component](#blackboardcondition_component)
  - [BlackboardCondition_ComponentRef](#blackboardcondition_componentref)
  - [CompareBlackboardValues_Bool](#compareblackboardvalues_bool)
  - [CompareBlackboardValues_Int](#compareblackboardvalues_int)
  - [CompareBlackboardValues_Number](#compareblackboardvalues_number)
  - [CompareBlackboardValues_String](#compareblackboardvalues_string)
  - [CompareBlackboardValues_Color](#compareblackboardvalues_color)
  - [CompareBlackboardValues_Vector2](#compareblackboardvalues_vector2)
  - [CompareBlackboardValues_Vector3](#compareblackboardvalues_vector3)
  - [CompareBlackboardValues_Vector4](#compareblackboardvalues_vector4)
  - [CompareBlackboardValues_Entity](#compareblackboardvalues_entity)
  - [CompareBlackboardValues_EntityRef](#compareblackboardvalues_entityref)
  - [CompareBlackboardValues_Component](#compareblackboardvalues_component)
  - [CompareBlackboardValues_ComponentRef](#compareblackboardvalues_componentref)
  - [Comparator](#comparator)
  - [OperatorPolicy](#operatorpolicy)
  - [ValueType](#valuetype)
- [Changelog & Download](#changelog)
---

## Overview
This project shows how to build monster and boss AI with MapleStory Worlds behaviour trees (`AIComponent`, `.behaviourtree` assets, and custom `@BTNode` scripts that extend `ActionNode`, `DecoratorNode` and `CompositeNode`). It contains three layers:
- **Simple AI** (`AIBehaviorTreeSimple`): patrol and follow nodes plus a `PatrolBehaviorTree` AI component that tracks nearby targets through a trigger.
- **Node library** (`Example-BehaviourTree` package): reusable movement, physics, lifecycle, FX/sound, timing and blackboard-writer action nodes; flow-control, state-check and blackboard-check decorators; random composites; and the `Comparator` / `OperatorPolicy` / `ValueType` utilities. The `AIChaseOrPatrol` sample component feeds the `ChaseOrPatrol` tree.
- **Advanced boss** (`AIBehaviorTreeAdvanced`): a `BossAI` component that writes the nearest players to the blackboard, plays state animations, telegraphs area attacks with growing warning circles timed to animation frames, fires pooled projectiles, tracks HP with a health bar, and runs long yielding attack methods through the `ExecuteMethod` node.

Supporting scripts cover monster/player combat (including a player knockback), object pooling, sprite/animation timing helpers, a collider debug visualizer, and HUD/popup/toast UI.

## Setup and Installation
* The project ships three maps: `BehaviorTreeDemo`, `BossMechanicsDemo` and `map01`. The AI scripts move entities with `KinematicbodyComponent` / `MovementComponent` and the knockback checks `RectTileMapComponent` tiles, so the demo maps are top-down (RectTile) maps.
* **Simple patrol/chase monsters** (`BehaviorTreeDemo`): monster entities carry `Monster`, `MonsterAttack`, `ColliderVisualizer` and `PatrolBehaviorTree` (an `AIComponent`), plus a `TriggerComponent` so entities entering the trigger become targets.
* **Sample chase-or-patrol monsters** (`BossMechanicsDemo`): monster entities carry `Monster`, `MonsterAttack`, `ColliderVisualizer` and `AIChaseOrPatrol`, whose `BehaviourTreeId` should point at `Sample/ChaseOrPatrol.behaviourtree` (it fills `bb_Player`).
* **Boss** (`AIBehaviorTreeAdvanced/Model_Boss_1.model`, placed in `BossMechanicsDemo`): carries `BossAI` (running the `BossMechanicsDemo_1.behaviourtree` tree), `BossAttackComponent`, `BossHitComponent` and `CustomStateAnimationComponent`, with a `StateComponent` using `BossState/BossStateSet` (`WAIT`, `ATTACKMELEE`, `ATTACKRANGE`, `DEAD`, ...).
    * Set `BossAI.healthBar` to an entity with `HealthbarComponent`, and `polygonGrowComp` / `polygonFullComp` to two `PolygonRendererComponent`s used as warning circles.
    * Set `BossAttackComponent.projectileModelID` to `Combat/Model_BossProjectile` (an entity with `BossProjectile`, `ProjectileAttackComponent` and a `TriggerComponent`). `BossAttackComponent` creates an `ObjectPool` child on the map and fills a `"BossProjectile"` pool on begin play.
    * The tree's blackboard uses `bb_Target`, `bb_TargetMelee`, `bb_TargetRange`, `bb_IsDead`, `bb_isMethodComplete`, `bb_warningRadius`, `bb_animationDuration`, `bb_meleeRadius`, `bb_rangeRadius` and others; `BossAI` writes the target and death keys.
* **Player**: `Global/DefaultPlayer.model` has `PlayerAttack` (hits monsters on the `"Attack"` action) and `PlayerHit` (hit cooldown plus a knockback when hit by an attacker in the `ATTACKMELEE` state, using the `KNOCKBACK` state in `Example-States/CustomPlayerState.stateset`).
* **UI**: `UIMyInfoSimple` is on the HUD in `ui/DefaultGroup.ui`. `UIPopup` and `UIToast` are Logic scripts whose properties reference UI entity IDs.
* `PoolManager`, `SpriteUtils`, `MathUtils`, `TileUtils`, `Util`, `Comparator`, `OperatorPolicy`, `ValueType`, `UIPopup` and `UIToast` are Logic scripts and need no attachment.
* To use any node from the library, add it to a `.behaviourtree` and set the owning `AIComponent`'s `BehaviourTreeId` to that tree.
* Press Play to run the world
    * Walk near the patrol monsters to see them switch between patrolling and chasing.
    * Approach the boss: it moves toward the nearest player, shows a green warning circle that grows before each attack, then performs either a melee area attack or a ranged attack that fires a volley of projectiles.
    * Attack the boss to lower its health bar; at 0 HP it switches to the `DEAD` state and the tree stops attacking.

## Script Documentation

> Behaviour-tree nodes (`@BTNode`) are ticked by their owning `AIComponent`; the trees in this project run on the server. Node methods have no `@ExecSpace` annotation, so they are listed as `Default (caller's side)`.

## BossAI

**Purpose**  
`AIComponent` subclass that runs the boss behaviour tree. On the server it refreshes the melee, ranged and nearest player targets on the blackboard every 0.5 s, tracks HP from `HitEvent`s and flags death on the blackboard, and exposes long-running yielding attack methods (`AttackMelee`, `AttackRange`) that the tree triggers through the `ExecuteMethod` node. On the client it keeps the health bar in sync.

---

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `warningColor` | Color | Warning circle colour. Not currently read by the script. |
| `polygonMaterialID` | string | Material for the full-size warning polygon. Not currently read. |
| `polygonGrowMaterialID` | string | Material for the growing warning polygon. Not currently read. |
| `meleeDetectRadius` | number | Players within this distance are written to the melee-target key. |
| `rangeDetectRadius` | number | Players within this distance are written to the ranged-target key. |
| `meleeRadius` | number | Radius of the melee area attack and its warning circle. |
| `rangeRadius` | number | Radius of the area hit around the boss during the ranged attack and its warning circle. |
| `healthBar` | HealthbarComponent | World/UI health bar updated with `hp / maxHp` on the client. |
| `projectileCount` | integer | Projectiles fired per ranged attack. |
| `rangedProjectileId` | string | Not currently read (`BossAttackComponent.projectileModelID` is used instead). |
| `attackPositionsParent` | Entity | Parent of possible ranged attack positions. Not currently read. |
| `meleeAttackFrame` | Vector2 | Frame range (x = first, y = last, 1-based) of the melee animation during which the hit lands. |
| `rangedAttackFrame` | Vector2 | Frame range of the ranged animation used for the warning and firing. |
| `bbMeleeTargetId` | string | Blackboard key for the closest player inside `meleeDetectRadius` (default `bb_TargetMelee`). |
| `bbRangeTargetId` | string | Blackboard key for the closest player inside `rangeDetectRadius` (default `bb_TargetRange`). |
| `bbNearestTarget` | string | Blackboard key for the closest player in the map (default `bb_Target`). |
| `bbIsDeadKey` | string | Blackboard bool key set to true when the boss dies (default `bb_IsDead`). |
| `maxHp` | number | Starting / maximum HP. |
| `hp` | number | Current HP. `@Sync`, hidden from inspector. |
| `isDead` | boolean | Guards against further damage once dead. `@Sync`, hidden from inspector. |
| `polygonGrowComp` | PolygonRendererComponent | Warning circle that grows from 0 to the attack radius during the wind-up. |
| `polygonFullComp` | PolygonRendererComponent | Warning circle drawn at the full attack radius during the wind-up. |
| `stateComp` | StateComponent | Cached state component. Hidden from inspector. |
| `stateAnimComp` | StateAnimationComponent | Cached state-animation component used to look up animation RUIDs. Hidden from inspector. |
| `attackComp` | BossAttackComponent | Cached attack component. Hidden from inspector. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBeginPlay` | Client/Server | Calls `Init()`. |
| `Init` | Default (caller's side) | Caches components, resets HP, fills the health bar (client) and starts the target timer (server). |
| `OnDestroy` | Client/Server | Clears the server target timer. |
| `UpdateTarget` | Default (server timer) | Writes the melee, ranged and nearest player targets to the blackboard. |
| `SetMethodComplete` | Default (caller's side) | Writes `bb_isMethodComplete`. |
| `Dead` | ServerOnly | Marks the boss dead, sets `bb_IsDead`, changes state to `DEAD`. |
| `OnSyncProperty` | ClientOnly | Refreshes the health bar when `hp` syncs. |
| `GetRandomPosition` | Default (caller's side) | Returns a random entry of `_T.rangedPositions` (currently unused). |
| `UpdateRadius` | Default (caller's side) | Places a polygon's vertices on a circle. |
| `ShowAttackWarningAndWait` | Default (caller's side) | Shows and grows the warning circles over a duration (yields). |
| `AttackMelee` | Default (caller's side) | Full melee attack sequence timed to animation frames (yields). |
| `AttackRange` | Default (caller's side) | Full ranged attack sequence with a projectile volley (yields). |

---

### Methods

<details>
<summary>Init</summary>

**Execution Scope**: `Default (caller's side)` (called from `OnBeginPlay` on both sides)  

**Purpose**  
Initializes the boss on server and client.

**Behavior**  
1. Caches `StateComponent`, `StateAnimationComponent` and `BossAttackComponent`.  
2. Sets `isDead = false` and `hp = maxHp`.  
3. On the client, calls `healthBar:Set(hp / maxHp)`.  
4. On the server, starts a repeating 0.5 s timer that calls `UpdateTarget()` (ID stored in `_T.targetCheckTimer`).

</details>

<details>
<summary>UpdateTarget</summary>

**Execution Scope**: `Default (caller's side)` (server timer callback)  

**Purpose**  
Refreshes the boss's targets on the blackboard.

**Behavior**  
1. Gets every player in the boss's map with `_UserService:GetUsersByMapComponent`.  
2. Tracks the closest player within `meleeDetectRadius`, the closest within `rangeDetectRadius`, and the closest overall.  
3. Writes them with `BlackBoard:SetEntity` to `bbMeleeTargetId`, `bbRangeTargetId` and `bbNearestTarget` (nil when no player qualifies).

</details>

<details>
<summary>Dead</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Handles boss death once.

**Behavior**  
1. Returns if already dead.  
2. Sets `isDead = true` and `BlackBoard:SetBool(bbIsDeadKey, true)` so the tree can react.  
3. Changes state to `"DEAD"`.

</details>

<details>
<summary>OnSyncProperty</summary>

**Execution Scope**: `ClientOnly`  

**Purpose**  
Keeps the health bar in sync with HP.

**Parameters**  
- `name` *(string)* – Name of the synced property.  
- `value` *(any)* – New value.

**Behavior**  
- When `name == "hp"`, calls `healthBar:Set(hp / maxHp)`.

</details>

<details>
<summary>UpdateRadius & ShowAttackWarningAndWait</summary>

**Execution Scope**: `Default (caller's side)`  

**Purpose**  
Draw the attack telegraph.

**Parameters**  
- `polygon` *(PolygonRendererComponent)*, `offset` *(Vector2)*, `radius` / `range` *(number)*, `duration` *(number)*.

**Behavior**  
- `UpdateRadius` moves the polygon's existing vertices evenly around a circle of `radius` centred on `offset`.  
- `ShowAttackWarningAndWait` enables both warning polygons, draws `polygonFullComp` at `range`, then grows `polygonGrowComp` from 0 to `range` over `duration` (10 updates per second using `wait`), and finally disables both.

</details>

<details>
<summary>AttackMelee</summary>

**Execution Scope**: `Default (caller's side)` (run from `OnMethodExecuted`)  

**Purpose**  
Performs the melee attack sequence, timed to the `ATTACKMELEE` animation.

**Behavior**  
1. `SetMethodComplete(false)` and changes state to `ATTACKMELEE`.  
2. Resolves the animation RUID from the `ActionSheet`.  
3. Waits through the frames before `meleeAttackFrame.x` with `_SpriteUtils:WaitForFrames`.  
4. Shows the warning for the length of the attack frame range (`_SpriteUtils:GetAnimationLength`).  
5. Calls `attackComp:AttackMelee` with an `AttackInfo` (damage 10) and `meleeRadius`.  
6. Waits out the remaining frames, changes state to `WAIT` and calls `SetMethodComplete(true)`.

</details>

<details>
<summary>AttackRange</summary>

**Execution Scope**: `Default (caller's side)` (run from `OnMethodExecuted`)  

**Purpose**  
Performs the ranged attack sequence, timed to the `ATTACKRANGE` animation.

**Behavior**  
1. `SetMethodComplete(false)`, logs the state-to-animation mapping, and reads the nearest target from `bbNearestTarget`.  
2. Changes state to `ATTACKRANGE`, resolves the animation RUID and waits through the wind-up frames.  
3. Shows the warning circle at `rangeRadius` for the length of `rangedAttackFrame`.  
4. Fires `projectileCount` projectiles with `attackComp:AttackRanged` (damage 10, explosion effect RUID), waiting 6 animation frames between shots.  
5. Waits out the remaining frames, changes state to `WAIT` and calls `SetMethodComplete(true)`.  
- Note: the offset to the target is computed from `targetPos`, which is nil when no target exists.

</details>

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandleHitEvent` | `HitEvent` | Self / ServerOnly | Subtracts `TotalDamage` from `hp` and calls `Dead()` at 0. |
| `OnMethodExecuted` | `OnMethodExecuted` | Self / ServerOnly | Calls the method named in `evt.methodName` on this component, so it can yield without blocking the tree tick. |

---

### Related Types & Services
- **AIComponent** – Base class that runs the behaviour tree and owns the `BlackBoard`.  
- **BossAttackComponent** – Performs the melee and ranged attacks.  
- **HealthbarComponent** – Displays HP.  
- **_SpriteUtils** – Frame timing helpers.  
- **_TimerService** / **_UserService** – Target-search timer and player lookup.  
- **ExecuteMethod / OnMethodExecuted** – Bridge between the tree and the attack methods.  

[Back to Top](#table-of-contents)

---

## AttackDuration

**Purpose**  
Action node that performs a single instant circular area attack against players using the AI entity's `AttackComponent`. The radius comes from the blackboard, so it can reuse the radius drawn by `ShowWarning`.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `radiusIDKey` | string | Blackboard key holding the attack radius (default `bb_warningRadius`). |
| `attackPosKey` | string | Optional blackboard key holding a Vector2 attack centre; empty = the AI's position. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnInit` | Default (caller's side) | Empty. |
| `OnBehave` | Default (caller's side) | Attacks once with a `CircleShape` on `CollisionGroups.Player` and returns Success; Failure if the entity, `AttackComponent`, transform or radius is missing. |

### Related Types & Services
- **ActionNode**, **AttackComponent**, **CircleShape**, **CollisionGroups**.

[Back to Top](#table-of-contents)

---

## AttackFast

**Purpose**  
Placeholder action node with no logic (no `OnInit` / `OnBehave` overrides). The working area attack node is `AttackDuration`.

[Back to Top](#table-of-contents)

---

## BehaviorTreeEvent

**Purpose**  
Empty placeholder `EventType` with no fields. It is not sent or handled anywhere; `OnMethodExecuted` is the event actually used by `ExecuteMethod`.

[Back to Top](#table-of-contents)

---

## ExecuteMethod

**Purpose**  
Action node that runs a long, yielding method on the AI component by name (for example `BossAI.AttackMelee` / `AttackRange`). It sends an `OnMethodExecuted` event so the method runs inside the AI component's event handler (where it may `wait()`), then stays Running until the method sets a completion flag on the blackboard.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `methodName` | string | Name of the method on the `ParentAI` component to run. |
| `methodIsCompleteKey` | string | Blackboard bool the target method sets to true when done (default `bb_isMethodComplete`). |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnInit` | Default (caller's side) | Sends the execution event if the method exists; otherwise logs a warning. |
| `OnBehave` | Default (caller's side) | Returns Success when the completion flag is true, Running otherwise, Failure if the method does not exist. |
| `SendExecutionEvt` | Default (caller's side) | Sends `OnMethodExecuted(methodName)` to the AI entity. |

### Related Types & Services
- **OnMethodExecuted** – Event handled by `BossAI.OnMethodExecuted`.  
- **BossAI.SetMethodComplete** – Writes the completion flag.

[Back to Top](#table-of-contents)

---

## OnMethodExecuted

**Purpose**  
Event sent by the `ExecuteMethod` node to the AI entity, asking its AI component to run a method by name.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `methodName` | string | Name of the method to invoke (e.g. `"AttackMelee"`, `"AttackRange"`). |

### Usage
- Sent by `ExecuteMethod.SendExecutionEvt`; handled by `BossAI.OnMethodExecuted`.

[Back to Top](#table-of-contents)

---

## PlayAnimationFrames

**Purpose**  
Stub action node intended to play a frame range of an animation clip. Only its properties are declared; it has no runtime logic. `WaitAnimationDuration` covers the frame-range timing use case.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `animationRUID` | string | Animation clip RUID. |
| `frameStartIndex` | integer | First frame of the range. |
| `frameEndIndex` | integer | Last frame of the range (999 = to the end). |

[Back to Top](#table-of-contents)

---

## SetState

**Purpose**  
Action node that switches the AI entity's `StateComponent` to a fixed state (which drives the state animation).

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `stateString` | string | State name passed to `StateComponent:ChangeState`. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBehave` | Default (caller's side) | Changes state and returns Success; Failure if there is no `StateComponent` or the name is empty. |

[Back to Top](#table-of-contents)

---

## ShowWarning

**Purpose**  
Action node that telegraphs an area attack: it draws a filled circle (a `PolygonRendererComponent` added to the AI entity on demand) that grows from zero to the full radius over `warnDuration` seconds, then hides it and returns Success. Pair it with `AttackDuration`, which reads the same radius key.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `warningColor` | Color | Fill colour, set when the polygon renderer is first created. |
| `warningIDKey` | string | Declared for the tree but not read. |
| `radiusIDKey` | string | Blackboard key holding the full warning radius (default `bb_warningRadius`). |
| `positionKey` | string | Optional blackboard key with a Vector2 circle centre; empty = on the AI entity. |
| `warnDurationKey` | string | Optional blackboard key that overrides `warnDuration` (e.g. an animation length). |
| `warnDuration` | number | Seconds for the circle to grow to full size. |
| `offset` | Vector2 | Circle centre relative to the AI entity. Hidden from inspector. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `GetOrAddPolygonRenderer` | Default (caller's side) | Returns the polygon renderer, adding it (24 points, warning colour, sprite sorting layer) the first time; refreshes the offset and duration from the blackboard. |
| `UpdateRadius` | Default (caller's side) | Places every vertex on a circle of the given radius around `offset`. |
| `OnInit` | Default (caller's side) | Resets the timer in `_T.warnTimer` and enables the circle. |
| `OnBehave` | Default (caller's side) | Grows the circle proportionally; Running until `warnDuration`, then hides it and returns Success. |

### Related Types & Services
- **PolygonRendererComponent**, **AttackDuration**, **WaitAnimationDuration** (can supply `warnDurationKey`).

[Back to Top](#table-of-contents)

---

## SpawnAtPosition

**Purpose**  
Action node that spawns one entity from a model at a blackboard position, parented to the AI entity's current map.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `positionKey` | string | Blackboard key holding the Vector2 spawn position (default `bb_spawnPosition`). |
| `modelID` | string | Model ID to spawn. |
| `entityName` | string | Name given to the spawned entity. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBehave` | Default (caller's side) | Spawns with `_SpawnService:SpawnByModelId`; Success if an entity was created, otherwise Failure. |

[Back to Top](#table-of-contents)

---

## SpawnProjectileAttack

**Purpose**  
Action node for the boss's ranged attack: performs an instant circular hit around the boss, then takes a `BossProjectile` from the object pool and launches it at the blackboard target's current position. The work happens once in `OnInit` (because `BossProjectile:Init` may yield while loading its animation); `OnBehave` just returns Success. Requires the pool to be filled first (`BossAttackComponent.OnBeginPlay`).

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `targetEntityKey` | string | Blackboard key holding the target entity (default `bb_Target`). |
| `radiusIDKey` | string | Blackboard key holding the area-hit radius (default `bb_rangeRadius`). |
| `poolName` | string | Object pool to take the projectile from (default `BossProjectile`). |
| `damage` | number | Damage dealt by the projectile explosion. |
| `effectRUID` | string | Explosion effect RUID; its sprite size sets the explosion radius. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnInit` | Default (caller's side) | Validates entity, `AttackComponent`, transform, target, radius and map (silently returning if missing), hits players in the radius, then gets, initializes and launches a pooled projectile. |
| `OnBehave` | Default (caller's side) | Returns Success. |

### Related Types & Services
- **_PoolManager**, **BossProjectile**, **AttackInfo**, **AttackComponent**.

[Back to Top](#table-of-contents)

---

## WaitAnimationDuration

**Purpose**  
Action node that measures how long a frame range of an animation clip lasts, writes the duration to the blackboard (so later nodes such as `ShowWarning` can use it), and optionally waits that long before succeeding. Loading the clip yields, so measuring is done in `OnInit`.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `animRUID` | string | Animation clip RUID to measure. |
| `resultValueKey` | string | Blackboard number key that receives the duration (default `bb_animationDuration`). |
| `fromTo` | Vector2 | Inclusive frame range (1-based; 999 = to the end). |
| `addDelay` | boolean | true = stay Running for the measured duration; false = succeed right after measuring. |
| `waitDuration` | number | Measured duration in seconds. Hidden from inspector. |
| `animLoaded` | boolean | Set once measuring has finished. Hidden from inspector. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnInit` | Default (caller's side) | Loads the clip, sums frame delays in range, multiplies by `PlayRate`, stores the result and writes it to the blackboard. Returns early if the RUID is empty or not an animation clip. |
| `OnBehave` | Default (caller's side) | Running until measured; then Success immediately (or after `waitDuration` when `addDelay` is true), resetting the timer and flag. |

[Back to Top](#table-of-contents)

---

## AttackInfo

**Purpose**  
Plain data struct describing one boss attack. Built by `BossAI` / `SpawnProjectileAttack` and passed to `BossAttackComponent` and `BossProjectile:Init`.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `Damage` | number | Damage applied by the projectile's `ProjectileAttackComponent`. |
| `effectRUID` | string | Explosion effect RUID; its sprite size also sets the explosion radius. |

[Back to Top](#table-of-contents)

---

## BossAttackComponent

**Purpose**  
`AttackComponent` on the boss. Pre-fills the `"BossProjectile"` object pool for the boss's map and provides the melee and ranged attacks that `BossAI`'s attack methods call.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `projectileModelID` | string | Model ID of the projectile entity to pool. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBeginPlay` | Client/Server | Calls `_PoolManager:Init("BossProjectile", 6, projectileModelID, CurrentMap)`. |
| `AttackMelee` | Default (caller's side) | `AttackFast` with a `CircleShape` of `radius` around the boss (`info` is not used; damage comes from `CalcDamage`). |
| `AttackRanged` | Default (caller's side) | Same circular hit, then gets a pooled projectile at the boss's position, calls `Init(info)` and `Launch(target position)`. |

### Related Types & Services
- **AttackComponent**, **_PoolManager**, **BossProjectile**, **AttackInfo**.

[Back to Top](#table-of-contents)

---

## BossHitComponent

**Purpose**  
`HitComponent` on the boss with no overrides. The resulting `HitEvent` is handled by `BossAI.HandleHitEvent`.

[Back to Top](#table-of-contents)

---

## BossProjectile

**Purpose**  
Pooled boss projectile. After `Init` and `Launch` it flies in a straight line toward a fixed point (moved by a repeating timer) and explodes when it gets close, when its flight time runs out, or when it touches a player. The explosion plays an effect, deals area damage through `ProjectileAttackComponent`, and returns the entity to the pool.

---

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `moveSpeed` | number | Travel speed in world units per second. |
| `flyDuration` | number | Maximum flight time; overwritten in `Init` with the projectile animation's length. |
| `attackComp` | ProjectileAttackComponent | Cached attack component. Hidden from inspector. |
| `attackInfo` | AttackInfo | Data for the current shot. Hidden from inspector. |
| `isDead` | boolean | True once exploded, so trigger contacts are ignored. Hidden from inspector. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `Init` | Default (caller's side) | Stores `info`, passes its damage to the attack component, sets `flyDuration` from the sprite animation length. |
| `Launch` | Default (caller's side) | Starts a ~30 Hz repeating timer that moves toward the target and explodes on arrival or timeout. |
| `Explode` | Default (caller's side) | Hides the projectile, plays the effect, deals area damage, and releases it to the pool. |
| `Reset` | Default (caller's side) | Clears `isDead` and makes the entity visible again. |

---

### Methods

<details>
<summary>Launch</summary>

**Execution Scope**: `Default (caller's side)`  

**Purpose**  
Moves the projectile toward a target point.

**Parameters**  
- `targetPos` *(Vector2)* – World position to fly to.

**Behavior**  
1. Starts `_TimerService:SetTimerRepeat` with a 1/30 s interval.  
2. Each tick moves `moveSpeed * delta` along the direction to the target.  
3. When within 0.5 units (squared distance < 0.25 via `_MathUtils:DistanceSquared`) or after `flyDuration`, clears its timer and calls `Explode()`.

</details>

<details>
<summary>Explode</summary>

**Execution Scope**: `Default (caller's side)`  

**Purpose**  
Detonates the projectile.

**Behavior**  
1. Hides the entity.  
2. Measures the explosion sprite with `_SpriteUtils:GetSpriteAnimSize` and uses half its width as the radius.  
3. Plays `attackInfo.effectRUID` with `_EffectService:PlayEffect`.  
4. Calls `attackComp:AttackFast` with a `CircleShape` on `CollisionGroups.Player`.  
5. Sets `isDead = true` and calls `_PoolManager:Release(self.Entity)`.

</details>

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `OnTriggerEnter` | `TriggerEnterEvent` | Self / Client/Server | Explodes early when a player touches the projectile (ignored once dead). |
| `OnPoolRelease` | `OnPoolRelease` | Self / Client/Server | Calls `Reset()` so the projectile can be reused. |

---

### Related Types & Services
- **ProjectileAttackComponent**, **AttackInfo**, **_PoolManager**, **_SpriteUtils**, **_MathUtils**, **_EffectService**, **_TimerService**.

[Back to Top](#table-of-contents)

---

## ProjectileAttackComponent

**Purpose**  
`AttackComponent` on the projectile entity. Its damage is set per shot by `BossProjectile:Init`.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `damage` | number | Damage dealt to each target hit. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `Init` | Default (caller's side) | Sets `damage`. |
| `CalcDamage` | Default (caller's side) | Override that returns the flat `damage`. |

[Back to Top](#table-of-contents)

---

## PatrolBehaviorTree

**Purpose**  
`AIComponent` used by the simple demo monsters. It tracks entities entering its trigger as targets, writes the nearest one to the blackboard every frame, flips the sprite to face its movement, and builds a small tree in code on begin play.

---

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `targets` | table | Map of entity ID to entity for everything currently inside the trigger. |
| `targetKey` | string | Blackboard key for the nearest target (default `bb_Target`). |
| `waypointsParent` | Entity | Parent entity of patrol waypoints (its blackboard write is commented out). |
| `waypointsKey` | string | Blackboard key for the waypoint parent (default `bb_WaypointsParent`). |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBeginPlay` | ServerOnly | Resets `_T.targetsCount` and calls `BuildTree()`. |
| `BuildTree` | Server | Creates a `SelectorNode` root with a `TestDecorator` node and sets it as the root. |
| `UpdateNearestTarget` | Server | Finds the nearest entity in `targets` and writes it to `targetKey`. |
| `AddTarget` | Server | Adds an entity to `targets` and increments the count. |
| `RemoveTarget` | Default (caller's side) | Removes an entity; clears `targetKey` when the last target leaves. |
| `OnUpdate` | ServerOnly | Calls `UpdateNearestTarget()` when there are targets, then `Flip()`. |
| `Flip` | ServerOnly | Sets `FlipX` from the `KinematicbodyComponent.MoveVelocity` direction. |

---

### Methods

<details>
<summary>BuildTree</summary>

**Execution Scope**: `Server`  

**Purpose**  
Builds the AI's tree in code.

**Behavior**  
1. Creates a `SelectorNode`.  
2. Creates a `TestDecorator` node with `CreateNode` and attaches it (the code that would give it an action child is commented out).  
3. Calls `SetRootNode(tree)`.

</details>

<details>
<summary>UpdateNearestTarget</summary>

**Execution Scope**: `Server`  

**Purpose**  
Keeps the blackboard target pointed at the closest tracked entity.

**Behavior**  
1. Loops over `targets` and measures distance to each.  
2. Writes the nearest entity to `targetKey` (the comparison against `_T.nearestTarget` is never updated, so the write happens on every call).

</details>

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandleTriggerEnterEvent` | `TriggerEnterEvent` | Self / ServerOnly | Calls `AddTarget` for the entering entity. |
| `HandleTriggerLeaveEvent` | `TriggerLeaveEvent` | Self / ServerOnly | Calls `RemoveTarget` for the leaving entity. |

---

### Related Types & Services
- **AIComponent**, **SelectorNode**, **TestDecorator**, **TriggerComponent**, **KinematicbodyComponent**, **SpriteRendererComponent**.

[Back to Top](#table-of-contents)

---

## ActionFollow

**Purpose**  
Action node that moves the AI toward a blackboard target entity with `MovementComponent:MoveToDirection`.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `ArrivalThreshold` | number | Distance at which the target counts as reached. |
| `TargetEntityKey` | string | Blackboard key holding the entity to follow (default `bb_Target`). |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBehave` | Default (caller's side) | Running while moving; stops and returns Success within `ArrivalThreshold`; Failure if the target or a component is missing. |

[Back to Top](#table-of-contents)

---

## ActionPatrol

**Purpose**  
Action node that patrols between waypoints: the children of a parent entity stored on the blackboard. Each run walks to the current waypoint, succeeds on arrival, and advances to the next (looping back to the first).

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `waypointID` | string | Blackboard key holding the waypoint parent entity. |
| `ArrivalThreshold` | number | Distance at which a waypoint counts as reached. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnInit` | Default (caller's side) | On first use, caches the parent's `Children` in `_T.waypoints` and starts at waypoint 0; both persist across runs. |
| `OnBehave` | Default (caller's side) | Moves toward the current waypoint (Running); on arrival stops, advances the index (wrapping) and returns Success; Failure if waypoints or components are missing. |

[Back to Top](#table-of-contents)

---

## HasTarget

**Purpose**  
Decorator that runs its child only when "is there a valid target entity on the blackboard" equals `compareValue`; otherwise returns Failure. With `finishIfStarted`, a child that started running is allowed to finish even if the condition changes.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `targetEntityKey` | string | Blackboard key holding the target entity (default `bb_Target`). |
| `compareValue` | boolean | Required result of `isvalid(target)` for the child to run. |
| `finishIfStarted` | boolean | Keep ticking a child that returned Running without re-checking. |
| `hasStarted` | boolean | Set once the child returned Running in this activation. Hidden from inspector. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnInit` | Default (caller's side) | Clears `hasStarted`. |
| `OnBehave` | Default (caller's side) | Returns the child's status when the condition holds (or the child already started and `finishIfStarted` is set); otherwise Failure. |

[Back to Top](#table-of-contents)

---

## TestDecorator

**Purpose**  
Debug decorator that ticks a child node stored in its `child` property, logs whether it succeeded, failed or is running, and returns the child's status unchanged. Returns Failure if no child is set.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `child` | any | Child node to tick (read from this property rather than `ChildBehave`). |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBehave` | Default (caller's side) | Calls `child:Behave(delta)`, logs the result, and returns it. |

[Back to Top](#table-of-contents)

---

## CustomStateAnimationComponent

**Purpose**  
`StateAnimationComponent` replacement used on the boss. On a state change it looks up the state's animation clip in the `ActionSheet` and assigns it directly to the entity's `SpriteRendererComponent.SpriteRUID`.

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `ReceiveStateChangeEvent` | Default (caller's side) | Override: maps the new state name to an animation key and sets the sprite's RUID if one exists. |
| `StateStringtoAnimationRUID` | Default (caller's side) | Returns the animation RUID mapped to a state name (nil if none). |

[Back to Top](#table-of-contents)

---

## ColliderVisualizer

**Purpose**  
Client-side debug gizmo. Finds the first collider-style component on its entity (Trigger, Hit, Interaction, PhysicsCollider, Portal or Climbable) and draws its outline every frame with a `LineRendererComponent` on spawned helper entities.

---

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `LineWidth` | number | Outline thickness. |
| `LineColor` | Color | Outline colour. |
| `collider` | any | Collider component being drawn. Hidden from inspector. |
| `isBoxOnly` | boolean | True for Portal/Climbable components, which only expose box data. Hidden from inspector. |
| `gizmoBody` | Entity | Child helper holding the `LineRendererComponent` (collider offset and scale). Hidden from inspector. |
| `gizmoAnchor` | Entity | Root helper that follows the entity's position and rotation. Hidden from inspector. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnUpdate` | ClientOnly | Reads the collider shape/offset/scale/rotation, redraws the outline and moves the gizmo. |
| `OnDestroy` | ClientOnly | Destroys the gizmo entities. |
| `CheckInit` | ClientOnly | Finds the collider and (re)spawns the gizmo entities; returns false if there is no collider. |
| `FindCollider` | ClientOnly | Picks the first supported collider component in priority order. |
| `DrawBox` | ClientOnly | Draws a closed rectangle. |
| `DrawCircle` | ClientOnly | Draws a circle approximation with `vertexNum` points (minimum 3). |
| `DrawPolygon` | ClientOnly | Draws the collider's polygon points as a closed loop. |
| `GetLossyScale` | ClientOnly | Multiplies the entity's scale with every ancestor's scale. |

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandleEntityEnabledInHierarchyChangedEvent` | `EntityEnabledInHierarchyChangedEvent` | Self / Client/Server | Mirrors the entity's enabled state onto the gizmo. |
| `HandleEntityVisibleInHierarchyChangedEvent` | `EntityVisibleInHierarchyChangedEvent` | Self / Client/Server | Mirrors the entity's visibility onto the gizmo. |

---

### Related Types & Services
- **LineRendererComponent**, **LinePoint**, **ColliderType**, **_SpawnService**, **_EntityService**.

[Back to Top](#table-of-contents)

---

## Monster

**Purpose**  
Monster health, death and optional respawn. Used by the demo monsters.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `MaxHp` | number | Maximum HP. `@Sync`. |
| `Hp` | number | Current HP. `@Sync`. |
| `RespawnOn` | boolean | Whether the monster respawns after death. `@Sync`. |
| `IsDead` | boolean | Whether the monster is dead. `@Sync`, hidden from inspector. |
| `RespawnDelay` | number | Seconds before respawning. `@Sync`. |
| `DestroyDelay` | number | Seconds before the dead monster is hidden/destroyed. `@Sync`. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBeginPlay` | Client/Server | Sets `Hp = MaxHp`. |
| `Dead` | ServerOnly | Sets `IsDead`, changes state to `DEAD`, and after `DestroyDelay` hides/disables the entity (destroying it if `RespawnOn` is false). |
| `Respawn` | ServerOnly | Restores visibility, HP and the `IDLE` state. |

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandleHitEvent` | `HitEvent` | Self / ServerOnly | Subtracts `TotalDamage`; on the killing blow calls `Dead()` and, if `RespawnOn`, schedules `Respawn()` after `RespawnDelay`. |

[Back to Top](#table-of-contents)

---

## MonsterAttack

**Purpose**  
Contact attack for monsters: uses the monster's sprite size as a box hitbox and repeatedly attacks players inside it.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `AttackInterval` | number | Seconds between attacks (`@MinValue(0)`). |
| `Shape` | any | `BoxShape` hitbox. Hidden from inspector. |
| `SpriteSize` | Vector2 | Sprite size in world units. Hidden from inspector. |
| `PositionOffset` | Vector2 | Offset from the pivot to the sprite centre. Hidden from inspector. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBeginPlay` | ServerOnly | Requires a `Monster` component; preloads the sprite, measures its first frame, and starts a repeating timer calling `AttackNear()` while alive. |
| `AttackNear` | Default (caller's side) | Updates the hitbox to the entity's position, scale and rotation and calls `AttackFast` on `CollisionGroups.Player`. |
| `IsAttackTarget` | Default (caller's side) | Only entities with a `PlayerComponent` can be targets. |

[Back to Top](#table-of-contents)

---

## PlayerAttack

**Purpose**  
Basic player attack: a box hitbox in front of the player that hits monsters when the player performs the `"Attack"` action.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `Shape` | any | `BoxShape` hitbox. Hidden from inspector. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBeginPlay` | ServerOnly | Creates a 1x1 `BoxShape`. |
| `AttackNormal` | ServerOnly | Positions the box 0.5 units ahead (by `LookDirectionX`) and 0.5 up, then calls `AttackFast` on `CollisionGroups.Monster`. |
| `CalcDamage` | Default (caller's side) | Returns 50. |
| `CalcCritical` | Default (caller's side) | 30% critical chance. |
| `GetCriticalDamageRate` | Default (caller's side) | Returns 2. |

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandlePlayerActionEvent` | `PlayerActionEvent` | Self / ServerOnly | Calls `AttackNormal()` when `ActionName == "Attack"`. |

[Back to Top](#table-of-contents)

---

## PlayerHit

**Purpose**  
Player hit handling: a hit-immunity cooldown, a choice between the `HIT` and custom `KNOCKBACK` states, and a decaying knockback when hit by an attacker in the `ATTACKMELEE` state.

---

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `ImmuneCooldown` | number | Minimum seconds between hits (`@MinValue(0)`). |
| `LastHitTime` | number | Time of the last accepted hit. Hidden from inspector. |
| `KnockbackSpeed` | number | Initial knockback speed (`@MinValue(0)`). |
| `KnockbackDamping` | number | Per-frame velocity multiplier (`@MinValue(0)`). |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `IsHitTarget` | Default (caller's side) | Accepts a hit only after `ImmuneCooldown` has passed. |
| `OnBeginPlay` | ClientOnly | Calls `StateComponent:DisconnectHitEvent()` so this script chooses the hit state itself. |
| `OnUpdate` | ServerOnly | Integrates the knockback velocity into position, stopping at blocked tiles. |

---

### Methods

<details>
<summary>OnUpdate</summary>

**Execution Scope**: `ServerOnly`  

**Purpose**  
Applies the knockback started by `HandleHitEvent`.

**Behavior**  
1. Returns if there is no `_T.knockbackVelocity`.  
2. Stops when the velocity magnitude is 0.05 or less, or when there is no `MovementComponent`.  
3. Computes the next position; if `_TileUtils:IsPositionBlocked` reports a wall or unpainted cell, stops.  
4. Otherwise calls `MovementComponent:SetWorldPosition` and multiplies the velocity by `KnockbackDamping`.

</details>

---

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandleHitEventState` | `HitEvent` | Self / ClientOnly | Changes state to `KNOCKBACK` if the attacker is in `ATTACKMELEE`, otherwise `HIT`. |
| `HandleHitEvent` | `HitEvent` | Self / ServerOnly | For `ATTACKMELEE` attackers, sets `_T.knockbackVelocity` away from the attacker and caches the map's tilemaps. |

---

### Related Types & Services
- **HitComponent**, **StateComponent**, **MovementComponent**, **_TileUtils**, **_UtilLogic**.

[Back to Top](#table-of-contents)

---

## UIMyInfo

**Purpose**  
Local player HUD that shows the nickname and an HP bar/label. The bar image width is scaled to `Hp / MaxHp` every frame; child entities are found by path.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `hpBar` | UITransformComponent | Transform of `info_bottom/Hp/img_bar`. |
| `maxWidth` | number | Full bar width captured at start. |
| `hpText` | TextComponent | `info_bottom/Hp/text_value` label. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBeginPlay` | ClientOnly | Sets the nickname and caches the bar and label. |
| `OnUpdate` | ClientOnly | Resizes the bar and updates the `"hp / maxhp"` text. |

[Back to Top](#table-of-contents)

---

## UIMyInfoSimple

**Purpose**  
Simpler local player HUD (used in `DefaultGroup.ui`): shows the nickname and HP as a slider value.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `myName` | TextComponent | `Text_name` child label. |
| `myHP` | SliderComponent | `HP_bar` child slider. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBeginPlay` | ClientOnly | Caches the label and slider. |
| `OnUpdate` | ClientOnly | Once the local player exists, sets the name and `Value = Hp / MaxHp`. |

[Back to Top](#table-of-contents)

---

## UIPopup

**Purpose**  
Logic that opens a popup with a message and OK/Cancel buttons, animated with a scale and fade tween, and runs optional callbacks.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `message` | TextComponent | Popup message text. |
| `btnOk` / `btnCancel` | ButtonComponent | OK and Cancel buttons. |
| `popupGroup` | Entity | Group enabled/disabled with the popup (its `CanvasGroupComponent` is faded). |
| `popup` | Entity | Popup panel whose `UIScale` is tweened. |
| `onOk` / `onCancel` | any | Callbacks for the buttons. |
| `duration` | number | Tween length in seconds. |
| `from` / `to` | number | Start and end scale. |
| `isOpen` | boolean | Whether the popup is open. |
| `isTweenPlaying` | boolean | Whether a tween is running (clicks are ignored meanwhile). |
| `tweenEventId` | number | Timer ID of the tween. |
| `okHandler` / `cancelHandler` | any | Button event connection handles. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `Open` | Default (caller's side) | Shows the popup with a message, stores callbacks, connects buttons and starts the open tween. |
| `OnClickOk` | Default (caller's side) | Runs `onOk` (once) and closes. |
| `OnClickCancel` | Default (caller's side) | Runs `onCancel` (once) and closes. |
| `Close` | Default (caller's side) | Disconnects buttons and starts the close tween. |
| `StartTween` | Default (caller's side) | 60 Hz timer that eases scale (`SineEaseIn`) and alpha; disables the group at the end of a close. |

[Back to Top](#table-of-contents)

---

## UIToast

**Purpose**  
Logic that shows a temporary toast message with a fade and slide in/out.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `message` | TextComponent | Toast text. |
| `toastGroup` | Entity | Group enabled while the toast is visible. |
| `duration` | number | Seconds the toast stays visible. |
| `tweenDuration` | number | Fade in/out length. |
| `tweenEventId` | number | Timer ID of the tween. |
| `isTweenPlaying` | boolean | Declared but not used. |
| `inited` | boolean | Whether `offset` has been captured. |
| `offset` | number | Vertical slide distance (from the text's anchored Y). |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `ShowMessage` | Client | Captures the offset once, sets the text and starts the tween. |
| `StartTween` | Default (caller's side) | Restarts any running tween; fades in, holds for `duration`, fades out and disables the group. |

[Back to Top](#table-of-contents)

---

## ObjectPoolComponent

**Purpose**  
Holds named pools of pre-spawned, disabled entities under its entity. Created per map by `PoolManager` (on an `ObjectPool` child of the map). Entity names double as the pool key.

---

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `pools` | table | Pool name to array of idle (disabled) entities. |
| `createCallback` | table | Pool name to a function that spawns a new entity for that pool. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `Fill` | Default (caller's side) | Creates a pool and pre-spawns `count` disabled entities from `modelID` (each tagged with `PooledObject`); does nothing if the pool exists. |
| `Get` | Default (caller's side) | Pops and enables an idle entity, or spawns a new one when empty; nil for an unknown pool. |
| `Release` | Default (caller's side) | Calls `PooledObject:Return()`, disables the entity and pushes it back on its pool. |

---

### Related Types & Services
- **PoolManager**, **PooledObject**, **_SpawnService**.

[Back to Top](#table-of-contents)

---

## OnPoolRelease

**Purpose**  
Event sent by `PoolManager:Release` to the released entity so its scripts can reset (e.g. `BossProjectile.OnPoolRelease`).

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `entity` | Entity | The entity returned to its pool. |

[Back to Top](#table-of-contents)

---

## PoolManager

**Purpose**  
Global object-pool logic (`_PoolManager`). Keeps one `ObjectPoolComponent` per map path and provides `Init`, `Get` and `Release`.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `pools` | table | Map path to that map's `ObjectPoolComponent`. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `Init` | Default (caller's side) | Finds `<map>/ObjectPool` (spawning one with an `ObjectPoolComponent` if missing), registers it, and fills `poolName` with `count` entities of `modelID`. |
| `Get` | Default (caller's side) | Gets an entity from the map's pool and moves it to `position`; returns nil if the map has no pool. |
| `Release` | Default (caller's side) | Returns the entity to its map's pool and sends `OnPoolRelease`. |
| `SendPoolReleaseEvent` | Default (caller's side) | Sends `OnPoolRelease` to the entity. |

[Back to Top](#table-of-contents)

---

## PooledObject

**Purpose**  
Marker component added to every pooled entity; its presence lets `ObjectPoolComponent.Release` verify the entity came from a pool. `Init` and `Return` are empty hooks.

[Back to Top](#table-of-contents)

---

## ActionButtonBehavior

**Purpose**  
Base struct for `ActionButtonComponent` actions. Subclasses override a `GetBehavior*` method to return a closure that the button calls on click. All base implementations return nil.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `target` | Entity | Entity the action operates on (not used by the current subclasses). |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `GetBehaviorWithBoolean` | Default (caller's side) | Returns a closure taking a boolean; nil by default. |
| `GetBehaviorWithNum` | Default (caller's side) | Intended for a number (declared with a boolean param); nil by default. |
| `GetBehaviorWithString` | Default (caller's side) | Intended for a string (declared with a boolean param); nil by default. |
| `GetBehavior` | Default (caller's side) | Returns a parameterless closure; nil by default. |

[Back to Top](#table-of-contents)

---

## ActionButtonComponent

**Purpose**  
`ButtonComponent` that runs a configurable action on click. Currently supports `execution = "ToggleEnable"`.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `target` | Entity | Entity the action acts on. |
| `execution` | string | Action name (e.g. `"ToggleEnable"`). |
| `paramString` / `paramNum` / `paramBool` | string / number / boolean | Action parameters (not used by `ToggleEnable`). |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `Build` | ClientOnly | Creates the behaviour closure for `execution` and stores it in `_T.behavior`. |
| `OnBeginPlay` | ClientOnly | Calls `Build()`. |
| `OnClick` | ClientOnly | Calls the stored behaviour if any. |

### Event Handlers

| Handler | Event Type | Sender / Scope | Purpose |
|---------|------------|----------------|---------|
| `HandleButtonClickEvent` | `ButtonClickEvent` | Self / Client | Calls `OnClick()`. |

[Back to Top](#table-of-contents)

---

## ToggleEnable

**Purpose**  
`ActionButtonBehavior` whose `GetBehavior(entity)` returns a closure that flips the entity between enabled and disabled (based on `EnabledInHierarchy`).

[Back to Top](#table-of-contents)

---

## HealthbarComponent

**Purpose**  
UI health bar made of a fill sprite, an optional handle that slides to the end of the fill, and an optional percentage label. Call `Set(pct)` with a 0..1 value (`BossAI` calls `Set(hp / maxHp)`).

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `handleGraphic` | Entity | Optional marker moved to the fill position (hidden at 100%). |
| `sprite` | SpriteGUIRendererComponent | Fill sprite whose `FillAmount` shows health. |
| `text` | TextGUIRendererComponent | Optional percentage label. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBeginPlay` | Client/Server | Normalizes anchors/pivots of the handle and fill so `Set` can position them. |
| `Set` | Default (caller's side) | Moves the handle, updates the label and sets `FillAmount`. |

[Back to Top](#table-of-contents)

---

## DestroyAfterFrames

**Purpose**  
Server-side self-destruct for one-shot effect entities: waits for the entity's sprite animation to play once, then destroys it.

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBeginPlay` | ServerOnly | Calls `WaitForFrames()` then destroys the entity. |
| `WaitForFrames` | Default (caller's side) | Sums the clip's frame delays times `PlayRate` and `wait`s that long; disables itself if the RUID is not an animation clip. |

[Back to Top](#table-of-contents)

---

## MathUtils

**Purpose**  
Global math helpers (`_MathUtils`).

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `RotatePointAroundZRotation` | Default (caller's side) | Scales and rotates a point by a transform's `Scale` and `ZRotation`. |
| `FastDistance` | Default (caller's side) | Alpha-max-plus-beta-min distance approximation without a square root. |
| `DistanceSquared` | Default (caller's side) | Exact squared distance (used by `BossProjectile`). |

[Back to Top](#table-of-contents)

---

## SpriteUtils

**Purpose**  
Global sprite/animation helpers (`_SpriteUtils`): measures sprite sizes in world units, computes animation lengths over frame ranges, and waits for frames. Loaded clips are cached.

---

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `AnimationsByRUID` | table | Cache of loaded `AnimationClip`s by RUID. |

---

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `GetSpriteAnimSize` | Default (caller's side) | Returns `{ SIZE, OFFSET }` in world units for a frame of an animation clip. |
| `WaitForFrames` | Default (caller's side) | `wait`s for the play time of a frame range (blocking). |
| `WaitForFramesAsync` | Default (caller's side) | Schedules a callback after the play time of a frame range. |
| `GetAnimation` | Default (caller's side) | Returns a cached clip, optionally loading and caching it (with a warning). |
| `GetAnimationFrameCount` | Default (caller's side) | Frame count of a cached clip (0 if not cached). |
| `GetAnimationLength` | Default (caller's side) | Sum of frame delays from `frameFrom` to `frameTo` divided by `PlayRate`; 0 for invalid input. |
| `GetSpriteSize` | Default (caller's side) | `{ SIZE, OFFSET }` for a sprite RUID or the first frame of a clip. |

---

### Methods

<details>
<summary>GetAnimationLength</summary>

**Execution Scope**: `Default (caller's side)`  

**Purpose**  
Returns the play time of a frame range.

**Parameters**  
- `ruid` *(string)* – Animation clip RUID.  
- `frameFrom` *(integer)* – First frame (1-based, inclusive).  
- `frameTo` *(integer)* – Last frame (inclusive; clamped to the frame count, so 999 means "to the end").

**Behavior**  
1. Returns 0 with a warning for an empty RUID, `frameFrom <= 0`, `frameFrom >= frameTo`, or a non-positive `PlayRate`.  
2. Loads the clip through `GetAnimation(ruid, true)`.  
3. Sums `Delay` for each frame in range and divides by `PlayRate`.

</details>

---

### Related Types & Services
- **_ResourceService**, **_TimerService**, **AnimationClip**.

[Back to Top](#table-of-contents)

---

## TileUtils

**Purpose**  
Global tile helpers (`_TileUtils`) used by the player knockback.

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `GetTilemaps` | Default (caller's side) | Collects every `RectTileMapComponent` on and under a map entity. |
| `IsPositionBlocked` | Default (caller's side) | True if any tilemap has no tile at the position or a collidable tile. |

[Back to Top](#table-of-contents)

---

## Util

**Purpose**  
General helper logic (`_Util`).

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `IsNilorEmptyString` | Default (caller's side) | True for nil or empty strings. |
| `TableLen` | Default (caller's side) | Counts entries in a table (0 for nil). |
| `IsNilorEmptyTable` | Default (caller's side) | True for nil or empty tables. |
| `DestroyEntity` | Server | Destroys an entity by ID after a delay. |

[Back to Top](#table-of-contents)

---

## AIChaseOrPatrol

**Purpose**  
Sample `AIComponent` that drives the `ChaseOrPatrol` tree: each frame on the server it looks for a player within `DetectionRange` and writes it to `bb_Player` (nil when none). It also draws the detection radius as a translucent circle and flips the sprite to face its movement.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `DetectionRange` | number | Player detection radius (also the drawn circle's radius). |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBeginPlay` | Client/Server | Calls `DrawRange()`. |
| `OnUpdate` | Client/Server | Calls `DetectPlayer()` and `Flip()` (both ServerOnly). |
| `DrawRange` | ServerOnly | Adds a `PolygonRendererComponent` with a 24-point green circle. |
| `DetectPlayer` | ServerOnly | Writes the first player found within range (not necessarily the nearest) to `bb_Player`. |
| `Flip` | ServerOnly | Sets `FlipX` from the `KinematicbodyComponent.MoveVelocity` direction. |

[Back to Top](#table-of-contents)

---

## AddForce

**Purpose**  
Action node that adds the Vector2 force stored under `ForceKey` to the AI's `RigidbodyComponent`. Success in one tick; Failure if the rigidbody or force is missing.

| Property | Type | Purpose |
|----------|------|---------|
| `ForceKey` | string | Blackboard key holding the force. |

[Back to Top](#table-of-contents)

---

## SetForce

**Purpose**  
Action node that sets (replaces) the AI's `RigidbodyComponent` force with the Vector2 stored under `ForceKey`. Success in one tick; Failure if anything is missing.

| Property | Type | Purpose |
|----------|------|---------|
| `ForceKey` | string | Blackboard key holding the force. |

[Back to Top](#table-of-contents)

---

## Chase

**Purpose**  
Action node that moves the AI straight toward a blackboard target entity by writing `WorldPosition` directly (no physics). Running while moving, Success on arrival (snapping if it would overshoot), Failure if data is missing or the speed is not positive.

| Property | Type | Purpose |
|----------|------|---------|
| `TargetEntityKey` | string | Blackboard key holding the entity to chase. |
| `MoveSpeedKey` | string | Blackboard key holding the speed (units/second). |
| `ArrivalThreshold` | number | Arrival distance. |

[Back to Top](#table-of-contents)

---

## MoveTo

**Purpose**  
Action node that moves the AI straight toward a blackboard Vector3 position by writing `WorldPosition` directly. Running while moving, Success on arrival (snapping if it would overshoot), Failure if data is missing.

| Property | Type | Purpose |
|----------|------|---------|
| `TargetPositionKey` | string | Blackboard key holding the destination. |
| `MoveSpeedKey` | string | Blackboard key holding the speed. |
| `ArrivalThreshold` | number | Arrival distance. |

[Back to Top](#table-of-contents)

---

## MoveToDirection

**Purpose**  
Action node that walks the AI toward a blackboard Vector3 position with `MovementComponent:MoveToDirection` (respecting the entity's movement rules). Stops and returns Success within `ArrivalThreshold`.

| Property | Type | Purpose |
|----------|------|---------|
| `LookAtPositionKey` | string | Blackboard key holding the destination. |
| `ArrivalThreshold` | number | Arrival distance. |

[Back to Top](#table-of-contents)

---

## MoveToTarget

**Purpose**  
Action node that walks the AI toward a blackboard target entity with `MovementComponent:MoveToDirection`, flipping the sprite to face it. It can follow the target live or go to the position captured in `OnInit`. Logs a warning and returns Failure when data is missing.

| Property | Type | Purpose |
|----------|------|---------|
| `TargetEntityKey` | string | Blackboard key holding the target. |
| `ArrivalThreshold` | number | Arrival distance. |
| `moveToLastKnownPosition` | boolean | If true, move to the target's position snapshotted in `OnInit` (`_T.lastKnownPosition`). |

[Back to Top](#table-of-contents)

---

## Jump

**Purpose**  
Action node that calls `MovementComponent:Jump()`. Success if the jump started; Failure if the component is missing or `Jump()` returns false.

[Back to Top](#table-of-contents)

---

## DownJump

**Purpose**  
Action node that calls `MovementComponent:DownJump()` to drop through the current foothold. Success if started; Failure otherwise.

[Back to Top](#table-of-contents)

---

## SpawnEntity

**Purpose**  
Action node that clones `SourceEntity` with `_SpawnService:SpawnByEntity` under `ParentEntity`, optionally writing the result to the blackboard. Failure only if the source or parent is unset.

| Property | Type | Purpose |
|----------|------|---------|
| `SourceEntity` | Entity | Entity to copy. |
| `SpawnEntityName` | string | Name for the spawned entity. |
| `SpawnPosition` | Vector3 | Spawn position. |
| `ParentEntity` | Entity | Parent of the new entity (required). |
| `IncludeChildren` | boolean | Copy children as well. |
| `ResultEntityKey` | string | Optional blackboard key that receives the spawned entity. |

[Back to Top](#table-of-contents)

---

## DestroyEntity

**Purpose**  
Action node that destroys the blackboard entity under `TargetEntityKey` after `Delay` seconds (negative delays are clamped to 0 in `OnInit`). Returns Success immediately.

| Property | Type | Purpose |
|----------|------|---------|
| `TargetEntityKey` | string | Blackboard key holding the entity. |
| `Delay` | number | Seconds before destruction. |

[Back to Top](#table-of-contents)

---

## SetEntityEnable

**Purpose**  
Action node that enables or disables the blackboard entity under `TargetEntityKey`.

| Property | Type | Purpose |
|----------|------|---------|
| `TargetEntityKey` | string | Blackboard key holding the entity. |
| `Enabled` | boolean | Enable state to apply. |

[Back to Top](#table-of-contents)

---

## PlayBasicParticle

**Purpose**  
Action node that plays a `BasicParticleType` effect via `_ParticleService:PlayBasicParticle` on `Instigator` (or the AI entity). Fire-and-forget; Failure if the type casts to `None`.

| Property | Type | Purpose |
|----------|------|---------|
| `ParticleType` | integer | `BasicParticleType` value (via `CastFrom`). |
| `Instigator` | Entity | Entity to play on; nil = AI entity. |
| `Position` / `ZRotation` / `Scale` | Vector3 / number / Vector3 | Effect transform. |
| `IsLoop` | boolean | Loop the effect. |

[Back to Top](#table-of-contents)

---

## PlayAreaParticle

**Purpose**  
Action node that plays an `AreaParticleType` effect over `AreaSize` via `_ParticleService:PlayAreaParticle`. Fire-and-forget; Failure if the type casts to `None`.

| Property | Type | Purpose |
|----------|------|---------|
| `ParticleType` | integer | `AreaParticleType` value. |
| `AreaSize` | Vector2 | Emission area size. |
| `Instigator` | Entity | Entity to play on; nil = AI entity. |
| `Position` / `ZRotation` / `Scale` | Vector3 / number / Vector3 | Effect transform. |
| `IsLoop` | boolean | Loop the effect. |

[Back to Top](#table-of-contents)

---

## PlaySpriteParticle

**Purpose**  
Action node that plays a `SpriteParticleType` effect using `SpriteRUID` via `_ParticleService:PlaySpriteParticle`. Fire-and-forget; Failure if the type casts to `None`.

| Property | Type | Purpose |
|----------|------|---------|
| `ParticleType` | integer | `SpriteParticleType` value. |
| `SpriteRUID` | string | Particle sprite. |
| `Instigator` | Entity | Entity to play on; nil = AI entity. |
| `Position` / `ZRotation` / `Scale` | Vector3 / number / Vector3 | Effect transform. |
| `IsLoop` | boolean | Loop the effect. |

[Back to Top](#table-of-contents)

---

## PlaySound

**Purpose**  
Action node that plays `SoundId` at `Volume` on the AI entity with `_SoundService:PlaySound`. Always returns Success.

| Property | Type | Purpose |
|----------|------|---------|
| `SoundId` | string | Sound RUID. |
| `Volume` | number | Playback volume. |

[Back to Top](#table-of-contents)

---

## Wait

**Purpose**  
Action node that returns Running until `WaitTime` seconds have elapsed since `OnInit`, then Success.

| Property | Type | Purpose |
|----------|------|---------|
| `WaitTime` | number | Seconds to wait. |
| `ElapsedTime` | number | Internal timer. Hidden from inspector. |

[Back to Top](#table-of-contents)

---

## WaitRandom

**Purpose**  
Action node that waits a random duration between `WaitTimeMin` and `WaitTimeMax`, re-rolled in each `OnInit`.

| Property | Type | Purpose |
|----------|------|---------|
| `WaitTimeMin` / `WaitTimeMax` | number | Bounds of the random wait. |
| `ElapsedTime` / `WaitTime` | number | Internal timer and rolled duration. Hidden from inspector. |

[Back to Top](#table-of-contents)

---

## WaitBlackboardTime

**Purpose**  
Action node that waits for the duration stored under `WaitTimeKey` (re-read every tick). Failure if the value is missing.

| Property | Type | Purpose |
|----------|------|---------|
| `WaitTimeKey` | string | Blackboard key holding the duration. |
| `ElapsedTime` | number | Internal timer. Hidden from inspector. |

[Back to Top](#table-of-contents)

---

## SetBlackboardValue_Bool

**Purpose**  
Action node that writes the boolean `Value` to the blackboard variable `Key` with `SetBool`. Success in one tick; Failure if there is no parent AI.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable name. |
| `Value` | boolean | Value to write. |

[Back to Top](#table-of-contents)

---

## SetBlackboardValue_Int

**Purpose**  
Writes the integer `Value` to `Key` with `SetInt`. Same behaviour as `SetBlackboardValue_Bool`.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable name. |
| `Value` | integer | Value to write. |

[Back to Top](#table-of-contents)

---

## SetBlackboardValue_Number

**Purpose**  
Writes the number `Value` to `Key` with `SetNumber`.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable name. |
| `Value` | number | Value to write. |

[Back to Top](#table-of-contents)

---

## SetBlackboardValue_String

**Purpose**  
Writes the string `Value` to `Key` with `SetString`.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable name. |
| `Value` | string | Value to write. |

[Back to Top](#table-of-contents)

---

## SetBlackboardValue_Color

**Purpose**  
Writes the Color `Value` to `Key` with `SetColor`.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable name. |
| `Value` | Color | Value to write (default white). |

[Back to Top](#table-of-contents)

---

## SetBlackboardValue_Vector2

**Purpose**  
Writes the Vector2 `Value` to `Key` with `SetVector2`.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable name. |
| `Value` | Vector2 | Value to write. |

[Back to Top](#table-of-contents)

---

## SetBlackboardValue_Vector3

**Purpose**  
Writes the Vector3 `Value` to `Key` with `SetVector3`.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable name. |
| `Value` | Vector3 | Value to write. |

[Back to Top](#table-of-contents)

---

## SetBlackboardValue_Vector4

**Purpose**  
Writes the Vector4 `Value` to `Key` with `SetVector4`.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable name. |
| `Value` | Vector4 | Value to write. |

[Back to Top](#table-of-contents)

---

## SetBlackboardValue_Entity

**Purpose**  
Writes the Entity `Value` to `Key` with `SetEntity`.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable name. |
| `Value` | Entity | Value to write. |

[Back to Top](#table-of-contents)

---

## SetBlackboardValue_EntityRef

**Purpose**  
Writes the EntityRef `Value` to `Key` with `SetEntityRef`.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable name. |
| `Value` | EntityRef | Value to write. |

[Back to Top](#table-of-contents)

---

## SetBlackboardValue_Component

**Purpose**  
Writes the Component `Value` to `Key` with `SetComponent`. `Value` and the blackboard variable should use the same concrete component type.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable name. |
| `Value` | Component | Value to write. |

[Back to Top](#table-of-contents)

---

## SetBlackboardValue_ComponentRef

**Purpose**  
Writes the ComponentRef `Value` to `Key` with `SetComponentRef` (copied into a local typed as the base `ComponentRef` first). `Value` and the blackboard variable should use the same concrete component type.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable name. |
| `Value` | ComponentRef | Value to write. |

[Back to Top](#table-of-contents)

---

## SetBlackboardValue_TargetPos

**Purpose**  
Action node that reads the Entity under `ValueKey` and writes its `TransformComponent.Position` (parent-relative, as a Vector2) under `Key`. Failure if there is no parent AI or no entity.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard Vector2 variable that receives the position. |
| `ValueKey` | string | Blackboard Entity variable holding the target. |

[Back to Top](#table-of-contents)

---

## RandomSelector

**Purpose**  
Composite node that, on each `OnInit`, picks a random child index different from the previous one (stored in `_T.randSequence`) and then ticks only that child, returning its status.

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnInit` | Default (caller's side) | Rolls a new child index with `_UtilLogic:RandomIntegerRange`, repeating until it differs from the last one. |
| `OnBehave` | Default (caller's side) | Returns `ChildBehave(index, delta)`. |

[Back to Top](#table-of-contents)

---

## RandomSequence

**Purpose**  
Composite node that runs all children in a shuffled order. Each `OnInit` builds a Fisher-Yates shuffle of the child indices; each Success advances to the next child, and the node returns Success once every child has succeeded (Running otherwise).

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnInit` | Default (caller's side) | Shuffles `1..ChildCount` into `_T.randSequence` and resets `_T.curIndex`. |
| `OnBehave` | Default (caller's side) | Ticks the current shuffled child; advances on Success; returns Success after the last child. A child Failure does not advance or fail the node. |

[Back to Top](#table-of-contents)

---

## Inverter

**Purpose**  
Decorator that swaps the child's Success and Failure; Running passes through.

[Back to Top](#table-of-contents)

---

## Succeeder

**Purpose**  
Decorator that returns Running while the child runs, and Success once it finishes (whether it succeeded or failed).

[Back to Top](#table-of-contents)

---

## Loop

**Purpose**  
Decorator that repeats its child: each child Success counts one iteration and the node keeps returning Running until `LoopCount` iterations are done (then Success), or forever when `IsInfinite` is true. A child Failure ends the loop with Failure.

| Property | Type | Purpose |
|----------|------|---------|
| `IsInfinite` | boolean | Repeat forever (ignores `LoopCount`). |
| `LoopCount` | integer | Successful runs required. |
| `CurrentCount` | integer | Iterations so far (reset in `OnInit`). Hidden from inspector. |

[Back to Top](#table-of-contents)

---

## TimeLimit

**Purpose**  
Decorator that gives its child a time budget: `TimeLeft` is set to `Duration` in `OnInit` and reduced each tick; once it reaches 0 the node returns Failure instead of ticking the child.

| Property | Type | Purpose |
|----------|------|---------|
| `Duration` | number | Time budget in seconds. |
| `TimeLeft` | number | Remaining budget. Hidden from inspector. |

[Back to Top](#table-of-contents)

---

## Cooldown

**Purpose**  
Decorator that rate-limits its child: after the child finishes (Success or Failure) it returns Failure without ticking the child until `Duration` seconds have passed (`_UtilLogic.ServerElapsedSeconds`).

| Property | Type | Purpose |
|----------|------|---------|
| `Duration` | number | Cooldown length in seconds. |
| `CooldownEndTime` | number | Time at which the cooldown expires. Hidden from inspector. |

[Back to Top](#table-of-contents)

---

## CanMove

**Purpose**  
Decorator that runs its child only if `RigidbodyComponent:PredictFootholdEnd(LookAheadDistance, IsForward)` reports the foothold does not end ahead (e.g. to stop at ledges). Failure without a rigidbody.

| Property | Type | Purpose |
|----------|------|---------|
| `LookAheadDistance` | number | Distance to probe. |
| `IsForward` | boolean | true probes right, false probes left (a fixed world direction). |

[Back to Top](#table-of-contents)

---

## IsOnGround

**Purpose**  
Decorator that runs its child only while `RigidbodyComponent:IsOnGround()` is true; otherwise Failure.

[Back to Top](#table-of-contents)

---

## IsJumping

**Purpose**  
Decorator that runs its child only while the entity is rising (`RealMoveVelocity.y > 0`) or not on the ground; otherwise Failure.

[Back to Top](#table-of-contents)

---

## BlackboardCondition_Bool

**Purpose**  
Decorator that reads the Bool variable `Key` and runs the child only when `_Comparator:Compare(_ValueType.Bool, Operator, value, CompareValue)` passes; otherwise Failure. An empty `Key` fails. Allowed operators: `IsSet`, `IsNotSet`, `Equal`, `NotEqual`.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable to test. |
| `Operator` | integer | 0 IsSet, 1 IsNotSet, 2 Equal, 3 NotEqual, 4 Greater, 5 GreaterOrEqual, 6 Less, 7 LessOrEqual. |
| `CompareValue` | boolean | Constant to compare against. |

[Back to Top](#table-of-contents)

---

## BlackboardCondition_Int

**Purpose**  
Same as `BlackboardCondition_Bool` for an Int variable (`GetInt`). All eight operators are allowed.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable to test. |
| `Operator` | integer | Comparison operator (see `Comparator`). |
| `CompareValue` | integer | Constant to compare against. |

[Back to Top](#table-of-contents)

---

## BlackboardCondition_Number

**Purpose**  
Same as `BlackboardCondition_Bool` for a Number variable (`GetNumber`). All eight operators are allowed.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable to test. |
| `Operator` | integer | Comparison operator. |
| `CompareValue` | number | Constant to compare against. |

[Back to Top](#table-of-contents)

---

## BlackboardCondition_String

**Purpose**  
Same as `BlackboardCondition_Bool` for a String variable. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable to test. |
| `Operator` | integer | Comparison operator. |
| `CompareValue` | string | Constant to compare against. |

[Back to Top](#table-of-contents)

---

## BlackboardCondition_Color

**Purpose**  
Same as `BlackboardCondition_Bool` for a Color variable. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable to test. |
| `Operator` | integer | Comparison operator. |
| `CompareValue` | Color | Constant to compare against (default white). |

[Back to Top](#table-of-contents)

---

## BlackboardCondition_Vector2

**Purpose**  
Same as `BlackboardCondition_Bool` for a Vector2 variable. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable to test. |
| `Operator` | integer | Comparison operator. |
| `CompareValue` | Vector2 | Constant to compare against. |

[Back to Top](#table-of-contents)

---

## BlackboardCondition_Vector3

**Purpose**  
Same as `BlackboardCondition_Bool` for a Vector3 variable. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable to test. |
| `Operator` | integer | Comparison operator. |
| `CompareValue` | Vector3 | Constant to compare against. |

[Back to Top](#table-of-contents)

---

## BlackboardCondition_Vector4

**Purpose**  
Same as `BlackboardCondition_Bool` for a Vector4 variable. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable to test. |
| `Operator` | integer | Comparison operator. |
| `CompareValue` | Vector4 | Constant to compare against. |

[Back to Top](#table-of-contents)

---

## BlackboardCondition_Entity

**Purpose**  
Same as `BlackboardCondition_Bool` for an Entity variable. Equality operators only; while `CompareValue` is nil only `IsSet` / `IsNotSet` can pass. Used by the `ChaseOrPatrol` sample tree.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable to test. |
| `Operator` | integer | Comparison operator. |
| `CompareValue` | Entity | Constant to compare against. |

[Back to Top](#table-of-contents)

---

## BlackboardCondition_EntityRef

**Purpose**  
Same as `BlackboardCondition_Entity` for an EntityRef variable.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable to test. |
| `Operator` | integer | Comparison operator. |
| `CompareValue` | EntityRef | Constant to compare against. |

[Back to Top](#table-of-contents)

---

## BlackboardCondition_Component

**Purpose**  
Same as `BlackboardCondition_Entity` for a Component variable. Node and blackboard variable should use the same concrete component type.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable to test. |
| `Operator` | integer | Comparison operator. |
| `CompareValue` | Component | Constant to compare against. |

[Back to Top](#table-of-contents)

---

## BlackboardCondition_ComponentRef

**Purpose**  
Same as `BlackboardCondition_Component` for a ComponentRef variable.

| Property | Type | Purpose |
|----------|------|---------|
| `Key` | string | Blackboard variable to test. |
| `Operator` | integer | Comparison operator. |
| `CompareValue` | ComponentRef | Constant to compare against. |

[Back to Top](#table-of-contents)

---

## CompareBlackboardValues_Bool

**Purpose**  
Decorator that reads two Bool variables (`LeftKey`, `RightKey`) and runs the child only when "left `Operator` right" passes via `_Comparator:Compare`; otherwise Failure. Empty keys are not rejected up front. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `LeftKey` | string | Left operand variable. |
| `RightKey` | string | Right operand variable (ignored by IsSet / IsNotSet). |
| `Operator` | integer | Comparison operator (see `Comparator`). |

[Back to Top](#table-of-contents)

---

## CompareBlackboardValues_Int

**Purpose**  
Same as `CompareBlackboardValues_Bool` for Int variables. All eight operators are allowed.

| Property | Type | Purpose |
|----------|------|---------|
| `LeftKey` / `RightKey` | string | Operand variables. |
| `Operator` | integer | Comparison operator. |

[Back to Top](#table-of-contents)

---

## CompareBlackboardValues_Number

**Purpose**  
Same as `CompareBlackboardValues_Bool` for Number variables. All eight operators are allowed.

| Property | Type | Purpose |
|----------|------|---------|
| `LeftKey` / `RightKey` | string | Operand variables. |
| `Operator` | integer | Comparison operator. |

[Back to Top](#table-of-contents)

---

## CompareBlackboardValues_String

**Purpose**  
Same as `CompareBlackboardValues_Bool` for String variables. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `LeftKey` / `RightKey` | string | Operand variables. |
| `Operator` | integer | Comparison operator. |

[Back to Top](#table-of-contents)

---

## CompareBlackboardValues_Color

**Purpose**  
Same as `CompareBlackboardValues_Bool` for Color variables. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `LeftKey` / `RightKey` | string | Operand variables. |
| `Operator` | integer | Comparison operator. |

[Back to Top](#table-of-contents)

---

## CompareBlackboardValues_Vector2

**Purpose**  
Same as `CompareBlackboardValues_Bool` for Vector2 variables. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `LeftKey` / `RightKey` | string | Operand variables. |
| `Operator` | integer | Comparison operator. |

[Back to Top](#table-of-contents)

---

## CompareBlackboardValues_Vector3

**Purpose**  
Same as `CompareBlackboardValues_Bool` for Vector3 variables. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `LeftKey` / `RightKey` | string | Operand variables. |
| `Operator` | integer | Comparison operator. |

[Back to Top](#table-of-contents)

---

## CompareBlackboardValues_Vector4

**Purpose**  
Same as `CompareBlackboardValues_Bool` for Vector4 variables. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `LeftKey` / `RightKey` | string | Operand variables. |
| `Operator` | integer | Comparison operator. |

[Back to Top](#table-of-contents)

---

## CompareBlackboardValues_Entity

**Purpose**  
Same as `CompareBlackboardValues_Bool` for Entity variables. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `LeftKey` / `RightKey` | string | Operand variables. |
| `Operator` | integer | Comparison operator. |

[Back to Top](#table-of-contents)

---

## CompareBlackboardValues_EntityRef

**Purpose**  
Same as `CompareBlackboardValues_Bool` for EntityRef variables. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `LeftKey` / `RightKey` | string | Operand variables. |
| `Operator` | integer | Comparison operator. |

[Back to Top](#table-of-contents)

---

## CompareBlackboardValues_Component

**Purpose**  
Same as `CompareBlackboardValues_Bool` for Component variables. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `LeftKey` / `RightKey` | string | Operand variables. |
| `Operator` | integer | Comparison operator. |
| `Probability` | number | Declared but not read (only this variant has it). |

[Back to Top](#table-of-contents)

---

## CompareBlackboardValues_ComponentRef

**Purpose**  
Same as `CompareBlackboardValues_Bool` for ComponentRef variables. Equality operators only.

| Property | Type | Purpose |
|----------|------|---------|
| `LeftKey` / `RightKey` | string | Operand variables. |
| `Operator` | integer | Comparison operator. |

[Back to Top](#table-of-contents)

---

## Comparator

**Purpose**  
Global comparison logic (`_Comparator`) used by the blackboard check decorators. Registers one function per operator on begin play and evaluates comparisons after checking the operator policy.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `IsSet` | integer | 0 – value is not nil (second operand ignored). |
| `IsNotSet` | integer | 1 – value is nil (second operand ignored). |
| `Equal` | integer | 2 – `a == b`. |
| `NotEqual` | integer | 3 – `a ~= b`. |
| `Greater` | integer | 4 – `a > b`. |
| `GreaterOrEqual` | integer | 5 – `a >= b`. |
| `Less` | integer | 6 – `a < b`. |
| `LessOrEqual` | integer | 7 – `a <= b`. |
| `Operators` | table | Operator value to comparison function. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBeginPlay` | Client/Server | Registers the eight comparison functions. |
| `Register` | Default (caller's side) | Stores a function for an operator. |
| `IsComparable` | Default (caller's side) | IsSet/IsNotSet always comparable; otherwise both values must be non-nil and the same Lua type. |
| `Compare` | Default (caller's side) | Returns false if the operator is not allowed for the value type (`_OperatorPolicy`), unregistered, or the values are not comparable; otherwise the function's result. |

[Back to Top](#table-of-contents)

---

## OperatorPolicy

**Purpose**  
Global logic (`_OperatorPolicy`) that declares which operators each blackboard value type allows.

### Properties

| Property | Type | Purpose |
|----------|------|---------|
| `OperatorsByType` | table | Value type to set of allowed operators. |

### Method Summary

| Method | Execution Scope | Purpose |
|--------|----------------|---------|
| `OnBeginPlay` | Client/Server | `Int` and `Number` allow all eight operators; every other type allows `IsSet`, `IsNotSet`, `Equal`, `NotEqual`. |
| `IsAllowed` | Default (caller's side) | True if the operator is allowed for the value type. |

[Back to Top](#table-of-contents)

---

## ValueType

**Purpose**  
Global enumeration logic (`_ValueType`) of blackboard value types.

| Property | Type | Value |
|----------|------|-------|
| `Bool` | integer | 0 |
| `Int` | integer | 1 |
| `Number` | integer | 2 |
| `String` | integer | 3 |
| `Color` | integer | 4 |
| `Vector2` | integer | 5 |
| `Vector3` | integer | 6 |
| `Vector4` | integer | 7 |
| `Entity` | integer | 8 |
| `EntityRef` | integer | 9 |
| `Component` | integer | 10 |
| `ComponentRef` | integer | 11 |

[Back to Top](#table-of-contents)

---

## Changelog
| Version | Changelog | Download |
|---------|-------------|-------------|
| V1_0 | Initial documentation of AI_Behavior_Trees scripts. | [Download](https://github.com/MapleStory-Worlds-Global/YoutubeTutorialProjects/raw/refs/heads/main/AI_Behavior_Trees/AIBehaviorTrees.mod) |