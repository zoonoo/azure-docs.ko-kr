---
title: 질감
description: 질감 리소스 워크플로
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681663"
---
# <a name="textures"></a>질감

질감은 변경할 수 없는 [공유 리소스](../concepts/lifetime.md)입니다. [자습서: 환경 및 재질 변경](../tutorials/unity/changing-environment-and-materials.md)에 설명 된 대로 [blob 저장소](../how-tos/conversion/blob-storage.md) 에서 텍스처를 로드 하 고 모델에 직접 적용할 수 있습니다. 그러나 일반적으로 질감은 [자료](materials.md)에서 참조 되는 [변환 된 모델](../how-tos/conversion/model-conversion.md)의 일부입니다.

## <a name="texture-types"></a>질감 형식

질감 형식 마다 사용 사례가 다릅니다.

* **2D 질감이** 주로 [자료](materials.md)에서 사용 됩니다.
* **Cubemaps** 는 [하늘](../overview/features/sky.md)에 사용할 수 있습니다.

## <a name="supported-texture-formats"></a>지원 되는 질감 형식

ARR에 지정 된 모든 질감은 [DDS 형식](https://en.wikipedia.org/wiki/DirectDraw_Surface)이어야 합니다. Mip 맵을 및 질감 압축을 사용 하는 것이 좋습니다. 변환 프로세스를 자동화 하려면 [TexConv 명령줄 도구](../resources/tools/tex-conv.md) 를 참조 하세요.

## <a name="loading-textures"></a>질감 로드

질감을 로드 하는 경우 예상 되는 형식을 지정 해야 합니다. 형식이 일치 하지 않으면 질감 로드에 실패 합니다.
동일한 URI를 사용 하 여 질감을 두 번 로드 하면 [공유 리소스인](../concepts/lifetime.md)것 처럼 동일한 텍스처 개체가 반환 됩니다.

모델을 로드 하는 것과 마찬가지로, 원본 blob 저장소에서 질감 자산을 주소 지정 하는 두 가지 변형이 있습니다.

* 질감 자산은 SAS URI로 주소를 지정할 수 있습니다. 관련 로드 함수는 `LoadTextureFromSASAsync` 매개 변수 `LoadTextureFromSASParams`를 사용 합니다. [기본 제공 질감](../overview/features/sky.md#built-in-environment-maps)을 로드 하는 경우에도이 변형을 사용 합니다.
* [Blob 저장소를 계정에 연결](../how-tos/create-an-account.md#link-storage-accounts)하는 경우 blob 저장소 매개 변수를 통해 텍스처에 직접 주소를 지정할 수 있습니다. 이 경우 관련 로드 함수는 `LoadTextureAsync` 매개 변수 `LoadTextureParams`를 사용 합니다.

다음 샘플 코드는 SAS URI (또는 기본 제공 텍스처)를 통해 텍스처를 로드 하는 방법을 보여 줍니다. 다른 경우에는 로딩 함수/매개 변수만 다릅니다.

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

질감이 사용 될 것으로 예상 되는 항목에 따라 질감 형식 및 콘텐츠에 대 한 제한이 있을 수 있습니다. 예를 들어, [.pbr 재질](../overview/features/pbr-materials.md) 의 황삭 맵은 회색조 여야 합니다.

> [!CAUTION]
> ARR의 모든 *비동기* 함수는 비동기 작업 개체를 반환 합니다. 작업이 완료 될 때까지 해당 개체에 대 한 참조를 저장 해야 합니다. 그렇지 않으면 c # 가비지 수집기가 작업을 일찍 삭제할 수 있으며 완료 되지 않습니다. 위의 샘플 코드에서 멤버 변수 ' _textureLoad '는 *완료* 된 이벤트가 도착할 때까지 참조를 보관 하는 데 사용 됩니다.

## <a name="next-steps"></a>다음 단계

* [재질](materials.md)
* [하늘색](../overview/features/sky.md)
