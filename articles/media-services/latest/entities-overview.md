---
title: V3 Api-Azure 사용 하 여 개발 | Microsoft Docs
description: 이 문서에서는 Media Services v3을 사용 하 여 개발 하는 경우 엔터티 및 Api에 적용 되는 규칙을 설명 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a5ab0b25a2a2db764854982b1a6801ce4f857dda
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891959"
---
# <a name="developing-with-media-services-v3-apis"></a>V3 Api를 Media Services를 사용 하 여 개발

이 문서에서는 Media Services v3을 사용 하 여 개발 하는 경우 엔터티 및 Api에 적용 되는 규칙을 설명 합니다.

## <a name="naming-conventions"></a>명명 규칙

Azure Media Services v3 리소스 이름(예: 자산, 작업, 변환)은 Azure Resource Manager 명명 제약 조건에 따라 달라집니다. Azure Resource Manager에 따라 리소스 이름은 항상 고유합니다. 따라서 리소스 이름에 대해 고유 식별자 문자열(예: GUID)을 사용할 수 있습니다. 

Media Services 리소스 이름에는 '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', 작은 따옴표 또는 제어 문자가 포함될 수 없습니다. 다른 문자를 모두 허용합니다. 리소스 이름의 최대 길이는 260자입니다. 

Azure Resource Manager 이름 지정에 대한 자세한 내용은 [명명 요구 사항](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) 및 [명명 규칙](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.

## <a name="v3-api-design-principles"></a>v3 API 디자인 원칙

v3 API의 핵심 디자인 원칙 중 하나는 API를 더 안전하게 만드는 것입니다. v3 API는 **가져오기** 또는 **나열** 작업에서 비밀 또는 자격 증명을 반환하지 않습니다. 키는 항상 null이거나, 비어 있거나, 응답에서 삭제됩니다. 비밀 또는 자격 증명을 가져오는 별도 작업 메서드를 호출해야 합니다. 다른 API와 달리 일부 API가 비밀을 검색/표시하는 경우에 별도 동작을 사용하면 다른 RBAC 보안 권한을 설정할 수 있습니다. RBAC를 사용하여 액세스를 관리하는 방법에 대한 정보는 [RBAC를 사용하여 액세스 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)를 참조하세요.

이 예제:

* StreamingLocator의 Get에서 ContentKey 값을 반환 하지 않습니다.
* 제한 키를 ContentKeyPolicy의 Get에서 반환 하지 않습니다.
* 에 대 한 URL (서명을 제거) 작업의 HTTP 입력 Url의 쿼리 문자열 부분을 반환 하지 않습니다.

[콘텐츠 키 정책 가져오기 - .NET](get-content-key-policy-dotnet-howto.md) 예제를 참조합니다.

## <a name="long-running-operations"></a>장기 실행 작업

작업 표시 `x-ms-long-running-operation` Azure Media services에서 [swagger 파일](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) 긴 작업을 실행 합니다. 

Azure 비동기 작업을 추적 하는 방법에 대 한 자세한 내용은 참조 하세요 [비동기 작업](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)합니다.

Media Services에는 다음과 같은 장기 실행 작업에 있습니다.

* LiveEvent 만들기
* LiveEvent 업데이트
* LiveEvent 삭제
* LiveEvent 시작
* LiveEvent 중지
* LiveEvent 다시 설정
* LiveOutput 만들기
* LiveOutput 삭제
* StreamingEndpoint 만들기
* StreamingEndpoint 업데이트
* StreamingEndpoint 삭제
* StreamingEndpoint 시작
* StreamingEndpoint 중지
* StreamingEndpoint 크기 조정

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services 엔터티 필터링, 순서 지정, 페이징

Media Services에서 지원하는 Media Services v3 엔터티에 대한 OData 쿼리 옵션은 다음과 같습니다. 

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

날짜/시간 형식의 엔터티 속성은 언제나 UTC 형식입니다.

### <a name="page-results"></a>페이지 결과

쿼리 응답에 많은 항목이 포함된 경우 서비스에서 "\@odata.nextLink" 속성을 반환하여 결과의 다음 페이지를 가져옵니다. 전체 결과 집합을 통해 페이지에 사용할 수 있습니다. 페이지 크기는 구성할 수 없습니다. 페이지 크기는 엔터티 형식에 따라 달라집니다. 자세한 내용은 다음 개별 섹션을 읽으세요.

(해당 변경 내용이 다운로드되지 않은 컬렉션의 일부인 경우)컬렉션을 통해 페이징하는 동안 엔터티가 생성되거나 삭제되면 변경 내용이 반환된 결과에 반영됩니다. 

> [!TIP]
> 항상 다음 링크를 사용하여 컬렉션을 열거하고, 특정 페이지 크기에 따라 달라지지 않아야 합니다.

### <a name="assets"></a>자산

#### <a name="filteringordering"></a>필터링/순서

다음 표에서는 [자산](https://docs.microsoft.com/rest/api/media/assets) 속성에 필터림 및 순서 지정 옵션을 적용하는 방법을 보여줍니다. 

|name|Filter|순서|
|---|---|---|
|id|||
|이름|eq, gt, lt| 오름차순 및 내림차순|
|properties.alternateId |eq||
|properties.assetId |eq||
|properties.container |||
|properties.created| eq, gt, lt| 오름차순 및 내림차순|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|형식|||

다음 C# 예제에서는 만든 날짜로 필터링합니다.

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

#### <a name="pagination"></a>페이지 매김 

네 개의 활성화된 정렬 순서 각각에 대해 페이지 매김이 지원됩니다. 현재 페이지 크기는 1000입니다.

##### <a name="c-example"></a>C# 예제

다음 C# 예제에서는 계정의 모든 자산을 통해 열거하는 방법을 보여줍니다.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

##### <a name="rest-example"></a>REST 예제

다음 예제에서 $skiptoken이 사용되는 위치를 잘 보세요. *amstestaccount*를 해당하는 계정 이름으로 바꾸고 *api-version* 값을 최신 버전으로 설정합니다.

다음과 같은 자산 목록을 요청하면:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

다음과 유사한 응답을 받게 됩니다.

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

그 후 가져오기 요청을 보내서 다음 페이지를 요청합니다.

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

더 많은 REST 예제는 [자산 - 목록](https://docs.microsoft.com/rest/api/media/assets/list)을 참조하세요.

### <a name="content-key-policies"></a>콘텐츠 키 정책

#### <a name="filteringordering"></a>필터링/순서

다음 표에는 이러한 옵션을 [콘텐츠 키 정책](https://docs.microsoft.com/rest/api/media/contentkeypolicies) 속성에 적용하는 방법이 나와 있습니다. 

|name|Filter|순서|
|---|---|---|
|id|||
|이름|eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.created |eq, ne, ge, le,  gt, lt|오름차순 및 내림차순|
|properties.description |eq, ne, ge, le, gt, lt||
|properties.lastModified|eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.options |||
|properties.policyId|eq, ne||
|형식|||

#### <a name="pagination"></a>페이지 매김

네 개의 활성화된 정렬 순서 각각에 대해 페이지 매김이 지원됩니다. 현재 페이지 크기는 10입니다.

다음 C# 예제에서는 계정의 모든 **콘텐츠 키 정책**을 열거하는 방법을 보여 줍니다.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

REST 예제는 [콘텐츠 키 정책 - List](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)를 참조하세요.

### <a name="jobs"></a>교육

#### <a name="filteringordering"></a>필터링/순서

다음 표에는 [작업](https://docs.microsoft.com/rest/api/media/jobs) 속성에 이러한 옵션을 적용하는 방법이 나와 있습니다. 

| name    | Filter                        | 순서 |
|---------|-------------------------------|-------|
| 이름                    | eq            | 오름차순 및 내림차순|
| properties.state        | eq, ne        |                         |
| properties.created      | gt, ge, lt, le| 오름차순 및 내림차순|
| properties.lastModified | gt, ge, lt, le | 오름차순 및 내림차순| 


#### <a name="pagination"></a>페이지 매김

작업 페이지 매김은 Media Services v3에서 지원됩니다.

다음 C# 예제에서는 계정의 모든 작업을 통해 열거하는 방법을 보여줍니다.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

REST 예제는 [작업 - 목록](https://docs.microsoft.com/rest/api/media/jobs/list)을 참조하세요.

### <a name="streaming-locators"></a>스트리밍 로케이터

#### <a name="filteringordering"></a>필터링/순서

다음 표에는 이러한 옵션을 StreamingLocator 속성에 적용하는 방법이 나와 있습니다. 

|name|Filter|순서|
|---|---|---|
|id |||
|이름|eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |eq, ne, ge, le,  gt, lt|오름차순 및 내림차순|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|형식   |||

#### <a name="pagination"></a>페이지 매김

네 개의 활성화된 정렬 순서 각각에 대해 페이지 매김이 지원됩니다. 현재 페이지 크기는 10입니다.

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

### <a name="streaming-policies"></a>스트리밍 정책

#### <a name="filteringordering"></a>필터링/순서

다음 표에는 이러한 옵션을 StreamingPolicy 속성에 적용하는 방법이 나와 있습니다. 

|name|Filter|순서|
|---|---|---|
|id|||
|이름|eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |eq, ne, ge, le,  gt, lt|오름차순 및 내림차순|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|형식|||

#### <a name="pagination"></a>페이지 매김

네 개의 활성화된 정렬 순서 각각에 대해 페이지 매김이 지원됩니다. 현재 페이지 크기는 10입니다.

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


### <a name="transform"></a>변환

#### <a name="filteringordering"></a>필터링/순서

다음 표에는 [변환](https://docs.microsoft.com/rest/api/media/transforms) 속성에 이러한 옵션을 적용하는 방법이 나와 있습니다. 

| name    | Filter                        | 순서 |
|---------|-------------------------------|-------|
| 이름                    | eq            | 오름차순 및 내림차순|
| properties.created      | gt, ge, lt, le| 오름차순 및 내림차순|
| properties.lastModified | gt, ge, lt, le | 오름차순 및 내림차순|

## <a name="next-steps"></a>다음 단계

[Stream 파일](stream-files-dotnet-quickstart.md)
