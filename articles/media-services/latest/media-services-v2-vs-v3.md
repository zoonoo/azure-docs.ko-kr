---
title: Azure 미디어 서비스 v2에서 v3로 마이그레이션
description: 이 문서에서는 Azure Media Services v3에서 도입된 변경 내용을 설명하고 두 버전 간의 차이점을 보여 줍니다.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, 스트림, 브로드캐스트, 라이브, 오프라인
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: fd094e35ceaa718ec1b258d74106b39744cbd16f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087827"
---
# <a name="media-services-v2-vs-v3"></a>미디어 서비스 v2 vs. v3

이 문서에서는 Azure Media Services v3에서 도입된 변경 내용을 설명하고 두 버전 간의 차이점을 보여 줍니다.

## <a name="general-changes-from-v2"></a>v2의 일반적인 변경 사항

* v3로 만든 자산의 경우 Media Services는 [Azure Storage 서버 측 저장소 암호화만](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)지원합니다.
    * Media Services에서 제공하는 [스토리지 암호화](../previous/media-services-rest-storage-encryption.md)(AES 256)를 사용하는 v2 API로 만든 자산에 v3 API를 사용할 수 있습니다.
    * v3 API를 사용하는 레거시 AES 256 [스토리지 암호화](../previous/media-services-rest-storage-encryption.md)로는 새 자산을 만들 수 없습니다.
* v3의 [에셋의](assets-concept.md)속성은 v2와 다르며 [속성이 어떻게 매핑되는지](#map-v3-asset-properties-to-v2)확인하십시오.
* v3 SDK가 Storage SDK에서 분리되었으며, 따라서 사용하고 싶은 Storage SDK를 보다 철저하게 제어하고 버전 문제를 피할 수 있게 되었습니다. 
* v3 API에서 모든 인코딩 비트 전송률은 비트/초입니다. 이것은 v2 Media Encoder Standard 미리 설정과 다른 점입니다. 예를 들어 v2의 비트 전송률은 128(kbps)로 지정되지만 v3에서는 128000(비트/초)이 됩니다. 
* Entities AssetFiles, AccessPolicies 및 IngestManifests는 v3에 없습니다.
* v3에서 IAsset.ParentAssets 속성이 없습니다.
* ContentKeys는 더 이상 엔터티가 아닌 스트리밍 로케이터의 속성입니다.
* Event Grid 지원은 NotificationEndpoints를 대체합니다.
* 다음 엔터티의 이름이 바뀌었습니다.
    * Job Output은 Task를 대체하며, 이제 Job의 일부입니다.
    * Streaming Locator는 Locator를 대체합니다.
    * Live Event는 Channel을 대체합니다.<br/>라이브 이벤트 요금은 라이브 채널 미터를 기반으로 청구됩니다. 자세한 내용은 [대금 청구](live-event-states-billing.md) 및 [가격 책정](https://azure.microsoft.com/pricing/details/media-services/)을 참조하세요.
    * Live Output은 Program을 대체합니다.
* 라이브 출력은 생성과 동시에 시작되고 삭제되면 중지됩니다. v2 API에서는 프로그램이 다르게 작동했습니다. 생성 후 시작해야 했습니다.
* 작업에 대한 정보를 얻으려면 작업이 만들어진 변환 이름을 알아야 합니다. 
* v2에서 XML [입력](../previous/media-services-input-metadata-schema.md) 및 [출력](../previous/media-services-output-metadata-schema.md) 메타데이터 파일은 인코딩 작업의 결과로 생성됩니다. v3에서는 메타데이터 형식이 XML에서 JSON으로 변경되었습니다. 
* 미디어 서비스 v2에서 초기화 벡터(IV)를 지정할 수 있습니다. 미디어 서비스 v3에서는 페어플레이 IV를 지정할 수 없습니다. 패키징 및 라이선스 제공에 미디어 서비스를 사용하는 고객에게는 영향을 미치지 않지만 타사 DRM 시스템을 사용하여 FairPlay 라이선스(하이브리드 모드)를 제공할 때 문제가 될 수 있습니다. 이 경우 FairPlay IV는 cbcs 키 ID에서 파생되며 이 수식을 사용하여 검색할 수 있습니다.

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    다음과 같이 바꿉니다.

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    자세한 내용은 라이브 [및 VOD 연산 모두에 대한 하이브리드 모드에서 미디어 서비스 v3에 대한 Azure Functions C# 코드를](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3)참조하십시오.
 
> [!NOTE]
> [미디어 서비스 v3 리소스에](media-services-apis-overview.md#naming-conventions)적용되는 명명 규칙을 검토합니다. 또한 [이름 지정 Blob을 검토합니다.](assets-concept.md#naming)

## <a name="feature-gaps-with-respect-to-v2-apis"></a>v2 API와 관련된 기능 격차

v3 API는 v2 API와 관련하여 다음과 같은 기능 격차가 있습니다. 격차를 해소하기 위한 작업이 진행 중입니다.

* v3를 통해 [프리미엄 인코더](../previous/media-services-premium-workflow-encoder-formats.md) 및 레거시 [미디어 분석 프로세서](../previous/media-services-analytics-overview.md)(Azure Media Services Indexer 2 미리 보기, Face Redactor 등)에 액세스할 수 없습니다.<br/>Media Indexer 1 또는 2 미리 보기에서 마이그레이션하려는 고객은 v3 API의 AudioAnalyzer 프리셋을 즉시 사용할 수 있습니다.  이 새 프리셋은 기존 Media Indexer 1 또는 2보다 많은 기능을 포함하고 있습니다. 
* [v2 API의 미디어 인코더 표준의](../previous/media-services-advanced-encoding-with-mes.md) 많은 고급 기능은 현재 다음과 같이 v3에서 사용할 수 없습니다.
  
    * 자산 연결
    * 오버레이
    * 자르기
    * 썸네일 스프라이트
    * 입력에 오디오가 없는 경우 무음 오디오 트랙 삽입
    * 입력에 비디오가 없는 경우 비디오 트랙 삽입
* 코드 변환을 사용하는 라이브 이벤트는 현재 슬레이트 삽입 중간 스트림 및 API 호출을 통한 광고 표시기 삽입을 지원하지 않습니다. 
 
## <a name="asset-specific-changes"></a>자산별 변경 사항

### <a name="map-v3-asset-properties-to-v2"></a>v3 자산 속성을 v2로 매핑

다음 표에서는 v3의 에셋 속성이 v2의 에셋 속성에 어떻게 매핑되는지 보여 주며, 이 에셋의 속성은 v2에 있는 [에셋의](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)속성에 어떻게 매핑되는지 보여줍니다.

|v3 속성|v2 속성|
|---|---|
|`id`- (고유) 전체 Azure 리소스 관리자 경로, [자산의](https://docs.microsoft.com/rest/api/media/assets/createorupdate) 예제 참조||
|`name`- (고유) [이름 지정 규칙](media-services-apis-overview.md#naming-conventions) 참조 ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`- (고유) 값은 `nb:cid:UUID:` 접두사로 시작합니다.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options`(생성 옵션)|
|`type`||

### <a name="storage-side-encryption"></a>스토리지 쪽 암호화

미사용 자산을 보호하려면 스토리지 쪽 암호화를 사용하여 자산을 암호화해야 합니다. 다음 표는 Media Services에서 스토리지 쪽 암호화가 작동하는 원리를 보여줍니다.

|암호화 옵션|설명|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services 스토리지 암호화|AES-256 암호화, 미디어 서비스에서 관리하는 키.|지원<sup>(1)</sup>|지원되지 않음<sup>(2)</sup>|
|[미사용 데이터에 대한 Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure 저장소에서 제공하는 서버 측 암호화, Azure 또는 고객이 관리하는 키입니다.|지원됨|지원됨|
|[스토리지 클라이언트 쪽 암호화](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure 저장소에서 제공하는 클라이언트 측 암호화로 키 볼트에서 고객이 관리하는 키입니다.|지원되지 않음|지원되지 않음|

<sup>1</sup> 미디어 서비스는 명확한/암호화 없이 콘텐츠 처리를 지원하지만, 그렇게 하는 것은 권장되지 않습니다.

<sup>2</sup> Media Services v3에서 스토리지 암호화(AES-256 암호화)는 자산을 Media Services v2를 사용하여 만들었을 경우 이전 버전과의 호환성에 대해서만 지원됩니다. 즉 v3는 기존 저장소 암호화 된 자산과 함께 작동하지만 새 자산을 만들 수는 없습니다.

## <a name="code-differences"></a>코드 차이점

다음 표는 일반적인 시나리오에서 v2와 v3의 코드가 어떻게 다른지 보여줍니다.

|시나리오|V2 API|V3 API|
|---|---|---|
|자산 만들기 및 파일 업로드 |[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|작업 제출|[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>먼저 변환을 만든 후 작업을 제출하는 방법을 보여줍니다.|
|AES 암호화를 사용하여 자산 게시 |1. 콘텐츠 만들기키권한부여정책옵션<br/>2. 콘텐츠 키 권한 부여 정책 만들기<br/>3. 자산 전달 정책 만들기<br/>4. 에셋 생성 및 콘텐츠 업로드 또는 작업 제출 및 출력 자산 사용<br/>5. 자산 전달 정책과 자산 연결<br/>6. 콘텐츠 만들기키<br/>7. 에셋에 콘텐츠키 첨부<br/>8. 액세스 정책 만들기<br/>9. 로케이터 만들기<br/><br/>[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. 콘텐츠 키 정책 만들기<br/>2. 에셋 만들기<br/>3. 콘텐츠를 업로드하거나 에셋을 작업 출력으로 사용합니다.<br/>4. 스트리밍 로케이터 만들기<br/><br/>[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|작업 세부 정보 가져오기 및 작업 관리 |[v2로 작업 관리](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[v3로 작업 관리](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> 이 문서에 책갈피를 지정하고 업데이트를 계속 확인하세요.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Media Services v2에서 v3로 이동하기 위한 마이그레이션 지침](migrate-from-v2-to-v3.md)
