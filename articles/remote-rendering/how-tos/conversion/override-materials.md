---
title: 모델 변환 중 재질 재정의
description: 변환 시 워크플로를 재정의 하는 재질에 대해 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681481"
---
# <a name="override-materials-during-model-conversion"></a>모델 변환 중 재질 재정의

변환 하는 동안 원본 모델의 재질 설정은 렌더러에 사용 되는 [.pbr 재질](../../overview/features/pbr-materials.md) 을 정의 하는 데 사용 됩니다.
[기본 변환이](../../reference/material-mapping.md) 원하는 결과를 제공 하지 않으며 변경 해야 하는 경우도 있습니다.
Azure 원격 렌더링에서 사용할 수 있도록 모델을 변환 하는 경우 자재 재정의 파일을 제공 하 여 재질을 기준으로 수행 되는 재질을 사용자 지정할 수 있습니다.
[모델 변환 구성](configure-model-conversion.md) 섹션에는 재질 재정의 파일 이름을 선언 하는 방법에 대 한 지침이 있습니다.

## <a name="the-override-file-used-during-conversion"></a>변환 하는 동안 사용 된 재정의 파일

간단한 예로, box 모델에 "Default" 라는 단일 재료가 있다고 가정해 보겠습니다. ARR에서 사용 하려면 albedo 색을 조정 해야 합니다.
이 경우 다음과 같이 `box_materials_override.json` 파일을 만들 수 있습니다.

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

이 `box_materials_override.json` 파일은 입력 컨테이너에 배치 되 고 `ConversionSettings.json` `box.fbx`,은 재정의 파일을 찾을 위치를 변환에 지시 하는에 추가 됩니다 ( [모델 변환 구성](configure-model-conversion.md)참조).

```json
{
    "material-override" : "box_materials_override.json"
}
```

모델을 변환 하면 새 설정이 적용 됩니다.

### <a name="color-materials"></a>색 재질

[색 재질](../../overview/features/color-materials.md) 모델은 조명과 독립적인 지속적으로 음영이 있는 표면에 대해 설명 합니다.
이는 예를 들어 Photogrammetry 알고리즘에서 만든 자산에 유용 합니다.
재질 재정의 파일에서을로 `unlit` `true`설정 하 여 재질을 색 재질로 선언할 수 있습니다.

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

경우에 따라 변환 프로세스에서 특정 질감 맵을 무시 하도록 할 수 있습니다. 이는 렌더러에서 의해 올바르게 인식 되지 않는 특수 맵을 생성 하는 도구에 의해 모델이 생성 된 경우에 발생할 수 있습니다. 예를 들어 불투명도 이외의 항목을 정의 하는 데 사용 되는 "OpacityMap" 또는 "NormalMap"이 "BumpMap"로 저장 되는 모델입니다. 후자의 경우 "NormalMap"을 무시 하는 것이 좋습니다 .이 경우 변환기가 "BumpMap"를 "NormalMap"로 사용 합니다.

원칙은 간단 합니다. 이라는 속성을 추가 하 `ignoreTextureMaps` 고 무시할 질감 맵을 추가 하기만 하면 됩니다.

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

무시할 수 있는 질감 맵의 전체 목록은 아래의 JSON 스키마를 참조 하세요.

## <a name="json-schema"></a>JSON 스키마

여기에는 자료 파일에 대 한 전체 JSON 스키마가 제공 됩니다. `unlit` 및 `ignoreTextureMaps`를 제외 하 고 사용할 수 있는 속성은 [색 재질](../../overview/features/color-materials.md) 및 [.pbr 재질](../../overview/features/pbr-materials.md) 모델의 섹션에서 설명 하는 속성의 하위 집합입니다.

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

* [색 재질](../../overview/features/color-materials.md)
* [PBR 재질](../../overview/features/pbr-materials.md)