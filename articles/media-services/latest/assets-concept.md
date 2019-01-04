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
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f9a6f0963ce8f45da567bb4f6326e9fcc8f435ef
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140135"
---
# <a name="assets"></a>자산

**자산** 에는 디지털 파일(비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 캡션 파일 포함)과 이러한 파일에 대한 메타데이터가 포함됩니다. 디지털 파일은 자산에 업로드한 후 Media Services 인코딩 및 스트리밍 워크플로에서 사용할 수 있습니다.

자산은 [Azure Storage 계정](storage-account-concept.md)의 BLOB 컨테이너에 매핑되고 자산의 파일은 해당 컨테이너에 블록 BLOB으로 저장됩니다. Storage SDK 클라이언트를 사용하여 컨테이너의 자산 파일과 상호 작용할 수 있습니다.

Azure Media Services는 계정이 범용 v2(GPv2) 저장소를 사용할 때 Blob 계층을 지원합니다. GPv2를 사용하는 경우 파일을 서늘하거나 추운 저장소로 이동할 수 있습니다. 콜드 저장소는 더 이상 필요 없는 원본 파일을 보관하는 데 적합합니다(예: 인코딩된 후).

Media Services v3에서 작업 입력은 자산 또는 HTTP URL에서 만들 수 있습니다. 작업에 대한 입력으로 사용할 수 있는 자산을 만들려면 [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)를 참조하세요.

또한 [Media Services에서 저장소 계정](storage-account-concept.md)과 [변환 및 작업](transform-concept.md)에 대해 읽어보세요.

## <a name="asset-definition"></a>자산 정의

다음 표에서는 자산의 속성을 표시하고 해당 정의를 제공합니다.

|이름|설명|
|---|---|
|id|리소스에 대한 정규화된 리소스 ID입니다.|
|이름|리소스의 이름입니다.|
|properties.alternateId |자산의 대체 ID입니다.|
|properties.assetId |자산 ID입니다.|
|properties.container |자산 Blob 컨테이너의 이름입니다.|
|properties.created |자산의 생성 날짜입니다.|
|properties.description|자산 설명입니다.|
|properties.lastModified |사잔을 마지막으로 수정한 날짜입니다.|
|properties.storageAccountName |저장소 계정 이름입니다.|
|properties.storageEncryptionFormat |자산 암호화 형식입니다. 없음 또는 MediaStorageEncryption 중 하나입니다.|
|형식|리소스 형식입니다.|

전체 정의는 [자산](https://docs.microsoft.com/rest/api/media/assets)을 참조하세요.

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

다음 C# 예제에서는 계정의 모든 자산을 통해 열거하는 방법을 보여줍니다.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

나머지 예제는 [자산 - 목록](https://docs.microsoft.com/rest/api/media/assets/list)을 참조하세요.

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
