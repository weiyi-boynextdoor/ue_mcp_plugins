# BP_ThirdPersonCharacter MCP Results

These are the raw MCP results used by
[BlueprintOperations.md](BlueprintOperations.md).

## `BlueprintTools.list_graphs`

```json
{
  "returnValue": [
    {
      "refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:UserConstructionScript"
    },
    {
      "refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:Move"
    },
    {
      "refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:Aim"
    },
    {
      "refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph"
    }
  ]
}
```

## `BlueprintTools.read_graph_dsl`: `EventGraph`

```json
{
  "returnValue": "(event EnhancedInputActionIA_Move (ActionValue_X ActionValue_Y ElapsedSeconds TriggeredSeconds InputAction))\n\n(event EnhancedInputActionIA_Look (ActionValue_X ActionValue_Y ElapsedSeconds TriggeredSeconds InputAction))\n\n(event Touch|EventPrimaryThumbstick (Axis_X Axis_Y)\n  (CallFunction|Move Axis_X Axis_Y))\n\n(event Touch|EventSecondaryThumbstick (Axis_X Axis_Y)\n  (CallFunction|Aim Axis_X Axis_Y))\n\n(event EnhancedInputActionIA_MouseLook (ActionValue_X ActionValue_Y ElapsedSeconds TriggeredSeconds InputAction))\n\n(event EnhancedInputActionIA_Jump (ActionValue ElapsedSeconds TriggeredSeconds InputAction))\n\n(event Touch|EventTouchJumpStart\n  (Character|Jump))\n\n(event Touch|EventTouchJumpEnd\n  (Character|StopJumping))\n"
}
```

Rendered DSL:

```lisp
(event EnhancedInputActionIA_Move (ActionValue_X ActionValue_Y ElapsedSeconds TriggeredSeconds InputAction))

(event EnhancedInputActionIA_Look (ActionValue_X ActionValue_Y ElapsedSeconds TriggeredSeconds InputAction))

(event Touch|EventPrimaryThumbstick (Axis_X Axis_Y)
  (CallFunction|Move Axis_X Axis_Y))

(event Touch|EventSecondaryThumbstick (Axis_X Axis_Y)
  (CallFunction|Aim Axis_X Axis_Y))

(event EnhancedInputActionIA_MouseLook (ActionValue_X ActionValue_Y ElapsedSeconds TriggeredSeconds InputAction))

(event EnhancedInputActionIA_Jump (ActionValue ElapsedSeconds TriggeredSeconds InputAction))

(event Touch|EventTouchJumpStart
  (Character|Jump))

(event Touch|EventTouchJumpEnd
  (Character|StopJumping))
```

## `BlueprintTools.read_graph_dsl`: `Move`

```json
{
  "returnValue": "(fn Move (X Axis Y Axis)\n  (Pawn|Input|AddMovementInput (Math|Vector|GetRightVector (Pawn|GetControlRotation) 0.0 (Pawn|GetControlRotation)) X Axis)\n  (Pawn|Input|AddMovementInput (Math|Vector|GetForwardVector 0.0 0.0 (Pawn|GetControlRotation)) Y Axis))"
}
```

Rendered DSL:

```lisp
(fn Move (X Axis Y Axis)
  (Pawn|Input|AddMovementInput
    (Math|Vector|GetRightVector
      (Pawn|GetControlRotation)
      0.0
      (Pawn|GetControlRotation))
    X Axis)
  (Pawn|Input|AddMovementInput
    (Math|Vector|GetForwardVector
      0.0
      0.0
      (Pawn|GetControlRotation))
    Y Axis))
```
