---
title: 변환용 파일 레이아웃
description: 입력 컨테이너에 파일을 저장 하는 최상의 방법에 대 한 권장 사항입니다.
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: 8acd19408c328ef3f534ba7bc41c96e395f768be
ms.sourcegitcommit: 655e4b75fa6d7881a0a410679ec25c77de196ea3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2020
ms.locfileid: "89510979"
---
# <a name="laying-out-files-for-conversion"></a>변환용 파일 레이아웃

자산을 올바르게 처리 하기 위해 변환 서비스는 모든 입력 파일을 찾을 수 있어야 합니다.
이러한 파일은 변환 되는 주 자산 파일과 일반적으로 자산 파일 내의 경로에서 참조 되는 일부 다른 파일에 구성 됩니다.
자산 변환에 대 한 요청에는 변환 서비스가 이러한 파일을 찾는 방법을 결정 하는 두 개의 매개 변수 `input.folderPath` (선택 사항)와가 제공 됩니다 `input.inputAssetPath` .
[변환 REST API](conversion-rest-api.md) 페이지에 완벽 하 게 문서화 되어 있습니다.
파일의 레이아웃을 지정 하기 위해에서 `folderPath` 자산을 처리할 때 변환 서비스에 사용할 수 있는 파일의 전체 집합을 결정 한다는 점에 유의 해야 합니다.

## <a name="placing-files-so-they-can-be-found"></a>파일을 찾을 수 있도록 배치

원본 자산이 외부 파일을 사용 하는 경우 해당 파일에 대 한 경로는 자산 내에 저장 됩니다.
변환 서비스는 자산의 원래 파일 시스템과 다른 파일 시스템에서 이러한 경로를 해석 해야 합니다.
경로가 상대 경로로 저장 되 고 원본 자산과 참조 하는 파일 간의 상대 위치는 변경 되지 않은 경우 변환 서비스에서 참조 된 파일을 쉽게 찾을 수 있습니다.

> [!Note]
> 입력 컨테이너에 파일을 저장 하는 것이 좋습니다. 그러면 파일의 상대 위치는 자산이 생성 될 때와 동일 하 게 됩니다.

## <a name="finding-textures"></a>질감 찾기

자산을 생성 하는 방법에는 여러 가지가 있으므로 변환 서비스를 유연 하 게 사용할 수 있어야 합니다.
특히 자산의 경로와 질감의 위치가 정확 하 게 일치 하지 않는 상황을 처리 해야 합니다.
예를 들어, 절대 경로를 포함 하는 자산이 생성 되는 경우를 예로 들 수 있습니다. 이러한 경로는 변환 서비스에서 사용 하는 filesystem과 일치 하지 않습니다.
이러한 상황을 처리 하기 위해 최상의 방법을 사용 하 여 질감을 찾습니다.

질감을 찾기 위한 알고리즘은 다음과 같습니다. 자산에 저장 된 경로를 지정 하면 원본 자산의 위치에서 상대 경로로 사용 되는 경우 존재 하는 파일을 대상으로 하는 가장 긴 하위 경로 접미사를 찾습니다.
이러한 하위 경로 (전체 경로 포함)가 파일을 대상으로 하지 않는 경우 질감이 누락 된 것으로 간주 됩니다.

다음 거리가 파일 시스템을 고려 합니다. 
```
G:\CONVERSION
├───Assets
│   │   myAsset.fbx
│   │   myTexture.png            <- A
│   │
│   └───Textures
│       │   myTexture.png        <- B
│       │
│       └───MyAssetTextures
│               myTexture.png    <- C
│
└───Textures
    │   myTexture.png            <- D
    │
    └───MyAssetTextures
            myTexture.png        <- E
```
Fbx가 상대 경로를 사용 하 여 텍스처를 참조 하는 경우 `..\Textures\MyAssetTextures\myTexture.png` 변환 서비스는 파일 E를 사용 합니다. 실제로는 파일 A, C 및 E가 있는 경우 해당 파일을 사용할 수 있습니다 .이 경우에는 가장 긴 접미사를 사용 하 여 파일 E를 선호 합니다.
`Textures\myTexture.png`는 저장 된 경로에 있는 접미사의 일부가 아니므로 B 및 D 파일은 사용 되지 않습니다.
자산이 또는 경로를 포함 하는 `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` 경우 `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png` 변환 서비스는 파일 a와 c가 있는 경우이를 찾을 수 있습니다 (a를 통해 c를 선호). 그러나 이러한 방식으로 E를 찾을 수 없으므로 파일을 재배치 해야 합니다.
이는 자산 옆에 있는 질감 폴더를 이동 하 여 해결할 수 있습니다.

> [!Note]
> 질감이 검색 되지 않는 경우 자산이 질감을 포함 하는 일부 하위 트리의 형제 인지 확인 하는 것이 가능한 해결 방법입니다.

## <a name="next-steps"></a>다음 단계

- [모델 변환](model-conversion.md)
