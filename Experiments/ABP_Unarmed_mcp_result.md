# ABP_Unarmed MCP Results

These are the MCP results used by
[AnimationBlueprintOperations.md](AnimationBlueprintOperations.md).
They document the original experiment, without rerunning mutations. Compact
responses and node responses are shown as decoded JSON from the MCP text
content. Discovery responses and property responses are explicitly marked as
excerpts or projections; omitted fields are not implied to be absent.

## Call 1: list_toolsets

Relevant excerpt from the returned toolset list:

```text
- editor_toolset.toolsets.asset.AssetTools: Provides tools for interacting with assets in the Unreal project and files on disk.
- editor_toolset.toolsets.blueprint.BlueprintTools: Provides tools for working with Blueprints.
- editor_toolset.toolsets.object.ObjectTools: Provides tools for inspecting and modifying the properties of Unreal Objects and Unreal Classes, including those in Blueprints.
```

The response also listed other Unreal toolsets.
## Call 2: describe_toolset BlueprintTools

Returned the BlueprintTools API schema. The displayed output was truncated.
This discovery response contained no asset data. Call 3 requested the schema
again; the relevant interfaces are listed there.
## Call 3: describe_toolset BlueprintTools

Projection of returned tool names used by this experiment:

```json
{
  "tools": [
    "get_graph",
    "find_nodes",
    "get_connected_subgraph",
    "break_pins",
    "connect_pins",
    "compile_blueprint",
    "get_node_infos"
  ]
}
```

Each schema included its description and input schema. Graph and node arguments
use objects with `refPath`. Pin arguments contain `direction`, `index_id`, and
`node`. `compile_blueprint` accepts `warnings_as_errors` (default `false`).
## Call 4: BlueprintTools.get_graph

```json
{
  "returnValue": {
    "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph"
  }
}
```

## Call 5: BlueprintTools.find_nodes

```json
{
  "returnValue": [
    {
      "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Root_0"
    },
    {
      "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_StateMachine_0"
    },
    {
      "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_SaveCachedPose_0"
    },
    {
      "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_StateMachine_1"
    },
    {
      "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Slot_0"
    },
    {
      "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.K2Node_VariableGet_0"
    },
    {
      "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.K2Node_CallFunction_0"
    },
    {
      "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
    }
  ]
}
```

## Call 6: BlueprintTools.get_connected_subgraph

Complete decoded node response before modification:

```json
{
  "returnValue": [
    {
      "output_pins": [
        {
          "value": "",
          "connected_pins": [
            {
              "direction": "EGPD_Input",
              "index_id": 0,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Root_0"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Output",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
            }
          },
          "type_id": "Pose Link Structure",
          "name": "Pose"
        }
      ],
      "input_pins": [
        {
          "value": "(LinkID=-1,SourceLinkID=-1)",
          "connected_pins": [
            {
              "direction": "EGPD_Output",
              "index_id": 0,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Slot_0"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Input",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
            }
          },
          "type_id": "Pose Link Structure",
          "name": "Source"
        },
        {
          "value": "1.000000",
          "connected_pins": [],
          "pin_id": {
            "direction": "EGPD_Input",
            "index_id": 1,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
            }
          },
          "type_id": "Float (single-precision)",
          "name": "Alpha"
        },
        {
          "value": "false",
          "connected_pins": [
            {
              "direction": "EGPD_Output",
              "index_id": 0,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.K2Node_CallFunction_0"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Input",
            "index_id": 2,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
            }
          },
          "type_id": "Boolean",
          "name": "ShouldDoIKTrace"
        }
      ],
      "position": {
        "x": -352,
        "y": 144
      },
      "node": {
        "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
      },
      "type_id": "Misc.|ControlRig"
    },
    {
      "output_pins": [],
      "input_pins": [
        {
          "value": "(LinkID=-1,SourceLinkID=-1)",
          "connected_pins": [
            {
              "direction": "EGPD_Output",
              "index_id": 0,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Input",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Root_0"
            }
          },
          "type_id": "Pose Link Structure",
          "name": "Result"
        }
      ],
      "position": {
        "x": -64,
        "y": 80
      },
      "node": {
        "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Root_0"
      },
      "type_id": "Misc.|OutputPose"
    },
    {
      "output_pins": [
        {
          "value": "false",
          "connected_pins": [
            {
              "direction": "EGPD_Input",
              "index_id": 2,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Output",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.K2Node_CallFunction_0"
            }
          },
          "type_id": "Boolean",
          "name": "ReturnValue"
        }
      ],
      "input_pins": [
        {
          "value": "false",
          "connected_pins": [
            {
              "direction": "EGPD_Output",
              "index_id": 0,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.K2Node_VariableGet_0"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Input",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.K2Node_CallFunction_0"
            }
          },
          "type_id": "Boolean",
          "name": "A"
        }
      ],
      "position": {
        "x": -320,
        "y": 288
      },
      "node": {
        "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.K2Node_CallFunction_0"
      },
      "type_id": "Math|Boolean|NOTBoolean"
    },
    {
      "output_pins": [
        {
          "value": "false",
          "connected_pins": [
            {
              "direction": "EGPD_Input",
              "index_id": 0,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.K2Node_CallFunction_0"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Output",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.K2Node_VariableGet_0"
            }
          },
          "type_id": "Boolean",
          "name": "IsFalling"
        }
      ],
      "input_pins": [],
      "position": {
        "x": -320,
        "y": 336
      },
      "node": {
        "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.K2Node_VariableGet_0"
      },
      "type_id": "|GetIsFalling"
    },
    {
      "output_pins": [
        {
          "value": "",
          "connected_pins": [
            {
              "direction": "EGPD_Input",
              "index_id": 0,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Output",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Slot_0"
            }
          },
          "type_id": "Pose Link Structure",
          "name": "Pose"
        }
      ],
      "input_pins": [
        {
          "value": "(LinkID=-1,SourceLinkID=-1)",
          "connected_pins": [
            {
              "direction": "EGPD_Output",
              "index_id": 0,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_StateMachine_1"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Input",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Slot_0"
            }
          },
          "type_id": "Pose Link Structure",
          "name": "Source"
        }
      ],
      "position": {
        "x": -576,
        "y": 128
      },
      "node": {
        "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Slot_0"
      },
      "type_id": "Animation|Montage|Slot'DefaultSlot'"
    },
    {
      "output_pins": [
        {
          "value": "",
          "connected_pins": [
            {
              "direction": "EGPD_Input",
              "index_id": 0,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Slot_0"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Output",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_StateMachine_1"
            }
          },
          "type_id": "Pose Link Structure",
          "name": "Pose"
        }
      ],
      "input_pins": [],
      "position": {
        "x": -768,
        "y": 128
      },
      "node": {
        "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_StateMachine_1"
      },
      "type_id": "Animation|StateMachines|MainStates"
    }
  ]
}
```

## Call 7: describe_toolset AssetTools

Relevant schema excerpt, with the fully qualified name shortened for readability:

```json
{
  "name": "save_assets",
  "inputSchema": {
    "type": "object",
    "properties": {
      "asset_paths": {
        "type": "array",
        "items": {
          "type": "string"
        }
      }
    },
    "required": [
      "asset_paths"
    ]
  },
  "outputSchema": {
    "type": "object",
    "properties": {
      "returnValue": {
        "type": "boolean"
      }
    },
    "required": [
      "returnValue"
    ]
  }
}
```

## Call 8: describe_toolset ObjectTools

Relevant schema summary:

| Tool | Required arguments | Return type |
|---|---|---|
| `list_properties` | `instance` (object reference) | `returnValue`: string containing property-schema JSON |
| `get_properties` | `instance` (object reference), `properties` (array of strings) | `returnValue`: string containing property-value JSON |

The full response included other ObjectTools interfaces.
## Call 9: ObjectTools.list_properties

Relevant projection of the JSON decoded from the `returnValue` string:

```json
{
  "node": {
    "title": "AnimNode_ControlRig",
    "type": "object",
    "properties": {
      "controlRigAssetReference": {
        "title": "ControlRigAssetStrongReference",
        "description": "The class to use for the rig.",
        "type": "object"
      },
      "alpha": {
        "type": "number",
        "description": "alpha value handler"
      },
      "alphaInputType": {
        "type": "string",
        "title": "EAnimAlphaInputType",
        "enum": [
          "Float",
          "Bool",
          "Curve"
        ]
      },
      "source": {
        "title": "PoseLink",
        "type": "object"
      }
    }
  }
}
```

The full schema also included transfer settings, alpha blend settings, optional
pin settings, update functions, binding, tag, and error message properties.
## Call 10: ObjectTools.get_properties

FootIK-focused projection of the JSON decoded from the `returnValue` string:

```json
{
  "node": {
    "controlRigAssetReference": {
      "blueprintRigClass": {
        "refPath": "/Game/Characters/Mannequins/Rigs/CR_Mannequin_FootIK.CR_Mannequin_FootIK_C"
      },
      "controlRigAsset": "None"
    },
    "alpha": 1,
    "alphaInputType": "Float",
    "bAlphaBoolEnabled": true,
    "lODThreshold": -1,
    "source": {
      "linkId": -1,
      "sourceLinkId": -1
    },
    "bResetInputPoseToInitial": true,
    "bTransferInputPose": true,
    "bTransferInputCurves": true,
    "bTransferPoseInGlobalSpace": false,
    "inputBonesToTransfer": [],
    "outputBonesToTransfer": []
  }
}
```

Other returned fields included default rig reference, alpha scaling and blending,
mappings, event queue, asset user data, property names, and update functions.
## Call 11: BlueprintTools.break_pins

```json
{
  "returnValue": null
}
```

No graph data was returned.
## Call 12: BlueprintTools.connect_pins

```json
{
  "returnValue": null
}
```

No graph data was returned. Call 14 verified the replacement connection.
## Call 13: BlueprintTools.compile_blueprint

```json
{
  "returnValue": null
}
```

Called with `warnings_as_errors: true`; no error response was returned.
## Call 14: BlueprintTools.get_node_infos

Complete decoded response for the three requested nodes after compilation:

```json
{
  "returnValue": [
    {
      "output_pins": [
        {
          "value": "",
          "connected_pins": [
            {
              "direction": "EGPD_Input",
              "index_id": 0,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Root_0"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Output",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Slot_0"
            }
          },
          "type_id": "Pose Link Structure",
          "name": "Pose"
        }
      ],
      "input_pins": [
        {
          "value": "(LinkID=-1,SourceLinkID=-1)",
          "connected_pins": [
            {
              "direction": "EGPD_Output",
              "index_id": 0,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_StateMachine_1"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Input",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Slot_0"
            }
          },
          "type_id": "Pose Link Structure",
          "name": "Source"
        }
      ],
      "position": {
        "x": -576,
        "y": 128
      },
      "node": {
        "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Slot_0"
      },
      "type_id": "Animation|Montage|Slot'DefaultSlot'"
    },
    {
      "output_pins": [],
      "input_pins": [
        {
          "value": "(LinkID=-1,SourceLinkID=-1)",
          "connected_pins": [
            {
              "direction": "EGPD_Output",
              "index_id": 0,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Slot_0"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Input",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Root_0"
            }
          },
          "type_id": "Pose Link Structure",
          "name": "Result"
        }
      ],
      "position": {
        "x": -64,
        "y": 80
      },
      "node": {
        "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_Root_0"
      },
      "type_id": "Misc.|OutputPose"
    },
    {
      "output_pins": [
        {
          "value": "",
          "connected_pins": [],
          "pin_id": {
            "direction": "EGPD_Output",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
            }
          },
          "type_id": "Pose Link Structure",
          "name": "Pose"
        }
      ],
      "input_pins": [
        {
          "value": "(LinkID=-1,SourceLinkID=-1)",
          "connected_pins": [],
          "pin_id": {
            "direction": "EGPD_Input",
            "index_id": 0,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
            }
          },
          "type_id": "Pose Link Structure",
          "name": "Source"
        },
        {
          "value": "1.000000",
          "connected_pins": [],
          "pin_id": {
            "direction": "EGPD_Input",
            "index_id": 1,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
            }
          },
          "type_id": "Float (single-precision)",
          "name": "Alpha"
        },
        {
          "value": "false",
          "connected_pins": [
            {
              "direction": "EGPD_Output",
              "index_id": 0,
              "node": {
                "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.K2Node_CallFunction_0"
              }
            }
          ],
          "pin_id": {
            "direction": "EGPD_Input",
            "index_id": 2,
            "node": {
              "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
            }
          },
          "type_id": "Boolean",
          "name": "ShouldDoIKTrace"
        }
      ],
      "position": {
        "x": -352,
        "y": 144
      },
      "node": {
        "refPath": "/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed.ABP_Unarmed:AnimGraph.AnimGraphNode_ControlRig_0"
      },
      "type_id": "Misc.|ControlRig"
    }
  ]
}
```

## Call 15: AssetTools.save_assets

```json
{
  "returnValue": true
}
```


