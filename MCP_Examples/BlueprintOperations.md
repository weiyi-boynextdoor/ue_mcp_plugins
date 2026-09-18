# Blueprint Operations

## Example 1: Inspect Third Person Character Movement

This example inspects how `BP_ThirdPersonCharacter` moves. It does not inspect
or modify character facing.

### Result

`IA_Move` produces a two-dimensional input value. Its `Triggered` execution pin
calls `Move`, passing `ActionValue_X` as `X Axis` and `ActionValue_Y` as
`Y Axis`.

Inside `Move`, the Blueprint reads the controller rotation and uses only its
yaw:

- `X Axis` scales the yaw-relative right vector.
- `Y Axis` scales the yaw-relative forward vector.
- Each vector is passed to `AddMovementInput`.

The same `Move` function is also called by `Touch|EventPrimaryThumbstick`.

### MCP call details

The run made **11 MCP calls: 10 succeeded and 1 was rejected**. Each call
returns a specific layer of information rather than the complete Blueprint.

| # | Interface | Why it was called | Return scope | What it returned |
|---:|---|---|---|---|
| 1 | `list_toolsets` | Discover available Unreal capabilities. | Toolset names and descriptions only; no tool schemas or Blueprint data. | Found `AssetTools` and `BlueprintTools`. |
| 2 | `describe_toolset(AssetTools)` | Learn the valid asset tools and arguments. | The entire `AssetTools` API schema; no project assets. | Included `find_assets` and `load_asset` schemas. |
| 3 | `describe_toolset(BlueprintTools)` | Learn the valid Blueprint inspection tools and arguments. | The entire `BlueprintTools` API schema; no Blueprint contents. | Included graph, node, and pin inspection schemas. |
| 4 | `call_tool(AssetTools.find_assets)` using a fully qualified tool name | Attempt to locate the character asset. | Error only. | `Unknown tool`; `call_tool` requires the short tool name. |
| 5 | `call_tool(find_assets)` | Locate the character without knowing its full content path. | Matching asset paths only; assets are not loaded. | One path: `/Game/ThirdPerson/Blueprints/BP_ThirdPersonCharacter`. |
| 6 | `call_tool(load_asset)` | Obtain a UObject reference accepted by Blueprint tools. | One object reference; no graphs, nodes, or properties. | `BP_ThirdPersonCharacter.BP_ThirdPersonCharacter`. |
| 7 | `call_tool(list_graphs)` | Discover where the Blueprint logic is stored. | References to every graph; graph contents are not returned. | Four graph references: `UserConstructionScript`, `Move`, `Aim`, and `EventGraph`. [Raw result](BP_ThirdPersonCharacter_mcp_result.md#blueprinttoolslist_graphs) |
| 8 | `call_tool(read_graph_dsl, EventGraph)` | Inspect input events and function calls. | A compact text representation of one graph, not the whole Blueprint. Some connections may be omitted. | Input events and the touch thumbstick path. The `IA_Move -> Move` body was omitted. [Raw result](BP_ThirdPersonCharacter_mcp_result.md#blueprinttoolsread_graph_dsl-eventgraph) |
| 9 | `call_tool(read_graph_dsl, Move)` | Inspect the movement function implementation. | A compact text representation of the `Move` graph only. | Controller-yaw right/forward vectors and two `AddMovementInput` calls using X/Y. [Raw result](BP_ThirdPersonCharacter_mcp_result.md#blueprinttoolsread_graph_dsl-move) |
| 10 | `call_tool(find_nodes, EventGraph)` | Get node references so omitted EventGraph connections can be checked. | Node references only; no pin details. The empty title filter selected the whole EventGraph. | 15 node references. [Returned nodes](BP_ThirdPersonCharacter_mcp_result.md#blueprinttoolsfind_nodes-eventgraph) |
| 11 | `call_tool(get_node_infos)` | Verify exact execution and value connections. | Full type, position, input-pin, output-pin, value, and connection data for the 15 requested nodes; still not the whole Blueprint. | Confirmed `IA_Move.Triggered -> Move`, X/Y mapping, and the touch-input path. [Returned node information](BP_ThirdPersonCharacter_mcp_result.md#blueprinttoolsget_node_infos-eventgraph) |

### Interface implementation locations

Paths are relative to the UE source root.

| Interface | Plugin | Source file |
|---|---|---|
| `list_toolsets`, `describe_toolset`, `call_tool` | `ModelContextProtocol` | `Engine/Plugins/Experimental/ModelContextProtocol/Source/ModelContextProtocolEditor/Private/ModelContextProtocolToolSearch.cpp` |
| `call_tool` registry lookup and dispatch | `ModelContextProtocol` | `Engine/Plugins/Experimental/ModelContextProtocol/Source/ModelContextProtocolEditor/Private/ModelContextProtocolToolsetRegistryAdapter.cpp` |
| `AssetTools.find_assets`, `AssetTools.load_asset` | `EditorToolset` | `Engine/Plugins/Experimental/Toolsets/EditorToolset/Content/Python/editor_toolset/toolsets/asset.py` |
| `BlueprintTools.list_graphs`, `read_graph_dsl`, `find_nodes`, `get_node_infos` | `EditorToolset` | `Engine/Plugins/Experimental/Toolsets/EditorToolset/Content/Python/editor_toolset/toolsets/blueprint.py` |

### Possible call optimizations

- Cache toolset schemas after discovery. A repeat run can omit calls 1-3.
- Use short tool names with `call_tool` to avoid call 4.
- If the asset path is already known, omit `find_assets`.
- Continue reading only `EventGraph` and `Move`; the other two graphs are not
  needed for movement.
- The largest response was call 11 because all 15 EventGraph nodes were
  requested. Filtering `find_nodes` for `IA_Move` and `Move` would reduce
  the response size, although separate filters may increase the number of
  calls.

With cached schemas and the known tool-name format, the same inspection would
take **7 successful calls**. With the asset path cached as well, it would take
**6**.

No Blueprint assets or properties were modified.
