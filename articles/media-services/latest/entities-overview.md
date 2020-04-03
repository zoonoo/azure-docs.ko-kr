---
title: 미디어 서비스 엔터티의 필터링, 순서 정렬 및 페이징
titleSuffix: Azure Media Services
description: Azure 미디어 서비스 v3 엔터티의 필터링, 순서 정렬 및 페이징에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7e4f1141a9d4bd58451782e8412063a22565556d
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584526"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>미디어 서비스 엔터티의 필터링, 순서 정렬 및 페이징

이 항목에서는 Azure Media Services v3 엔터티를 나열할 때 사용할 수 있는 OData 쿼리 옵션 및 페이지 매김 지원에 대해 설명합니다.

## <a name="considerations"></a>고려 사항

* `Datetime` 형식에 있는 엔터티의 속성은 항상 UTC 형식으로 되어 있습니다.
* 요청을 보내기 전에 쿼리 문자열의 공백을 URL 인코딩해야 합니다.

## <a name="comparison-operators"></a>비교 연산자

다음 연산자들을 사용하여 필드를 상수 값과 비교할 수 있습니다.

같음 연산자:

- `eq`: 필드가 상수 *값과 동일한지* 테스트합니다.
- `ne`: 필드가 상수 *값과 같지 않은지* 테스트합니다.

범위 연산자:

- `gt`: 필드가 상수 *값보다 큰지* 테스트합니다.
- `lt`: 필드가 상수 *값보다 작은지* 테스트합니다.
- `ge`: 필드가 상수 *값보다 크거나 같는지* 테스트합니다.
- `le`: 필드가 상수 *값보다 적거나 같는지* 테스트합니다.

## <a name="filter"></a>Assert

`$filter` OData 필터 매개 변수를 제공하여 관심 있는 개체만 찾습니다.

다음 REST 예제는 자산의 `alternateId` 값을 필터링합니다.

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

다음 C# 예제는 자산의 생성된 날짜를 필터링합니다.

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>에 의해 주문

반환된 개체를 지정된 매개 변수로 정렬하는 데 사용합니다. `$orderby` 예를 들어:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

결과를 오름차순 또는 내림차순으로 `asc` 정렬하려면 공백으로 구분된 필드 이름 또는 `desc` 필드 이름을 부가합니다. 예: `$orderby properties/created desc`

## <a name="skip-token"></a>토큰 건너뛰기

쿼리 응답에 많은 항목이 포함된 `$skiptoken` 경우`@odata.nextLink`서비스는 다음 결과 페이지를 얻는 데 사용하는 () 값을 반환합니다. 전체 결과 집합을 페이징하는 데 사용합니다.

미디어 서비스 v3에서는 페이지 크기를 구성할 수 없습니다. 페이지 크기는 엔터티 유형에 따라 다릅니다. 자세한 내용은 다음 의 개별 섹션을 참조하십시오.

컬렉션을 페이징하는 동안 엔터티가 만들어지거나 삭제되면 변경 내용이 반환된 결과에 반영됩니다(이러한 변경 내용이 다운로드되지 않은 컬렉션의 일부인 경우).

> [!TIP]
> 항상 `nextLink` 컬렉션을 등록하는 데 사용하며 특정 페이지 크기에 의존하지 마십시오.
>
> 엔터티페이지가 `nextLink` 두 개 이상인 경우에만 값이 표시됩니다.

사용되는 위치의 `$skiptoken` 다음 예제를 고려합니다. *amstestaccount*를 해당하는 계정 이름으로 바꾸고 *api-version* 값을 최신 버전으로 설정합니다.

다음과 같은 자산 목록을 요청하는 경우:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

이 것과 유사한 응답을 다시 받게 됩니다.

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

그 후 가져오기 요청을 보내서 다음 페이지를 요청합니다.

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

다음 C# 예제에서는 계정의 모든 스트리밍 로케이터를 통해 등록하는 방법을 보여 주었습니다.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>논리 연산자 를 사용하여 쿼리 옵션 결합

미디어 서비스 v3는 **OR** 및 **AND** 논리 연산자지원을 지원합니다. 

다음 REST 예제는 작업의 상태를 확인합니다.

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

다음과 같이 C#에서 동일한 쿼리를 생성합니다. 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>엔터티의 필터링 및 순서 정렬 옵션

다음 표에서는 필터링 및 순서 옵션을 다른 엔터티에 적용하는 방법을 보여 주며 다음과 같은 방법을 보여 주실 수 있습니다.

|엔터티 이름|속성 이름|Assert|주문|
|---|---|---|---|
|[자산](https://docs.microsoft.com/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` 및 `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` 및 `desc`|
|[콘텐츠 키 정책](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 및 `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 및 `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 및 `desc`|
||properties.policyId|`eq`, `ne`||
|[작업](https://docs.microsoft.com/rest/api/media/jobs)| name  | `eq`            | `asc` 및 `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` 및 `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` 및 `desc`| 
|[스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 및 `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` 및 `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 및 `desc`|
|[스트리밍 정책](https://docs.microsoft.com/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 및 `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` 및 `desc`|
|[변환](https://docs.microsoft.com/rest/api/media/transforms)| name | `eq`            | `asc` 및 `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` 및 `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` 및 `desc`|

## <a name="next-steps"></a>다음 단계

* [자산 목록](https://docs.microsoft.com/rest/api/media/assets/list)
* [콘텐츠 키 정책 목록](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [작업 목록](https://docs.microsoft.com/rest/api/media/jobs/list)
* [스트리밍 정책 목록](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [목록 스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [파일 스트리밍](stream-files-dotnet-quickstart.md)
* [할당량 및 제한](limits-quotas-constraints.md)
