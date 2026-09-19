# WBP_Test MCP Results

These results support [WidgetBlueprintOperations.md](WidgetBlueprintOperations.md).
They document the original creation run; no MCP calls were replayed to produce
this report. Compact responses retain their decoded JSON values. Large schemas
are excerpts/projections. Outer MCP `content` envelopes are omitted.

For readability, references below use these aliases. An alias in a table means
the corresponding `{"refPath":"..."}` object, not a literal MCP argument.

| Alias | refPath |
|---|---|
| BP | `/Game/UI/WBP_Test.WBP_Test` |
| Root | `/Game/UI/WBP_Test.WBP_Test:WidgetTree.RootCanvas` |
| Row | `/Game/UI/WBP_Test.WBP_Test:WidgetTree.ButtonRow` |
| Confirm | `/Game/UI/WBP_Test.WBP_Test:WidgetTree.Confirm` |
| ConfirmText | `/Game/UI/WBP_Test.WBP_Test:WidgetTree.ConfirmText` |
| Cancel | `/Game/UI/WBP_Test.WBP_Test:WidgetTree.Cancel` |
| CancelText | `/Game/UI/WBP_Test.WBP_Test:WidgetTree.CancelText` |
| RowSlot | `/Game/UI/WBP_Test.WBP_Test:WidgetTree.RootCanvas.CanvasPanelSlot_0` |
| ConfirmSlot | `/Game/UI/WBP_Test.WBP_Test:WidgetTree.ButtonRow.HorizontalBoxSlot_0` |
| CancelSlot | `/Game/UI/WBP_Test.WBP_Test:WidgetTree.ButtonRow.HorizontalBoxSlot_1` |
| ConfirmTextSlot | `/Game/UI/WBP_Test.WBP_Test:WidgetTree.Confirm.ButtonSlot_0` |
| CancelTextSlot | `/Game/UI/WBP_Test.WBP_Test:WidgetTree.Cancel.ButtonSlot_0` |

## Calls 1-5: Discovery

Call 1, `list_toolsets({})`, returned a text listing of available toolsets.
Relevant entries were `UMGToolSet.UMGToolSet`, `AssetTools`, `ObjectTools`, and
`BlueprintTools` (full names are in the main report).

The UMG description included this workflow:

```text
1. Call ObjectTools.list_properties(widget) to discover exact property names.
2. Call ObjectTools.get_properties(widget, [...]) with those exact names.
3. Call ObjectTools.set_properties(widget, {...}) with those exact names.
```

Calls 2, 3, and 4 requested the full UMG, asset, and object schemas respectively.
The combined displayed response was truncated. Call 5 requested the full UMG
schema again and selected relevant entries locally. The following is a signature
projection, not a verbatim schema dump:

```text
CreateWidgetBlueprint(folderPath, assetName, parentClass) -> Blueprint reference
AddWidget(widgetBlueprint, widgetClass, widgetDisplayName,
          parentWidget=null, childIndex=-1) -> UMGWidgetInfo
GetWidgets(widgetBlueprint) -> info and widgets
exists(path) -> bool
save_assets(asset_paths) -> bool
list_properties(instance) -> JSON-formatted schema string
get_properties(instance, properties) -> JSON-formatted values string
set_properties(instance, values) -> bool
```

`set_properties.values` is a JSON-formatted **string**. Object references are
objects containing `refPath`. The repeated UMG schema projection also included
`GetWidgetTreeDepth`, `GetWidgetDescription`, and `GetWidgetClassInfo`; those
tools were not called in this experiment.

## Calls 6-8: Asset creation and compile discovery

Call 6: `AssetTools.exists`

```json
{"path":"/Game/UI/WBP_Test"}
```

Returned:

```json
{"returnValue":false}
```

Call 7: `UMGToolSet.CreateWidgetBlueprint`

```json
{
  "folderPath": "/Game/UI",
  "assetName": "WBP_Test",
  "parentClass": {"refPath": "/Script/UMG.UserWidget"}
}
```

Returned:

```json
{"returnValue":{"refPath":"/Game/UI/WBP_Test.WBP_Test"}}
```

Call 8 requested `describe_toolset(BlueprintTools)` and locally filtered for
compile/open tools. The displayed projection contained `compile_blueprint`:
required `blueprint` reference, optional `warnings_as_errors` boolean defaulting
to `false`. Its description says compilation failures raise errors, and setting
that flag also treats warnings as errors.

## Calls 9-14: Widget creation

All six calls used `AddWidget` with `widgetBlueprint: BP`. `childIndex` was
omitted, so each child was appended. `parentWidget` was omitted for call 9,
making the CanvasPanel the root of the empty tree.

| Call | widgetClass.refPath | widgetDisplayName | parentWidget |
|---:|---|---|---|
| 9 | `/Script/UMG.CanvasPanel` | `RootCanvas` | omitted |
| 10 | `/Script/UMG.HorizontalBox` | `ButtonRow` | Root |
| 11 | `/Script/UMG.Button` | `Confirm` | Row |
| 12 | `/Script/UMG.TextBlock` | `ConfirmText` | Confirm |
| 13 | `/Script/UMG.Button` | `Cancel` | Row |
| 14 | `/Script/UMG.TextBlock` | `CancelText` | Cancel |

Each response was `{"returnValue": <UMGWidgetInfo>}`. This table projects the
returned fields using the reference aliases above:

| Call | widget | parent | slot | widgetName | bIsVariable |
|---:|---|---|---|---|---|
| 9 | Root | `"None"` | `"None"` | RootCanvas | false |
| 10 | Row | Root | RowSlot | ButtonRow | false |
| 11 | Confirm | Row | ConfirmSlot | Confirm | true |
| 12 | ConfirmText | Confirm | ConfirmTextSlot | ConfirmText | false |
| 13 | Cancel | Row | CancelSlot | Cancel | true |
| 14 | CancelText | Cancel | CancelTextSlot | CancelText | false |

For all six records, `namedSlotHost` was the string `"None"`, `bInherited` was
`false`, and `uIComponents` was `[]`. Each `widgetClassPath` matched the requested
class reference. For example, call 11 returned:

```json
{
  "returnValue": {
    "widget": {"refPath":"/Game/UI/WBP_Test.WBP_Test:WidgetTree.Confirm"},
    "parent": {"refPath":"/Game/UI/WBP_Test.WBP_Test:WidgetTree.ButtonRow"},
    "slot": {"refPath":"/Game/UI/WBP_Test.WBP_Test:WidgetTree.ButtonRow.HorizontalBoxSlot_0"},
    "namedSlotHost": "None",
    "widgetClassPath": {"refPath":"/Script/UMG.Button"},
    "widgetName": "Confirm",
    "bIsVariable": true,
    "bInherited": false,
    "uIComponents": []
  }
}
```

## Calls 15-19: Property discovery

Five `ObjectTools.list_properties` requests inspected RowSlot, ConfirmSlot,
CancelSlot, ConfirmText, and CancelText, respectively. Each returned a
`returnValue` containing a JSON-formatted schema string. The large combined
display was truncated; the relevant schema projection is:

| Call | instance | Relevant returned properties/types |
|---:|---|---|
| 15 | RowSlot | `layoutData`: offsets (left/top/right/bottom numbers), anchors (minimum/maximum x/y), alignment (x/y); `bAutoSize`: boolean; `zOrder`: integer. |
| 16 | ConfirmSlot | `size`: value and sizeRule (`Automatic` or `Fill`); `padding`: left/top/right/bottom; horizontal and vertical alignment enums. |
| 17 | CancelSlot | Same HorizontalBoxSlot schema as call 16. |
| 18 | ConfirmText | `text`: string, plus font, color, wrapping, layout, and inherited widget properties. |
| 19 | CancelText | Same TextBlock schema as call 18. |

## Calls 20-24: Original property values

Each `get_properties` response was `{"returnValue":"<JSON string>"}`.
The blocks below decode that inner string for readability.

Call 20: instance RowSlot, properties `["layoutData","bAutoSize"]`:

```json
{
  "layoutData": {
    "offsets": {"left":0,"top":0,"right":100,"bottom":30},
    "anchors": {"minimum":{"x":0,"y":0},"maximum":{"x":0,"y":0}},
    "alignment": {"x":0,"y":0}
  },
  "bAutoSize": false
}
```

Calls 21 and 22: instances ConfirmSlot and CancelSlot, respectively, properties
`["size","padding"]`. Each returned:

```json
{
  "size": {"value":1,"sizeRule":"Automatic"},
  "padding": {"left":0,"top":0,"right":0,"bottom":0}
}
```

Calls 23 and 24: instances ConfirmText and CancelText, properties `["text"]`.
Each returned:

```json
{"text":"Text Block"}
```

## Calls 25-29: Property writes

Each call used `ObjectTools.set_properties(instance, values)`. The JSON objects
below were serialized into the `values` string argument, not passed as nested
objects. Each of the five calls returned exactly:

```json
{"returnValue":true}
```

Call 25: instance RowSlot:

```json
{
  "layoutData": {
    "offsets": {"left":0,"top":0,"right":336,"bottom":56},
    "anchors": {"minimum":{"x":0.5,"y":0.5},"maximum":{"x":0.5,"y":0.5}},
    "alignment": {"x":0.5,"y":0.5}
  },
  "bAutoSize": false
}
```

Call 26: instance ConfirmSlot:

```json
{
  "size": {"value":1,"sizeRule":"Fill"},
  "padding": {"left":0,"top":0,"right":8,"bottom":0}
}
```

Call 27: instance CancelSlot:

```json
{
  "size": {"value":1,"sizeRule":"Fill"},
  "padding": {"left":8,"top":0,"right":0,"bottom":0}
}
```

Call 28: instance ConfirmText:

```json
{"text":"Confirm"}
```

Call 29: instance CancelText:

```json
{"text":"Cancel"}
```

## Calls 30-34: Compilation, save, and verification

Call 30: `BlueprintTools.compile_blueprint`

```json
{
  "blueprint": {"refPath":"/Game/UI/WBP_Test.WBP_Test"},
  "warnings_as_errors": true
}
```

Returned without an error response:

```json
{"returnValue":null}
```

Call 31: `AssetTools.save_assets`

```json
{"asset_paths":["/Game/UI/WBP_Test"]}
```

Returned:

```json
{"returnValue":true}
```

Call 32: `UMGToolSet.GetWidgets`, arguments `widgetBlueprint: BP`. The returned
`returnValue.info` was:

```json
{
  "parentClass": {"refPath":"/Script/UMG.UserWidget"},
  "rootWidgetClass": {"refPath":"/Script/UMG.CanvasPanel"},
  "widgetCount": 6,
  "inheritedWidgetCount": 0,
  "namedSlotCount": 0
}
```

`returnValue.widgets` contained the same six complete widget-info records
documented under calls 9–14, in that order, with matching references, parents,
slots, classes, names, and flags. Both Button records had `bIsVariable: true`.
This response did not include label text or slot layout property values.

Call 33: `ObjectTools.get_properties`, instance ConfirmText, properties
`["text"]`. Raw decoded MCP payload:

```json
{"returnValue":"{\"text\":\"Confirm\"}"}
```

Call 34: the same interface and properties for CancelText:

```json
{"returnValue":"{\"text\":\"Cancel\"}"}
```
