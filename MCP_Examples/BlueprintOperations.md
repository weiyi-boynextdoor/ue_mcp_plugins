# Blueprint Operations

## Example 1: Getting a Full View of a Blueprint

This example shows how to build a complete, read-only view of
`BP_ThirdPersonCharacter` through Unreal MCP. No single tool returns every part
of a Blueprint, so the result is assembled from its asset relationships,
graphs, nodes, pin connections, class structure, components, and properties.

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

- `AssetTools.find_assets` locates the Blueprint and related assets.
- `AssetTools.load_asset` loads assets and returns references for other tools.
- `AssetTools.get_dependencies` and `AssetTools.get_referencers` expose the
  Blueprint's external asset relationships.
- `BlueprintTools.list_graphs`, `list_events`, `list_functions`,
  `list_variables`, and `get_parent` summarize the Blueprint structure.
- `BlueprintTools.read_graph_dsl` returns a readable representation of each
  graph.
- `BlueprintTools.find_nodes` and `get_node_infos` expose exact node and pin
  connections.
- `BlueprintTools.get_default_object` returns the character CDO.
- `ActorTools.get_components` enumerates the Blueprint's actor components.
- `ObjectTools.list_properties` and `get_properties` inspect the CDO,
  components, and referenced assets.

## Building the full Blueprint view

1. Locate the Blueprint with `AssetTools.find_assets`, then load it with
   `AssetTools.load_asset`. Loading returns the object reference required by
   the Blueprint tools.
2. Establish the Blueprint's overall structure with
   `BlueprintTools.list_graphs`, `list_events`, `list_functions`,
   `list_variables`, and `get_parent`. This prevents hidden function graphs or
   inherited behavior from being mistaken for missing logic.
3. Call `BlueprintTools.read_graph_dsl` for every graph returned by
   `list_graphs`. The DSL is the quickest readable overview of each execution
   graph and exposes function calls and data flow.
4. Do not treat the DSL as the complete graph representation. For each graph,
   call `BlueprintTools.find_nodes` with an empty title filter to enumerate all
   nodes, then pass those references to `BlueprintTools.get_node_infos`.
   `get_node_infos` supplies the exact node types, input and output pins,
   default values, positions, and connected-pin references. This second pass
   reveals connections or disconnected nodes that the compact DSL may omit.
5. Use `AssetTools.get_dependencies` and `get_referencers` to follow references
   outside the Blueprint. Load any relevant referenced assets and inspect them
   with the same asset or object tools. This provides the context that is not
   stored directly in the graph.
6. Retrieve the generated class default object with
   `BlueprintTools.get_default_object`. Use `ActorTools.get_components` to
   enumerate inherited and Blueprint-created components.
7. For the CDO, components, and referenced assets, call
   `ObjectTools.list_properties` before `ObjectTools.get_properties`.
   `list_properties` provides the valid property names and schemas; selectively
   reading those properties completes the non-graph portion of the Blueprint
   view without dumping irrelevant engine state.

The full view is therefore the combination of the graph DSL for readability,
node and pin data for exact topology, asset references for external context,
and CDO/component properties for state that is not represented by graph nodes.
