# BehaviourTree Authoring Spec

- **Project root**: `c:\Users\Admin\Documents\Nexon\Dev\TutorialProjects\YoutubeTutorialProjects\AI_Behavior_Trees`
- **Engine CoreVersion**: `26.7.0.0`
- **Generated**: 2026-09-04 16:56:15
- **Discovered**: 42 action nodes, 34 decorator nodes, 2 custom composite nodes

> Compact catalog for tree construction. Custom-node UUIDs were read from real `.codeblock` files in this project -- never invent them.

---

## 1. Composite nodes

| nodeName | definitionId | btNodeType |
|---|---|---|
| `SequenceNode` | `SequenceNode` | 1 |
| `SelectorNode` | `SelectorNode` | 1 |
| `ParallelNode` | `ParallelNode` | 1 |

### Custom composite nodes (`extends CompositeNode`)

| Name | definitionId | btNodeType | Properties |
|---|---|---|---|
| `RandomSelector` | `codeblock://b83275ac-02c2-4780-ac16-82122c170671` | 1 | (none) |
| `RandomSequence` | `codeblock://4b482d0e-3d66-4010-aa66-9dd4bde3f878` | 1 | (none) |

## 2. Custom action nodes

| Name | definitionId | btNodeType | Properties |
|---|---|---|---|
| `ActionFollow` | `codeblock://7e347847-0706-415b-8453-c8eb5c0da080` | 0 | `ArrivalThreshold`<br>`TargetEntityKey` |
| `ActionPatrol` | `codeblock://227b064b-9324-4012-9b58-9518986f2874` | 0 | `waypointID`<br>`ArrivalThreshold` |
| `AddForce` | `codeblock://0096b23a-9a2d-4a21-8138-ceecaa7f0997` | 0 | `ForceKey` |
| `AttackDuration` | `codeblock://7d109e06-87a9-4575-b551-a89d2e9b1a46` | 0 | `radiusIDKey`<br>`attackPosKey` |
| `AttackFast` | `codeblock://eb889191-f905-4aee-a6c1-23ef365abc7e` | 0 | (none) |
| `Chase` | `codeblock://2528e2d7-877c-42b5-b196-7277dec0fa00` | 0 | `TargetEntityKey`<br>`MoveSpeedKey`<br>`ArrivalThreshold` |
| `DestroyEntity` | `codeblock://b2d67820-f86d-4878-a4fd-687f6a74e236` | 0 | `TargetEntityKey`<br>`Delay` |
| `DownJump` | `codeblock://0b45dc5b-17d7-41ea-bd63-e2983b2f6d47` | 0 | (none) |
| `ExecuteMethod` | `codeblock://8860c18e-4eed-4879-b7f4-8f2a7e7b9799` | 0 | `methodName`<br>`methodIsCompleteKey` |
| `Jump` | `codeblock://7032d5a4-6ff9-498f-a2ef-4a96e6df25a8` | 0 | (none) |
| `MoveTo` | `codeblock://fb6afe42-1224-46e5-b76d-ca561e86c2c2` | 0 | `TargetPositionKey`<br>`MoveSpeedKey`<br>`ArrivalThreshold` |
| `MoveToDirection` | `codeblock://4fff9275-d943-4258-a243-aff0f5e4e7aa` | 0 | `LookAtPositionKey`<br>`ArrivalThreshold` |
| `MoveToTarget` | `codeblock://4184ad77-718b-4b99-add0-8ef72362c07c` | 0 | `TargetEntityKey`<br>`ArrivalThreshold`<br>`moveToLastKnownPosition` |
| `PlayAnimationFrames` | `codeblock://42331358-567b-4fde-8c55-5e84d52fb6fc` | 0 | `animationRUID`<br>`frameStartIndex`<br>`frameEndIndex` |
| `PlayAreaParticle` | `codeblock://525d361a-9ea0-4955-bc30-796a318ed0c8` | 0 | `ParticleType`<br>`AreaSize`<br>`Instigator`<br>`Position`<br>`ZRotation`<br>`Scale`<br>`IsLoop` |
| `PlayBasicParticle` | `codeblock://79a8b601-ba5b-4dd0-97c8-dcf0d877b1f2` | 0 | `ParticleType`<br>`Instigator`<br>`Position`<br>`ZRotation`<br>`Scale`<br>`IsLoop` |
| `PlaySound` | `codeblock://35d556a1-da04-46d0-bf22-8b78605eb717` | 0 | `SoundId`<br>`Volume` |
| `PlaySpriteParticle` | `codeblock://5c6eba17-5ead-4e64-a867-c73995460906` | 0 | `ParticleType`<br>`SpriteRUID`<br>`Instigator`<br>`Position`<br>`ZRotation`<br>`Scale`<br>`IsLoop` |
| `SetBlackboardValue_Bool` | `codeblock://90f55790-9d5a-4a80-82d4-6a63dd0c0e50` | 0 | `Key`<br>`Value` |
| `SetBlackboardValue_Color` | `codeblock://b8d97f8d-2ad8-4ebe-8e69-e9b0609c5d40` | 0 | `Key`<br>`Value` |
| `SetBlackboardValue_Component` | `codeblock://f7b91cf0-212c-4197-8101-15b67c45020b` | 0 | `Key`<br>`Value` |
| `SetBlackboardValue_ComponentRef` | `codeblock://2aa1e6ad-dac9-480e-98a1-8b52e65da17e` | 0 | `Key`<br>`Value` |
| `SetBlackboardValue_Entity` | `codeblock://11229fd7-ae52-4027-9a82-a05aada77bab` | 0 | `Key`<br>`Value` |
| `SetBlackboardValue_EntityRef` | `codeblock://a8ad02c9-7f34-4cd2-a04f-9b6a26c057ed` | 0 | `Key`<br>`Value` |
| `SetBlackboardValue_Int` | `codeblock://fe33ada7-4dee-495d-8e4c-eff1511ff194` | 0 | `Key`<br>`Value` |
| `SetBlackboardValue_Number` | `codeblock://3ac023c4-841e-44eb-8fb7-a7db059beaaa` | 0 | `Key`<br>`Value` |
| `SetBlackboardValue_String` | `codeblock://51d3750b-91a1-4111-914c-baefb6197002` | 0 | `Key`<br>`Value` |
| `SetBlackboardValue_TargetPos` | `codeblock://7ee9d164-be37-49c0-b7a6-88e35e561c7f` | 0 | `Key`<br>`ValueKey` |
| `SetBlackboardValue_Vector2` | `codeblock://a9410254-52bf-41df-a1cb-b99a478f4eb3` | 0 | `Key`<br>`Value` |
| `SetBlackboardValue_Vector3` | `codeblock://fd694868-8b78-4008-bf6a-c01d4a9be02a` | 0 | `Key`<br>`Value` |
| `SetBlackboardValue_Vector4` | `codeblock://68e627e4-e629-43e5-8889-1da37c2f8f9d` | 0 | `Key`<br>`Value` |
| `SetEntityEnable` | `codeblock://ff8e7744-36a8-4c57-882c-07444958ddf6` | 0 | `TargetEntityKey`<br>`Enabled` |
| `SetForce` | `codeblock://0775ce4d-096c-4f50-bfd0-190321d96306` | 0 | `ForceKey` |
| `SetState` | `codeblock://36953273-56e9-4942-a6e5-3f339ecc35e8` | 0 | `stateString` |
| `ShowWarning` | `codeblock://e5deb883-4336-4217-a9a7-f075be90da7f` | 0 | `warningColor`<br>`warningIDKey`<br>`radiusIDKey`<br>`positionKey`<br>`warnDurationKey`<br>`warnDuration` |
| `SpawnAtPosition` | `codeblock://8c7af48b-8f88-4d13-9c80-59c6b0fda844` | 0 | `positionKey`<br>`modelID`<br>`entityName` |
| `SpawnEntity` | `codeblock://ba401cc8-2e4a-4ca4-b925-c0c937627d94` | 0 | `SourceEntity`<br>`SpawnEntityName`<br>`SpawnPosition`<br>`ParentEntity`<br>`IncludeChildren`<br>`ResultEntityKey` |
| `SpawnProjectileAttack` | `codeblock://73b8843d-9365-4f9b-9bd0-141c9505cffa` | 0 | `targetEntityKey`<br>`radiusIDKey`<br>`poolName`<br>`damage`<br>`effectRUID` |
| `Wait` | `codeblock://e0dfcaa7-e676-4501-8f2c-24be18917a3b` | 0 | `WaitTime` |
| `WaitAnimationDuration` | `codeblock://2191f6c9-d224-4f59-b4b0-08290a56cb58` | 0 | `animRUID`<br>`resultValueKey`<br>`fromTo`<br>`addDelay` |
| `WaitBlackboardTime` | `codeblock://0738b64c-1fcc-486b-8fec-6947f4d54782` | 0 | `WaitTimeKey` |
| `WaitRandom` | `codeblock://1643b567-8a61-4e73-bdbb-1bef1f1fcf9e` | 0 | `WaitTimeMin`<br>`WaitTimeMax` |

## 3. Custom decorator nodes

| Name | definitionId | btNodeType | Properties |
|---|---|---|---|
| `BlackboardCondition_Bool` | `codeblock://febb647e-5d7c-4c9d-a98a-b76624198b48` | 2 | `Key`<br>`Operator`<br>`CompareValue` |
| `BlackboardCondition_Color` | `codeblock://38381e80-91c3-4dbe-bca2-d046ef5615f4` | 2 | `Key`<br>`Operator`<br>`CompareValue` |
| `BlackboardCondition_Component` | `codeblock://96a37b82-3a50-4cb3-806b-9fe670ec1595` | 2 | `Key`<br>`Operator`<br>`CompareValue` |
| `BlackboardCondition_ComponentRef` | `codeblock://18e06aac-a9ba-454e-92fc-815ed89ddf87` | 2 | `Key`<br>`Operator`<br>`CompareValue` |
| `BlackboardCondition_Entity` | `codeblock://f684d400-4c54-4dc3-9214-9b16c1a7abd5` | 2 | `Key`<br>`Operator`<br>`CompareValue` |
| `BlackboardCondition_EntityRef` | `codeblock://8ec79156-8dd2-4c20-b019-5078f6ccfb74` | 2 | `Key`<br>`Operator`<br>`CompareValue` |
| `BlackboardCondition_Int` | `codeblock://80d5a095-ab75-4e8d-93f8-65d86a0fa12a` | 2 | `Key`<br>`Operator`<br>`CompareValue` |
| `BlackboardCondition_Number` | `codeblock://c97854ef-3051-4df8-91d3-77b01e78203f` | 2 | `Key`<br>`Operator`<br>`CompareValue` |
| `BlackboardCondition_String` | `codeblock://0dfbf6ee-8cd7-47d9-b831-577b284f0044` | 2 | `Key`<br>`Operator`<br>`CompareValue` |
| `BlackboardCondition_Vector2` | `codeblock://26b5d75c-ca6a-4a22-898f-97f9e0663905` | 2 | `Key`<br>`Operator`<br>`CompareValue` |
| `BlackboardCondition_Vector3` | `codeblock://40a290a2-6f12-4f2f-91e8-b263e5083e42` | 2 | `Key`<br>`Operator`<br>`CompareValue` |
| `BlackboardCondition_Vector4` | `codeblock://a4ec69a0-7edc-4bf9-b15e-b2e2c8f3c03f` | 2 | `Key`<br>`Operator`<br>`CompareValue` |
| `CanMove` | `codeblock://8f7faec4-3007-458b-aabd-ac8c44e0cb9b` | 2 | `LookAheadDistance`<br>`IsForward` |
| `CompareBlackboardValues_Bool` | `codeblock://f5bd5c98-b708-4ec4-9173-0d14d9f057bd` | 2 | `LeftKey`<br>`RightKey`<br>`Operator` |
| `CompareBlackboardValues_Color` | `codeblock://34f541c4-f1af-4a96-bc6a-5c23ae0afc0b` | 2 | `LeftKey`<br>`RightKey`<br>`Operator` |
| `CompareBlackboardValues_Component` | `codeblock://ba19a9f2-3900-46ce-873a-7685cf907da5` | 2 | `LeftKey`<br>`RightKey`<br>`Operator`<br>`Probability` |
| `CompareBlackboardValues_ComponentRef` | `codeblock://21fbf73f-ccdd-4ab8-a821-5ade315ba363` | 2 | `LeftKey`<br>`RightKey`<br>`Operator` |
| `CompareBlackboardValues_Entity` | `codeblock://15463bb8-1e58-4d6e-98b1-497ddcfc1145` | 2 | `LeftKey`<br>`RightKey`<br>`Operator` |
| `CompareBlackboardValues_EntityRef` | `codeblock://34a79bcf-3821-457c-a6ba-29e2a67630c1` | 2 | `LeftKey`<br>`RightKey`<br>`Operator` |
| `CompareBlackboardValues_Int` | `codeblock://877288fd-e716-4ebd-8824-6a9b33f14747` | 2 | `LeftKey`<br>`RightKey`<br>`Operator` |
| `CompareBlackboardValues_Number` | `codeblock://3f92ca47-c929-4e69-8ce8-bdbcd0470fd4` | 2 | `LeftKey`<br>`RightKey`<br>`Operator` |
| `CompareBlackboardValues_String` | `codeblock://da0cafcb-2093-4239-917a-4c9d04a4eb53` | 2 | `LeftKey`<br>`RightKey`<br>`Operator` |
| `CompareBlackboardValues_Vector2` | `codeblock://e1483523-0d0c-4099-a749-c48c2ec5d4a5` | 2 | `LeftKey`<br>`RightKey`<br>`Operator` |
| `CompareBlackboardValues_Vector3` | `codeblock://494f5c02-fba6-484c-804b-b4381d15c459` | 2 | `LeftKey`<br>`RightKey`<br>`Operator` |
| `CompareBlackboardValues_Vector4` | `codeblock://112960c3-66c8-43c9-a6e5-4ddf4fd710a3` | 2 | `LeftKey`<br>`RightKey`<br>`Operator` |
| `Cooldown` | `codeblock://b8b08730-ab03-491b-a8c2-41e49e694c53` | 2 | `Duration` |
| `HasTarget` | `codeblock://ec90007b-b76d-4f94-80ad-04c6f52d47c2` | 2 | `targetEntityKey`<br>`compareValue`<br>`finishIfStarted` |
| `Inverter` | `codeblock://33658f1e-498a-4ab0-af17-9c981b24eb81` | 2 | (none) |
| `IsJumping` | `codeblock://65e7ac90-d4de-43df-9491-dd2360937bf2` | 2 | (none) |
| `IsOnGround` | `codeblock://cdb2b230-9d12-469e-9874-ea6272a08abf` | 2 | (none) |
| `Loop` | `codeblock://fe86eda5-6853-4c04-93ae-b03ffaed0664` | 2 | `IsInfinite`<br>`LoopCount` |
| `Succeeder` | `codeblock://649442bd-a61b-4c32-acb0-45f03315461f` | 2 | (none) |
| `TestDecorator` | `codeblock://62a2eadb-d06a-40c9-a203-610c23daf263` | 2 | `child` |
| `TimeLimit` | `codeblock://05b1ec77-2e7a-4977-b0cd-1df7c1699d98` | 2 | `Duration` |

## 4. Type map

Use this for `nodeProperties[].propertyType.type` and `Blackboard.Variables[].Type.type`. `ObjectValue shape` applies to Blackboard variables.

| mlua type | serialized type | ObjectValue shape |
|---|---|---|
| `bool` | `System.Boolean, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089` | `true / false` |
| `boolean` | `System.Boolean, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089` | `true / false` |
| `string` | `System.String, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089` | `"<string>"` |
| `integer` | `System.Int64, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089` | `<int>` |
| `number` | `System.Double, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089` | `<num> (use 3.0 not 3)` |
| `Vector2` | `MOD.Core.MODVector2, MOD.Core, Version=26.7.0.0, Culture=neutral, PublicKeyToken=null` | `{ "x": <num>, "y": <num> }` |
| `Vector3` | `MOD.Core.MODVector3, MOD.Core, Version=26.7.0.0, Culture=neutral, PublicKeyToken=null` | `{ "x": <num>, "y": <num>, "z": <num> }` |
| `Vector4` | `MOD.Core.MODVector4, MOD.Core, Version=26.7.0.0, Culture=neutral, PublicKeyToken=null` | `{ "x": <num>, "y": <num>, "z": <num>, "w": <num> }` |
| `Color` | `MOD.Core.MODColor, MOD.Core, Version=26.7.0.0, Culture=neutral, PublicKeyToken=null` | `{ "r": <0..1>, "g": <0..1>, "b": <0..1>, "a": <0..1> }` |
| `Entity` | `MOD.Core.MODEntity, MOD.Core, Version=26.7.0.0, Culture=neutral, PublicKeyToken=null` | `{ "tempEntityId": null, "IsRelative": false, "EntityId": "<entity-uuid>", "Version2": false }` |
| `Component` | `MOD.Core.Component.MODComponent, MOD.Core, Version=26.7.0.0, Culture=neutral, PublicKeyToken=null` | `{ "IsRelative": false, "ComponentId": "<entity-uuid>:<ComponentName>", "UseNested": false }` |
| `ComponentRef` | `MOD.Core.MODComponentRef, MOD.Core, Version=26.7.0.0, Culture=neutral, PublicKeyToken=null` | `{ "IsRelative": false, "ComponentId": "<entity-uuid>:<ComponentName>", "UseNested": false }` |
| `EntityRef` | `MOD.Core.MODEntityRef, MOD.Core, Version=26.7.0.0, Culture=neutral, PublicKeyToken=null` | `(verify against an existing serialized example before use)` |

