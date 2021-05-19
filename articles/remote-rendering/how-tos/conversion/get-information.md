---
title: 변환 정보 가져오기
description: 변환 정보 가져오기
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 89ec0ad40822785457e988cf9e0f9bd6d00ed81f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91576628"
---
# <a name="get-information-about-conversions"></a>변환 정보 가져오기

## <a name="information-about-a-conversion-the-result-file"></a>변환에 대한 정보: 결과 파일

변환 서비스는 자산을 변환할 때 모든 문제에 대한 요약을 "결과 파일"에 기록합니다. 예를 들어 `buggy.gltf` 파일을 변환하는 경우 출력 컨테이너에는 `buggy.result.json`이라는 파일이 포함됩니다.

결과 파일은 변환 중에 발생한 오류 및 경고를 나열하고 `succeeded`, `failed`, `succeeded with warnings` 중 하나의 결과 요약을 제공합니다.
결과 파일은 개체의 JSON 배열로 구성되며, 각 개체는 `warning`, `error`, `internal warning`, `internal error`, `result` 중 하나의 문자열 속성을 포함합니다. 최대 한 개의 오류(`error` 또는 `internal error`)가 있으며 하나의 `result`가 항상 있습니다.

## <a name="example-result-file"></a>*결과* 파일 예

다음 예는 arrAsset을 성공적으로 생성한 변환을 설명합니다. 그러나 누락된 질감이 있으므로 결과 arrAsset이 의도된 것이 아닐 수 있습니다.

```JSON
[
  {"warning":"4004","title":"Missing texture","details":{"texture":"buggy_baseColor.png","material":"buggy_col"}},
  {"result":"succeeded with warnings"}
]
```

## <a name="information-about-a-converted-model-the-info-file"></a>변환된 모델에 대한 정보: info 파일

변환 서비스에 의해 생성된 arrAsset 파일은 렌더링 서비스에서 사용하기 위한 것입니다. 그러나 렌더링 세션을 시작하지 않고 모델에 대한 정보에 액세스하려는 경우가 있을 수 있습니다. 이 워크플로를 지원하기 위해 변환 서비스는 출력 컨테이너의 arrAsset 파일 외에 JSON 파일을 배치합니다. 예를 들어 `buggy.gltf` 파일을 변환하는 경우 출력 컨테이너에는 변환된 자산 `buggy.arrAsset` 외에 `buggy.info.json`이라는 파일이 포함됩니다. 원본 모델, 변환된 모델 및 변환 자체에 대한 정보를 포함합니다.

## <a name="example-info-file"></a>*info* 파일 예

다음은 `buggy.gltf`라는 파일을 변환하여 생성된 *info* 파일 예입니다.

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "materialOverrides": {
        "numOverrides": 4,
        "numOverriddenMaterials": 4
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>정보 파일의 정보

### <a name="the-files-section"></a>*files* 섹션

이 섹션에는 제공된 파일 이름이 포함되어 있습니다.

* `input`: 소스 파일 이름입니다.
* `output`: 사용자가 기본값이 아닌 이름을 지정한 경우 출력 파일의 이름입니다.

### <a name="the-conversionsettings-section"></a>*conversionSettings* 섹션

이 섹션에서는 모델을 변환할 때 지정된 [ConversionSettings](configure-model-conversion.md#settings-file) 복사본을 보관합니다.

### <a name="the-inputinfo-section"></a>*inputInfo* 섹션

이 섹션에서는 원본 파일 형식에 대한 정보를 기록합니다.

* `sourceAssetExtension`: 원본 파일의 파일 확장명입니다.
* `sourceAssetFormat`: 원본 파일 형식에 대한 설명입니다.
* `sourceAssetFormatVersion`: 원본 파일 형식의 버전입니다.
* `sourceAssetGenerator`: 사용할 수 있는 경우 소스 파일을 생성한 도구의 이름입니다.

### <a name="the-materialoverrides-section"></a>*materialOverrides* 섹션

이 섹션에서는 변환 서비스에 재질 재정의 파일이 제공될 때 [재질 재정의](override-materials.md)에 대한 정보를 제공합니다.
여기에는 다음 정보가 포함되어 있습니다.
* `numOverrides`: 재질 재정의 파일에서 읽은 재정의 항목 수입니다.
* `numOverriddenMaterials`: 재정의된 재질의 수입니다.

### <a name="the-inputstatistics-section"></a>*inputStatistics* 섹션

이 섹션에서는 원본 장면에 대한 정보를 제공합니다. 이 섹션의 값과 원본 모델을 만든 도구의 해당 값 사이에는 종종 차이가 있습니다. 내보내기 및 변환 단계에서 모델이 수정되기 때문에 이러한 차이가 발생합니다.

* `numMeshes`: 각 파트가 단일 재질을 참조할 수 있는 메시 파트의 수입니다.
* `numFaces`: 전체 모델의 총 _삼각형_ 수입니다. 변환하는 동안 메시가 삼각 측량되는 것에 유의하세요. 이 숫자는 [표준 렌더링 서버 크기](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons)의 다각형 제한에 적용됩니다.
* `numVertices`: 전체 모델의 총 꼭짓점 수입니다.
* `numMaterial`: 전체 모델의 총 재질 수입니다.
* `numFacesSmallestMesh`: 모델의 가장 작은 메시의 삼각형 수입니다.
* `numFacesBiggestMesh`: 모델의 가장 큰 메시의 삼각형 수입니다.
* `numNodes`: 모델 장면 그래프의 노드 수입니다.
* `numMeshUsagesInScene`: 노드가 메시를 참조하는 횟수입니다. 둘 이상의 노드가 동일한 메시를 참조할 수 있습니다.
* `maxNodeDepth`: 장면 그래프 내 노드의 최대 깊이입니다.

### <a name="the-outputinfo-section"></a>*outputInfo* 섹션

이 섹션에서는 생성된 출력에 대한 일반 정보를 기록합니다.

* `conversionToolVersion`: 모델 변환기의 버전입니다.
* `conversionHash`: 렌더링에 기여할 수 있는 arrAsset 내의 데이터의 해시입니다. 이 해시를 사용하여 동일한 파일에 대해 변환 서비스를 다시 실행할 때 다른 결과가 생성되는지 여부를 파악할 수 있습니다.

### <a name="the-outputstatistics-section"></a>*outputStatistics* 섹션

이 섹션에서는 변환된 자산에서 계산된 정보를 기록합니다.

* `numMeshPartsCreated`: arrAsset의 메시 수입니다. 인스턴싱은 변환 프로세스의 영향을 받기 때문에 `inputStatistics` 섹션의 `numMeshes`와 다를 수 있습니다.
* `numMeshPartsInstanced`: ArrAsset에서 재사용되는 메시의 수입니다.
* `recenteringOffset`: [ConversionSettings](configure-model-conversion.md)에서 `recenterToOrigin` 옵션을 사용하는 경우 이 값은 변환된 모델을 원래 위치로 다시 이동하는 좌표 이동입니다.
* `boundingBox`: 모델의 경계입니다.

## <a name="deprecated-features"></a>사용되지 않는 기능

변환 서비스는 `stdout.txt` 및 `stderr.txt` 파일을 출력 컨테이너에 기록하며 이러한 파일은 경고 및 오류의 유일한 원본입니다.
이러한 파일은 이제 사용되지 않습니다. 대신 이 목적을 위해 [결과 파일](#information-about-a-conversion-the-result-file)을 사용하세요.

## <a name="next-steps"></a>다음 단계

* [모델 변환](model-conversion.md)
* [모델 변환 구성](configure-model-conversion.md)
