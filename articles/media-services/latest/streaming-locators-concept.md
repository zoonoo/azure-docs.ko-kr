---
title: Azure Media Services의 스트리밍 로케이터 | Microsoft Docs
description: 이 문서에서는 스트리밍 로케이터의 개념과 Azure Media Services에서 이러한 로케이터를 사용하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 41b2d0ad1e072fb2bf5860ae80f8f25f886b37f7
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582684"
---
# <a name="streaming-locators"></a>스트리밍 로케이터

출력 자산의 비디오를 재생할 클라이언트에 사용할 수 있도록 하려면 [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만든 다음, 스트리밍 URL을 빌드해야 합니다. URL을 빌드하려면 스트리밍 엔드포인트 호스트 이름과 스트리밍 로케이터 경로를 연결해야 합니다. .NET 샘플은 [스트리밍 로케이터 가져오기](stream-files-tutorial-with-api.md#get-a-streaming-locator)를 참조하세요.

**스트리밍 로케이터**를 만드는 과정을 게시라고 합니다. 기본적으로 스트리밍 **로케이터는** API 호출을 한 직후에 유효하며 선택적 시작 및 종료 시간을 구성하지 않는 한 API 호출이 삭제될 때까지 지속됩니다. 

**스트리밍 로케이터를**만들 때 **자산** 이름과 **스트리밍 정책** 이름을 지정해야 합니다. 자세한 내용은 아래 항목을 참조하세요.

* [자산](assets-concept.md)
* [스트리밍 정책](streaming-policy-concept.md)
* [콘텐츠 키 정책](content-key-policy-concept.md)

스트리밍 로케이터에서 시작 및 종료 시간을 지정할 수 있으며, 이 시간은 사용자가 이러한 시간(예: 2019년 5월~2019년 5월 5일)까지만 콘텐츠를 재생할 수 있도록 합니다.  

## <a name="considerations"></a>고려 사항

* **스트리밍 로케이터는** 업데이터로 처리되지 않습니다. 
* 날짜/시간 형식의 **스트리밍 로케이터** 속성은 언제나 UTC 형식입니다.
* Media Services 계정에 대한 제한된 정책 세트를 설계하고 동일한 옵션이 필요할 때마다 스트리밍 로케이터에 해당 세트를 다시 사용하는 것이 좋습니다. 자세한 내용은 [할당량 및 제한을](limits-quotas-constraints.md)참조하십시오.

## <a name="create-streaming-locators"></a>스트리밍 로케이터 만들기  

### <a name="not-encrypted"></a>암호화되지 않음

파일을 암호화되지 않은 투명하게 스트리밍하려면 미리 정의된 투명 스트리밍 정책을 'Predefined_ClearStreamingOnly'로 설정합니다(.NET에서 사전 정의된 StreamingPolicy.ClearStreamingOnly 번만 열거형).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>암호화 

CENC 암호화로 콘텐츠를 암호화해야 하는 경우 정책을 'Predefined_MultiDrmCencStreaming'으로 설정합니다. 와이드바인 암호화는 DASH 스트림에 적용되고 PlayReady to Smooth가 적용됩니다. 키는 구성된 DRM 라이선스에 따라 재생 클라이언트로 배달됩니다.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

또한 CBCS (페어 플레이)와 HLS 스트림을 암호화하려는 경우, 'Predefined_MultiDrmStreaming'를 사용합니다.

> [!NOTE]
> Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="associate-filters-with-streaming-locators"></a>필터를 스트리밍 로케이터와 연결

[필터 를 참조하십시오: 스트리밍 로케이터와 연결.](filters-concept.md#associating-filters-with-streaming-locator)

## <a name="filter-order-page-streaming-locator-entities"></a>필터링, 순서, 페이지 스트리밍 로케이터 엔터티

[Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)을 참조하세요.

## <a name="list-streaming-locators-by-asset-name"></a>자산 이름으로 스트리밍 로케이터 목록

연결된 자산 이름을 기반으로 스트리밍 로케이터를 얻으려면 다음 작업을 사용합니다.

|언어|API|
|---|---|
|REST (영문)|[목록 스트리밍로케이터](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams 자산 목록 스트리밍 로케이터](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[리스트 스트리밍로케이터](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[에셋 스트리밍로케이터](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>참고 항목

* [자산](assets-concept.md)
* [스트리밍 정책](streaming-policy-concept.md)
* [콘텐츠 키 정책](content-key-policy-concept.md)
* [자습서: .NET을 사용하여 동영상을 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>다음 단계

[스트리밍 로케이터를 만들고 URL을 빌드하는 방법](create-streaming-locator-build-url.md)
