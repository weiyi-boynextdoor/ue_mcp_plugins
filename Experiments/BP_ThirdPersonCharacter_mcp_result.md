# BP_ThirdPersonCharacter MCP Results

These are the MCP results used by
[BlueprintOperations.md](BlueprintOperations.md). Compact responses are shown
as raw JSON. The large `get_node_infos` response is listed as a complete node
inventory plus a movement-focused JSON projection.

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

## `BlueprintTools.find_nodes`: `EventGraph`

The empty title filter returned references to all 15 nodes in the graph.

```json
{
  "returnValue": [
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_CallFunction_23"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_EnhancedInputAction_6"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_EnhancedInputAction_1"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_EnhancedInputAction_4"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_CallFunction_16"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_Event_6"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_EnhancedInputAction_7"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_Event_2"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_Event_4"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_Event_5"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_CallFunction_38"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_CallFunction_39"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_CallFunction_41"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_CallFunction_42"},
    {"refPath": "/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter.BP_ThirdPersonCharacter:EventGraph.K2Node_CallFunction_40"}
  ]
}
```

## `BlueprintTools.get_node_infos`: `EventGraph`

The call received the 15 references above and returned 15 `NodeInfo` objects.
Every object contained:

- `node`: the node reference.
- `type_id`: the node category and title.
- `position`: graph coordinates.
- `input_pins` and `output_pins`: pin name, type, default value, pin ID,
  and every connected pin.

The complete returned node inventory is:

| Node | `type_id` | Position | Returned connection information |
|---|---|---:|---|
| `K2Node_CallFunction_23` | `Character\|StopJumping` | (-2416, -320) | Execute input connected from `IA_Jump.Completed` and `TouchJumpEnd.then`. |
| `K2Node_EnhancedInputAction_6` | `Input\|EnhancedActionEvents\|EnhancedInputActionIA_Jump` | (-2800, -480) | `Started -> Jump`; `Completed -> StopJumping`; Boolean action value and timing pins were also returned. |
| `K2Node_EnhancedInputAction_1` | `Input\|EnhancedActionEvents\|EnhancedInputActionIA_Move` | (-1920, -1376) | `Triggered -> K2Node_CallFunction_38.execute`; `ActionValue_X -> X Axis`; `ActionValue_Y -> Y Axis`. |
| `K2Node_EnhancedInputAction_4` | `Input\|EnhancedActionEvents\|EnhancedInputActionIA_Look` | (-2832, -1360) | `Triggered -> K2Node_CallFunction_41.execute`; X/Y values connect to the `Aim` call. |
| `K2Node_CallFunction_16` | `Character\|Jump` | (-2416, -464) | Execute input connected from `IA_Jump.Started` and `TouchJumpStart.then`. |
| `K2Node_Event_6` | `AddEvent\|Touch\|EventSecondaryThumbstick` | (-2832, -1120) | `then -> K2Node_CallFunction_42.execute`; `Axis_X/Y` connect to the `Aim` call. |
| `K2Node_EnhancedInputAction_7` | `Input\|EnhancedActionEvents\|EnhancedInputActionIA_MouseLook` | (-2832, -864) | `Triggered -> K2Node_CallFunction_40.execute`; X/Y values connect to the `Aim` call. |
| `K2Node_Event_2` | `AddEvent\|Touch\|EventPrimaryThumbstick` | (-1920, -1136) | `then -> K2Node_CallFunction_39.execute`; `Axis_X/Y` connect to the `Move` call. |
| `K2Node_Event_4` | `AddEvent\|Touch\|EventTouchJumpStart` | (-2784, -256) | `then -> K2Node_CallFunction_16.execute`. |
| `K2Node_Event_5` | `AddEvent\|Touch\|EventTouchJumpEnd` | (-2784, -160) | `then -> K2Node_CallFunction_23.execute`. |
| `K2Node_CallFunction_38` | `\|Move` | (-1536, -1392) | Execute, `X Axis`, and `Y Axis` inputs connected from `IA_Move`. |
| `K2Node_CallFunction_39` | `\|Move` | (-1536, -1136) | Execute and X/Y inputs connected from the primary thumbstick event. |
| `K2Node_CallFunction_41` | `\|Aim` | (-2464, -1376) | Execute and X/Y inputs connected from `IA_Look`. |
| `K2Node_CallFunction_42` | `\|Aim` | (-2464, -1120) | Execute and X/Y inputs connected from the secondary thumbstick event. |
| `K2Node_CallFunction_40` | `\|Aim` | (-2464, -880) | Execute and X/Y inputs connected from `IA_MouseLook`. |

The movement-specific records in the response were:

```json
[
  {
    "node": "K2Node_EnhancedInputAction_1",
    "type_id": "Input|EnhancedActionEvents|EnhancedInputActionIA_Move",
    "position": {"x": -1920, "y": -1376},
    "connected_outputs": {
      "Triggered": "K2Node_CallFunction_38.execute",
      "ActionValue_X": "K2Node_CallFunction_38.X Axis",
      "ActionValue_Y": "K2Node_CallFunction_38.Y Axis"
    },
    "InputAction": "/Game/Input/Actions/IA_Move.IA_Move"
  },
  {
    "node": "K2Node_CallFunction_38",
    "type_id": "|Move",
    "position": {"x": -1536, "y": -1392},
    "connected_inputs": {
      "execute": "K2Node_EnhancedInputAction_1.Triggered",
      "X Axis": "K2Node_EnhancedInputAction_1.ActionValue_X",
      "Y Axis": "K2Node_EnhancedInputAction_1.ActionValue_Y"
    }
  }
]
```

The JSON above is a movement-focused projection of those two full
`NodeInfo` objects. The MCP response also included unconnected execution
pins, pin type IDs, default values, and structured pin IDs for all 15 nodes.
