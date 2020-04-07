---
title: 모델 변환 중 재질 재정의
description: 변환 시 재질 재사용 워크플로우 설명
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681481"
---
# <a name="override-materials-during-model-conversion"></a>모델 변환 중 재질 재정의

변환 하는 동안 소스 모델의 재료 설정렌더에서 사용 하는 [PBR 재질을](../../overview/features/pbr-materials.md) 정의 하는 데 사용 됩니다.
경우에 따라 [기본 변환이](../../reference/material-mapping.md) 원하는 결과를 제공하지 않으므로 변경해야 합니다.
모델이 Azure 원격 렌더링에서 사용하도록 변환되는 경우 재질 재정의 파일을 제공하여 재료 변환이 재료별로 수행되는 방식을 사용자 지정할 수 있습니다.
[모델 변환 구성](configure-model-conversion.md) 섹션에는 재질 재정의 파일 이름을 선언하는 지침이 있습니다.

## <a name="the-override-file-used-during-conversion"></a>변환 중에 사용된 재정의 파일

간단한 예로 상자 모델에 "기본값"이라는 단일 재질이 있다고 가정해 보겠습니다. 알베도 색상은 ARR에서 사용하기 위해 조정되어야 합니다.
이 경우 `box_materials_override.json` 다음과 같이 파일을 만들 수 있습니다.

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

파일이 `box_materials_override.json` 입력 컨테이너에 배치되고 `ConversionSettings.json` a가 `box.fbx`옆에 추가되어 재정의 파일을 찾을 위치를 변환에 알려줍니다(모델 [변환 구성](configure-model-conversion.md)참조).

```json
{
    "material-override" : "box_materials_override.json"
}
```

모델을 변환하면 새 설정이 적용됩니다.

### <a name="color-materials"></a>색상 재료

[색상 재질](../../overview/features/color-materials.md) 모델은 조명과 무관한 끊임없이 그늘진 표면을 설명합니다.
예를 들어 사진 측정 알고리즘에 의해 만들어진 자산에 유용합니다.
재질 재정의 파일에서 재질은 을 설정하여 `unlit` 색상 `true`재질로 선언할 수 있습니다.

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>특정 텍스처 맵 무시

경우에 따라 변환 프로세스에서 특정 텍스처 맵을 무시하려고 할 수 있습니다. 렌더러에서 올바르게 이해하지 못하는 특수 맵을 생성하는 도구에서 모델을 생성한 경우일 수 있습니다. 예를 들어 불투명도 이외의 것을 정의하는 데 사용되는 "불투명도맵"이나 "NormalMap"이 "범프맵"으로 저장되는 모델입니다. (후자의 경우 변환기가 "범맵"을 "NormalMap"으로 사용하도록 하는 "NormalMap"을 무시하려고 합니다.)

원리는 간단합니다. 라는 속성을 추가하고 `ignoreTextureMaps` 무시할 텍스처 맵을 추가하기만 하면 됩니다.

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

무시할 수 있는 텍스처 맵의 전체 목록은 아래 JSON 스키마를 참조하십시오.

## <a name="json-schema"></a>JSON 스키마

재질 파일에 대한 전체 JSON 스키마가 여기에 제공됩니다. 및, 사용 가능한 특성은 [색상 재료](../../overview/features/color-materials.md) 및 [PBR 재질](../../overview/features/pbr-materials.md) 모델의 단면에 설명된 속성의 하위 집합입니다. `unlit` `ignoreTextureMaps`

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>다음 단계

* [색상 재료](../../overview/features/color-materials.md)
* [PBR 재료](../../overview/features/pbr-materials.md)