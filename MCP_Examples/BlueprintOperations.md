# Blueprint Operations

## Example 1: Inspecting Third Person Character Input

This example used Unreal MCP to determine how
`BP_ThirdPersonCharacter` is driven by player input. The inspection followed
the input data from mapping contexts, through Enhanced Input events in the
character's Event Graph, and into the `Move`, `Aim`, `Jump`, and `StopJumping`
calls. It also inspected relevant character movement and camera properties.

No Blueprint or asset was modified.

## Plugins

- **Unreal MCP** (`ModelContextProtocol`) exposes the Unreal MCP server.
- **Editor Toolset** (`EditorToolset`) provides the asset, Blueprint, object,
  and actor inspection tools.

## MCP tools used and their owners

### Unreal MCP (`ModelContextProtocol`)

- `list_toolsets` discovers registered toolsets.
- `describe_toolset` returns their tool names and argument schemas.
- `call_tool` invokes a tool from a registered toolset.

### Editor Toolset (`EditorToolset`)

- `AssetTools.find_assets` locates the character and input mapping contexts.
- `AssetTools.load_asset` loads Blueprints, input actions, and mapping contexts.
- `AssetTools.get_dependencies` and `AssetTools.get_referencers` connect the
  input actions to `IMC_Default`, `IMC_MouseLook`, and the player controller.
- `BlueprintTools.list_graphs`, `list_events`, `list_functions`,
  `list_variables`, and `get_parent` summarize the character Blueprint.
- `BlueprintTools.read_graph_dsl` reads the Event Graph and the `Move` and
  `Aim` function graphs.
- `BlueprintTools.find_nodes` and `get_node_infos` expose exact node and pin
  connections, including the Enhanced Input trigger outputs.
- `BlueprintTools.get_default_object` returns the character CDO.
- `ActorTools.get_components` locates the movement, camera, and spring-arm
  components.
- `ObjectTools.list_properties` and `get_properties` read input mappings,
  action types, movement rotation settings, and camera settings.

## Result

`BP_ThirdPersonPlayerController` adds `IMC_Default` on `BeginPlay` and adds
`IMC_MouseLook` when touch controls are not active. In
`BP_ThirdPersonCharacter`:

- `IA_Move` calls `Move`, which applies controller-yaw-relative forward and
  right movement.
- `IA_Look` and `IA_MouseLook` call `Aim`, which applies controller yaw and
  pitch input.
- `IA_Jump` calls `Jump` on `Started` and `StopJumping` on `Completed`.
- Touch thumbsticks call the same `Move` and `Aim` functions, while touch jump
  events call `Jump` and `StopJumping`.

The default mapping context maps movement to WASD, arrow keys, and the left
gamepad stick; jumping to Spacebar and the bottom gamepad face button; and
looking to the right gamepad stick. `IMC_MouseLook` maps mouse movement to
`IA_MouseLook`.
