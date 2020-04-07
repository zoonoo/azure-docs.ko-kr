---
title: 모델 변환 구성
description: 모든 모델 변환 매개변수에 대한 설명
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681572"
---
# <a name="configure-the-model-conversion"></a>모델 변환 구성

이 장에서는 모델 변환에 대한 옵션을 설명합니다.

## <a name="settings-file"></a>설정 파일

호출된 `ConversionSettings.json` 파일이 입력 모델 옆의 입력 컨테이너에 있는 경우 모델 변환 프로세스에 대한 추가 구성을 제공하는 데 사용됩니다.

파일의 내용은 다음과 같은 json 스키마를 충족해야 합니다.

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

예제 `ConversionSettings.json` 파일은 다음과 같은 것일 수 있습니다.

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>형상 매개변수

* `scaling`- 이 매개변수는 모델을 균일하게 배율조정합니다. 크기 조정을 사용하여 모델을 늘리거나 축소할 수 있습니다(예: 테이블 맨 위에 건물 모델을 표시). 렌더링 엔진은 길이를 미터단위로 지정할 것으로 예상하므로 모델이 다른 단위로 정의될 때 이 매개변수의 또 다른 중요한 용도가 발생합니다. 예를 들어 모델이 센티미터로 정의된 경우 0.01의 축척을 적용하면 모델이 올바른 크기로 렌더링됩니다.
일부 소스 데이터 형식(예: .fbx)은 단위 크기 조정 힌트를 제공하며, 이 경우 변환은 모델을 미터 단위로 암시적으로 확장합니다. 소스 형식에서 제공하는 암시적 크기 조정은 크기 조정 매개 변수 위에 적용됩니다.
최종 배율 인수는 형상 정점과 장면 그래프 노드의 로컬 변환에 적용됩니다. 루트 엔터티의 변환에 대한 크기 조정은 수정되지 않은 상태로 유지됩니다.

* `recenterToOrigin`- 모델이 경계 상자가 원점을 가운데에 배치되도록 변환해야 함을 명시합니다.
소스 모델이 원점에서 멀리 변위된 경우 중심 지정이 중요하며, 이 경우 부동 점 정밀도 문제가 발생하여 렌더링 아티팩트가 발생할 수 있습니다.

* `opaqueMaterialDefaultSidedness`- 렌더링 엔진은 불투명 한 재질이 양면이라고 가정합니다.
의도된 동작이 아닌 경우 이 매개 변수를 "SingleSided"로 설정해야 합니다. 자세한 내용은 [단일 양면 렌더링](../../overview/features/single-sided-rendering.md)을 참조하십시오.

### <a name="material-overrides"></a>재질 재지정

* `material-override`-이 매개 변수는 [변환하는 동안](override-materials.md)재료의 처리를 사용자 정의 할 수 있습니다.

### <a name="color-space-parameters"></a>색상 공간 매개변수

렌더링 엔진은 색상 값이 선형 공간에 있을 것으로 예상합니다.
감마 공간을 사용하여 모델이 정의된 경우 이러한 옵션을 true로 설정해야 합니다.

* `gammaToLinearMaterial`- 감마 공간에서 선형 공간으로 재료 색상을 변환
* `gammaToLinearVertex`- 감마 공간에서 선형 공간으로 정점 색상을 변환

> [!NOTE]
> FBX 파일의 경우 이러한 `true` 설정은 기본적으로 설정됩니다. 다른 모든 파일 형식의 `false`경우 기본값은 입니다.

### <a name="scene-parameters"></a>장면 매개변수

* `sceneGraphMode`- 소스 파일의 장면 그래프가 변환되는 방법을 정의합니다.
  * `dynamic`(기본값): 파일의 모든 개체가 API의 [엔터티로](../../concepts/entities.md) 노출되며 독립적으로 변환할 수 있습니다. 런타임의 노드 계층 구조는 소스 파일의 구조와 동일합니다.
  * `static`: 모든 개체는 API에 노출되지만 독립적으로 변환할 수 없습니다.
  * `none`: 장면 그래프가 하나의 오브젝트로 축소됩니다.

각 모드마다 런타임 성능이 다릅니다. 모드에서는 `dynamic` 부품이 이동되지 않은 경우에도 그래프의 [엔터티](../../concepts/entities.md) 수에 따라 성능 비용이 선형으로 조정됩니다. '폭발 뷰' 애니메이션과 같은 응용 프로그램에 필요한 부품을 개별적으로 이동하는 경우에만 사용해야 합니다.

이 `static` 모드는 전체 장면 그래프를 내보지만 이 그래프 내부의 부품은 루트 파트를 기준으로 일정한 변환을 갖습니다. 그러나 개체의 루트 노드는 상당한 성능 비용 없이 계속 이동, 회전 또는 크기를 조정할 수 있습니다. 또한 [공간 쿼리는](../../overview/features/spatial-queries.md) 개별 부품을 반환하고 각 부품은 [상태 재정의를](../../overview/features/override-hierarchical-state.md)통해 수정할 수 있습니다. 이 모드를 사용하면 개체당 런타임 오버헤드가 무시할 수 있습니다. 개체당 검사가 필요하지만 개체당 변환이 변경되지 않는 대형 장면에 이상적입니다.

이 `none` 모드는 런타임 오버헤드가 가장 적고 로딩 시간이 약간 더 좋습니다. 이 모드에서는 단일 객체를 검사하거나 변환할 수 없습니다. 사용 사례는 예를 들어 처음에 의미 있는 장면 그래프가 없는 사진 측정 모델입니다.

> [!TIP]
> 많은 응용 프로그램에서 여러 모델을 로드합니다. 각 모델의 사용 방법에 따라 변환 매개변수를 최적화해야 합니다. 예를 들어 사용자가 분리하여 자세히 검사할 수 있도록 자동차 모델을 표시하려면 모드로 `dynamic` 변환해야 합니다. 그러나 또한 쇼 룸 환경에 차를 배치하려는 경우 해당 모델을 설정하거나 `sceneGraphMode` `static` 로 `none`변환할 수 있습니다.

### <a name="physics-parameters"></a>물리 매개 변수

* `generateCollisionMesh`- 모델에서 [공간 쿼리에](../../overview/features/spatial-queries.md) 대한 지원이 필요한 경우 이 옵션을 사용하도록 설정해야 합니다. 최악의 경우 충돌 메시를 만들면 변환 시간이 두 배가 될 수 있습니다. 충돌 메쉬가 있는 모델은 로드하는 데 `dynamic` 시간이 오래 걸리며 장면 그래프를 사용할 때런시 성능 오버헤드도 높아집니다. 전반적인 최적의 성능을 위해 공간 쿼리가 필요하지 않은 모든 모델에서 이 옵션을 사용하지 않도록 설정해야 합니다.

### <a name="unlit-materials"></a>조명이 없는 재질

* `unlitMaterials`- 기본적으로 변환은 [PBR 재질을](../../overview/features/pbr-materials.md)만드는 것을 선호합니다. 이 옵션은 변환기가 모든 재질을 [대신 색상 재질로](../../overview/features/color-materials.md) 처리하도록 지시합니다. 사진 측정을 통해 생성된 모델과 같이 조명을 이미 통합한 데이터가 있는 경우 이 옵션을 사용하면 각 재질을 개별적으로 [재정의할](override-materials.md) 필요 없이 모든 재질에 대해 올바른 변환을 신속하게 적용할 수 있습니다.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>퐁 재질 모델을 사용 하 고 이전 FBX 형식에서 변환

* `fbxAssumeMetallic`- FBX 형식의 이전 버전은 퐁 재료 모델을 사용하여 재료를 정의합니다. 변환 프로세스는 이러한 재질이 렌더러의 [PBR 모델에](../../overview/features/pbr-materials.md)매핑되는 방식을 추론해야 합니다. 일반적으로 이 작업은 잘 작동하지만 재질에 텍스처, 높은 반사 값 및 회색이 아닌 알베도 색상이 없는 경우 모호성이 발생할 수 있습니다. 이러한 상황에서 변환은 높은 반사 값의 우선 순위를 지정하거나, 반사율이 높은 금속 소재를 정의하거나, 알베도 색상이 사라지는 것을 정의하거나, 반짝이는 다채로운 플라스틱과 같은 것을 정의하는 알베도 색상의 우선 순위를 지정해야 합니다. 기본적으로 변환 프로세스는 높은 반사 값이 모호성이 적용되는 경우 금속 재질을 암시한다고 가정합니다. 이 매개 변수는 `false` 반대로 전환하도록 설정할 수 있습니다.

### <a name="coordinate-system-overriding"></a>좌표계 재의거

* `axis`- 좌표계 단위 벡터를 재정의합니다. 기본값은 `["+x", "+y", "+z"]`. 이론적으로 FBX 형식에는 이러한 벡터가 정의되고 변환이 해당 정보를 사용하여 장면을 변환하는 헤더가 있습니다. glTF 형식은 고정 좌표계도 정의합니다. 실제로 일부 자산은 헤더에 잘못된 정보가 있거나 다른 좌표계 규칙으로 저장되었습니다. 이 옵션을 사용하면 보정할 좌표계를 재정의할 수 있습니다. 예를 들어 `"axis" : ["+x", "+z", "-y"]` Z축과 Y축을 교환하고 Y축 방향을 반전시켜 좌표계 전달을 유지합니다.

### <a name="vertex-format"></a>정점 형식

메시의 정점 형식을 조정하여 메모리 절감을 위해 정밀도를 교환할 수 있습니다. 메모리 공간이 작을수록 더 큰 모델을 로드하거나 더 나은 성능을 얻을 수 있습니다. 그러나 데이터에 따라 잘못된 형식이 렌더링 품질에 큰 영향을 미칠 수 있습니다.

> [!CAUTION]
> 정점 형식을 변경하는 것은 모델이 더 이상 메모리에 맞지 않거나 최상의 성능을 최적화할 때 최후의 수단이 되어야 합니다. 변경 사항은 명백한 아티팩트와 미묘한 아티팩트를 쉽게 도입할 수 있습니다. 무엇을 찾아야 할지 알지 못하면 기본값을 변경해서는 안 됩니다.

이러한 조정이 가능합니다.

* 특정 데이터 스트림을 명시적으로 포함하거나 제외할 수 있습니다.
* 데이터 스트림의 정확도를 줄여 메모리 공간을 줄일 수 있습니다.

파일의 `vertex` `.json` 다음 섹션은 선택 사항입니다. 명시적으로 지정되지 않은 각 부분에 대해 변환 서비스가 기본 설정으로 돌아갑니다.

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

구성 요소를 `NONE`강제로 적용하면 출력 메시에 각각의 스트림이 없습니다.

#### <a name="component-formats-per-vertex-stream"></a>정점 스트림당 구성 요소 형식

이러한 형식은 각 구성 요소에 대해 허용됩니다.

| 정점 구성 요소 | 지원되는 형식(굵게 = 기본값) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, 없음 |
|색상1| 8_8_8_8_UNSIGNED_NORMALIZED, **없음**|
|노멀| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, 없음 |
|탄젠트| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, 없음 |
|이중 법선| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, 없음 |
|텍스코르드0| **32_32_FLOAT**, 16_16_FLOAT, 없음 |
|텍스코르드1| **32_32_FLOAT**, 16_16_FLOAT, 없음 |

#### <a name="supported-component-formats"></a>지원되는 구성 요소 형식

형식의 메모리 발자국은 다음과 같습니다.

| 형식 | 설명 | 정점당 바이트 |
|:-------|:------------|:---------------|
|32_32_FLOAT|2성분 전체 부동점 정밀도|8
|16_16_FLOAT|2성분 하프 플로팅 포인트 정밀도|4
|32_32_32_FLOAT|3성분 풀 플로팅 포인트 정밀도|12
|16_16_16_16_FLOAT|4 성분 하프 플로팅 포인트 정밀도|8
|8_8_8_8_UNSIGNED_NORMALIZED|범위로 `[0; 1]` 정규화된 4성분 바이트|4
|8_8_8_8_SIGNED_NORMALIZED|범위로 `[-1; 1]` 정규화된 4성분 바이트|4

#### <a name="best-practices-for-component-format-changes"></a>구성 요소 형식 변경에 대한 모범 사례

* `position`: 정확도가 떨어지는 것만으로도 충분하지 않습니다. **16_16_16_16_FLOAT** 작은 모델에서도 눈에 띄는 양자화 아티팩트를 소개합니다.
* `normal``binormal`: `tangent`일반적으로 이러한 값은 함께 변경됩니다. 정상적인 양자화로 인한 눈에 띄는 조명 아티팩트가 없는 한 정확도를 높일 이유가 없습니다. 그러나 경우에 따라 이러한 구성 요소를 **NONE으로**설정할 수 있습니다.
  * `normal``binormal` 과는 `tangent`달리 모델에서 하나 이상의 재질이 켜져 있어야 하는 경우에만 필요합니다. ARR에서는 [PBR 재질이](../../overview/features/pbr-materials.md) 언제든지 모델에 사용되는 경우입니다.
  * `tangent``binormal` 라이트가 있는 재질이 법선 맵 텍스처를 사용하는 경우에만 필요합니다.
* `texcoord0`: `texcoord1` 텍스처 좌표는 값이 `[0; 1]` 범위에 있을 때와 주소가 지정된 텍스처의 최대 크기가 2048 x 2048 픽셀인 경우 정확도 감소(16_16_FLOAT)를 사용할 수 있습니다.**16_16_FLOAT** 이러한 제한을 초과하면 텍스처 매핑품질이 저하됩니다.

#### <a name="example"></a>예제

텍스처에 조명이 구워지는 사진 문법 모델이 있다고 가정합니다. 모델을 렌더링하는 데 필요한 것은 정점 위치와 텍스처 좌표뿐입니다.

기본적으로 변환기는 언젠가 모델에서 PBR 재질을 사용할 수 있다고 가정해야 하므로 `normal`. `tangent`및 `binormal` 데이터를 생성합니다. 따라서 정점당 메모리 사용량은 `position` (12바이트) `texcoord0` + (8바이트) `normal` + (4바이트) + `tangent` (4바이트) + (4바이트) = `binormal` 32바이트입니다. 이 유형의 큰 모델은 수백만 개의 정점을 쉽게 가질 수 있으므로 수백만 기가바이트의 메모리를 차지할 수 있는 모델이 생성됩니다. 이러한 대량의 데이터는 성능에 영향을 미치며 메모리가 부족할 수도 있습니다.

모델에 동적 조명이 필요하지 않으며 모든 텍스처 좌표가 `[0; 1]` 범위에 있다는 `normal`것을 `tangent`알면 `texcoord0` 정점당 16바이트만 설정하여 정밀도(절반)로`16_16_FLOAT`설정할 `binormal` `NONE` 수 있습니다. 메시 데이터를 절반으로 줄이면 더 큰 모델을 로드할 수 있으며 잠재적으로 성능이 향상될 수 있습니다.

## <a name="typical-use-cases"></a>일반적인 사용 사례

지정된 사용 사례에 대한 좋은 가져오기 설정을 찾는 것은 지루한 프로세스일 수 있습니다. 반면에 전환 설정은 런타임 성능에 큰 영향을 줄 수 있습니다.

특정 최적화에 적합한 특정 사용 사례 클래스가 있습니다. 몇 가지 예는 아래에 있습니다.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>사용 사례 : 건축 시각화 / 대형 야외지도

* 이러한 유형의 장면은 정적인 경향이 있으므로 이동 가능한 부품이 필요하지 않습니다. 따라서 런타임 `sceneGraphMode` 성능을 향상시키는 `static` 또는 `none`짝수로 설정할 수 있습니다. 모드를 `static` 사용하면 장면의 루트 노드를 계속 이동, 회전 및 배율 조정(예: 1인칭 뷰의 경우) 및 테이블 맨 위 뷰 간에 동적으로 전환할 수 있습니다.

* 부품을 이동해야 하는 경우 일반적으로 레이캐스트 또는 기타 [공간 쿼리에](../../overview/features/spatial-queries.md)대한 지원이 필요하므로 처음에 해당 부품을 선택할 수 있습니다. 반면에 무언가를 이동하지 않으려는 경우 공간 쿼리에 참여할 필요가 없으므로 플래그를 `generateCollisionMesh` 끌 수 있습니다. 이 스위치는 변환 시간, 로딩 시간 및 프레임당 업데이트 비용당 런타임에 큰 영향을 미칩니다.

* 응용 프로그램이 절단 [평면을](../../overview/features/cut-planes.md)사용하지 `opaqueMaterialDefaultSidedness` 않는 경우 플래그를 해제해야합니다. 성능 향상은 일반적으로 20%-30%입니다. 절단 평면은 계속 사용할 수 있지만 개체의 내부 부분을 들여다볼 때 백페이스가 없을 수 있습니다. 자세한 내용은 [단일 양면 렌더링](../../overview/features/single-sided-rendering.md)을 참조하십시오.

### <a name="use-case-photogrammetry-models"></a>사용 사례: 사진 측정 모델

사진 계량 분석 모델을 렌더링할 때 일반적으로 장면 그래프가 필요하지 `sceneGraphMode` `none`않으므로 을 로 설정할 수 있습니다. 이러한 모델에는 시작할 복잡한 장면 그래프가 거의 포함되어 있지 않으므로 이 옵션의 영향은 미미해야 합니다.

조명은 이미 텍스처에 구워져 있으므로 동적 조명이 필요하지 않습니다. 따라서

* 모든 재질을 `true` 불이 켜지지 않은 색상 [재질로](../../overview/features/color-materials.md)바꾸도록 플래그를 설정합니다. `unlitMaterials`
* 정점 형식에서 불필요한 데이터를 제거합니다. 위의 [예제를](#example) 참조하십시오.

### <a name="use-case-visualization-of-compact-machines-etc"></a>사용 사례 : 소형 기계 등의 시각화

이러한 사용 사례에서 모델은 종종 작은 볼륨 내에서 매우 높은 세부 사항을 가지고 있습니다. 렌더러는 이러한 경우를 잘 처리하도록 크게 최적화되어 있습니다. 그러나 이전 사용 사례에서 언급한 대부분의 최적화는 여기에 적용되지 않습니다.

* 개별 부품은 선택 가능하고 이동 `sceneGraphMode` 가능해야 하므로 `dynamic`에 남아 있어야 합니다.
* 광선 캐스트는 일반적으로 응용 프로그램의 필수적인 부분이므로 충돌 메시()가 생성되어야 합니다.
* 잘라낸 평면은 `opaqueMaterialDefaultSidedness` 플래그를 사용하도록 설정하면 더 잘 보입니다.

## <a name="next-steps"></a>다음 단계

* [모델 변환](model-conversion.md)
* [색상 재료](../../overview/features/color-materials.md)
* [PBR 재료](../../overview/features/pbr-materials.md)
* [모델 변환 중 재질 재정의](override-materials.md)
