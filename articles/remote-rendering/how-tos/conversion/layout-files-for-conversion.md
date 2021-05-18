---
title: 변환용 파일 레이아웃
description: 입력 컨테이너에 파일을 저장하는 최상의 방법에 대한 권장 사항입니다.
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: 13c4b841fe2d0b62472628d9382b5f6ee3d1fa6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91318078"
---
# <a name="laying-out-files-for-conversion"></a>변환용 파일 레이아웃

자산을 올바르게 처리하기 위해 변환 서비스는 모든 입력 파일을 찾을 수 있어야 합니다.
이러한 파일은 변환되는 주 자산 파일과 일반적으로 자산 파일 내의 경로에서 참조되는 일부 다른 파일로 구성됩니다.
자산 변환에 대한 요청에는 변환 서비스가 이러한 파일을 찾는 방법을 결정하는 두 개의 매개 변수 `input.folderPath`(선택 사항)와 `input.inputAssetPath`가 제공됩니다.
[변환 REST API](conversion-rest-api.md) 페이지에 전체적으로 문서화되어 있습니다.
파일을 배치하기 위해서는 `folderPath`가 자산을 처리할 때 변환 서비스에 사용할 수 있는 파일의 전체 집합을 결정한다는 점에 유의해야 합니다.

## <a name="placing-files-so-they-can-be-found"></a>파일을 찾을 수 있도록 배치

원본 자산이 외부 파일을 사용하는 경우 해당 파일에 대한 경로는 자산 내에 저장됩니다.
변환 서비스는 자산의 원래 파일 시스템과 다른 파일 시스템에서 이러한 경로를 해석해야 합니다.
경로가 상대 경로로 저장되고 원본 자산과 참조하는 파일 간의 상대 위치는 변경되지 않은 경우 변환 서비스에서 참조된 파일을 쉽게 찾을 수 있습니다.

> [!Note]
> 입력 컨테이너에 파일을 저장하는 것이 좋습니다. 그러면 파일의 상대 위치가 자산이 생성될 때의 위치와 동일하게 됩니다.

> [!Note]
> 상대 경로를 전달하는 자산을 만드는 것이 좋습니다.
> [3ds Max 재질 설정](../../tutorials/modeling/3dsmax-material-setup.md)에 대한 자습서에서는 자산이 상대 경로를 사용하도록 하는 방법에 대한 3ds Max 예를 제공합니다.

## <a name="finding-textures"></a>질감 찾기

자산을 생성하는 방법에는 여러 가지가 있으므로 변환 서비스를 유연하게 사용할 수 있어야 합니다.
특히 자산의 경로와 질감의 위치가 정확하게 일치하지 않는 상황을 처리해야 합니다.
예를 들어, 절대 경로를 포함하는 자산이 생성되는 경우 이러한 경로는 변환 서비스에서 사용하는 파일 시스템과 일치하지 않습니다.
이러한 상황을 처리하기 위해 최상의 방법을 사용하여 질감을 찾습니다.

질감을 찾기 위한 알고리즘은 다음과 같습니다. 자산에 저장되는 경로를 지정하면 원본 자산의 위치에서 상대 경로로 사용될 때 존재하는 파일을 대상으로 하는 가장 긴 하위 경로 접미사를 찾습니다.
이러한 하위 경로(전체 경로 포함)가 파일을 대상으로 하지 않는 경우 질감이 누락된 것으로 간주됩니다.

다음과 같은 파일 시스템을 고려합니다. 
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
myAsset.fbx가 상대 경로 `..\Textures\MyAssetTextures\myTexture.png`를 사용하여 질감을 참조하는 경우 변환 서비스는 파일 E를 사용합니다. 실제로는 파일 A, C 및 E가 있는 경우 해당 파일을 사용할 수 있으며 파일 E가 가장 긴 접미사를 사용하므로 선호도가 높습니다.
`Textures\myTexture.png`는 저장된 경로의 접미사 일부가 아니므로 파일 B와 D는 사용되지 않습니다.
대신 자산에 `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` 또는 `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png` 경로가 포함된 경우 변환 서비스는 파일 A와 C가 있는 경우 이를 찾을 수 있습니다(A보다 C를 선호). 그러나 이러한 방식으로는 E를 찾을 수 없으므로 파일을 재배치해야 합니다.
이 문제는 자산 옆에 Textures 폴더를 이동하여 해결할 수 있습니다.

> [!Note]
> 질감이 검색되지 않는 경우 가능한 해결책은 자산이 질감을 포함하는 일부 하위 트리의 형제인지 확인하는 것입니다.

## <a name="next-steps"></a>다음 단계

- [모델 변환](model-conversion.md)
