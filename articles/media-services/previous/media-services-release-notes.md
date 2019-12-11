---
title: Azure Media Services 릴리스 정보 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure Media Services v2 릴리스 정보에 대해 설명 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: d4c8dd4b3a53d484d5240f3514d171231fc93ae0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74968513"
---
# <a name="azure-media-services-release-notes"></a>Azure Media Services 릴리스 정보

Azure Media Services에 대 한 이러한 릴리스 정보는 이전 릴리스의 변경 내용과 알려진 문제를 요약 합니다.

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). 또한 [v2에서 v3로 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md) 을 참조 하세요.

사용자에 게 영향을 주는 문제를 해결 하는 데 집중할 수 있도록 고객의 의견을 듣고 싶습니다. 문제를 보고 하거나 질문 하려면 [Azure Media Services MSDN 포럼]에서 게시물을 제출 하세요. 

## <a name="a-idissuesknown-issues"></a>알려진 문제 <a id="issues"/>
### <a name="a-idgeneral_issuesmedia-services-general-issues"></a><a id="general_issues"/>Media Services 일반적인 문제

| Probléma | Leírás |
| --- | --- |
| REST API에는 몇 가지 일반적인 HTTP 헤더가 제공 되지 않습니다. |REST API를 사용 하 여 Media Services 응용 프로그램을 개발 하는 경우 몇 가지 일반적인 HTTP 헤더 필드 (클라이언트 요청 id, 요청 ID 및 반환-클라이언트 요청 ID 포함)가 지원 되지 않습니다. 헤더는 이후 업데이트에서 추가 될 예정입니다. |
| 퍼센트 인코딩은 허용 되지 않습니다. |Media Services는 스트리밍 콘텐츠의 Url을 빌드할 때 IAssetFile.Name 속성의 값을 사용 합니다 (예: `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`). 이러한 이유로 퍼센트 인코딩은 허용 되지 않습니다. Name 속성 값에는! * ' ();: @ & = + $,/?% # [] "와 같은 [퍼센트 인코딩 예약 문자](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)를 사용할 수 없습니다. 또한 파일 이름 확장명에는 "."가 하나만 있을 수 있습니다. |
| Azure Storage SDK 버전 3.x의 일부인 ListBlobs 메서드가 실패 합니다. |Media Services [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) 버전을 기반으로 SAS url을 생성 합니다. 저장소 SDK를 사용 하 여 blob 컨테이너의 blob을 나열 하려면 저장소 SDK 버전 2.x의 일부인 [CloudBlobContainer](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) 메서드를 사용 합니다. |
| Media Services 제한 메커니즘은 서비스에 과도 한 요청을 수행 하는 응용 프로그램에 대 한 리소스 사용을 제한 합니다. 서비스에서 "서비스를 사용할 수 없음" 503 HTTP 상태 코드를 반환할 수 있습니다. |자세한 내용은 [Media Services 오류 코드](media-services-encoding-error-codes.md)에서 503 HTTP 상태 코드에 대 한 설명을 참조 하세요. |
| 엔터티를 쿼리하면 공용 REST 버전 2가 쿼리 결과를 1000 결과로 제한 하므로 한 번에 1000 엔터티 제한이 반환 됩니다. |[이 .net 예제](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) 및 [이 REST API 예제](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)에 설명 된 대로 Skip 및 Take (.net)/top (REST)을 사용 합니다. |
| 일부 클라이언트는 부드러운 스트리밍 매니페스트에서 반복 태그 문제를 가져올 수 있습니다. |자세한 내용은 [이 섹션](media-services-deliver-content-overview.md#known-issues)을 참조하세요. |
| Media Services .NET SDK 개체는 직렬화 할 수 없으며, 결과적으로 Azure Cache for Redis에서 작동 하지 않습니다. |SDK AssetCollection 개체를 직렬화 하 여 Redis에 대 한 Azure 캐시에 추가 하려고 하면 예외가 throw 됩니다. |
|REST API는 자산 또는 계정 수준 필터를 가져오려고 할 때 "이 버전의 REST Api에서 필터에 액세스할 수 없습니다." 라는 오류 메시지와 함께 응답 합니다.|필터를 가져오는 데 사용 되는 것 보다 최신 API 버전으로 필터를 만들거나 수정 했습니다. 이는 고객이 사용 하는 코드 또는 도구에서 두 API 버전을 사용 하는 경우에 발생할 수 있습니다.  여기에서 가장 좋은 해결책은 최신 또는 두 개의 API 버전을 사용 하도록 코드를 업그레이드 하는 것입니다.|

## <a name="a-idrest_version_historyrest-api-version-history"></a><a id="rest_version_history"/>REST API 버전 기록
Media Services REST API 버전 기록에 대 한 자세한 내용은 [Azure Media Services REST API 참조]를 참조 하세요.

## <a name="september-2019"></a>2019. szeptember

### <a name="deprecation-of-media-processors"></a>미디어 프로세서의 사용 중단

*Azure Media Indexer* 및 *Azure Media Indexer 2 Preview*의 사용 중단을 발표 하 고 있습니다. [Azure Media Indexer](media-services-index-content.md) 미디어 프로세서는 2020 년 10 월 1 일에 사용 중지 됩니다. [Azure Media Indexer 2 Preview](media-services-process-content-with-indexer2.md) 미디어 프로세서는 2020 년 1 월 1 일에 사용 중지 됩니다. [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) 이러한 레거시 미디어 프로세서를 대체 합니다.

자세한 내용은 [Azure Media Indexer에서 마이그레이션 및 Azure Media Indexer 2에서 Azure Media Services Video Indexer로 마이그레이션](migrate-indexer-v1-v2.md)을 참조 하세요.

## <a name="august-2019"></a>2019. augusztus

### <a name="deprecation-of-media-processors"></a>미디어 프로세서의 사용 중단

2020 년 3 월 31 일에 사용이 중지 되는 wame ( *Windows Azure Media Encoder* ) 및 *Azure Media Encoder* (AME) 미디어 프로세서의 사용 중단을 발표 하 고 있습니다.

자세한 내용은 [WAME to Media Encoder Standard로 마이그레이션](https://go.microsoft.com/fwlink/?LinkId=2101334) 및 [AME를 Media Encoder Standard로 마이그레이션](https://go.microsoft.com/fwlink/?LinkId=2101335)을 참조 하세요.

## <a name="march-2019"></a>2019. március

Azure Media Services의 미디어 Hyperlapse 미리 보기 기능은 더 이상 사용 되지 않습니다.

## <a name="december-2018"></a>2018. december

Azure Media Services의 미디어 Hyperlapse 미리 보기 기능은 곧 사용이 중지 될 예정입니다. 2018 년 12 월 19 일부 터 Media Services는 더 이상 미디어 Hyperlapse를 변경 하거나 개선 하지 않습니다. 2019 년 3 월 29 일에는 사용이 중지 되 고 더 이상 사용할 수 없습니다.

## <a name="october-2018"></a>2018. október

### <a name="cmaf-support"></a>CMAF 지원

CMAF 및 ' cbcs ' 암호화 지원 Apple HLS (iOS 11 이상) 및 CMAF를 지 원하는 MPEG-대시 플레이어

### <a name="web-vtt-thumbnail-sprites"></a>웹 VTT 이미지 스프라이트

이제 Media Services를 사용 하 여 v2 Api를 사용 하 여 웹 VTT 축소판 그림 스프라이트를 생성할 수 있습니다. 자세한 내용은 [축소판 그림 생성 스프라이트](generate-thumbnail-sprite.md)를 참조 하세요.

## <a name="july-2018"></a>2018. július

최신 서비스 릴리스에서는 작업이 실패 하는 경우 두 개 이상의 줄로 분할 되는 방식에 따라 서비스에서 반환 되는 오류 메시지에 대 한 약간의 서식 변경 내용이 있습니다.

## <a name="may-2018"></a>2018. május 

2018 년 5 월 12 일부터 라이브 채널은 더 이상 RTP/MPEG-2 전송 스트림 수집 프로토콜을 지원 하지 않습니다. RTP/MPEG-2에서 RTMP 또는 조각난 MP4 (부드러운 스트리밍) 수집 프로토콜로 마이그레이션해야 합니다.

## <a name="october-2017-release"></a>2017. októberi kiadás
> [!IMPORTANT] 
> Media Services는 Azure Access Control Service 인증 키에 대 한 사용 중단 지원입니다. 2018 년 6 월 22 일에 Access Control Service 키를 사용 하 여 코드를 통해 Media Services 백 엔드에서 더 이상 인증할 수 없습니다. [AZURE ad 기반 인증](media-services-use-aad-auth-to-access-ams-api.md)마다 Azure Active Directory (azure ad)를 사용 하도록 코드를 업데이트 해야 합니다. Azure Portal에서이 변경에 대 한 경고를 봅니다.

### <a name="updates-for-october-2017"></a>10 월 2017에 대 한 업데이트
#### <a name="sdks"></a>SDK-k
* .NET SDK가 Azure AD 인증을 지원 하도록 업데이트 되었습니다. Azure AD로의 빠른 마이그레이션을 장려 하기 위해 Nuget.org의 최신 .NET SDK에서 Access Control Service 인증에 대 한 지원이 제거 되었습니다. 
* JAVA SDK가 Azure AD 인증을 지원 하도록 업데이트 되었습니다. Azure AD 인증에 대 한 지원이 Java SDK에 추가 되었습니다. Media Services에서 Java SDK를 사용 하는 방법에 대 한 자세한 내용은 [Azure Media Services java 클라이언트 SDK 시작](media-services-java-how-to-use.md) 을 참조 하세요.

#### <a name="file-based-encoding"></a>파일 기반 인코딩
* 이제 프리미엄 인코더를 사용 하 여 콘텐츠를 265 고해상도 비디오 코딩 (HEVC) 비디오 코덱으로 인코딩할 수 있습니다. 다른 코덱 (예: H. 264)에 대해 265를 선택 하는 경우 가격 책정에 영향을 주지 않습니다. HEVC 특허 라이선스에 대 한 자세한 내용은 [온라인 서비스 약관](https://azure.microsoft.com/support/legal/)을 참조 하세요.
* IOS11 또는 GoPro 주인공 6을 사용 하 여 캡처한 비디오와 같이 HEVC (265) 비디오 코덱으로 인코딩된 원본 비디오의 경우 이제 프리미엄 인코더 또는 표준 인코더를 사용 하 여 해당 비디오를 인코딩할 수 있습니다. 특허 라이선스에 대 한 자세한 내용은 [온라인 서비스 약관](https://azure.microsoft.com/support/legal/)을 참조 하세요.
* 여러 언어 오디오 트랙을 포함 하는 콘텐츠의 경우 언어 값은 해당 파일 형식 사양에 따라 올바른 레이블이 지정 되어야 합니다 (예: ISO MP4). 그런 다음 표준 인코더를 사용 하 여 스트리밍을 위한 콘텐츠를 인코딩할 수 있습니다. 결과 스트리밍 로케이터는 사용 가능한 오디오 언어를 나열 합니다.
* 이제 표준 인코더는 "AAC Audio" 및 "AAC 양호 음질 오디오" 라는 두 개의 새로운 오디오 전용 시스템 미리 설정을 지원 합니다. 각각 128 Kbps 및 192 Kbps의 비트 전송률로 스테레오 AAC (advanced audio 코딩) 출력을 생성 합니다.
* 이제 프리미엄 인코더는 입력으로 QuickTime/MOV 파일 형식을 지원 합니다. 비디오 코덱은 [이 GitHub 문서에 나열 된 Apple ProRes 유형](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats)중 하나 여야 합니다. 오디오는 AAC 또는 PCM (펄스 코드 변조) 중 하나 여야 합니다. 프리미엄 인코더는 입력으로 QuickTime/MOV 파일에 래핑된 DVC/DVCPro 비디오를 지원 하지 않습니다. 표준 인코더는 이러한 비디오 코덱을 지원 합니다.
* 인코더에서 다음과 같은 버그가 수정 되었습니다.

    * 이제 입력 자산을 사용 하 여 작업을 제출할 수 있습니다. 이러한 작업이 완료 되 면 자산을 수정할 수 있습니다 (예: 자산 내에서 파일 추가, 삭제 또는 이름 바꾸기). 그리고 추가 작업을 제출할 수 있습니다.
    * 표준 인코더에서 생성 한 JPEG 축소판의 품질이 향상 되었습니다.
    * 표준 인코더는 매우 짧은 기간 비디오에서 입력 메타 데이터 및 미리 보기 생성을 더 잘 처리 합니다.
    * 표준 인코더에 사용 된 h.264 디코더를 개선 하면 드물게 발생 하는 특정 아티팩트가 제거 됩니다. 

#### <a name="media-analytics"></a>Media Analytics
Azure Media Redactor의 일반 공급:이 미디어 프로세서는 선택한 개인의 얼굴을 흐리게 하 여 익명화를 수행 하 고, 공용 안전 및 뉴스 미디어 시나리오에서 사용 하기에 적합 합니다. 

이 새 프로세서에 대 한 개요는 [이 블로그 게시물](https://azure.microsoft.com/blog/azure-media-redactor/)을 참조 하세요. 설명서 및 설정에 대 한 자세한 내용은 [Azure 미디어 분석를 사용 하 여 얼굴](media-services-face-redaction.md)편집을 참조 하세요.



## <a name="june-2017-release"></a>6 월 2017 릴리스

Media Services은 이제 [AZURE AD 기반 인증](media-services-use-aad-auth-to-access-ams-api.md)을 지원 합니다.

> [!IMPORTANT]
> 현재 Media Services는 Access Control Service 인증 모델을 지원 합니다. Access Control Service 권한 부여는 2018 년 6 월 1 일부 터 더 이상 사용 되지 않습니다. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

## <a name="march-2017-release"></a>3 월 2017 릴리스

이제 표준 인코더를 사용 하 여 인코딩 작업을 만들 때 "적응 스트리밍" 사전 설정 문자열을 지정 하 여 [비트 전송률 사다리를 자동 생성할](media-services-autogen-bitrate-ladder-with-mes.md) 수 있습니다. Media Services 스트리밍을 위해 비디오를 인코딩하려면 "적응 스트리밍" 사전 설정을 사용 합니다. 특정 시나리오에 대 한 인코딩 사전 설정을 사용자 지정 하려면 [이러한 사전 설정](media-services-mes-presets-overview.md)으로 시작할 수 있습니다.

이제 Media Encoder Standard 또는 Media Encoder Premium Workflow를 사용 하 여 [fMP4 청크를 생성 하는 인코딩 작업을 만들](media-services-generate-fmp4-chunks.md)수 있습니다. 

## <a name="february-2017-release"></a>2 월 2017 릴리스

2017 년 4 월 1 일부 터, 계정에서 90 일 보다 오래 된 작업 레코드는 연결 된 태스크 레코드와 함께 자동으로 삭제 됩니다. 총 레코드 수가 최대 할당량 보다 낮은 경우에도 삭제가 발생 합니다. 작업/태스크 정보를 보관 하려면 [Media Services .NET SDK를 사용 하 여 자산 및 관련 엔터티 관리](media-services-dotnet-manage-entities.md)에 설명 된 코드를 사용할 수 있습니다.

## <a name="january-2017-release"></a>1 월 2017 릴리스

Media Services에서 스트리밍 끝점은 추가 배포를 위해 CDN (content delivery network) 또는 클라이언트 플레이어 응용 프로그램에 직접 콘텐츠를 배달할 수 있는 스트리밍 서비스를 나타냅니다. Media Services는 원활한 Azure Content Delivery Network 통합도 제공 합니다. StreamingEndpoint 서비스의 아웃 바운드 스트림은 라이브 스트림, 주문형 비디오 또는 Media Services 계정에 있는 자산의 점진적 다운로드 일 수 있습니다. 각 Media Services 계정에는 기본 스트리밍 끝점이 포함 됩니다. 계정으로 추가 스트리밍 끝점을 만들 수 있습니다. 

스트리밍 끝점에는 1.0 및 2.0의 두 가지 버전이 있습니다. 2017 년 1 월 10 일부 터 새로 만든 Media Services 계정에는 버전 2.0 기본 스트리밍 끝점이 포함 됩니다. 이 계정에 추가 하는 추가 스트리밍 끝점도 버전 2.0입니다. 이 변경 내용은 기존 계정에 영향을 주지 않습니다. 기존 스트리밍 끝점은 버전 1.0 이며 버전 2.0로 업그레이드할 수 있습니다. 이러한 변경으로 인 한 동작, 요금 청구 및 기능이 변경 되었습니다. További információk: [Streamvégpontok áttekintése](media-services-streaming-endpoints-overview.md).

2\.15 버전부터 스트리밍 끝점 엔터티에 다음 속성을 추가 Media Services.

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

이러한 속성에 대 한 자세한 내용은 [Streamingendpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)를 참조 하세요. 

## <a name="december-2016-release"></a>12 월 2016 릴리스

 이제 Media Services를 사용 하 여 해당 서비스에 대 한 원격 분석/메트릭 데이터에 액세스할 수 있습니다. 최신 버전의 Media Services 사용 하 여 라이브 채널, 스트리밍 끝점 및 보관 엔터티에 대 한 원격 분석 데이터를 수집할 수 있습니다. 자세한 내용은 [Media Services 원격 분석](media-services-telemetry-overview.md)을 참조 하세요.

## <a name="a-idjuly_changes16july-2016-release"></a><a id="july_changes16"/>7 월 2016 릴리스
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>매니페스트 파일 (*)을 업데이트 합니다. ISM) 인코딩 작업에 의해 생성 됨
인코딩 작업이 Media Encoder Standard 또는 미디어 인코더 프리미엄으로 전송 되는 경우 인코딩 작업은 출력 자산에 [스트리밍 매니페스트 파일](media-services-deliver-content-overview.md) (* ism)을 생성 합니다. 최신 서비스 릴리스와 함께이 스트리밍 매니페스트 파일의 구문이 업데이트 되었습니다.

> [!NOTE]
> 스트리밍 매니페스트 (ism) 파일의 구문은 내부용으로 예약 되어 있습니다. 이후 릴리스에서 변경 될 수 있습니다. 이 파일의 내용을 수정 하거나 조작 하지 마세요.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>새 클라이언트 매니페스트 (*. ISMC) 파일은 인코딩 작업이 하나 이상의 MP4 파일을 출력할 때 출력 자산에 생성 됩니다.
최신 서비스 릴리스로 시작 하 여 하나 이상의 MP4 파일을 생성 하는 인코딩 태스크가 완료 된 후 출력 자산에는 스트리밍 클라이언트 매니페스트 (*. ismc) 파일도 포함 됩니다. . Ismc 파일은 동적 스트리밍의 성능을 향상 시키는 데 도움이 됩니다. 

> [!NOTE]
> 클라이언트 매니페스트 (. ismc) 파일의 구문은 내부용으로 예약 되어 있습니다. 이후 릴리스에서 변경 될 수 있습니다. 이 파일의 내용을 수정 하거나 조작 하지 마세요.
> 
> 

További információt [ebben a blogban](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) talál.

### <a name="known-issues"></a>Ismert problémák
일부 클라이언트는 부드러운 스트리밍 매니페스트에서 반복 태그 문제를 가져올 수 있습니다. 자세한 내용은 [이 섹션](media-services-deliver-content-overview.md#known-issues)을 참조하세요.

## <a id="apr_changes16"></a>4 월 2016 릴리스
### <a name="media-analytics"></a>Media Analytics
 Media Services은 강력한 비디오 인텔리전스를 위해 미디어 분석 도입 되었습니다. 자세한 내용은 [Media Services Analytics 개요](media-services-analytics-overview.md)를 참조 하세요.

### <a name="apple-fairplay-preview"></a>Apple FairPlay (미리 보기)
이제 Media Services를 사용 하 여 Apple FairPlay에서 HLS (HTTP 라이브 스트리밍) 콘텐츠를 동적으로 암호화할 수 있습니다. 또한 Media Services 라이선스 배달 서비스를 사용 하 여 클라이언트에 FairPlay 라이선스를 제공할 수 있습니다. 자세한 내용은 "Azure Media Services를 사용 하 여 Apple FairPlay로 보호 되는 HLS 콘텐츠 스트리밍"을 참조 하세요.

## <a id="feb_changes16"></a>2 월 2016 릴리스
최신 버전의 .NET 용 Media Services SDK (3.5.3)에는 Google Widevine 관련 버그 수정이 포함 되어 있습니다. Widevine로 암호화 된 여러 자산에 대해 Asset배달 Ypolicy를 다시 사용할 수 없습니다. 이 버그 수정의 일환으로 SDK에 추가 된 속성은 다음과 같습니다. WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>1 월 2016 릴리스
인코더 이름의 혼동을 줄이기 위해 인코딩 예약 단위 이름이 바뀌었습니다.

기본, 표준 및 프리미엄 인코딩 예약 단위의 이름이 S1, S2 및 S3 예약 단위로 각각 바뀌었습니다. 현재 기본 encoding 예약 단위를 사용 하는 고객은 Azure Portal의 레이블 (및 청구서)로 S1을 볼 것입니다. Standard 및 Premium을 사용 하는 고객은 각각 S2 및 S3 레이블을 참조 하세요. 

## <a id="dec_changes_15"></a>12 월 2015 릴리스

### <a name="media-encoder-deprecation-announcement"></a>미디어 인코더 사용 중단 알림

 미디어 인코더는 Media Encoder Standard 릴리스에서 약 12 개월 후에 사용 되지 않습니다.

### <a name="azure-sdk-for-php"></a>Azure SDK a PHP-hoz
Azure SDK 팀은 Media Services의 업데이트 및 새 기능을 포함 하는 [PHP 용 AZURE SDK](https://github.com/Azure/azure-sdk-for-php) 패키지의 새 릴리스를 게시 했습니다. 특히 PHP 용 Media Services SDK는 이제 최신 [콘텐츠 보호](media-services-content-protection-overview.md) 기능을 지원 합니다. 이러한 기능은 토큰 제한을 사용 하거나 사용 하지 않는 AES 및 DRM (PlayReady 및 Widevine)을 사용한 동적 암호화입니다. 또한 [인코딩 단위](media-services-dotnet-encoding-units.md)크기 조정을 지원 합니다.

További információ eléréséhez lásd:

* 다음 [코드 샘플](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) 을 통해 빠르게 시작할 수 있습니다.
  * **vodworkflow_aes**:이 php 파일은 aes-128 동적 암호화 및 키 배달 서비스를 사용 하는 방법을 보여 줍니다. 이는 [AES-128 동적 암호화 및 키 배달 서비스 사용](media-services-protect-with-aes128.md)에 설명 된 .net 샘플을 기반으로 합니다.
  * **vodworkflow_aes**:이 php 파일은 PlayReady 동적 암호화 및 라이선스 배달 서비스를 사용 하는 방법을 보여 줍니다. 이는 [PlayReady 및/또는 Widevine 동적 일반 암호화 사용](media-services-protect-with-playready-widevine.md)에 설명 된 .net 샘플을 기반으로 합니다.
  * **scale_encoding_units**:이 php 파일은 인코딩 예약 단위 크기를 조정 하는 방법을 보여 줍니다.

## <a id="nov_changes_15"></a>11 월 2015 릴리스
 Media Services는 이제 클라우드에서 Widevine 라이선스 배달 서비스를 제공 합니다. További információt [ebben a blogban](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/) talál. 또한 [이 자습서](media-services-protect-with-playready-widevine.md) 및 [GitHub 리포지토리](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)를 참조 하세요. 

Media Services에서 제공 하는 widevine 라이선스 배달 서비스는 미리 보기 상태입니다. További információt [ebben a blogban](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/) talál.

## <a id="oct_changes_15"></a>2015 년 10 월 릴리스
Media Services는 이제 브라질 남부, 인도 서 부, 인도 남부 및 인도 중부 데이터 센터에 살고 있습니다. 이제 Azure Portal를 사용 하 여 [Media Service 계정을 만들고](media-services-portal-create-account.md) [Media Services 문서 웹 페이지](https://azure.microsoft.com/documentation/services/media-services/)에서 설명 하는 다양 한 작업을 수행할 수 있습니다. Live Encoding은 이러한 데이터 센터에서 사용할 수 없습니다. 또한 모든 유형의 인코딩 예약 단위를 이러한 데이터 센터에서 사용할 수 있는 것은 아닙니다.

* 브라질 남부: 표준 및 기본 encoding 예약 단위만 사용할 수 있습니다.
* 인도 서 부, 인도 남부 및 인도 중부: 기본 encoding 예약 단위만 사용할 수 있습니다.

## <a id="september_changes_15"></a>9 월 2015 릴리스
이제 Media Services은 Widevine 모듈식 DRM 기술을 사용 하 여 주문형 비디오와 라이브 스트림 둘 다를 보호 하는 기능을 제공 합니다. 다음 배달 서비스 파트너를 사용 하 여 Widevine 라이선스를 제공할 수 있습니다.
* [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

További információt [ebben a blogban](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) talál.
  
Az AssetDeliveryConfiguration Widevine használatára történő beállításához használja a [Media Services .NET SDK-t](https://www.nuget.org/packages/windowsazure.mediaservices/) (a 3.5.1-es vagy egy újabb verziót), vagy a REST API-t. 
* Media Services Apple ProRes 비디오에 대 한 지원이 추가 되었습니다. 이제 Apple ProRes 또는 기타 코덱을 사용 하는 QuickTime 원본 비디오 파일을 업로드할 수 있습니다. További információt [ebben a blogban](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/) talál.
* 이제 Media Encoder Standard를 사용 하 여 하위 클리핑 및 라이브 보관을 추출할 수 있습니다. További információt [ebben a blogban](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/) talál.
* 다음 필터링 업데이트가 수행 되었습니다. 
  
  * 이제 오디오 전용 필터를 사용 하 여 Apple HLS 형식을 사용할 수 있습니다. URL에 (오디오만 = false)를 지정 하 여 오디오 전용 트랙을 제거 하려면이 업데이트를 사용할 수 있습니다.
  * 자산에 대 한 필터를 정의할 때 이제는 단일 URL에 여러 (최대 3 개)의 필터를 결합할 수 있습니다.
    
    További információt [ebben a blogban](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) talál.
* 이제 Media Services은 HLS 버전 4에서 I-프레임을 지원 합니다. I-프레임 지원은 빨리 감기와 되감기 작업을 최적화 합니다. 기본적으로 모든 HLS 버전 4 출력에는 I-프레임 재생 목록 (EXT-I-I-스트림-INF)이 포함 됩니다.
További információt [ebben a blogban](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) talál.

## <a id="august_changes_15"></a>8 월 2015 릴리스
* Java 버전 0.8.0부터 릴리스에 대 한 Media Services SDK와 새 샘플을 이제 사용할 수 있습니다. További információ eléréséhez lásd:
    
* Azure Media Player 다중 오디오 스트림 지원으로 업데이트 되었습니다. További információkat [ebben a blogban](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/) talál.

## <a id="july_changes_15"></a>7 월 2015 릴리스
* Media Encoder Standard의 일반 공급이 발표 되었습니다. További információkat [ebben a blogban](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/) talál.
  
    [이 섹션](https://go.microsoft.com/fwlink/?LinkId=618336)에 설명 된 대로 Media Encoder Standard는 미리 설정을 사용 합니다. 4K 인코드에 대 한 기본 설정을 사용 하는 경우 프리미엄 예약 단위 형식을 가져옵니다. 자세한 내용은 [Encoding 크기 조정](media-services-scale-media-processing-overview.md)을 참조 하세요.
* 라이브 실시간 캡션은 Media Services 및 Media Player와 함께 사용 됩니다. További információkat [ebben a blogban](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/) talál.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK 업데이트
Media Services .NET SDK는 이제 버전 v3.4.0.0입니다. 다음 업데이트가 수행 되었습니다. 

* 라이브 보관에 대 한 지원이 구현 되었습니다. 라이브 아카이브를 포함 한 자산은 다운로드할 수 없습니다.
* 동적 필터에 대 한 지원이 구현 되었습니다.
* 사용자가 자산을 삭제 하는 동안 저장소 컨테이너를 유지할 수 있도록 기능이 구현 되었습니다.
* 채널에서 재시도 정책과 관련 하 여 버그 수정이 수행 되었습니다.
* Media Encoder Premium Workflow 사용 하도록 설정 되었습니다.

## <a id="june_changes_15"></a>6 월 2015 릴리스
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK 업데이트
Media Services .NET SDK는 이제 버전 3.3.0.0입니다. 다음 업데이트가 수행 되었습니다. 

* Openid connect Connect 검색 사양에 대 한 지원이 추가 되었습니다.
* Id 공급자 측에서 키 롤오버를 처리 하기 위한 지원이 추가 되었습니다.

Openid connect Connect 검색 문서를 제공 하는 id 공급자를 사용 하는 경우 (Azure AD, Google 및 Salesforce에서) Openid connect Connect 검색 사양에서 jwt (JSON 웹 토큰)의 유효성을 검사 하기 위해 서명 키를 가져오도록 Media Services에 지시할 수 있습니다. 

자세한 내용은 [Openid connect Connect 검색 사양의 JSON 웹 키를 사용 하 여 Media Services에서 JWT 인증 작업을](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/)참조 하세요.

## <a id="may_changes_15"></a>2015 년 5 월 릴리스
다음과 같은 새로운 기능이 발표 되었습니다.

* [Media Services 사용 하는 라이브 인코딩의 미리 보기](media-services-manage-live-encoder-enabled-channels.md)
* [동적 매니페스트](media-services-dynamic-manifest-overview.md)

## <a id="april_changes_15"></a>4 월 2015 릴리스
### <a name="general-media-services-updates"></a>일반 Media Services 업데이트
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) 발표 되었습니다.
* Media Services REST 2.10부터 RTMP (실시간 메시징 프로토콜)를 수집 하도록 구성 된 채널이 기본 및 보조 수집 Url을 사용 하 여 생성 됩니다. 자세한 내용은 [채널 수집 구성](media-services-live-streaming-with-onprem-encoders.md#channel_input)을 참조 하세요.
* Azure Media Indexer 업데이트 되었습니다.
* 스페인어 언어가 추가 되었습니다.
* XML 형식에 대 한 새 구성이 추가 되었습니다.

További információt [ebben a blogban](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/) talál.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK 업데이트
Media Services .NET SDK는 이제 버전 3.2.0.0입니다. 다음 업데이트가 수행 되었습니다.

* 주요 변경 내용: TokenRestrictionTemplate 및 TokenRestrictionTemplate가 문자열 유형으로 변경 되었습니다.
* 사용자 지정 다시 시도 정책 만들기와 관련 된 업데이트가 수행 되었습니다.
* 파일 업로드 및 다운로드와 관련 된 버그 수정이 수행 되었습니다.
* 이제 MediaServicesCredentials 클래스는 기본 및 보조 액세스 제어 끝점에 대 한 인증을 허용 합니다.

## <a id="march_changes_15"></a>3 월 2015 릴리스
### <a name="general-media-services-updates"></a>일반 Media Services 업데이트
* Media Services 이제 Content Delivery Network 통합을 제공 합니다. 통합을 지원 하기 위해 CdnEnabled 속성이 StreamingEndpoint에 추가 되었습니다. CdnEnabled는 버전 2.9부터 REST Api와 함께 사용할 수 있습니다. 자세한 내용은 [Streamingendpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)를 참조 하세요. 3\.1.0.2부터 버전부터 .NET SDK와 함께 CdnEnabled를 사용할 수 있습니다. 자세한 내용은 [Streamingendpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx)를 참조 하세요.
* Media Encoder Premium Workflow 발표 되었습니다. 자세한 내용은 [Azure Media Services 프리미엄 인코딩 소개](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)를 참조 하세요.

## <a id="february_changes_15"></a>2 월 2015 릴리스
### <a name="general-media-services-updates"></a>일반 Media Services 업데이트
이제 Media Services REST API 버전 2.9입니다. 이 버전부터 스트리밍 끝점과 Content Delivery Network 통합을 사용 하도록 설정할 수 있습니다. 자세한 내용은 [Streamingendpoint](https://msdn.microsoft.com/library/dn783468.aspx)를 참조 하세요.

## <a id="january_changes_15"></a>1 월 2015 릴리스
### <a name="general-media-services-updates"></a>일반 Media Services 업데이트
동적 암호화를 사용한 콘텐츠 보호의 일반 가용성이 발표 되었습니다. 자세한 내용은 [DRM 기술의 일반 공급으로 스트리밍 보안 향상 Media Services](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/)를 참조 하세요.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK 업데이트
Media Services .NET SDK는 이제 버전은 3.1.0.1입니다.

이 릴리스에서는 기본 Windowsazure.servicebus 생성자가 사용 되지 않는 것으로 표시 되었습니다. 새 생성자는로 TokenType을 인수로 사용 합니다.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>12 월 2014 릴리스
### <a name="general-media-services-updates"></a>일반 Media Services 업데이트
* 일부 업데이트 및 새 기능이 Media Indexer에 추가 되었습니다. 자세한 내용은 [Azure Media Indexer 버전 1.1.6.7 릴리스 정보](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/)를 참조 하세요.
* Encoding 예약 단위를 업데이트 하는 데 사용할 수 있는 새로운 REST API 추가 되었습니다. 자세한 내용은 [EncodingReservedUnitType WITH REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)항목을 참조 하세요.
* 키 배달 서비스에 대 한 CORS 지원이 추가 되었습니다.
* 권한 부여 정책 옵션을 쿼리 하는 성능이 향상 되었습니다.
* 중국 데이터 센터에서는 이제 다른 데이터 센터와 마찬가지로 [키 배달 URL](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) 이 고객 당입니다.
* HLS 자동 대상 기간이 추가 되었습니다. 라이브 스트리밍을 수행할 때 HLS는 항상 동적으로 패키지 됩니다. 기본적으로 Media Services는 키 프레임 간격 (Key프레임 간격)을 기준으로 HLS 세그먼트 패키징 비율 (FragmentsPerSegment)을 자동으로 계산 합니다. 이 메서드는 라이브 인코더에서 수신 되는 GOP (그룹 그룹) 라고도 합니다. 자세한 내용은 [Media Services 라이브 스트리밍 사용](https://msdn.microsoft.com/library/azure/dn783466.aspx)을 참조 하세요.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK 업데이트
[Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) 는 이제 버전은 3.1.0.0입니다. 다음 업데이트가 수행 되었습니다.

* .NET SDK 종속성이 .NET 4.5 Framework로 업그레이드 되었습니다.
* 인코딩 예약 단위를 업데이트 하는 데 사용할 수 있는 새 API가 추가 되었습니다. 자세한 내용은 [예약 단위 유형 업데이트 및 .net을 사용 하 여 인코딩 예약 단위 늘리기](media-services-dotnet-encoding-units.md)를 참조 하세요.
* 토큰 인증에 대 한 JWT 지원이 추가 되었습니다. 자세한 내용은 [Media Services 및 동적 암호화의 JWT 토큰 인증](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)을 참조 하세요.
* PlayReady 라이선스 템플릿의 BeginDate 및 ExpirationDate에 대 한 상대 오프셋이 추가 되었습니다.

## <a id="november_changes_14"></a>11 월 2014 릴리스
* 이제 Media Services를 사용 하 여 SSL 연결을 통해 live 부드러운 스트리밍 (fMP4) 콘텐츠를 수집할 수 있습니다. SSL을 통해 수집 하려면 수집 URL을 HTTPS로 업데이트 해야 합니다. 현재 Media Services에서는 사용자 지정 도메인에 대 한 SSL을 지원 하지 않습니다. 라이브 스트리밍에 대 한 자세한 내용은 [Azure Media Services 라이브 스트리밍 사용](https://msdn.microsoft.com/library/azure/dn783466.aspx)을 참조 하세요.
* 현재 SSL 연결을 통해 RTMP 라이브 스트림을 수집할 수 없습니다.
* 콘텐츠를 배달 하는 스트리밍 끝점이 2014 년 9 월 10 일 이후에 만들어진 경우에만 SSL을 통해 스트리밍할 수 있습니다. 스트리밍 Url이 2014 년 9 월 10 일 이후에 만들어진 스트리밍 끝점을 기반으로 하는 경우 URL에는 "streaming.mediaservices.windows.net" (새 형식)이 포함 됩니다. "Origin.mediaservices.windows.net" (이전 형식)이 포함 된 스트리밍 Url은 SSL을 지원 하지 않습니다. URL이 이전 형식인 경우 SSL을 통해 스트리밍하려면 [새 스트리밍 끝점을 만듭니다](media-services-portal-manage-streaming-endpoints.md). SSL을 통해 콘텐츠를 스트리밍하려면 새 스트리밍 끝점을 기반으로 하는 Url을 사용 합니다.

### <a id="oct_sdk"></a>Media Services .NET SDK
.NET 용 Media Services SDK 확장은 이제 버전 2.0.0.3입니다.

.NET 용 Media Services SDK는 이제 버전 3.0.0.8입니다. 다음 업데이트가 수행 되었습니다.

* 리팩터링은 재시도 정책 클래스에서 구현 되었습니다.
* HTTP 요청 헤더에 사용자 에이전트 문자열이 추가 되었습니다.
* NuGet 복원 빌드 단계가 추가 되었습니다.
* 리포지토리에서 x509 인증서를 사용 하도록 시나리오 테스트를 수정 했습니다.
* 채널 및 스트리밍 종료 업데이트 시 유효성 검사 설정이 추가 되었습니다.

### <a name="new-github-repository-to-host-media-services-samples"></a>Media Services 샘플을 호스트 하는 새 GitHub 리포지토리
샘플은 [Media Services 샘플 GitHub 리포지토리](https://github.com/Azure/Azure-Media-Services-Samples)에 있습니다.

## <a id="september_changes_14"></a>9 월 2014 릴리스
Media Services REST 메타 데이터는 이제 버전 2.7입니다. 최신 REST 업데이트에 대 한 자세한 내용은 [Media Services REST API 참조](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)를 참조 하세요.

.NET 용 Media Services SDK는 이제 버전 3.0.0.7입니다.

### <a id="sept_14_breaking_changes"></a>주요 변경 내용
* 원본 이름이 [Streamingendpoint]로 바뀌었습니다.
* Azure Portal를 사용 하 여 MP4 파일을 인코딩한 후 게시할 때 기본 동작이 변경 되었습니다.

### <a id="sept_14_GA_changes"></a>일반 공급 릴리스에 포함 된 새로운 기능/시나리오
* Media Indexer 미디어 프로세서가 도입 되었습니다. 자세한 내용은 [Media Indexer를 사용 하 여 미디어 파일 인덱싱](https://msdn.microsoft.com/library/azure/dn783455.aspx)을 참조 하세요.
* [Streamingendpoint] 엔터티를 사용 하 여 사용자 지정 도메인 (호스트) 이름을 추가할 수 있습니다.
  
    사용자 지정 도메인 이름을 Media Services 스트리밍 끝점 이름으로 사용 하려면 스트리밍 끝점에 사용자 지정 호스트 이름을 추가 합니다. Media Services REST Api 또는 .NET SDK를 사용 하 여 사용자 지정 호스트 이름을 추가 합니다.
  
    A következő szempontokat kell figyelembe venni:
  
  * 사용자 지정 도메인 이름에 대 한 소유권이 있어야 합니다.
  * 도메인 이름의 소유권은 Media Services에서 유효성을 검사 해야 합니다. 도메인의 유효성을 검사 하려면 MediaServicesAccountId 부모 도메인을 매핑하여 DNS windowsazure.mediaservices를 확인 하는 CName을 만듭니다.
  * 사용자 지정 호스트 이름 (예: sports.contoso.com)을 Media Services StreamingEndpoint 호스트 이름 (예: amstest.streaming.mediaservices.windows.net)에 매핑하는 다른 CName을 만들어야 합니다.

    자세한 내용은 [Streamingendpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx) 문서의 customhostnames 이름 속성을 참조 하세요.

### <a id="sept_14_preview_changes"></a>공개 미리 보기 릴리스에 포함 된 새로운 기능/시나리오
* 라이브 스트리밍 미리 보기. 자세한 내용은 [Media Services 라이브 스트리밍 사용](https://msdn.microsoft.com/library/azure/dn783466.aspx)을 참조 하세요.
* 키 배달 서비스. 자세한 내용은 [AES-128 동적 암호화 및 키 배달 서비스 사용](https://msdn.microsoft.com/library/azure/dn783457.aspx)을 참조 하세요.
* AES 동적 암호화. 자세한 내용은 [AES-128 동적 암호화 및 키 배달 서비스 사용](https://msdn.microsoft.com/library/azure/dn783457.aspx)을 참조 하세요.
* PlayReady 라이선스 배달 서비스입니다. 
* PlayReady 동적 암호화. 
* PlayReady 라이선스 템플릿을 Media Services 합니다. 자세한 내용은 [A Media Services PlayReady licencsablon áttekintése]를 참조 하세요.
* 저장소로 암호화 된 자산을 스트리밍합니다. 자세한 내용은 [저장소로 암호화 된 콘텐츠 스트리밍](https://msdn.microsoft.com/library/azure/dn783451.aspx)을 참조 하세요.

## <a id="august_changes_14"></a>8 월 2014 릴리스
자산을 인코딩하는 경우 인코딩 작업이 완료 되 면 출력 자산이 생성 됩니다. 이 릴리스가 나타날 때까지 Media Services 인코더는 출력 자산에 대 한 메타 데이터를 생성 했습니다. 이 릴리스부터 인코더는 입력 자산에 대 한 메타 데이터도 생성 합니다. 자세한 내용은 [Bemeneti metaadatok] 및 [Kimeneti metaadatok]를 참조 하세요.

## <a id="july_changes_14"></a>7 월 2014 릴리스
Azure Media Services 패키지 작성 및 암호기에 대 한 다음 버그가 수정 되었습니다.

* 라이브 보관 자산이 HLS로 전송 되 면 오디오만 재생 됩니다 .이 문제는 해결 되었으며 이제 오디오와 비디오를 모두 재생할 수 있습니다.
* 자산이 HLS 및 AES 128 비트 봉투 암호화로 패키지 되는 경우, 패키지 스트림은 Android 장치에서 재생 되지 않습니다 .이 버그는 수정 되었으며 HLS를 지 원하는 Android 장치에서 패키지 스트림이 다시 재생 됩니다.

## <a id="may_changes_14"></a>2014 년 5 월 릴리스
### <a id="may_14_changes"></a>일반 Media Services 업데이트
이제 [Dinamikus csomagolás] 사용 하 여 HLS 버전 3을 스트리밍할 수 있습니다. HLS 버전 3을 스트리밍하려면 원본 로케이터 경로 *. p s/매니페스트 (format = m3u8-aapl-v3-aapl-v3)에 다음 형식을 추가 합니다. 자세한 내용은 [이 포럼](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3)을 참조 하세요.

또한 동적 패키징을 사용 하 여 playready로 암호화 된 부드러운 스트리밍을 기반으로 PlayReady로 암호화 된 HLS (버전 3 및 버전 4)를 제공 합니다. PlayReady를 사용 하 여 부드러운 스트리밍을 암호화 하는 방법에 대 한 자세한 내용은 [playready로 부드러운 스트리밍 보호](https://msdn.microsoft.com/library/azure/dn189154.aspx)를 참조 하세요.

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK 업데이트
Media Services .NET SDK는 이제 버전 3.0.0.5입니다. 다음 업데이트가 수행 되었습니다.

* 미디어 자산을 업로드 및 다운로드 하는 경우 속도와 복원 력이 향상 됩니다.
* 다시 시도 논리와 일시적 예외 처리가 개선 되었습니다. 
  
  * 쿼리, 변경 내용 저장, 파일 업로드 또는 다운로드 시 발생 하는 예외에 대 한 일시적인 오류 감지 및 다시 시도 논리가 향상 되었습니다. 
  * 웹 예외가 발생 하는 경우 (예를 들어 Access Control Service 토큰 요청 중) 심각한 오류가 더 빠르게 실패 합니다.

자세한 내용은 [.NET 용 Media Services SDK의 다시 시도 논리]를 참조 하세요.

## <a id="jan_feb_changes_14"></a>2014 년 1 월/2 월 릴리스
### <a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 및 3.0.0.3
3\.0.0.1 및 3.0.0.2의 변경 내용에는 다음이 포함 됩니다.

* OrderBy 문을 사용한 LINQ 쿼리 사용과 관련 된 문제가 수정 되었습니다.
* [GitHub] 의 테스트 솔루션이 단위 기반 테스트와 시나리오 기반 테스트로 분할 되었습니다.

변경 내용에 대 한 자세한 내용은 [Media Services .NET SDK 3.0.0.1 and 3.0.0.2 릴리스](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html)를 참조 하세요.

버전 3.0.0.3에서 다음과 같이 변경 되었습니다.

* 3\.0.3.0 버전을 사용 하도록 Azure 저장소 종속성이 업그레이드 되었습니다.
* 3\.0에 대해 이전 버전과의 호환성 문제가 해결 되었습니다. *.* 놓을.

## <a id="december_changes_13"></a>12 월 2013 릴리스
### <a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> 3\.0. x. x 릴리스는 이전 버전인 2.4. x. x 릴리스와 호환 되지 않습니다.
> 
> 

최신 버전의 Media Services SDK가 이제 3.0.0.0. NuGet에서 최신 패키지를 다운로드 하거나 [GitHub]에서 비트를 가져올 수 있습니다.

Media Services SDK 버전 3.0.0.0부터 [AZURE AD Access Control Service](https://msdn.microsoft.com/library/hh147631.aspx) 토큰을 다시 사용할 수 있습니다. 자세한 내용은 [.net 용 MEDIA SERVICES SDK를 사용](https://msdn.microsoft.com/library/azure/jj129571.aspx)하 여 Media Services에 연결에서 "Access Control Service 토큰 다시 사용" 섹션을 참조 하세요.

### <a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK 확장 2.0.0.0
 Media Services .NET SDK extensions는 코드를 단순화 하 고 Media Services를 사용 하 여 보다 쉽게 개발할 수 있도록 하는 확장 메서드 및 도우미 함수 집합입니다. [Media Services .NET SDK extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev)에서 최신 비트를 가져올 수 있습니다.

## <a id="november_changes_13"></a>11 월 2013 릴리스
### <a name="nov_13_donnet_changes"></a>Media Services .NET SDK 변경 내용
이 버전부터 .NET 용 Media Services SDK는 Media Services REST API 계층에 대 한 호출이 수행 될 때 발생할 수 있는 일시적인 오류를 처리 합니다.

## <a id="august_changes_13"></a>8 월 2013 릴리스
### <a name="aug_13_powershell_changes"></a>Azure SDK 도구에 포함 된 Media Services PowerShell cmdlet
이제 [AZURE SDK 도구](https://github.com/Azure/azure-sdk-tools)에 다음 Media Services PowerShell cmdlet이 포함 되어 있습니다.

* AzureMediaServices 

    Például:`Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Például:`New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Például:`New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Például:`Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a id="june_changes_13"></a>6 월 2013 릴리스
### <a name="june_13_general_changes"></a>변경 내용 Media Services
이 섹션에서 설명 하는 다음과 같은 변경 내용은 2013 년 6 월 Media Services 릴리스에 포함 된 업데이트입니다.

* 여러 저장소 계정을 미디어 서비스 계정에 연결할 수 있습니다. 
    * StorageAccount
    * Asset. StorageAccountName 및 Asset. Storageaccountname
* 작업 우선 순위를 업데이트할 수 있습니다. 
* 알림 관련 엔터티 및 속성: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Feladat
* Asset. Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Media Services .NET SDK 변경 내용
6 월 2013 Media Services SDK 릴리스에는 다음과 같은 변경 내용이 포함 되어 있습니다. 최신 Media Services SDK는 GitHub에서 사용할 수 있습니다.

* 버전 2.3.0.0부터 Media Services SDK는 여러 저장소 계정을 Media Services 계정에 연결 하는 것을 지원 합니다. 이 기능을 지 원하는 Api는 다음과 같습니다.
  
    * IStorageAccount 형식
    * Windowsazure.servicebus. Windowsazure.mediaservices. CloudMediaContext 속성
    * StorageAccount 속성
    * StorageAccountName 속성
  
      자세한 내용은 [여러 저장소 계정에서 Media Services 자산 관리](https://msdn.microsoft.com/library/azure/dn271889.aspx)를 참조 하세요.
* 알림 관련 Api. 버전 2.2.0.0부터 Azure Queue storage 알림을 수신할 수 있습니다. 자세한 내용은 [Media Services 작업 알림 처리](https://msdn.microsoft.com/library/azure/dn261241.aspx)를 참조 하세요.
  
    * Windowsazure.servicebus. Windowsazure.mediaservices-JobNotificationSubscriptions 속성
    * Windowsazure.servicebus. Windowsazure.mediaservices. Microsoft.windowsazure.mediaservices.client.inotificationendpoint 형식
    * Windowsazure.servicebus. Windowsazure.mediaservices Notificationsubscription 형식
    * Windowsazure.servicebus. Windowsazure.mediaservices. NotificationEndPointCollection 형식
    * Windowsazure.servicebus. Windowsazure.mediaservices 형식 형식
* 저장소 클라이언트 SDK 2.0 (Windowsazure.servicebus. Microsoft.windowsazure.storageclient)에 대 한 종속성
* OData 5.5에 대 한 종속성 (Microsoft. Data. OData .dll)

## <a id="december_changes_12"></a>12 월 2012 릴리스
### <a name="dec_12_dotnet_changes"></a>Media Services .NET SDK 변경 내용
* IntelliSense: 많은 형식에 대해 누락 된 IntelliSense 설명서가 추가 되었습니다.
* Microsoft.practices.enterpriselibrary.transientfaulthandling: SDK에서 아직이 어셈블리의 이전 버전에 대 한 종속성이 있는 문제를 해결 했습니다. 이제 SDK는이 어셈블리의 버전 5.1.1209.1을 참조 합니다.

11 월 2012 SDK에서 발견 된 문제에 대 한 수정 사항:

* IAsset: 이제 모든 로케이터가 삭제 된 후이 수가 새로운 IAsset 인터페이스에서 올바르게 보고 됩니다.
* IAssetFile. Iassetfile.contentfilesize:이 값은 이제 IAssetFile (filepath)로 업로드 한 후에 올바르게 설정 됩니다.
* IAssetFile. Iassetfile.contentfilesize: 이제 자산 파일을 만들 때이 속성을 설정할 수 있습니다. 이전에는 읽기 전용 이었습니다.
* IAssetFile (filepath): 여러 파일을 자산에 업로드 하는 경우이 동기 업로드 메서드가 다음 오류를 throw 하는 문제를 해결 했습니다. 오류는 "서버에서 요청을 인증 하지 못했습니다. 권한 부여 헤더의 값이 서명을 포함 하 여 올바른 형식 인지 확인 하십시오. "
* IAssetFile. UploadAsync: 파일의 동시 업로드를 5 개 파일로 제한 하는 문제를 수정 했습니다.
* IAssetFile. Iassetfile.uploadprogresschanged:이 이벤트는 이제 SDK에서 제공 됩니다.
* IAssetFile Async (string, BlobTransferClient, ILocator, CancellationToken): 이제이 메서드 오버 로드가 제공 됩니다.
* IAssetFile Async: 파일의 동시 다운로드를 5 개 파일로 제한 하는 문제를 수정 했습니다.
* IAssetFile (): IAssetFile에 대해 업로드 된 파일이 없으면 delete를 호출할 때 예외가 throw 되는 문제가 수정 되었습니다.
* 작업: 작업 템플릿을 사용 하 여 "m p 4를 부드러운 스트림으로 연결" 작업을 "PlayReady 보호 작업"에 연결 하는 경우 작업이 전혀 생성 되지 않는 문제가 해결 되었습니다.
* 찾지 못하여 발생 ():이 메서드는 인증서 구성 문제에 따라 인증서를 찾지 못해 null 참조 예외를 더 이상 throw 하지 않습니다.

## <a id="november_changes_12"></a>11 월 2012 릴리스
이 섹션에 언급 된 변경 내용은 11 월 2012 (버전 2.0.0.0) SDK에 포함 된 업데이트입니다. 이러한 변경으로 인해 6 월 2012 preview SDK 릴리스를 위해 작성 된 코드를 수정 하거나 다시 작성 해야 할 수 있습니다.

* Objektumok
  
    * IAsset (assetName)는 *유일한* 자산 생성 함수입니다. IAsset는 더 이상 메서드 호출의 일부로 파일을 업로드 하지 않습니다. IAssetFile를 사용 하 여 업로드 합니다.
    * IAsset 메서드 및 Iasset.publish 메서드와 assetstate.publish 열거형 값이 서비스 SDK에서 제거 되었습니다. 이 값에 의존 하는 모든 코드를 다시 작성 해야 합니다.
* FileInfo
  
    * 이 클래스는 제거 되 고 IAssetFile로 대체 되었습니다.
  
* 하려면 iassetfiles
  
    * IAssetFile는 FileInfo를 대체 하며 다른 동작을 포함 합니다. 이를 사용 하려면 하려면 iassetfiles 개체를 인스턴스화한 다음 Media Services SDK 또는 저장소 SDK를 사용 하 여 파일을 업로드 합니다. 다음 IAssetFile 오버 로드를 사용할 수 있습니다.
  
        * IAssetFile (filePath):이 동기 메서드는 스레드를 차단 하므로 단일 파일을 업로드할 때만 권장 됩니다.
        * IAssetFile. UploadAsync (filePath, blobTransferClient, locator, cancellationToken):이 비동기 메서드는 기본 업로드 메커니즘입니다. 
    
            알려진 버그: 취소 토큰을 사용 하는 경우 업로드가 취소 됩니다. 작업에는 여러 취소 상태가 있을 수 있습니다. 예외를 적절히 catch 하 고 처리 해야 합니다.
* Locator
  
    * 원본 관련 버전이 제거 되었습니다. SAS 관련 컨텍스트입니다. Context.locators.createsaslocator (asset, accessPolicy)는 일반 공급으로 사용 되지 않거나 제거 된 것으로 표시 됩니다. 업데이트 된 동작은 "새 기능"의 "로케이터" 섹션을 참조 하세요.

## <a id="june_changes_12"></a>6 월 2012 preview 릴리스
SDK 11 월 릴리스의 새로운 기능은 다음과 같습니다.

* 엔터티 삭제
  
    * IAsset, IAssetFile, ILocator IAccessPolicy 및 IContentKey 개체는 이제 개체 수준에서 삭제 됩니다. 즉, 컬렉션에서 삭제를 요청 하는 대신 cloudMediaContext (Objcollection)를 삭제 합니다.
* Locator
  
    * 이제는 CreateLocator 메서드를 사용 하 여 로케이터를 만들어야 합니다. 만들려는 특정 형식의 로케이터에 대 한 인수로 LocatorType. OnDemandOrigin 열거형 값을 사용 해야 합니다.
    * 새 속성이 로케이터에 추가 되어 콘텐츠에 사용할 수 있는 Uri를 더욱 쉽게 얻을 수 있습니다. 이러한 로케이터를 다시 디자인 하면 향후 타사 확장성에 더 많은 유연성을 제공 하 고 미디어 클라이언트 응용 프로그램의 사용 편의성을 높일 수 있습니다.
* 비동기 메서드 지원
  
    * 모든 메서드에 비동기 지원이 추가 되었습니다.

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure Media Services MSDN 포럼]: https://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services REST API 참조]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Bemeneti metaadatok]: https://msdn.microsoft.com/library/azure/dn783120.aspx
[Kimeneti metaadatok]: https://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: https://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: https://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: https://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: https://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: https://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: https://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: https://azure.microsoft.com/services/preview/
[A Media Services PlayReady licencsablon áttekintése]: https://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: https://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Dinamikus csomagolás]: https://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: https://msdn.microsoft.com/library/azure/dn189154.aspx
[.NET 용 Media Services SDK의 다시 시도 논리]: https://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: https://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: https://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: https://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: https://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: https://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: https://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: https://msdn.microsoft.com/library/azure/dn261241.aspx

