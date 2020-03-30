---
title: Azure 미디어 서비스의 자산 관리
titleSuffix: Azure Media Services
description: 업로드 또는 라이브 인제스트를 통해 미디어를 입력하고, 출력 미디어(작업 출력에서) 및 (스트리밍용)에서 미디어를 게시하는 자산입니다. 이 항목에서는 새 자산을 만들고 파일을 업로드하는 방법에 대한 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: fcdb8af770fa0068e8413d4609a56223a9a20ce2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345890"
---
# <a name="manage-assets"></a>자산 관리

Azure 미디어 서비스에서 [자산은](https://docs.microsoft.com/rest/api/media/assets) 

* 미디어 파일을 에셋에 업로드하고,
* 라이브 스트림을 에셋에 수집하고 보관하고,
* 분석 작업의 인코딩 결과를 자산에 출력하고,
* 스트리밍을 위한 미디어 게시, 
* 자산에서 파일을 다운로드할 수 있습니다.

이 항목에서는 에셋에 파일을 업로드하고 다른 일반적인 작업을 수행하는 방법에 대한 개요를 제공합니다. 또한 코드 샘플 및 관련 항목에 대한 링크도 제공합니다.

## <a name="prerequisite"></a>필수 요소 

개발을 시작하기 전에 다음 사항을 검토합니다.

* [개념](concepts-overview.md)
* [Media Services v3 API로 개발](media-services-apis-overview.md)(API 액세스, 명명 규칙 등에 대한 정보 포함) 

## <a name="upload-media-files-into-an-asset"></a>미디어 파일을 에셋에 업로드

디지털 파일을 저장소에 업로드하고 에셋과 연결한 후 미디어 서비스 인코딩, 스트리밍 및 콘텐츠 워크플로우 분석에 사용할 수 있습니다. 일반적인 Media Services 워크플로 중 하나는 파일 업로드, 인코딩 및 스트리밍입니다. 이 섹션에서는 일반 단계를 간략하게 설명합니다.

1. Media Services v3 API를 사용하여 새 "입력" 자산을 만듭니다. 이 작업은 Media Services 계정과 연결된 스토리지 계정에 컨테이너를 만듭니다. API는 컨테이너 이름을 반환합니다(예: `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    자산과 연결하려는 Blob 컨테이너가 이미 있는 경우 자산을 만들 때 컨테이너 이름을 지정할 수 있습니다. Media Services는 현재 컨테이너 루트의 Blob만 지원하고 파일 이름의 경로는 지원하지 않습니다. 따라서 파일 이름이 "input.mp4"인 컨테이너는 작동합니다. 그러나 "비디오/입력/input.mp4" 파일 이름이 있는 컨테이너는 작동 하지 않습니다.

    Azure CLI를 사용하여 구독에서 권한이 있는 스토리지 계정 및 컨테이너에 직접 업로드할 수 있습니다.

    컨테이너 이름은 고유해야 하며 스토리지 명명 지침을 준수해야 합니다. 이름이 Media Services 자산 컨테이너 이름(자산-GUID) 서식을 따를 필요는 없습니다.

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. 읽기-쓰기 권한을 사용하여 디지털 파일을 자산 컨테이너에 업로드하는 데 사용할 SAS URL을 가져옵니다.

    Media Services API를 사용하여 [자산 컨테이너 URL을 나열](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)할 수 있습니다.

    **AssetContainerSas.listContainerSas는** 설정 `expiryTime`한 [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) 매개 변수를 사용합니다. 시간은 24시간 < 설정해야 합니다.

    [ListContainerSasInput각](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) 저장소 계정에 대해 두 개의 저장소 계정 키가 있기 때문에 여러 SAS URL을 반환합니다. 저장소 계정에는 저장소 계정 키를 원활하게 회전할 수 있기 때문에 두 개의 키가 있습니다(예: 다른 키를 사용하는 동안 하나를 변경한 다음 새 키를 사용하여 시작하고 다른 키를 회전). 첫 번째 SAS URL은 저장소 키1 및 두 번째 저장소 키2를 나타냅니다.
3. Azure 저장소 API 또는 SDK(예: [저장소 REST API](../../storage/common/storage-rest-api-auth.md) 또는 [.NET SDK)를](../../storage/blobs/storage-quickstart-blobs-dotnet.md)사용하여 자산을 자산 컨테이너에 업로드합니다.
4. Media Services v3 API를 사용하여 "입력" 자산을 처리하는 Transform 및 Job을 만듭니다. 자세한 내용은 [변환 및 작업](transform-concept.md)을 참조하십시오.
5. "출력" 자산의 콘텐츠를 스트리밍합니다.

### <a name="create-a-new-asset"></a>새 자산 만들기

> [!NOTE]
> Datetime 형식의 에셋의 속성은 항상 UTC 형식으로 되어 있습니다.

#### <a name="rest"></a>REST (영문)

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

REST 예제는 [REST를 사용하여 자산 만들기](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) 예제를 참조하세요.

이 예제에서는 설명, 컨테이너 이름, 저장소 계정 및 기타 유용한 정보를 지정할 수 있는 **요청 본문을** 만드는 방법을 보여 주며 있습니다.

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

### <a name="see-also"></a>참조

* [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)
* [HTTPS URL에서 작업 입력 만들기](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>실시간 스트림을 자산에 인제스트 하고 보관

미디어 서비스에서 [라이브 출력](https://docs.microsoft.com/rest/api/media/liveoutputs) 개체는 라이브 스트림을 미디어 서비스 계정의 자산에 포착하고 기록하는 디지털 비디오 레코더와 같습니다. 기록된 콘텐츠는 [자산](https://docs.microsoft.com/rest/api/media/assets) 리소스에 의해 정의된 컨테이너에 유지됩니다.

자세한 내용은 다음을 참조하세요.

* [클라우드 DVR 사용](live-event-cloud-dvr.md)
* [스트리밍 라이브 자습서](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>작업 결과를 자산에 출력합니다.

Media Services에서 비디오를 처리할 때(예: 인코딩 또는 분석) [작업](transforms-jobs-concept.md)결과를 저장하기 위해 출력 [자산을](assets-concept.md) 만들어야 합니다.

자세한 내용은 다음을 참조하세요.

* [비디오 인코딩](encoding-concept.md)
* [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>스트리밍을 위한 에셋 게시

스트리밍에 대 한 자산을 게시 하려면 [스트리밍 로케이터를](streaming-locators-concept.md)만들어야 합니다. 스트리밍 로케이터는 게시할 자산 이름을 알아야 합니다. 

자세한 내용은 다음을 참조하세요.

[자습서: 미디어 서비스 v3를 통해 비디오를 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>출력 자산에서 작업 결과 다운로드

그런 다음 미디어 서비스 및 저장소 API를 사용하여 작업의 이러한 결과를 로컬 폴더에 다운로드할 수 있습니다. 

다운로드 [파일](download-results-howto.md) 예제를 참조하십시오.

## <a name="filtering-ordering-paging"></a>필터링, 정렬, 페이징

[Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

업로드, 인코딩, 분석, 실시간 스트리밍 및 주문형 스트리밍 방법을 보여 주는 전체 코드 예제를 참조하세요. 

* [자바](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [나머지](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
