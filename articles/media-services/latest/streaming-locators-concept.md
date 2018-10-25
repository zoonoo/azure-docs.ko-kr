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
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: 56e9a0b4eec347d2367c38ab00c6c9d9dca60752
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986899"
---
# <a name="streaming-locators"></a>스트리밍 로케이터

인코딩된 비디오 또는 오디오 파일을 재생하는 데 사용할 수 있는 URL을 클라이언트에 제공하려면 [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만들고 스트리밍 URL을 작성해야 합니다. 자세한 내용은 [파일 스트리밍](stream-files-dotnet-quickstart.md)을 참조하세요.

## <a name="streaminglocator-definition"></a>StreamingLocator 정의

다음 표에는 StreamingLocator의 속성 및 해당 정의가 나와 있습니다.

|이름|type|설명|
|---|---|---|
|id |string|리소스에 대한 정규화된 리소스 ID입니다.|
|이름   |string|리소스의 이름입니다.|
|properties.alternativeMediaId  |string|이 스트리밍 로케이터의 대체 미디어 ID입니다.|
|properties.assetName   |string|자산 이름입니다.|
|properties.contentKeys |StreamingLocatorContentKey[]|이 스트리밍 로케이터에서 사용하는 ContentKeys입니다.|
|properties.created |string|스트리밍 로케이터를 만든 시간입니다.|
|properties.defaultContentKeyPolicyName |string|이 스트리밍 로케이터에서 사용하는 ContentKeyPolicy의 기본 이름입니다.|
|properties.endTime |string|스트리밍 로케이터의 종료 시간입니다.|
|properties.startTime   |string|스트리밍 로케이터의 시작 시간입니다.|
|properties.streamingLocatorId  |string|스트리밍 로케이터의 StreamingLocatorId입니다.|
|properties.streamingPolicyName |string|이 스트리밍 로케이터에서 사용하는 스트리밍 정책의 이름입니다. 만든 스트리밍 정책의 이름을 지정하거나 미리 정의된 스트리밍 정책 중 하나를 사용합니다. 사용 가능한 미리 정의된 스트리밍 정책은 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' 및 'Predefined_MultiDrmStreaming'입니다.|
|형식   |string|리소스 형식입니다.|

전체 정의는 [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)를 참조하세요.

## <a name="filtering-ordering-paging"></a>필터링, 정렬, 페이징

Media Services에서 지원하는 스트리밍 로케이터에 대한 OData 쿼리 옵션은 다음과 같습니다. 

* $filter 
* $orderby 
* $top 
* $skiptoken 

연산자 설명:

* Eq = 같음
* Ne = 같지 않음
* Ge = 크거나 같음
* Le = 작거나 같음
* Gt = 보다 큼
* Lt = 보다 작음

### <a name="filteringordering"></a>필터링/순서

다음 표에는 이러한 옵션을 StreamingLocator 속성에 적용하는 방법이 나와 있습니다. 

|이름|Filter|순서|
|---|---|---|
|id |||
|이름|Eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |Eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |Eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|형식   |||

### <a name="pagination"></a>페이지 매김

네 개의 활성화된 정렬 순서 각각에 대해 페이지 매김이 지원됩니다. 현재 페이지 크기는 10입니다.

> [!TIP]
> 항상 다음 링크를 사용하여 컬렉션을 열거하고, 특정 페이지 크기에 따라 달라지지 않아야 합니다.

쿼리 응답에 많은 항목이 포함된 경우 서비스에서 "\@odata.nextLink" 속성을 반환하여 결과의 다음 페이지를 가져옵니다. 전체 결과 집합을 통해 페이지에 사용할 수 있습니다. 페이지 크기는 구성할 수 없습니다. 

컬렉션을 페이징하는 동안 StreamingLocators가 만들어지거나 삭제되면 변경 내용이 반환되는 결과에 반영됩니다(이러한 변경 내용이 다운로드되지 않은 컬렉션의 일부인 경우). 

다음 C# 예제에서는 계정의 모든 StreamingLocators를 열거하는 방법을 보여줍니다.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

REST 예제의 경우 [스트리밍 로케이터 - List](https://docs.microsoft.com/rest/api/media/streaminglocators/list)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-dotnet-quickstart.md)
