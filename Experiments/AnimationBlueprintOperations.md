# Animation Blueprint Operations

## Experiment 1: Inspect and modify FootIK in AnimGraph

This experiment inspects and bypasses the foot IK logic in
`/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed` through Unreal MCP.

### Result

MCP could inspect the AnimGraph nodes, read their properties and pin connections,
and modify the pose connections using the general `BlueprintTools` interfaces.

The foot IK node is `AnimGraphNode_ControlRig_0`. Reading its `node` property
confirmed that `controlRigAssetReference.blueprintRigClass` references
`/Game/Characters/Mannequins/Rigs/CR_Mannequin_FootIK.CR_Mannequin_FootIK_C`.
Its `Alpha` is `1`, and `NOT IsFalling` drives its `ShouldDoIKTrace` input.

Before:

```text
MainStates -> Slot 'DefaultSlot' -> Control Rig (CR_Mannequin_FootIK) -> Output Pose
IsFalling -> NOT Boolean -> Control Rig.ShouldDoIKTrace
```

After:

```text
MainStates -> Slot 'DefaultSlot' -> Output Pose
```

The Control Rig node remains in the graph with both pose pins disconnected.
The `IsFalling -> NOT Boolean -> ShouldDoIKTrace` connection remains attached
to that disconnected node. The operation bypassed IK rather than deleting nodes.

Compilation with `warnings_as_errors: true` completed without an error response.
A subsequent node inspection confirmed the new connections, and saving returned
`true`. Git reported the target `.uasset` as modified. Runtime visual behavior
was not tested.

### MCP call details

The run made **15 MCP calls; all succeeded**. The first Blueprint schema response
was truncated in the displayed tool output, so the schema was requested again
and filtered locally. This was not an MCP rejection.

Toolset abbreviations below expand to:

- `BlueprintTools`: `editor_toolset.toolsets.blueprint.BlueprintTools`
- `AssetTools`: `editor_toolset.toolsets.asset.AssetTools`
- `ObjectTools`: `editor_toolset.toolsets.object.ObjectTools`

Every `call_tool` supplied the corresponding full `toolset_name` and a short
`tool_name`. Shell checks and local filtering of tool metadata are not included
in the MCP call count. Return values below describe the decoded JSON inside the
MCP text response, rather than reproducing the outer `content` envelope.
Detailed results are in [ABP_Unarmed_mcp_result.md](ABP_Unarmed_mcp_result.md);
large discovery and property responses are labeled as excerpts or projections.

| # | Interface | Why it was called | Return scope | What it returned |
|---:|---|---|---|---|
| 1 | `list_toolsets({})` | Discover available Unreal capabilities. | Toolset names and descriptions; no schemas or asset contents. | Included `BlueprintTools`, `AssetTools`, and `ObjectTools`. [Returned result](ABP_Unarmed_mcp_result.md#call-1-list_toolsets) |
| 2 | `describe_toolset(BlueprintTools)` | Discover graph inspection and editing tools. | The entire Blueprint API schema; no Blueprint data. | Graph, node, pin, and compilation interfaces. The displayed response was truncated. [Returned result](ABP_Unarmed_mcp_result.md#call-2-describe_toolset-blueprinttools) |
| 3 | `describe_toolset(BlueprintTools)` | Recover the schema and locally select node/pin interfaces. | The entire Blueprint API schema again. | Schemas for `find_nodes`, `get_connected_subgraph`, `get_node_infos`, `break_pins`, and `connect_pins`, among others. [Returned result](ABP_Unarmed_mcp_result.md#call-3-describe_toolset-blueprinttools) |
| 4 | `call_tool(get_graph)` | Resolve `AnimGraph` on the known Blueprint asset. | One graph reference. | `{"returnValue":{"refPath":"/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph"}}` [Returned result](ABP_Unarmed_mcp_result.md#call-4-blueprinttoolsget_graph) |
| 5 | `call_tool(find_nodes)` with `title: ""` | Enumerate nodes in AnimGraph. | Node references only; no pin or property data. | Eight nodes: `AnimGraphNode_Root_0`, `AnimGraphNode_StateMachine_0`, `AnimGraphNode_SaveCachedPose_0`, `AnimGraphNode_StateMachine_1`, `AnimGraphNode_Slot_0`, `K2Node_VariableGet_0`, `K2Node_CallFunction_0`, and `AnimGraphNode_ControlRig_0`. [Returned result](ABP_Unarmed_mcp_result.md#call-5-blueprinttoolsfind_nodes) |
| 6 | `call_tool(get_connected_subgraph)` on `AnimGraphNode_ControlRig_0` | Inspect the suspected IK node and its connected logic. | Type, position, pins, values, and connections for the connected component. | Six nodes: Control Rig, Output Pose, NOT Boolean, Get IsFalling, DefaultSlot, and MainStates. Confirmed the original pose chain and `ShouldDoIKTrace` wiring. The other state machine and cached-pose node were outside this connected component. [Returned result](ABP_Unarmed_mcp_result.md#call-6-blueprinttoolsget_connected_subgraph) |
| 7 | `describe_toolset(AssetTools)` | Discover how to save the modified asset. | The entire asset API schema. | Included `save_assets(asset_paths)` returning a boolean. [Returned result](ABP_Unarmed_mcp_result.md#call-7-describe_toolset-assettools) |
| 8 | `describe_toolset(ObjectTools)` | Discover how to inspect the Control Rig asset reference. | The entire object API schema. | Included `list_properties(instance)` and `get_properties(instance, properties)`. [Returned result](ABP_Unarmed_mcp_result.md#call-8-describe_toolset-objecttools) |
| 9 | `call_tool(list_properties)` on the Control Rig node | Discover exact property names before reading them. | Property schema for that node; no current values. | Included `node`, its `controlRigAssetReference` and alpha settings, plus graph-node properties. [Returned result](ABP_Unarmed_mcp_result.md#call-9-objecttoolslist_properties) |
| 10 | `call_tool(get_properties)` with `properties: ["node"]` | Identify which rig the node runs. | The serialized `node` property, including rig settings. | `controlRigAssetReference.blueprintRigClass.refPath` identified `CR_Mannequin_FootIK_C`; `alpha` was `1`, and `alphaInputType` was `Float`. [Returned result](ABP_Unarmed_mcp_result.md#call-10-objecttoolsget_properties) |
| 11 | `call_tool(break_pins)` | Disconnect DefaultSlot's pose output from Control Rig's Source input. | Void result; no graph snapshot. | `{"returnValue":null}` [Returned result](ABP_Unarmed_mcp_result.md#call-11-blueprinttoolsbreak_pins) |
| 12 | `call_tool(connect_pins)` | Connect DefaultSlot's pose output directly to Output Pose's Result input. | Void result; no graph snapshot. | `{"returnValue":null}`. The later readback confirmed that the previous Control Rig-to-Output Pose connection was replaced. [Returned result](ABP_Unarmed_mcp_result.md#call-12-blueprinttoolsconnect_pins) |
| 13 | `call_tool(compile_blueprint)` with `warnings_as_errors: true` | Compile and check the modified Blueprint. | Void result; failures would be reported as errors. | `{"returnValue":null}` with no error response. [Returned result](ABP_Unarmed_mcp_result.md#call-13-blueprinttoolscompile_blueprint) |
| 14 | `call_tool(get_node_infos)` | Verify DefaultSlot, Output Pose, and Control Rig after compilation. | Full node and pin information for these three nodes only. | DefaultSlot output connected only to Root input; Root input connected to DefaultSlot output; Control Rig Source and Pose both had empty `connected_pins`. `ShouldDoIKTrace` still connected to NOT Boolean. [Returned result](ABP_Unarmed_mcp_result.md#call-14-blueprinttoolsget_node_infos) |
| 15 | `call_tool(save_assets)` | Persist only the target Blueprint. | Save success flag. | `{"returnValue":true}` [Returned result](ABP_Unarmed_mcp_result.md#call-15-assettoolssave_assets) |

### Mutation arguments and verification

The Blueprint reference used in calls 4 and 13 was:

```json
{"refPath":"/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed"}
```

Call 11 used `BlueprintTools.break_pins` with these arguments:

```json
{
  "output_pin": {
    "direction": "EGPD_Output",
    "index_id": 0,
    "node": {
      "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Slot_0"
    }
  },
  "input_pin": {
    "direction": "EGPD_Input",
    "index_id": 0,
    "node": {
      "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
    }
  }
}
```

Call 12 used `BlueprintTools.connect_pins` with these arguments:

```json
{
  "output_pin": {
    "direction": "EGPD_Output",
    "index_id": 0,
    "node": {
      "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Slot_0"
    }
  },
  "input_pin": {
    "direction": "EGPD_Input",
    "index_id": 0,
    "node": {
      "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Root_0"
    }
  }
}
```

The pin IDs came from call 6. The connected pose pins all used `index_id: 0`;
the Control Rig's `ShouldDoIKTrace` input used `index_id: 2` and was not edited.
No separate break call was made for Control Rig's output-to-Root connection.
Call 14 verified that connecting DefaultSlot to Root replaced that connection.

Call 15 passed:

```json
{"asset_paths":["/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed"]}
```

The shell check `git status --short -- Content/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.uasset`
returned no changes before editing and the following after saving:

```text
 M Content/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.uasset
```

### Interface implementation locations

Paths are relative to the UE source root recorded in `.ue_source_path`.

| Interface | Plugin | Source file |
|---|---|---|
| `list_toolsets`, `describe_toolset`, `call_tool` | `ModelContextProtocol` | `Engine/Plugins/Experimental/ModelContextProtocol/Source/ModelContextProtocolEditor/Private/ModelContextProtocolToolSearch.cpp` |
| `call_tool` registry lookup and dispatch | `ModelContextProtocol` | `Engine/Plugins/Experimental/ModelContextProtocol/Source/ModelContextProtocolEditor/Private/ModelContextProtocolToolsetRegistryAdapter.cpp` |
| `BlueprintTools.get_graph`, `find_nodes`, `get_connected_subgraph`, `break_pins`, `connect_pins`, `compile_blueprint`, `get_node_infos` | `EditorToolset` | `Engine/Plugins/Experimental/Toolsets/EditorToolset/Content/Python/editor_toolset/toolsets/blueprint.py` |
| `ObjectTools.list_properties`, `get_properties` | `EditorToolset` | `Engine/Plugins/Experimental/Toolsets/EditorToolset/Content/Python/editor_toolset/toolsets/object.py` |
| `AssetTools.save_assets` | `EditorToolset` | `Engine/Plugins/Experimental/Toolsets/EditorToolset/Content/Python/editor_toolset/toolsets/asset.py` |

### Possible call optimizations

- Cache and locally filter discovered schemas to avoid the repeated Blueprint
  schema request and oversized displayed responses.
- With all three toolset schemas cached, omit calls 1, 2, 3, 7, and 8. The same
  inspection, modification, compilation, verification, and save take **10 calls**.
- The asset path was already known, so no `find_assets` or `load_asset` call was
  necessary. The known object path was accepted directly as a `refPath`.
- `get_connected_subgraph` provided the relevant pose chain and IK condition
  without reading nested state-machine graphs or rewriting the graph as DSL.

Only `ABP_Unarmed.uasset` was modified by this experiment. The Control Rig asset
itself was not edited.

### Follow-up: Replace Control Rig with Foot Placement

After the first bypass test, the user reconnected the original Control Rig and
enabled Animation Warping. This follow-up successfully created and configured
Foot Placement through MCP, replaced the active pose chain, compiled with
`warnings_as_errors: true`, and saved the asset.

```text
MainStates -> DefaultSlot -> Local To Component -> Foot Placement
           -> Leg IK -> Component To Local -> Output Pose

NOT IsFalling -> Foot Placement.bAlphaBoolEnabled
             -> Leg IK.bAlphaBoolEnabled
```

The original `AnimGraphNode_ControlRig_0` is retained below the new chain. All
of its pin connections, including `ShouldDoIKTrace`, are disconnected.
The historical results above describe the earlier bypass test; this follow-up
is the latest saved state.

Foot Placement outputs adjusted IK foot targets and pelvis transforms. A downstream
Leg IK node solves the actual leg chains against these targets. The two space
conversion nodes adapt the local-space slot/output to these component-space
skeletal controls. This behavior was checked against the local engine source.

| Setting | Value |
|---|---|
| Pelvis / IK root | `pelvis` / `ik_foot_root` |
| Left leg FK / IK / ball | `foot_l` / `ik_foot_l` / `ball_l` |
| Right leg FK / IK / ball | `foot_r` / `ik_foot_r` / `ball_r` |
| Bones per limb | `2` for both nodes and both legs |
| Plant speed mode | `Graph`; no per-foot speed curves were added |
| Foot Placement and Leg IK alpha mode | `Bool`, driven by `NOT IsFalling` |
| Leg IK forward / hinge axes | `Y` / `None` |
| Leg IK rotation limit / knee twist correction | `false` / `true` |
| Trace and interpolation settings | Retained Foot Placement defaults |

Graph speed mode derives speed from pose changes and any available root-motion
attribute. This experiment did not add or validate root-motion attributes or tune
planting thresholds. Compile success and property/connection readback verify the
asset edit, not visual equivalence to the Control Rig. Runtime walking, slopes,
steps, and jumping still need visual evaluation.

#### Follow-up MCP call details

The follow-up made **39 MCP calls, all successful**. Existing BlueprintTools and
AssetTools interfaces from the earlier experiment were reused. The first compile
was followed by a small correction to gate Leg IK in air, then a final compile.

Each row links to its request and response in the
[external result file](ABP_Unarmed_FootPlacement_mcp_result.md).
Call 2 is explicitly recorded as a relevant-match excerpt; other responses are
preserved in full, including the broad Component search.

| # | Interface | Returned result and effect |
|---:|---|---|
| 1 | `get_connected_subgraph` | Confirmed the user had restored DefaultSlot -> Control Rig -> Output Pose. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-1) |
| 2 | `find_node_types` | Found Animation|SkeletalControls|FootPlacement; relevant match excerpt is recorded. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-2) |
| 3 | `create_node` | Created AnimGraphNode_FootPlacement_0. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-3) |
| 4 | `list_properties` | Returned Foot Placement property schema, including bone definitions and alpha mode. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-4) |
| 5 | `describe_toolset ObjectTools` | Returned ObjectTools API schema; discovered set_properties(instance, values). [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-5) |
| 6 | `find_node_types` | Found Animation|SkeletalControls|LegIK. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-6) |
| 7 | `find_node_types` | Returned broad Component matches, including both animation space conversion node types. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-7) |
| 8 | `get_properties` | Returned Foot Placement defaults: no bones, empty legs, Manual speed mode, Float alpha. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-8) |
| 9 | `create_node` | Created AnimGraphNode_LegIK_0. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-9) |
| 10 | `list_properties` | Returned Leg IK property schema, including legsDefinition. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-10) |
| 11 | `describe_toolset SkeletalMeshTools` | Returned skeletal mesh inspection API schema. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-11) |
| 12 | `create_node` | Created AnimGraphNode_LocalToComponentSpace_0. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-12) |
| 13 | `create_node` | Created AnimGraphNode_ComponentToLocalSpace_0. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-13) |
| 14 | `get_bone_names` | Returned Quinn mesh bone names; confirmed pelvis, foot, ball, and IK bones. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-14) |
| 15 | `get_node_infos` | Returned pin IDs and pose-space types for the four new nodes. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-15) |
| 16 | `set_properties` | Returned true after configuring Foot Placement bones, Graph speed mode, and Bool alpha. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-16) |
| 17 | `set_properties` | Returned true after configuring two Leg IK chains. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-17) |
| 18 | `get_node_infos` | Confirmed Foot Placement now exposes Boolean bAlphaBoolEnabled at input index 1. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-18) |
| 19 | `break_pins` | Returned null; disconnected DefaultSlot -> Control Rig. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-19) |
| 20 | `break_pins` | Returned null; disconnected Control Rig -> Output Pose. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-20) |
| 21 | `break_pins` | Returned null; disconnected NOT IsFalling -> Control Rig.ShouldDoIKTrace. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-21) |
| 22 | `connect_pins` | Returned null; connected DefaultSlot -> Local To Component. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-22) |
| 23 | `connect_pins` | Returned null; connected Local To Component -> Foot Placement. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-23) |
| 24 | `connect_pins` | Returned null; connected Foot Placement -> Leg IK. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-24) |
| 25 | `connect_pins` | Returned null; connected Leg IK -> Component To Local. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-25) |
| 26 | `connect_pins` | Returned null; connected Component To Local -> Output Pose. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-26) |
| 27 | `connect_pins` | Returned null; connected NOT IsFalling -> Foot Placement enable. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-27) |
| 28 | `set_node_position` | Returned null; moved Output Pose to the end of the new chain. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-28) |
| 29 | `set_node_position` | Returned null; moved disconnected Control Rig below the new chain. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-29) |
| 30 | `compile_blueprint` | Returned null; first strict compile passed. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-30) |
| 31 | `get_node_infos` | Returned eight nodes; confirmed the pose chain and disconnected Control Rig. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-31) |
| 32 | `get_properties` | Returned configured Foot Placement properties. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-32) |
| 33 | `get_properties` | Returned configured Leg IK properties; alpha was still Float at this point. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-33) |
| 34 | `set_properties` | Returned true; changed Leg IK alpha to Bool to also disable solving in air. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-34) |
| 35 | `get_node_infos` | Confirmed Leg IK Boolean enable pin at input index 1; pose connections preserved. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-35) |
| 36 | `connect_pins` | Returned null; connected NOT IsFalling -> Leg IK enable. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-36) |
| 37 | `compile_blueprint` | Returned null; final strict compile passed. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-37) |
| 38 | `get_node_infos` | Returned final eight-node snapshot; verified both enable links and all Control Rig pins disconnected. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-38) |
| 39 | `save_assets` | Returned true; saved only ABP_Unarmed. [Result](ABP_Unarmed_FootPlacement_mcp_result.md#call-39) |

#### Additional implementation references

Paths are relative to `.ue_source_path`.

- `Engine/Plugins/Animation/AnimationWarping/Source/Runtime/Public/BoneControllers/AnimNode_FootPlacement.h`: bone definitions, settings, and speed-mode descriptions.
- `Engine/Plugins/Animation/AnimationWarping/Source/Runtime/Private/BoneControllers/AnimNode_FootPlacement.cpp`: foot target/pelvis output and Graph speed calculation.
- `Engine/Plugins/Animation/AnimationWarping/Source/Editor/Private/AnimGraph/AnimGraphNode_FootPlacement.cpp`: editor node title and description.
- `Engine/Source/Runtime/AnimGraphRuntime/Public/BoneControllers/AnimNode_LegIK.h`: leg definition and default solver settings.

For future runs, use more specific node-type filters than `Component` to avoid
returning unrelated node types. Configure both nodes' Boolean alpha modes before
wiring to avoid the extra correction/readback/compile cycle in this run.
