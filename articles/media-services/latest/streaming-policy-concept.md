---
title: Azure Media Services의 스트리밍 정책 | Microsoft Docs
description: 이 문서에서는 스트리밍 정책이란 무엇이며 Azure Media Services에서 스트리밍 정책을 사용하는 방법을 설명합니다.
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
ms.openlocfilehash: c5f441fef95989e5c82586d96fc6c10e00a9627c
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085498"
---
# <a name="streaming-policies"></a>스트리밍 정책

Azure Media Services v3에서 스트리밍 정책을 사용하여 StreamingLocators의 스트리밍 프로토콜 및 암호화 옵션을 정의할 수 있습니다. 만든 스트리밍 정책의 이름을 지정하거나 미리 정의된 스트리밍 정책 중 하나를 사용할 수 있습니다. 현재 사용 가능한 미리 정의된 스트리밍 정책은 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' 및 'Predefined_MultiDrmStreaming'입니다.

> [!IMPORTANT]
> 사용자 지정 [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies)를 사용하는 경우 Media Service 계정에 대해 이러한 정책을 제한적으로 설계하고 동일한 암호화 옵션 및 프로토콜이 필요할 때마다 StreamingLocator에 다시 사용해야 합니다. Media Service 계정에는 StreamingPolicy 항목의 수에 대한 할당량이 있습니다. 각 StreamingLocator에 대해 새 StreamingPolicy를 만들지 말아야 합니다.

## <a name="streamingpolicy-definition"></a>StreamingPolicy 정의

다음 표에는 StreamingPolicy의 속성 및 해당 정의가 나와 있습니다.

|이름|설명|
|---|---|
|id|리소스에 대한 정규화된 리소스 ID입니다.|
|이름|리소스의 이름입니다.|
|properties.commonEncryptionCbcs|CommonEncryptionCbcs 구성|
|properties.commonEncryptionCenc|CommonEncryptionCenc 구성|
|properties.created |스트리밍 정책을 만든 시간|
|properties.defaultContentKeyPolicyName |현재 스트리밍 정책에서 사용하는 기본 ContentKey|
|properties.envelopeEncryption  |EnvelopeEncryption 구성|
|properties.noEncryption|NoEncryption 구성|
|형식|리소스 형식입니다.|

전체 정의는 [스트리밍 정책](https://docs.microsoft.com/rest/api/media/streamingpolicies)을 참조하세요.

## <a name="filtering-ordering-paging"></a>필터링, 정렬, 페이징

Media Services에서 지원하는 스트리밍 정책에 대한 OData 쿼리 옵션은 다음과 같습니다. 

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

다음 표에는 이러한 옵션을 StreamingPolicy 속성에 적용하는 방법이 나와 있습니다. 

|이름|Filter|순서|
|---|---|---|
|id|||
|이름|Eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |Eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|형식|||

### <a name="pagination"></a>페이지 매김

네 개의 활성화된 정렬 순서 각각에 대해 페이지 매김이 지원됩니다. 현재 페이지 크기는 10입니다.

> [!TIP]
> 항상 다음 링크를 사용하여 컬렉션을 열거하고, 특정 페이지 크기에 따라 달라지지 않아야 합니다.

쿼리 응답에 많은 항목이 포함된 경우 서비스에서 "\@odata.nextLink" 속성을 반환하여 결과의 다음 페이지를 가져옵니다. 전체 결과 집합을 통해 페이지에 사용할 수 있습니다. 페이지 크기는 구성할 수 없습니다. 

컬렉션을 페이징하는 동안 StreamingPolicy가 만들어지거나 삭제되면 변경 내용이 반환되는 결과에 반영됩니다(이러한 변경 내용이 다운로드되지 않은 컬렉션의 일부인 경우). 

다음 C# 예제에서는 계정의 모든 StreamingPolicies를 열거하는 방법을 보여줍니다.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

REST 예제의 경우 [스트리밍 정책 - 목록](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-dotnet-quickstart.md)
