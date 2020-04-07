---
title: 변환된 모델에 대한 정보 얻기
description: 모든 모델 변환 매개변수에 대한 설명
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681520"
---
# <a name="get-information-about-a-converted-model"></a>변환된 모델에 대한 정보 얻기

변환 서비스에서 생성되는 arrAsset 파일은 렌더링 서비스에서 소비하기 위한 것입니다. 그러나 렌더링 세션을 시작하지 않고 모델에 대한 정보에 액세스하려는 경우가 있을 수 있습니다. 따라서 변환 서비스는 출력 컨테이너에 arrAsset 파일 옆에 JSON 파일을 배치합니다. 예를 들어 파일이 `buggy.gltf` 변환되는 경우 출력 컨테이너에는 변환된 자산 `buggy.info.json` `buggy.arrAsset`옆에 라는 파일이 포함됩니다. 여기에는 소스 모델, 변환된 모델 및 변환 자체에 대한 정보가 들어 있습니다.

## <a name="example-info-file"></a>예제 *정보* 파일

다음은 다음 이라는 파일을 변환하여 생성된 예제 `buggy.gltf` *정보 파일입니다.*

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

### <a name="the-files-section"></a>*파일* 섹션

이 섹션에는 제공된 파일 이름이 포함되어 있습니다.

* `input`: 원본 파일의 이름입니다.
* `output`: 사용자가 기본이 아닌 이름을 지정한 경우 출력 파일의 이름입니다.

### <a name="the-conversionsettings-section"></a>*변환 설정* 섹션

이 섹션에는 모델을 변환할 때 지정된 [변환 설정의](configure-model-conversion.md#settings-file) 복사본이 있습니다.

### <a name="the-inputinfo-section"></a>*입력정보* 섹션

이 섹션에서는 소스 파일 형식에 대한 정보를 기록합니다.

* `sourceAssetExtension`: 소스 파일의 파일 확장명입니다.
* `sourceAssetFormat`: 소스 파일 형식에 대한 설명입니다.
* `sourceAssetFormatVersion`: 소스 파일 형식의 버전입니다.
* `sourceAssetGenerator`: 원본 파일을 생성한 도구의 이름(사용 가능한 경우)입니다.

### <a name="the-inputstatistics-section"></a>*입력통계* 섹션

이 섹션에서는 소스 장면에 대한 정보를 제공합니다. 이 단면의 값과 소스 모델을 만든 도구의 등가 값 사이에는 불일치가 있는 경우가 많습니다. 내보내기 및 변환 단계에서 모델이 수정되기 때문에 이러한 차이가 예상됩니다.

* `numMeshes`: 각 부품이 단일 재질을 참조할 수 있는 메시 부품의 수입니다.
* `numFaces`: 전체 모델의 총 _삼각형_ 수입니다. 메시는 변환 중에 삼각측량으로 표시됩니다.
* `numVertices`: 전체 모델의 총 정점 수입니다.
* `numMaterial`: 전체 모델의 총 재료 수입니다.
* `numFacesSmallestMesh`: 모델의 가장 작은 메시의 삼각형 수입니다.
* `numFacesBiggestMesh`: 모델의 가장 큰 메시의 삼각형 수입니다.
* `numNodes`: 모델의 장면 그래프의 노드 수입니다.
* `numMeshUsagesInScene`: 노드가 메시참조 횟수입니다. 두 개 이상의 노드가 동일한 메시를 참조할 수 있습니다.
* `maxNodeDepth`: 장면 그래프 내의 노드의 최대 깊이입니다.

### <a name="the-outputinfo-section"></a>*출력정보* 섹션

이 섹션에서는 생성된 출력에 대한 일반 정보를 기록합니다.

* `conversionToolVersion`: 모델 컨버터 버전.
* `conversionHash`: 렌더링에 기여할 수 있는 arrAsset 내의 데이터의 해시입니다. 변환 서비스가 동일한 파일에서 다시 실행할 때 다른 결과를 생성했는지 여부를 이해하는 데 사용할 수 있습니다.

### <a name="the-outputstatistics-section"></a>*출력통계* 섹션

이 섹션에서는 변환된 자산에서 계산된 정보를 기록합니다.

* `numMeshPartsCreated`: arrAsset의 메시 수입니다. 인스턴스화는 `numMeshes` 변환 `inputStatistics` 프로세스의 영향을 받므로 섹션과 다를 수 있습니다.
* `numMeshPartsInstanced`: arrAsset에서 재사용되는 메시 수입니다.
* `recenteringOffset`: 변환 `recenterToOrigin` [설정의](configure-model-conversion.md) 옵션이 활성화된 경우 이 값은 변환된 모델을 원래 위치로 다시 이동하는 변환값입니다.
* `boundingBox`: 모델의 경계입니다.

## <a name="next-steps"></a>다음 단계

* [모델 변환](model-conversion.md)
* [모델 변환 구성](configure-model-conversion.md)
