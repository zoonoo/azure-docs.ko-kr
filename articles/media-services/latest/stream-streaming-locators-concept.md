---
title: Azure Media Services의 스트리밍 로케이터
description: 이 문서에서는 스트리밍 로케이터의 개념과 Azure Media Services에서 이러한 로케이터를 사용하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: e4374e564ea2fb8a5cc3f8bada6eb3ff27663912
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634279"
---
# <a name="streaming-locators"></a>스트리밍 로케이터

출력 자산의 비디오를 재생할 클라이언트에 사용할 수 있도록 하려면 [스트리밍 로케이터](/rest/api/media/streaminglocators)를 만든 다음, 스트리밍 URL을 빌드해야 합니다. URL을 빌드하려면 스트리밍 엔드포인트 호스트 이름과 스트리밍 로케이터 경로를 연결해야 합니다. .NET 샘플은 [스트리밍 로케이터 가져오기](stream-files-tutorial-with-api.md#get-a-streaming-locator)를 참조하세요.

**스트리밍 로케이터** 를 만드는 과정을 게시라고 합니다. 기본적으로 **스트리밍 로케이터** 는 API 호출을 수행한 직후부터 유효하며, 선택적인 시작 및 종료 시간을 구성하지 않는 한 삭제될 때까지 지속됩니다. 

**스트리밍 로케이터** 를 만드는 경우 **자산** 이름 및 **스트리밍 정책** 이름을 지정해야 합니다. 자세한 내용은 다음 항목을 참조하세요.

* [Assets](assets-concept.md)
* [스트리밍 정책](stream-streaming-policy-concept.md)
* [콘텐츠 키 정책](drm-content-key-policy-concept.md)

사용자가 이 기간(예: 2019/5/1~2019/5/5)에만 콘텐츠를 재생하도록 허용하는 스트리밍 로케이터 시작 시간과 종료 시간을 지정할 수도 있습니다.  

## <a name="considerations"></a>고려 사항

* **스트리밍 로케이터** 는 업데이트할 수 없습니다. 
* 날짜/시간 형식의 **스트리밍 로케이터** 속성은 언제나 UTC 형식입니다.
* Media Services 계정에 대한 제한된 정책 세트를 설계하고 동일한 옵션이 필요할 때마다 스트리밍 로케이터에 해당 세트를 다시 사용하는 것이 좋습니다. 자세한 내용은 [할당량 및 제한](limits-quotas-constraints-reference.md)을 참조하세요.

## <a name="create-streaming-locators"></a>스트리밍 로케이터 만들기  

### <a name="not-encrypted"></a>암호화되지 않음

파일을 있는 그대로(암호화하지 않고) 스트리밍하려는 경우 미리 정의된 암호화되지 않은 스트리밍 정책을 'Predefined_ClearStreamingOnly'로 설정합니다(.NET에서는 PredefinedStreamingPolicy.ClearStreamingOnly 열거형을 사용할 수 있음).

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

### <a name="encrypted"></a>암호화됨 

CENC 암호화를 사용하여 콘텐츠를 암호화해야 하는 경우 정책을 'Predefined_MultiDrmCencStreaming'으로 설정합니다. Widevine 암호화는 DASH 스트림 및 PlayReady to Smooth에 적용됩니다. 키는 구성된 DRM 라이선스에 따라 재생 클라이언트에 전달됩니다.

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

CBCS(FairPlay)를 사용하여 HLS 스트림도 암호화하려면 'Predefined_MultiDrmStreaming'을 사용합니다.

> [!NOTE]
> Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="associate-filters-with-streaming-locators"></a>스트리밍 로케이터와 필터 연결

[필터: 스트리밍 로케이터와 연결](filters-concept.md#associating-filters-with-streaming-locator)을 참조하세요.

## <a name="filter-order-page-streaming-locator-entities"></a>필터, 순서, 페이지 스트리밍 로케이터 엔터티

[Media Services 엔터티 필터링, 순서 지정, 페이징](filter-order-page-entities-how-to.md)을 참조하세요.

## <a name="list-streaming-locators-by-asset-name"></a>자산 이름별 스트리밍 로케이터 나열

연결된 자산 이름을 기반으로 스트리밍 로케이터를 가져오려면 다음 작업을 수행합니다.

|언어|API|
|---|---|
|REST|[liststreaminglocators](/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](/cli/azure/ams/asset#az_ams_asset_list_streaming_locators)|
|.NET|[ListStreamingLocators](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>참고 항목

* [Assets](assets-concept.md)
* [스트리밍 정책](stream-streaming-policy-concept.md)
* [콘텐츠 키 정책](drm-content-key-policy-concept.md)
* [자습서: .NET을 사용하여 비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>다음 단계

[스트리밍 로케이터 및 빌드 URL을 만드는 방법](create-streaming-locator-build-url.md)
