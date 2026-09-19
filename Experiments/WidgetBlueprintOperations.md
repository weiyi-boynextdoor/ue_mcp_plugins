# Widget Blueprint Operations

## Experiment 1: Create simple widget blueprint

This experiment creates `/Game/UI/WBP_Test` through Unreal MCP with Confirm
and Cancel buttons. The request was interpreted as a UMG Widget Blueprint.

### Result

The Widget Blueprint was created with `UserWidget` as its parent class:

```text
RootCanvas (CanvasPanel)
└── ButtonRow (HorizontalBox)
    ├── Confirm (Button, exposed as a variable)
    │   └── ConfirmText (TextBlock: "Confirm")
    └── Cancel (Button, exposed as a variable)
        └── CancelText (TextBlock: "Cancel")
```

The layout was configured as a centered 336 × 56 row, with equal-width button
slots and a 16-unit gap. A CanvasPanel provides center anchoring; a HorizontalBox
keeps the two buttons side by side. Each Button needs a TextBlock child to display
its label. The buttons were already exposed as variables by `AddWidget`, so no
`ToggleWidgetAsVariable` call was needed. No click handlers were requested or added.

Compilation with `warnings_as_errors: true` completed without an error response,
and saving returned `true`. A subsequent tree query confirmed all six widgets and
both button variables; property readback confirmed the two labels. Layout writes
returned `true`, but layout properties were not read back after setting them.
The experiment did not include a screenshot, PIE interaction test, or disk reload.

### MCP call details

The run made **34 Unreal MCP calls; all completed without an error response**.
Local tool discovery, JavaScript orchestration, and response filtering are not
additional Unreal MCP calls. Calls grouped below were separate MCP requests.

Toolset abbreviations expand to:

- `UMGToolSet`: `UMGToolSet.UMGToolSet`
- `AssetTools`: `editor_toolset.toolsets.asset.AssetTools`
- `ObjectTools`: `editor_toolset.toolsets.object.ObjectTools`
- `BlueprintTools`: `editor_toolset.toolsets.blueprint.BlueprintTools`

Every `call_tool` used the full `toolset_name` and a short `tool_name`.
Responses and exact mutation arguments are kept separately in
[WBP_Test_mcp_result.md](WBP_Test_mcp_result.md). Large discovery responses are
documented as excerpts or projections, not complete raw response captures.

| # | Interface | Why it was called | Return scope | What it returned |
|---:|---|---|---|---|
| 1 | `list_toolsets({})` | Discover the connected editor's capabilities. | Names and descriptions, without API schemas. | UMG creation tools and general asset, object, and Blueprint tools. [Details](WBP_Test_mcp_result.md#calls-1-5-discovery) |
| 2–4 | `describe_toolset(UMGToolSet / AssetTools / ObjectTools)` | Learn creation, existence, save, and property APIs. | Complete toolset schemas, not asset data. | Required signatures and the UMG property inspection workflow. The combined displayed output was truncated. [Details](WBP_Test_mcp_result.md#calls-1-5-discovery) |
| 5 | `describe_toolset(UMGToolSet)` | Recover the relevant creation schemas after truncation. | Complete schema, filtered locally. | `CreateWidgetBlueprint`, `AddWidget`, and widget inspection signatures. [Details](WBP_Test_mcp_result.md#calls-1-5-discovery) |
| 6 | `AssetTools.exists` | Check the destination before creating an asset. | Boolean only. | `false` for `/Game/UI/WBP_Test`. [Details](WBP_Test_mcp_result.md#calls-6-8-asset-creation-and-compile-discovery) |
| 7 | `UMGToolSet.CreateWidgetBlueprint` | Create the requested UMG asset under `/Game/UI`. | Blueprint UObject reference. | `/Game/UI/WBP_Test.WBP_Test`. [Details](WBP_Test_mcp_result.md#calls-6-8-asset-creation-and-compile-discovery) |
| 8 | `describe_toolset(BlueprintTools)` | Discover how to compile the new asset. | Complete schema, filtered locally. | `compile_blueprint(blueprint, warnings_as_errors)`. [Details](WBP_Test_mcp_result.md#calls-6-8-asset-creation-and-compile-discovery) |
| 9–14 | `UMGToolSet.AddWidget` | Build the hierarchy using returned parent references. | One widget's reference, parent, slot, class, and flags per call. | Canvas, row, Confirm, ConfirmText, Cancel, CancelText, in that order. [Details](WBP_Test_mcp_result.md#calls-9-14-widget-creation) |
| 15–19 | `ObjectTools.list_properties` | Discover exact names and types before changing properties. | Property schemas, not current values. | Canvas slot layout, HorizontalBox slot sizing/padding, and TextBlock text schemas. [Details](WBP_Test_mcp_result.md#calls-15-19-property-discovery) |
| 20–24 | `ObjectTools.get_properties` | Read the properties selected for modification. | Only the requested current values. | Default top-left layout, automatic button sizing, zero padding, and `Text Block` labels. [Details](WBP_Test_mcp_result.md#calls-20-24-original-property-values) |
| 25–29 | `ObjectTools.set_properties` | Apply center anchoring, equal sizing, spacing, and labels. | Boolean per write, not a post-write snapshot. | Five `true` results. [Arguments and results](WBP_Test_mcp_result.md#calls-25-29-property-writes) |
| 30 | `BlueprintTools.compile_blueprint` | Check the completed Blueprint with warnings treated as errors. | Void result. | `returnValue: null`, without an error response. [Details](WBP_Test_mcp_result.md#calls-30-34-compilation-save-and-verification) |
| 31 | `AssetTools.save_assets` | Persist only the new asset. | Save success flag. | `true`. [Details](WBP_Test_mcp_result.md#calls-30-34-compilation-save-and-verification) |
| 32 | `UMGToolSet.GetWidgets` | Verify hierarchy and variable exposure after compilation/save. | Blueprint summary and depth-first widget inventory. | Six widgets, no inherited widgets or named slots; both buttons are variables. [Details](WBP_Test_mcp_result.md#calls-30-34-compilation-save-and-verification) |
| 33–34 | `ObjectTools.get_properties` | Verify the actual text labels. | Text property only. | `Confirm` and `Cancel`. [Details](WBP_Test_mcp_result.md#calls-30-34-compilation-save-and-verification) |

### Interface choices and possible call optimizations

The dedicated UMG tools created the widget tree directly, while ObjectTools
handled reflected widget and slot properties. This avoided editor UI automation
and did not require custom Python or project source changes. Returned references
were reused instead of guessing the names of generated slot objects.

The UMG toolset requires discovering property names before reading and writing
them. The run followed `list_properties -> get_properties -> set_properties`
for each of the five objects whose properties were changed. It did not inspect
every untouched widget or slot.

The initial UMG schema was requested twice because the displayed output was
truncated. Caching that schema would remove one call. With all four toolset
schemas already available and discovery unnecessary, calls 1–5 and 8 could be
omitted, leaving 28 calls with the same asset operations and verification.
Filtering large responses locally reduces displayed output, but does not reduce
the full schema returned by the MCP server.
