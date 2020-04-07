---
title: 질감
description: 텍스처 리소스 워크플로우
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681663"
---
# <a name="textures"></a>질감

텍스처는 변경할 수 없는 [공유 리소스입니다.](../concepts/lifetime.md) 텍스처는 [Blob 저장소에서](../how-tos/conversion/blob-storage.md) 로드하고 [자습서: 환경 및 재질 변경에서](../tutorials/unity/changing-environment-and-materials.md)설명한 대로 모델에 직접 적용할 수 있습니다. 가장 일반적으로 텍스처는 [변환된 모델의](../how-tos/conversion/model-conversion.md)일부가 되며, 여기서 텍스처는 [재질에서](materials.md)참조됩니다.

## <a name="texture-types"></a>텍스처 유형

텍스처 유형에 따라 사용 사례가 다릅니다.

* **2D 텍스처는** 주로 [재료에](materials.md)사용됩니다.
* **큐브맵은** [하늘에](../overview/features/sky.md)사용할 수 있습니다.

## <a name="supported-texture-formats"></a>지원되는 텍스처 형식

ARR에 주어진 모든 텍스처는 [DDS 형식이어야](https://en.wikipedia.org/wiki/DirectDraw_Surface)합니다. 밉맵 및 텍스처 압축이 바람직합니다. 변환 프로세스를 자동화하려면 [TexConv 명령줄 도구를](../resources/tools/tex-conv.md) 참조하십시오.

## <a name="loading-textures"></a>텍스처 로드

텍스처를 로드할 때는 예상형식을 지정해야 합니다. 형식이 일치하지 않으면 텍스처 로드가 실패합니다.
동일한 URI로 텍스처를 두 번 로드하면 [공유 리소스와](../concepts/lifetime.md)동일한 텍스처 개체가 반환됩니다.

모델을 로드하는 것과 마찬가지로 소스 Blob 저장소에서 텍스처 자산을 해결하는 두 가지 변형이 있습니다.

* 텍스처 자산은 SAS URI를 통해 해결할 수 있습니다. 관련 로딩 `LoadTextureFromSASAsync` 함수는 매개 변수가 `LoadTextureFromSASParams`있습니다. [기본 제공 텍스처를](../overview/features/sky.md#built-in-environment-maps)로드할 때도 이 변형을 사용합니다.
* Blob 저장소가 계정에 연결된 경우 텍스처를 Blob 저장소 매개 변수로 직접 해결할 수 [있습니다.](../how-tos/create-an-account.md#link-storage-accounts) 이 경우 관련 로딩 `LoadTextureAsync` `LoadTextureParams`함수는 매개 변수가 있습니다.

다음 샘플 코드는 SAS URI(또는 기본 제공 텍스처)를 통해 텍스처를 로드하는 방법을 보여 주며, 다른 경우에 따라 로딩 함수/매개변수만 다릅니다.

``` cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureAsync(new LoadTextureParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

텍스처가 사용되는 내용에 따라 텍스처 유형 과 콘텐츠에 제한이 있을 수 있습니다. 예를 들어 [PBR 재질의](../overview/features/pbr-materials.md) 거칠기 맵은 그레이스케일이어야 합니다.

> [!CAUTION]
> ARR의 모든 *비동기* 함수는 비동기 작업 개체를 반환합니다. 작업이 완료될 때까지 해당 개체에 대한 참조를 저장해야 합니다. 그렇지 않으면 C# 가비지 수집기는 작업을 일찍 삭제할 수 있으며 완료할 수 없습니다. 위의 샘플 코드에서 멤버 변수 '_textureLoad'은 *완료된* 이벤트가 도착할 때까지 참조를 보유하는 데 사용됩니다.

## <a name="next-steps"></a>다음 단계

* [원자재](materials.md)
* [하늘](../overview/features/sky.md)
