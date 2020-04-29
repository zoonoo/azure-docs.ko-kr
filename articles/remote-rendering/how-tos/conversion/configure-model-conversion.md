---
title: 모델 변환 구성
description: 모든 모델 변환 매개 변수에 대 한 설명
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681572"
---
# <a name="configure-the-model-conversion"></a>모델 변환 구성

이 장에서는 모델 변환에 대 한 옵션을 설명 합니다.

## <a name="settings-file"></a>설정 파일

이라는 `ConversionSettings.json` 파일이 입력 모델 옆의 입력 컨테이너에 있으면 모델 변환 프로세스에 대 한 추가 구성을 제공 하는 데 사용 됩니다.

파일의 내용은 다음 json 스키마를 충족 해야 합니다.

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "material-override" : { "type" : "string", "default" : "" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        }
    },
    "additionalProperties" : false
}
```

예제 `ConversionSettings.json` 파일은 다음과 같을 수 있습니다.

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Geometry 매개 변수

* `scaling`-이 매개 변수는 모델의 크기를 균일 하 게 조정 합니다. 크기 조정을 사용 하 여 모델을 확장 하거나 축소할 수 있습니다. 예를 들어 맨 위에 테이블을 구성 하는 모델을 표시할 수 있습니다. 렌더링 엔진은 길이를 미터 단위로 지정 하므로이 매개 변수의 또 다른 중요 한 용도는 모델이 다른 단위로 정의 될 때 발생 합니다. 예를 들어 모델이 센티미터 단위로 정의 된 경우 눈금 0.01을 적용 하면 모델을 올바른 크기로 렌더링 해야 합니다.
일부 원본 데이터 형식 (예: fbx)은 단위 크기 조정 힌트를 제공 합니다 .이 경우 변환에서 모델을 미터 단위로 암시적으로 확장 합니다. 원본 형식에서 제공 하는 암시적 확장은 크기 조정 매개 변수 위에 적용 됩니다.
최종 배율 인수는 장면 그래프 노드의 로컬 변환과 기 하 도형 꼭 짓 점에 적용 됩니다. 루트 엔터티의 변환에 대 한 크기 조정은 수정 되지 않고 유지 됩니다.

* `recenterToOrigin`-해당 경계 상자가 원점 가운데에 오도록 모델을 변환 해야 함을 설명 합니다.
원본 모델이 원본에서 멀리 떨어져 있는 경우에는 부동 소수점 정밀도 문제로 인해 렌더링 아티팩트가 발생할 수 있으므로 가운데 맞춤은 중요 합니다.

* `opaqueMaterialDefaultSidedness`-렌더링 엔진은 불투명 자료가 양면 이라고 가정 합니다.
의도 된 동작이 아닌 경우이 매개 변수를 "SingleSided"로 설정 해야 합니다. 자세한 내용은 [단일 면 렌더링](../../overview/features/single-sided-rendering.md)을 참조 하세요.

### <a name="material-overrides"></a>재질 재정의

* `material-override`-이 매개 변수를 사용 하면 변환 중에 재질의 처리를 [사용자 지정할](override-materials.md)수 있습니다.

### <a name="color-space-parameters"></a>색 공간 매개 변수

렌더링 엔진은 색 값이 선형 공간에 있을 것으로 예상 합니다.
감마 공간을 사용 하 여 모델을 정의 하는 경우 이러한 옵션을 true로 설정 해야 합니다.

* `gammaToLinearMaterial`-재질 색을 감마 공간에서 선형 공간으로 변환 합니다.
* `gammaToLinearVertex`-꼭지점 색을 감마 공간에서 선형 공간으로 변환 합니다.

> [!NOTE]
> FBX 파일의 경우 이러한 설정은 기본적으로 `true` 로 설정 됩니다. 다른 모든 파일 형식의 경우 기본값은 `false`입니다.

### <a name="scene-parameters"></a>장면 매개 변수

* `sceneGraphMode`-소스 파일의 장면 그래프를 변환 하는 방법을 정의 합니다.
  * `dynamic`(기본값): 파일의 모든 개체는 API에서 [엔터티로](../../concepts/entities.md) 노출 되며 독립적으로 변환 될 수 있습니다. 런타임에 노드 계층 구조는 소스 파일의 구조와 동일 합니다.
  * `static`: 모든 개체가 API에 노출 되지만 독립적으로 변환할 수 없습니다.
  * `none`: 장면 그래프가 하나의 개체로 축소 됩니다.

각 모드에는 서로 다른 런타임 성능이 있습니다. 모드 `dynamic` 에서 성능 비용은 아무 부분도 이동 하지 않은 경우에도 그래프에서 [엔터티](../../concepts/entities.md) 수로 선형으로 확장 됩니다. 응용 프로그램에 대해 파트를 개별적으로 이동 해야 하는 경우에만 사용 해야 합니다 (예: ' 분해 뷰 ' 애니메이션).

모드 `static` 는 전체 장면 그래프를 내보냅니다. 하지만이 그래프 내의 파트에는 루트 부분을 기준으로 하는 상수 변환이 있습니다. 그러나 개체의 루트 노드는 상당한 성능 비용 없이 이동, 회전 또는 확장할 수 있습니다. 또한 [공간 쿼리](../../overview/features/spatial-queries.md) 는 개별 부분을 반환 하 고 각 부분은 [상태 재정의](../../overview/features/override-hierarchical-state.md)를 통해 수정할 수 있습니다. 이 모드에서 개체 당 런타임 오버 헤드는 무시 됩니다. 개체 별로 검사를 수행 해야 하지만 개체별 변환이 변경 되지 않는 경우에는 많은 장면을 사용할 때 유용 합니다.

이 `none` 모드에는 런타임 오버 헤드가 최소화 되며 로드 시간도 약간 더 좋습니다. 이 모드에서는 단일 개체의 검사 또는 변환이 불가능 합니다. 사용 사례는 첫 번째 위치의 의미 있는 장면 그래프가 없는 photogrammetry 모델입니다.

> [!TIP]
> 많은 응용 프로그램에서 여러 모델을 로드 합니다. 각 모델에 대 한 변환 매개 변수를 사용 하는 방법에 따라 최적화 해야 합니다. 예를 들어 사용자의 자동차 모델을 표시 하 여 자세히 살펴보고 자세히 조사 하려면 모드를 사용 하 여 `dynamic` 변환 해야 합니다. 그러나 차량 표시 환경에 자동차를 추가 하려는 경우에는이 모델을 또는로 `sceneGraphMode` `static` 설정 하 여 변환할 수 있습니다. `none`

### <a name="physics-parameters"></a>물리 매개 변수

* `generateCollisionMesh`-모델의 [공간 쿼리](../../overview/features/spatial-queries.md) 를 지원 해야 하는 경우이 옵션을 사용 하도록 설정 해야 합니다. 최악의 경우 충돌 메시를 만들 때 변환 시간을 두 배로 만들 수 있습니다. 충돌 메시를 사용 하는 모델을 로드 하는 데 `dynamic` 시간이 오래 걸리고 장면 그래프를 사용할 때 런타임 성능 오버 헤드가 증가 합니다. 전체 성능을 최적화 하려면 공간 쿼리가 필요 하지 않은 모든 모델에서이 옵션을 사용 하지 않도록 설정 해야 합니다.

### <a name="unlit-materials"></a>Unlit 자료

* `unlitMaterials`-기본적으로 변환 시에는 [.pbr 자료](../../overview/features/pbr-materials.md)를 만드는 것이 좋습니다. 이 옵션은 모든 자료를 [색 자료](../../overview/features/color-materials.md) 로 처리 하도록 변환기에 지시 합니다. Photogrammetry를 통해 만들어진 모델과 같이 이미 조명을 통합 하는 데이터가 있는 경우이 옵션을 사용 하면 [각 자료](override-materials.md) 를 개별적으로 재정의할 필요 없이 모든 자료에 대해 올바른 변환을 빠르게 적용할 수 있습니다.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>퐁 재질 모델을 사용 하 여 이전 FBX 형식에서 변환

* `fbxAssumeMetallic`-이전 버전의 FBX 형식은 퐁 재질 모델을 사용 하 여 해당 자료를 정의 합니다. 변환 프로세스에서는 이러한 자료를 렌더러의 [.pbr 모델](../../overview/features/pbr-materials.md)에 매핑하는 방법을 추론 해야 합니다. 일반적으로이는 잘 작동 하지만 재질에 질감, 높은 반사 값 및 회색이 아닌 albedo 색이 없으면 모호성이 발생할 수 있습니다. 이 경우 변환은 높은 반사 값의 우선 순위를 정의 하 고, albedo 색이 dissolves 않는 금속성 재질을 정의 하 고, albedo 색의 우선 순위를 정의 하 여 빛나는 색상형 플라스틱 같은 항목을 정의 하는 것을 선택 해야 합니다. 기본적으로 변환 프로세스에서는 모호성이 적용 되는 경우에도 매우 많은 반사 값이 금속성 자료를 의미 한다고 가정 합니다. 이 매개 변수를로 `false` 설정 하 여 반대로 전환할 수 있습니다.

### <a name="coordinate-system-overriding"></a>좌표계 재정의

* `axis`-좌표계 단위 벡터를 재정의 합니다. 기본값은 `["+x", "+y", "+z"]`입니다. 이론적으로 FBX 형식에는 이러한 벡터가 정의 되 고 변환에서이 정보를 사용 하 여 장면을 변환 하는 헤더가 있습니다. 인 글 Tf 형식은 고정 좌표계도 정의 합니다. 실제로 일부 자산은 헤더에 잘못 된 정보가 있거나 다른 좌표계 규칙을 사용 하 여 저장 되었습니다. 이 옵션을 사용 하면 좌표계를 재정의 하 여 보정할 수 있습니다. 예를 들어 `"axis" : ["+x", "+z", "-y"]` 는 Z 축과 y 축을 교환 하 고 y 축 방향을 반전 하 여 좌표계를 유지 합니다.

### <a name="vertex-format"></a>꼭 짓 점 형식

메모리 절약을 위해 전체 자릿수를 지정 하기 위해 메시의 꼭 짓 점 형식을 조정할 수 있습니다. 메모리 공간이 적으면 더 큰 모델을 로드 하거나 더 나은 성능을 달성할 수 있습니다. 그러나 데이터에 따라 잘못 된 형식이 렌더링 품질에 크게 영향을 줄 수 있습니다.

> [!CAUTION]
> 꼭 짓 점 형식을 변경 하는 것은 모델이 더 이상 메모리에 맞지 않거나 최상의 성능을 위해 최적화할 때 최후의 수단 이어야 합니다. 변경 내용은 명확 하 고 미묘한 렌더링 아티팩트를 쉽게 도입할 수 있습니다. 확인할 항목을 모르는 경우에는 기본값을 변경 하면 안 됩니다.

다음과 같이 조정할 수 있습니다.

* 특정 데이터 스트림은 명시적으로 포함 하거나 제외할 수 있습니다.
* 메모리 공간을 줄이기 위해 데이터 스트림의 정확도를 줄일 수 있습니다.

`.json` 파일의 다음 `vertex` 섹션은 선택 사항입니다. 명시적으로 지정 되지 않은 각 부분에 대해 변환 서비스는 기본 설정으로 대체 됩니다.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

구성 요소를에 `NONE`강제 적용 하 여 출력 메시에 해당 스트림이 없다는 것을 보장할 수 있습니다.

#### <a name="component-formats-per-vertex-stream"></a>꼭 짓 점 스트림 당 구성 요소 형식

이러한 형식은 각 구성 요소에 대해 허용 됩니다.

| 꼭 짓 점 구성 요소 | 지원 되는 형식 (굵게 = 기본값) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, 없음 |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED, **없음**|
|노멀| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, 없음 |
|탄젠트| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, 없음 |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, 없음 |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, 없음 |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, 없음 |

#### <a name="supported-component-formats"></a>지원 되는 구성 요소 형식

형식의 메모리 차지는 다음과 같습니다.

| 형식 | Description | 꼭 짓 점 당 바이트 수 |
|:-------|:------------|:---------------|
|32_32_FLOAT|2 구성 요소 전체 부동 소수점 전체 자릿수|8
|16_16_FLOAT|2 구성 요소 1/2 부동 소수점 전체 자릿수|4
|32_32_32_FLOAT|3 구성 요소 전체 부동 소수점 전체 자릿수|12
|16_16_16_16_FLOAT|4 구성 요소 1/2 부동 소수점 전체 자릿수|8
|8_8_8_8_UNSIGNED_NORMALIZED|4 구성 요소 바이트 ( `[0; 1]` 범위로 정규화)|4
|8_8_8_8_SIGNED_NORMALIZED|4 구성 요소 바이트 ( `[-1; 1]` 범위로 정규화)|4

#### <a name="best-practices-for-component-format-changes"></a>구성 요소 형식 변경에 대 한 모범 사례

* `position`: 정확도가 줄어들면 충분 합니다. **16_16_16_16_FLOAT** 은 작은 모델의 경우에도 눈에 띄는 양자화 아티팩트를 도입 합니다.
* `normal`, `tangent`, `binormal`: 일반적으로 이러한 값은 함께 변경 됩니다. 정상적인 양자화에서 발생 하는 눈에 띄는 조명 아티팩트가 없으면 정확도를 높일 이유가 없습니다. 그러나 경우에 따라 이러한 구성 요소를 **NONE**으로 설정할 수 있습니다.
  * `normal`, `tangent`및 `binormal` 은 모델에서 하나 이상의 자료가 켜질 때만 필요 합니다. ARR에서 언제 든 지 모델에 대해 [.pbr 자료](../../overview/features/pbr-materials.md) 를 사용 하는 경우가 여기에 해당 합니다.
  * `tangent`및 `binormal` 는 lit 재질에서 일반 지도 텍스처를 사용 하는 경우에만 필요 합니다.
* `texcoord0`, `texcoord1` : 질감 좌표는 값이 `[0; 1]` 범위 내에 유지 되 고 주소 지정 된 질감이 최대 크기인 2048 x 2048 픽셀 일 때 감소 된 정확도 (**16_16_FLOAT**)를 사용할 수 있습니다. 이러한 제한을 초과 하면 질감 매핑의 품질이 저하 됩니다.

#### <a name="example"></a>예제

조명이 질감으로 구운 하는 photogrammetry 모델이 있다고 가정 합니다. 모델을 렌더링 하는 데 필요한 모든 것이 꼭 짓 점 위치 및 질감 좌표입니다.

기본적으로 변환기는 언제 든 지 모델에서 .pbr 자료를 사용 하 여, `normal` `tangent`및 `binormal` 데이터를 생성 하는 것으로 가정 합니다. 따라서 꼭 짓 점 당 메모리 사용량은 `position` (12 바이트) + `texcoord0` (8 바이트 `normal` ) + (4 바이트) `tangent` `binormal` + (4 바이트) + (4 바이트) = 32 바이트입니다. 이 유형의 모델을 더 많이 사용 하면 수백만 개의 꼭지점이 있을 수 있으며이로 인해 모델에는 여러 기가바이트의 메모리가 사용 될 수 있습니다. 이러한 많은 양의 데이터는 성능에 영향을 주므로 메모리 부족도 발생할 수 있습니다.

모델에 동적 조명이 필요 하지 않으며 `[0; 1]` 모든 텍스처 좌표가 범위 내에 있다는 것을 알고 있으면, `normal` `tangent`, 및 `binormal` 를 `NONE` `texcoord0` 50 개의 전체 자릿수 (`16_16_FLOAT`)로 설정 하 여 꼭 짓 점 당 16 바이트만 생성 하도록 할 수 있습니다. 메시 데이터를 절반으로 잘라내면 더 큰 모델을 로드 하 고 잠재적으로 성능을 향상 시킬 수 있습니다.

## <a name="typical-use-cases"></a>일반적인 사용 사례

지정 된 사용 사례에 대 한 적절 한 가져오기 설정 찾기는 지루한 프로세스가 될 수 있습니다. 반면에 변환 설정은 런타임 성능에 상당한 영향을 미칠 수 있습니다.

특정 최적화를 위한 특정 사용 사례 클래스가 있습니다. 몇 가지 예가 아래에 나와 있습니다.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>사용 사례: 아키텍처 시각화/대량 옥외 지도

* 이러한 유형의 장면은 정적인 경우가 많으므로 이동 가능한 부분이 필요 하지 않습니다. 따라서을 또는 `sceneGraphMode` `static` `none`로 설정 하 여 런타임 성능을 향상 시킬 수 있습니다. 모드 `static` 를 사용 하는 경우 장면 루트 노드를 계속 이동, 회전 및 크기 조정할 수 있습니다. 예를 들어 1:1 배율 (첫 번째 사용자 보기의 경우)과 테이블 맨 위 보기 간에 동적으로 전환할 수 있습니다.

* 부분을 이동 해야 하는 경우에는 일반적으로 해당 파트를 선택할 수 있도록 raycasts 또는 기타 [공간 쿼리](../../overview/features/spatial-queries.md)를 지원 해야 함을 의미 합니다. 반면에 다른 항목을 이동 하지 않는 경우 공간 쿼리에 참여 하지 않아도 되므로 플래그를 `generateCollisionMesh` 해제할 수 있는 가능성이 높습니다. 이 스위치는 변환 시간, 로드 시간 및 런타임 프레임당 업데이트 비용에 상당한 영향을 미칩니다.

* 응용 프로그램에서 [잘라내기 평면](../../overview/features/cut-planes.md)을 사용 하지 않는 경우 `opaqueMaterialDefaultSidedness` 플래그를 해제 해야 합니다. 성능 향상은 일반적으로 20%-30%입니다. 잘린 비행기는 계속 사용할 수 있지만 개체의 내부 부분을 볼 때에는 반대 되는 것이 아니라 카운터를 직관적으로 볼 수 있습니다. 자세한 내용은 [단일 면 렌더링](../../overview/features/single-sided-rendering.md)을 참조 하세요.

### <a name="use-case-photogrammetry-models"></a>사용 사례: Photogrammetry 모델

Photogrammetry 모델을 렌더링할 때는 일반적으로 장면 그래프가 필요 하지 않으므로 `sceneGraphMode` 를로 `none`설정할 수 있습니다. 이러한 모델에는 시작 하기 위한 복잡 한 장면 그래프가 거의 포함 되어 있지 않으므로이 옵션의 영향은 중요 하지 않습니다.

조명이 질감에 이미 구운 때문에 동적 조명이 필요 하지 않습니다. 따라서

* `unlitMaterials` 플래그를로 `true` 설정 하 여 모든 자료를 unlit [색 자료](../../overview/features/color-materials.md)로 설정 합니다.
* 꼭 짓 점 형식에서 불필요 한 데이터를 제거 합니다. 위의 [예제](#example) 를 참조 하십시오.

### <a name="use-case-visualization-of-compact-machines-etc"></a>사용 사례: 컴팩트 컴퓨터의 시각화 등

이러한 사용 사례에서 모델은 작은 볼륨 내에서 매우 높은 정보를 포함 하는 경우가 많습니다. 렌더러는 이러한 경우를 처리 하도록 최적화 되어 있습니다. 그러나 위의 사용 사례에서 언급 된 대부분의 최적화는 여기에 적용 되지 않습니다.

* 개별 부분은 선택 가능 하 고 이동 가능 해야 하므로 `sceneGraphMode` 는로 `dynamic`남겨 두어야 합니다.
* 광선 캐스트는 일반적으로 응용 프로그램의 필수적인 부분 이므로 충돌 망을 생성 해야 합니다.
* 플래그를 사용 하는 경우 `opaqueMaterialDefaultSidedness` 에는 더 나은 평면으로 보입니다.

## <a name="next-steps"></a>다음 단계

* [모델 변환](model-conversion.md)
* [색 재질](../../overview/features/color-materials.md)
* [PBR 재질](../../overview/features/pbr-materials.md)
* [모델 변환 중 재질 재정의](override-materials.md)
