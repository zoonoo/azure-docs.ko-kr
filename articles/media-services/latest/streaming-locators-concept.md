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
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: 5897b7df2460257784c40eb974c473573ec4003d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299160"
---
# <a name="streaming-locators"></a>스트리밍 로케이터

출력 자산의 비디오를 재생할 클라이언트에 사용할 수 있도록 하려면 [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만든 다음, 스트리밍 URL을 빌드해야 합니다. URL을 작성 하려면 스트리밍 끝점 호스트 이름 및 스트리밍 로케이터 경로 연결 해야 합니다. .NET 샘플은 [스트리밍 로케이터 가져오기](stream-files-tutorial-with-api.md#get-a-streaming-locator)를 참조하세요.

**스트리밍 로케이터**를 만드는 과정을 게시라고 합니다. 기본적으로 **스트리밍 로케이터**는 API 호출을 수행한 직후부터 유효하며, 선택적인 시작 및 종료 시간을 구성하지 않는 한 삭제될 때까지 지속됩니다. 

만들 때를 **스트리밍 로케이터**를 지정 해야 합니다는 **자산** 이름 및 **스트리밍 정책** 이름입니다. 자세한 내용은 다음 항목을 참조하십시오.

* [Assets](assets-concept.md)
* [스트리밍 정책](streaming-policy-concept.md)
* [콘텐츠 키 정책](content-key-policy-concept.md)

(5/1/2019 5/5/2019에) 간 예를 들어,이 시간 사이의 콘텐츠를 재생 하는 사용자 수만 있는 스트리밍 로케이터에 시작 및 종료 시간을 지정할 수 있습니다.  

## <a name="considerations"></a>고려 사항

* **스트리밍 로케이터** 을 업데이트할 수 없습니다. 
* 날짜/시간 형식의 **스트리밍 로케이터** 속성은 언제나 UTC 형식입니다.
* Media Services 계정에 대한 제한된 정책 세트를 설계하고 동일한 옵션이 필요할 때마다 스트리밍 로케이터에 해당 세트를 다시 사용하는 것이 좋습니다. 자세한 내용은 [할당량 및 제한 사항](limits-quotas-constraints.md)을 참조하세요.

## <a name="create-streaming-locators"></a>스트리밍 로케이터 만들기  

### <a name="not-encrypted"></a>암호화되지 않음

프로그램 파일에서-the-지우기 (암호화 되지 않은) 스트리밍 하려는 경우 미리 정의 된 clear 스트리밍 정책 설정: 'Predefined_ClearStreamingOnly'을 (.net에서 사용할 수 있습니다 PredefinedStreamingPolicy.ClearStreamingOnly 열거형)입니다.

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

CENC 암호화를 사용 하 여 콘텐츠를 암호화 해야 할 경우에 'Predefined_MultiDrmCencStreaming' 정책을 설정 합니다. Widevine 암호화 적용할 DASH 스트림을 및 PlayReady 부드러운 합니다. 키를 구성된 하는 DRM 라이선스에 따라 재생 클라이언트에 배달 됩니다.

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

또한 CBCS (FairPlay)를 사용 하 여 HLS 스트림을 암호화 하려면 'Predefined_MultiDrmStreaming'를 사용 합니다.

## <a name="associate-filters-with-streaming-locators"></a>스트리밍 로케이터를 사용 하 여 연결 필터

참조 [필터: 스트리밍 로케이터를 사용 하 여 연결](filters-concept.md#associating-filters-with-streaming-locator)합니다.

## <a name="filter-order-page-streaming-locator-entities"></a>필터, 순서 및 스트리밍 로케이터 엔터티 페이지

[Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)을 참조하세요.

## <a name="list-streaming-locators-by-asset-name"></a>자산 이름으로 스트리밍 Locator 나열

연결된 자산 이름을 기반으로 하는 스트리밍 로케이터를 가져오려면 다음 작업을 사용 합니다.

|언어|API|
|---|---|
|REST (영문)|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/assets?view=azure-node-latest#liststreaminglocators-string--string--string--object-)|

## <a name="also-see"></a>참고 항목

* [Assets](assets-concept.md)
* [스트리밍 정책](streaming-policy-concept.md)
* [콘텐츠 키 정책](content-key-policy-concept.md)

## <a name="next-steps"></a>다음 단계

[자습서: .NET](stream-files-tutorial-with-api.md)를 사용하여 비디오 업로드, 인코딩 및 스트림
