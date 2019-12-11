---
title: Azure Media Services에서 로케이터 스트리밍 Microsoft Docs
description: 이 문서에서는 스트리밍 로케이터가 무엇 인지와 Azure Media Services에서 사용 하는 방법에 대해 설명 합니다.
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
ms.openlocfilehash: a227c5d0edfb9b49e70452cfcfd08b29ba381857
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969109"
---
# <a name="streaming-locators"></a>Streamelési lokátor

Ahhoz, hogy a kimeneti adategységben található videók elérhetők legyenek az ügyfelek számára lejátszásra, létre kell hozni egy [streamelési lokátort](https://docs.microsoft.com/rest/api/media/streaminglocators), majd streamelési URL-címeket. URL을 작성 하려면 스트리밍 끝점 호스트 이름 및 스트리밍 로케이터 경로를 연결 해야 합니다. Egy .NET-példáért tekintse meg a [streamelési lokátor beszerzését](stream-files-tutorial-with-api.md#get-a-streaming-locator) bemutató cikket.

**스트리밍 로케이터** 를 만드는 과정을 게시 라고 합니다. 기본적으로 **스트리밍 로케이터** 는 선택적 시작 및 종료 시간을 구성 하지 않는 한 API 호출을 수행한 직후에 유효 하며 삭제 될 때까지 지속 됩니다. 

**스트리밍 로케이터**를 만들 때 **자산** 이름과 **스트리밍 정책** 이름을 지정 해야 합니다. További információkért tekintse át a következők témaköröket:

* [Adategységek](assets-concept.md)
* [Streamelési szabályzatok](streaming-policy-concept.md)
* [Tartalomkulcs-szabályzatok](content-key-policy-concept.md)

사용자가 이러한 시간 사이에 콘텐츠를 재생 하도록 허용 하는 스트리밍 로케이터 시작 및 종료 시간을 지정할 수도 있습니다 (예: 5/1/2019 ~ 5/5/2019).  

## <a name="considerations"></a>Megfontolandó szempontok

* **스트리밍 로케이터** 는 업데이트할 수 없습니다. 
* Datetime 형식의 **스트리밍 로케이터** 속성은 항상 UTC 형식입니다.
* 미디어 서비스 계정에 대해 제한 된 정책 집합을 디자인 하 고 동일한 옵션이 필요할 때마다 스트리밍 로케이터를 다시 사용 해야 합니다. 자세한 내용은 [할당량 및 제한 사항](limits-quotas-constraints.md)을 참조 하세요.

## <a name="create-streaming-locators"></a>스트리밍 로케이터 만들기  

### <a name="not-encrypted"></a>암호화 되지 않음

암호화 되지 않은 상태로 파일을 스트리밍하려면 미리 정의 된 일반 스트리밍 정책: ' Predefined_ClearStreamingOnly '로 설정 합니다. .NET에서는 PredefinedStreamingPolicy를 사용 하 여 열거형을 사용할 수 있습니다.

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

### <a name="encrypted"></a>Titkosított 

CENC 암호화를 사용 하 여 콘텐츠를 암호화 해야 하는 경우 정책을 ' Predefined_MultiDrmCencStreaming '로 설정 합니다. Widevine 암호화가 대시 스트림에 적용 되 고 PlayReady가 자연스럽 게 적용 됩니다. 구성 된 DRM 라이선스에 따라 키가 재생 클라이언트에 전달 됩니다.

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

CBCS (FairPlay)를 사용 하 여 HLS 스트림을 암호화 하려는 경우에는 ' Predefined_MultiDrmStreaming '를 사용 합니다.

> [!NOTE]
> Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="associate-filters-with-streaming-locators"></a>스트리밍 로케이터를 사용 하 여 필터 연결

[필터: 스트리밍 로케이터 연결을](filters-concept.md#associating-filters-with-streaming-locator)참조 하세요.

## <a name="filter-order-page-streaming-locator-entities"></a>필터, 순서, 페이지 스트리밍 로케이터 엔터티

[Media Services 엔터티의 필터링, 순서 지정, 페이징을](entities-overview.md)참조 하세요.

## <a name="list-streaming-locators-by-asset-name"></a>자산 이름별 스트리밍 로케이터 나열

연결 된 자산 이름에 따라 스트리밍 로케이터를 가져오려면 다음 작업을 사용 합니다.

|Nyelv|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="also-see"></a>참고 항목

* [Adategységek](assets-concept.md)
* [Streamelési szabályzatok](streaming-policy-concept.md)
* [Tartalomkulcs-szabályzatok](content-key-policy-concept.md)

## <a name="next-steps"></a>Következő lépések

[자습서: .NET을 사용 하 여 비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
