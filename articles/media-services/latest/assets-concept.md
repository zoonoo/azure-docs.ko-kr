---
title: 자산
titleSuffix: Azure Media Services
description: Azure Media Services에서 자산 및 사용 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3860823787b860f2504d6fb13b9479d1feec9d28
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505805"
---
# <a name="assets-in-azure-media-services"></a>Azure Media Services 자산

Azure Media Services [자산](https://docs.microsoft.com/rest/api/media/assets) 에는 Azure Storage에 저장 된 디지털 파일 (비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 닫힌 캡션 파일 포함)에 대 한 정보가 포함 되어 있습니다.

자산은 [Azure Storage 계정](storage-account-concept.md)의 Blob 컨테이너에 매핑되고 자산의 파일은 해당 컨테이너에 블록 Blob으로 저장됩니다. Media Services는 계정이 범용 v2(GPv2) 스토리지를 사용할 때 Blob 계층을 지원합니다. GPv2를 사용하는 경우 파일을 [쿨 또는 보관 스토리지](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)로 이동할 수 있습니다. **보관** 저장소는 인코딩된 후와 같이 더 이상 필요 하지 않은 경우 원본 파일을 보관 하는 데 적합 합니다.

**보관** 스토리지 계층은 이미 인코딩되었고 인코딩 작업 출력이 출력 Blob 컨테이너에 배치된 대용량 원본 파일에만 사용할 것을 권장합니다. 자산에 연결 하 여 콘텐츠를 스트리밍 또는 분석 하는 데 사용 하려는 출력 컨테이너의 blob은 **핫** 또는 **쿨** 저장소 계층에 있어야 합니다.

### <a name="naming"></a>이름 지정 

#### <a name="assets"></a>자산

자산의 이름은 고유 해야 합니다. Media Services v3 리소스 이름 (예: 자산, 작업, 변환)에는 Azure Resource Manager 명명 제약 조건이 적용 됩니다. 자세한 내용은 [명명 규칙](media-services-apis-overview.md#naming-conventions)을 참조 하세요.

#### <a name="blobs"></a>Blob

자산 내의 파일/b a s 이름은 [blob 이름 요구 사항](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) 및 [NTFS 이름 요구 사항을](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)따라야 합니다. 이러한 요구 사항에 대 한 이유는 파일을 blob 저장소에서 로컬 NTFS 디스크로 복사 하 여 처리할 수 있다는 것입니다.

## <a name="upload-digital-files-into-assets"></a>자산에 디지털 파일 업로드

디지털 파일이 저장소에 업로드 되 고 자산과 연결 된 후에는 콘텐츠 워크플로 Media Services 인코딩, 스트리밍 및 분석에 사용할 수 있습니다. 일반적인 Media Services 워크플로 중 하나는 파일 업로드, 인코딩 및 스트리밍입니다. 이 섹션에서는 일반 단계를 간략하게 설명합니다.

> [!TIP]
> 개발을 시작 하기 전에 [Media Services V3 api를 사용 하 여 개발](media-services-apis-overview.md) (api에 액세스 하는 방법, 명명 규칙 등)을 검토 합니다.

1. Media Services v3 API를 사용하여 새 "입력" 자산을 만듭니다. 이 작업은 Media Services 계정과 연결된 스토리지 계정에 컨테이너를 만듭니다. API는 컨테이너 이름을 반환합니다(예: `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    자산과 연결 하려는 blob 컨테이너가 이미 있는 경우 자산을 만들 때 컨테이너 이름을 지정할 수 있습니다. Media Services는 현재 컨테이너 루트의 Blob만 지원하고 파일 이름의 경로는 지원하지 않습니다. 따라서 파일 이름이 "input.mp4"인 컨테이너는 작동합니다. 그러나 "비디오/입력/입력. mp4" 파일 이름이 있는 컨테이너는 작동 하지 않습니다.

    Azure CLI를 사용하여 구독에서 권한이 있는 스토리지 계정 및 컨테이너에 직접 업로드할 수 있습니다.

    컨테이너 이름은 고유해야 하며 스토리지 명명 지침을 준수해야 합니다. 이름이 Media Services 자산 컨테이너 이름(자산-GUID) 서식을 따를 필요는 없습니다.

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. 읽기-쓰기 권한을 사용하여 디지털 파일을 자산 컨테이너에 업로드하는 데 사용할 SAS URL을 가져옵니다. Media Services API를 사용하여 [자산 컨테이너 URL을 나열](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)할 수 있습니다.
3. Azure Storage Api 또는 Sdk (예: [저장소 REST API](../../storage/common/storage-rest-api-auth.md) 또는 [.net SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md))를 사용 하 여 자산 컨테이너에 파일을 업로드 합니다.
4. Media Services v3 API를 사용하여 "입력" 자산을 처리하는 Transform 및 Job을 만듭니다. 자세한 내용은 [Transform 및 Jobs](transform-concept.md)를 참조하세요.
5. "출력" 자산의 콘텐츠를 스트리밍합니다.

자산을 만들고, 저장소에서 자산의 컨테이너에 쓸 수 있는 SAS URL을 가져오고, SAS URL을 사용 하 여 저장소의 컨테이너에 파일을 업로드 하는 방법을 보여 주는 전체 .NET 예제는 [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)를 참조 하세요.

### <a name="create-a-new-asset"></a>새 자산 만들기

> [!NOTE]
> Datetime 형식의 자산 속성은 항상 UTC 형식입니다.

#### <a name="rest"></a>REST (영문)

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

REST 예제는 [REST를 사용하여 자산 만들기](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) 예제를 참조하세요.

이 예제에서는 설명, 컨테이너 이름, 저장소 계정 및 기타 유용한 정보를 지정할 수 있는 **요청 본문** 을 만드는 방법을 보여 줍니다.

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

## <a name="map-v3-asset-properties-to-v2"></a>V2에 v3 자산 속성 매핑

다음 표에서는 v3의 [자산](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)속성이 v 2의 자산 속성에 매핑되는 방법을 보여 줍니다.

|v3 속성|v2 속성|
|---|---|
|`id`-(고유) 전체 Azure Resource Manager 경로 ( [자산의](https://docs.microsoft.com/rest/api/media/assets/createorupdate) 예제 참조)||
|`name`-(고유) [명명 규칙](media-services-apis-overview.md#naming-conventions) 참조 ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`-(고유) 값은 `nb:cid:UUID:` 접두사로 시작 합니다.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (생성 옵션)|
|`type`||

## <a name="storage-side-encryption"></a>스토리지 쪽 암호화

미사용 자산을 보호하려면 스토리지 쪽 암호화를 사용하여 자산을 암호화해야 합니다. 다음 표는 Media Services에서 스토리지 쪽 암호화가 작동하는 원리를 보여줍니다.

|암호화 옵션|Description|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services 스토리지 암호화|AES-256 암호화, Media Services에서 관리 하는 키입니다.|지원<sup>(1)</sup>|지원되지 않음<sup>(2)</sup>|
|[미사용 데이터에 대한 Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure Storage에서 제공 하는 서버 쪽 암호화, Azure 또는 고객이 관리 하는 키입니다.|지원됨|지원됨|
|[스토리지 클라이언트 쪽 암호화](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure storage에서 제공 하는 클라이언트 쪽 암호화는 Key Vault에서 고객이 관리 하는 키입니다.|지원되지 않음|지원되지 않음|

<sup>1</sup> Media Services는 암호화 형식 없이 clear/의 콘텐츠 처리를 지원 하지만 그렇게 하지 않는 것이 좋습니다.

<sup>2</sup> Media Services v3에서 스토리지 암호화(AES-256 암호화)는 자산을 Media Services v2를 사용하여 만들었을 경우 이전 버전과의 호환성에 대해서만 지원됩니다. 즉, v3은 기존 storage 암호화 된 자산과 함께 작동 하지만 새로 만들 수는 없습니다.

## <a name="filtering-ordering-paging"></a>필터링, 정렬, 페이징

[Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [파일 스트리밍](stream-files-dotnet-quickstart.md)
* [클라우드 DVR 사용](live-event-cloud-dvr.md)
* [Media Services v2와 v3의 차이점](migrate-from-v2-to-v3.md)
