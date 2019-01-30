---
title: Media Services의 자산 - Azure | Microsoft Docs
description: 이 문서에서는 자산이 무엇이고 Azure Media Services가 어떤 자산을 사용하는지 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/01/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8507d51f0d4d49d89fc24b38ed73df7488261daa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969578"
---
# <a name="assets"></a>자산

Azure Media Services의 [자산](https://docs.microsoft.com/rest/api/media/assets)에는 디지털 파일(비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 캡션 파일 포함)과 이러한 파일에 대한 메타데이터가 포함됩니다. 디지털 파일이 자산에 업로드되면 Media Services 콘텐츠 워크플로 인코딩, 스트리밍, 분석에 사용할 수 있습니다. 자세한 내용은 아래의 [자산에 디지털 파일 업로드](#upload-digital-files-into-assets) 섹션을 참조하세요.

자산은 [Azure Storage 계정](storage-account-concept.md)의 Blob 컨테이너에 매핑되고 자산의 파일은 해당 컨테이너에 블록 Blob으로 저장됩니다. Media Services는 계정이 범용 v2(GPv2) 스토리지를 사용할 때 Blob 계층을 지원합니다. GPv2를 사용하는 경우 파일을 [쿨 또는 보관 스토리지](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)로 이동할 수 있습니다. **보관** 스토리지는 더 이상 필요 없는 원본 파일을 보관하는 데 적합합니다(예: 인코딩된 후).

**보관** 스토리지 계층은 이미 인코딩되었고 인코딩 작업 출력이 출력 Blob 컨테이너에 배치된 대용량 원본 파일에만 사용할 것을 권장합니다. 자산과 연결하여 콘텐츠를 스트리밍 또는 분석하는 데 사용하려는 출력 컨테이너의 Blob은 **핫** 또는 **쿨** 스토리지 계층에 있어야 합니다.

## <a name="asset-definition"></a>자산 정의

다음 표에서는 자산의 속성을 표시하고 해당 정의를 제공합니다.

|이름|설명|
|---|---|
|id|리소스에 대한 정규화된 리소스 ID입니다.|
|이름|리소스의 이름입니다.|
|properties.alternateId |자산의 대체 ID입니다.|
|properties.assetId |자산 ID입니다.|
|properties.container |자산 Blob 컨테이너의 이름입니다.|
|properties.created |자산의 생성 날짜입니다.<br/> Datetime은 항상 UTC 형식입니다.|
|properties.description|자산 설명입니다.|
|properties.lastModified |사잔을 마지막으로 수정한 날짜입니다. <br/> Datetime은 항상 UTC 형식입니다.|
|properties.storageAccountName |저장소 계정 이름입니다.|
|properties.storageEncryptionFormat |자산 암호화 형식입니다. 없음 또는 MediaStorageEncryption 중 하나입니다.|
|형식|리소스 형식입니다.|

전체 정의는 [자산](https://docs.microsoft.com/rest/api/media/assets)을 참조하세요.

## <a name="upload-digital-files-into-assets"></a>자산에 디지털 파일 업로드

일반적인 Media Services 워크플로 중 하나는 파일 업로드, 인코딩 및 스트리밍입니다. 이 섹션에서는 일반 단계를 간략하게 설명합니다.

1. Media Services v3 API를 사용하여 새 "입력" 자산을 만듭니다. 이 작업은 Media Services 계정과 연결된 스토리지 계정에 컨테이너를 만듭니다. API는 컨테이너 이름을 반환합니다(예: `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    자산과 연결하려는 Blob 컨테이너가 이미 있는 경우 자산을 만들 때 컨테이너 이름을 지정할 수 있습니다. Media Services는 현재 컨테이너 루트의 Blob만 지원하고 파일 이름의 경로는 지원하지 않습니다. 따라서 파일 이름이 "input.mp4"인 컨테이너는 작동합니다. 하지만 파일 이름이 "videos/inputs/input.mp4"인 컨테이너는 작동하지 않습니다.

    Azure CLI를 사용하여 구독에서 권한이 있는 스토리지 계정 및 컨테이너에 직접 업로드할 수 있습니다. <br/>컨테이너 이름은 고유해야 하며 스토리지 명명 지침을 준수해야 합니다. 이름이 Media Services 자산 컨테이너 이름(자산-GUID) 서식을 따를 필요는 없습니다. 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. 읽기-쓰기 권한을 사용하여 디지털 파일을 자산 컨테이너에 업로드하는 데 사용할 SAS URL을 가져옵니다. Media Services API를 사용하여 [자산 컨테이너 URL을 나열](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)할 수 있습니다.
3. Azure Storage API 또는 SDK(예: [Storage REST API](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md) 또는 [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md))를 사용하여 파일을 자산 컨테이너에 업로드합니다. 
4. Media Services v3 API를 사용하여 "입력" 자산을 처리하는 Transform 및 Job을 만듭니다. 자세한 내용은 [Transform 및 Jobs](transform-concept.md)를 참조하세요.
5. "출력" 자산의 콘텐츠를 스트리밍합니다.

> [!TIP]
> 자산을 만들고, 쓰기 가능한 SAS URL을 스토리지의 자산 컨테이너로 가져오고, SAS URL을 사용하여 파일을 스토리지의 컨테이너에 업로드하는 방법을 보여주는 전체 .NET 예제는 [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)를 참조하세요.

### <a name="create-a-new-asset"></a>새 자산 만들기

#### <a name="rest"></a>REST (영문)

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

REST 예제는 [REST를 사용하여 자산 만들기](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) 예제를 참조하세요.

이 예제에서는 설명, 컨테이너 이름, 스토리지 계정, 기타 정보를 포함하여 유용한 정보를 지정할 수 있는 **요청 본문**을 만드는 방법을 보여줍니다.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

전체 예제는 [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)를 참조하세요. Media Services v3의 작업 입력은 HTTPS URL에서도 만들 수 있습니다([HTTPS URL에서 작업 입력 만들기](job-input-from-http-how-to.md) 참조).

## <a name="filtering-ordering-paging"></a>필터링, 정렬, 페이징

Media Services는 자산에 대한 다음 OData 쿼리 옵션을 지원합니다. 

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

다음 표에서는 자산 속성에 이러한 옵션을 적용하는 방법을 보여줍니다. 

|이름|Filter|순서|
|---|---|---|
|id|||
|이름|지원: Eq, Gt, Lt|지원: 오름차순 및 내림차순|
|properties.alternateId |지원: Eq||
|properties.assetId |지원: Eq||
|properties.container |||
|properties.created|지원: Eq, Gt, Lt| 지원: 오름차순 및 내림차순|
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

### <a name="pagination"></a>페이지 매김

네 개의 활성화된 정렬 순서 각각에 대해 페이지 매김이 지원됩니다. 현재 페이지 크기는 1000입니다.

> [!TIP]
> 항상 다음 링크를 사용하여 컬렉션을 열거하고, 특정 페이지 크기에 따라 달라지지 않아야 합니다.

쿼리 응답에 많은 항목이 포함된 경우 서비스에서 "\@odata.nextLink" 속성을 반환하여 결과의 다음 페이지를 가져옵니다. 전체 결과 집합을 통해 페이지에 사용할 수 있습니다. 페이지 크기는 구성할 수 없습니다. 

(해당 변경 내용이 다운로드되지 않은 컬렉션의 일부인 경우)컬렉션을 통해 페이징하는 동안 자산이 생성되거나 삭제되면 변경 내용이 반환된 결과에 반영됩니다. 

#### <a name="c-example"></a>C# 예제

다음 C# 예제에서는 계정의 모든 자산을 통해 열거하는 방법을 보여줍니다.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>REST 예제

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

## <a name="storage-side-encryption"></a>저장소 쪽 암호화

미사용 자산을 보호하려면 저장소 쪽 암호화를 사용하여 자산을 암호화해야 합니다. 다음 표는 Media Services에서 저장소 쪽 암호화가 작동하는 원리를 보여줍니다.

|암호화 옵션|설명|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services 저장소 암호화|AES-256 암호화, Media Services에서 키 관리|지원<sup>(1)</sup>|지원되지 않음<sup>(2)</sup>|
|[미사용 데이터에 대한 Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure Storage가 제공하는 서버 쪽 암호화, Azure 또는 고객이 키 관리|지원됨|지원됨|
|[저장소 클라이언트 쪽 암호화](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure Storage가 제공하는 클라이언트 쪽 암호화, Key Vault의 고객이 키 관리|지원되지 않음|지원되지 않음|

<sup>1</sup> Media Services가 깨끗한/어떠한 형태의 암호화도 없는 콘텐츠 처리를 지원하기는 하지만, 그렇게 하지 않는 것이 좋습니다.

<sup>2</sup> Media Services v3에서 저장소 암호화(AES-256 암호화)는 자산을 Media Services v2를 사용하여 만들었을 경우 이전 버전과의 호환성에 대해서만 지원됩니다. v3는 기존 저장소 암호화된 자산과 함께 작동하지만 새로 만들기를 허용하지는 않습니다.

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-dotnet-quickstart.md)

[Media Services v2와 v3의 차이점](migrate-from-v2-to-v3.md)
