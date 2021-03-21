---
title: 질감
description: 텍스처 리소스 워크플로
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594420"
---
# <a name="textures"></a>질감

텍스처는 변경할 수 없는 [공유 리소스](../concepts/lifetime.md)입니다. 텍스처는 [Blob 스토리지](../how-tos/conversion/blob-storage.md)에서 로드되고 [자습서: 환경 및 재질 변경](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md)에 설명된 대로 모델에 직접 적용될 수 있습니다. 그러나 가장 일반적으로 텍스처는 [변환된 모델](../how-tos/conversion/model-conversion.md)의 일부로 해당 [재료](materials.md)에 의해 참조됩니다.

## <a name="texture-types"></a>텍스처 형식

텍스처 형식마다 사용 사례가 다릅니다.

* **2D 텍스처** 는 주로 [재료](materials.md)에서 사용됩니다.
* [sky](../overview/features/sky.md)에 **Cubemaps** 를 사용할 수 있습니다.

## <a name="supported-texture-formats"></a>지원되는 텍스처 형식

ARR에 지정된 모든 텍스처는 [DDS 형식](https://en.wikipedia.org/wiki/DirectDraw_Surface)이어야 합니다. mipmaps 및 텍스처 압축을 사용하는 것이 좋습니다.

## <a name="loading-textures"></a>텍스처 로드

텍스처를 로드하는 경우 예상되는 형식을 지정해야 합니다. 형식이 일치하지 않으면 텍스처 로드가 실패합니다.
동일한 URI를 사용하여 텍스처를 두 번 로드하면 동일한 텍스처 개체가 반환됩니다. 이는 [공유 리소스](../concepts/lifetime.md)이기 때문입니다.

모델을 로드하는 것과 유사하게 원본 Blob 스토리지에서 텍스처 자산을 처리하는 두 가지 변형이 있습니다.

* [Blob 스토리지가 계정에 연결](../how-tos/create-an-account.md#link-storage-accounts)된 경우 Blob 스토리지 매개 변수를 통해 텍스처를 직접 처리합니다. 이 경우 매개 변수 `LoadTextureOptions`와 관련된 로딩 함수는 `LoadTextureAsync`입니다.
* 텍스처 자산은 SAS URI로 처리할 수 있습니다. 매개 변수 `LoadTextureFromSasOptions`와 관련된 로딩 함수는 `LoadTextureFromSasAsync`입니다. [기본 제공 텍스처](../overview/features/sky.md#built-in-environment-maps)를 로드하는 경우에도 이 변형을 사용합니다.

다음 샘플 코드에서는 질감을 로드 하는 방법을 보여 줍니다.

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

SAS variant를 사용 하는 경우에는 로딩 함수/매개 변수만 다릅니다.

텍스처가 사용될 것으로 예상되는 항목에 따라 텍스처 형식 및 콘텐츠에 제한이 있을 수 있습니다. 예를 들어 [PBR 재질](../overview/features/pbr-materials.md)의 초고 맵은 회색조여야 합니다.

## <a name="api-documentation"></a>API 설명서

* [C # 질감 클래스](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C # RenderingConnection LoadTextureAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C # RenderingConnection LoadTextureFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [C + + 질감 클래스](/cpp/api/remote-rendering/texture)
* [C + + RenderingConnection:: LoadTextureAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C + + RenderingConnection:: LoadTextureFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>다음 단계

* [재질](materials.md)
* [Sky](../overview/features/sky.md)