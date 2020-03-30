---
title: 미디어 서비스 용어 및 개념
titleSuffix: Azure Media Services
description: Azure 미디어 서비스에 대한 용어 및 개념에 대해 알아봅니다.
services: media-servicesgit
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 02d0897774261a25a2fccb70a31d0f264c458740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500096"
---
# <a name="media-services-terminology-and-concepts"></a>미디어 서비스 용어 및 개념

이 항목에서는 Azure Media Services 용어 및 개념에 대한 간략한 개요를 제공합니다. 또한 이 문서에서는 미디어 서비스 v3 개념 및 기능에 대한 자세한 설명과 함께 문서에 대한 링크를 제공합니다.

이 항목에 설명된 기본 개념은 개발을 시작하기 전에 검토해야 합니다.

> [!NOTE]
> 현재 [Azure 포털을](https://portal.azure.com/) 사용할 수 있습니다. [Live Events](live-events-outputs-concept.md) [Assets](assets-concept.md) [get info about accessing APIs](access-api-portal.md)
> 다른 모든 관리 작업(예: [변환 및 작업](transforms-jobs-concept.md) 및 콘텐츠 [보호)의](content-protection-overview.md)경우 [REST API,](https://aka.ms/ams-v3-rest-ref) [CLI](https://aka.ms/ams-v3-cli-ref)또는 지원되는 [SDK](media-services-apis-overview.md#sdks)중 하나를 사용합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>미디어 서비스 v3 용어집

|용어|설명|
|---|---|
|라이브 이벤트|**라이브 이벤트는** 비디오, 오디오 및 실시간 메타데이터의 라이브 스트림을 수집, 트랜스코딩(선택 사항)하고 패키징하기 위한 파이프라인을 나타냅니다.<br/><br/>Media Services v2 API에서 마이그레이션하는 고객의 경우 **라이브 이벤트는** v2의 **채널** 엔터티를 대체합니다. 자세한 내용은 [v2에서 v3로 마이그레이션을](migrate-from-v2-to-v3.md)참조하십시오.|
|스트리밍 엔드포인트/패키징/오리진|**스트리밍 엔드포인트는** 클라이언트 플레이어 애플리케이션에 직접 라이브 및 온디맨드 콘텐츠를 제공할 수 있는 동적(적시) 패키징 및 오리진 서비스를 나타냅니다. 일반적인 스트리밍 미디어 프로토콜(HLS 또는 DASH) 중 하나를 사용합니다. 또한 스트리밍 **엔드포인트는** 업계 최고의 디지털 권한 관리 시스템(DrM)에 동적(적시에) 암호화를 제공합니다.<br/><br/>미디어 스트리밍 업계에서 이 서비스를 일반적으로 **패키지 또는** **Origin이라고**합니다.  이 기능에 대한 업계의 다른 일반적인 용어로는 JITP(적시 패키지) 또는 JITE(적시 암호화)가 있습니다.

## <a name="media-services-v3-concepts"></a>미디어 서비스 v3 컨셉

|개념|설명|링크|
|---|---|---|
|에셋 및 콘텐츠 업로드|Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들고 디지털 파일을 **자산에**업로드해야 합니다.|[클라우드 업로드 및 스토리지](storage-account-concept.md)<br/><br/>[자산 개념](assets-concept.md)|
|콘텐츠 인코딩|고품질 디지털 미디어 파일을 자산에 업로드하면 다양한 브라우저 및 장치에서 재생할 수 있는 형식으로 인코딩할 수 있습니다. <br/><br/>미디어 서비스 v3로 인코딩하려면 **변환** 및 **채용 작업을**만들어야 합니다.|[변환 및 작업](transforms-jobs-concept.md)<br/><br/>[Media Services를 사용하여 인코딩](encoding-concept.md)|
|콘텐츠 분석(Video Indexer)|미디어 서비스 v3를 사용하면 Media Services v3 사전 설정을 사용하여 비디오 및 오디오 파일에서 통찰력을 추출할 수 있습니다. 미디어 서비스 v3 사전 설정을 사용하여 콘텐츠를 분석하려면 **변환** 및 **작업을**만들어야 합니다.<br/><br/>더 자세한 정보를 원할 경우 [비디오 인덱서를 직접 사용하십시오.](https://docs.microsoft.com/azure/media-services/video-indexer/)|[비디오 및 오디오 파일 분석](analyzing-video-audio-files-concept.md)|
|패키징 및 제공|콘텐츠가 인코딩되면 **동적 패키징을**활용할 수 있습니다. 미디어 서비스에서 **스트리밍 엔드포인트는** 클라이언트 플레이어에게 미디어 콘텐츠를 제공하는 데 사용되는 동적 패키징 서비스입니다. 클라이언트에서 재생할 수 있도록 출력 자산의 비디오를 사용하려면 **스트리밍 로케이터를** 만든 다음 스트리밍 URL을 빌드해야 합니다. <br/><br/>**스트리밍 로케이터를**만들 때 자산 이름 외에 **스트리밍 정책을**지정해야 합니다. **스트리밍 정책을** 사용하면 스트리밍 **로케이터에**대한 스트리밍 프로토콜 및 암호화 옵션(있는 경우)을 정의할 수 있습니다. 동적 패키징은 콘텐츠를 라이브 또는 주문형 스트리밍여부에 관계없이 사용됩니다. <br/><br/>미디어 서비스 **동적 매니페스트를** 사용하여 비디오의 특정 변환 또는 하위 클립만 스트리밍할 수 있습니다.|[동적 패키징](dynamic-packaging-overview.md)<br/><br/>[스트리밍 엔드포인트](streaming-endpoint-concept.md)<br/><br/>[스트리밍 로케이터](streaming-locators-concept.md)<br/><br/>[스트리밍 정책](streaming-policy-concept.md)<br/><br/>[동적 매니페스트](filters-dynamic-manifest-overview.md)<br/><br/>[필터](filters-concept.md)|
|콘텐츠 보호|미디어 서비스를 사용하면 고급 암호화 표준(AES-128) 또는/및/및 세 가지 주요 DRM 시스템 중 어떤 시스템인 Microsoft PlayReady, Google 와이드바인 및 Apple FairPlay를 통해 동적으로 암호화된 라이브 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. <br/><br/>스트림에서 암호화 옵션을 지정하는 경우 **콘텐츠 키 정책을** 만들고 스트리밍 **로케이터**에 연결합니다. **콘텐츠 키 정책을** 사용하면 콘텐츠 키가 최종 클라이언트에 전달되는 방식을 구성할 수 있습니다.<br/><br/> 동일한 옵션이 필요할 때마다 정책을 다시 사용해 보십시오.| [콘텐츠 키 정책](content-key-policy-concept.md)<br/><br/>[콘텐츠 보호](content-protection-overview.md)|
|라이브 스트리밍|Media Services를 사용하면 Azure 클라우드에서 고객에게 라이브 이벤트를 전송할 수 있습니다. **라이브 이벤트**는 라이브 비디오 피드 수집 및 처리를 담당합니다. **라이브 이벤트를**만들 때 원격 인코더에서 라이브 신호를 보내는 데 사용할 수 있는 입력 끝점이 만들어집니다. **라이브 이벤트로**스트림이 흐르면 **에셋,** **라이브 출력**및 **스트리밍 로케이터를**만들어 스트리밍 이벤트를 시작할 수 있습니다. **라이브 출력은** 스트림을 **에셋에** 보관하고 스트리밍 **엔드포인트를**통해 시청자가 사용할 수 있도록 합니다. 라이브 이벤트는 통과(온-프레미스 라이브 인코더가 여러 비트 전송률 스트림을 전송) 또는 *라이브 인코딩(온-프레미스* 라이브 인코더가 단일 비트 레이트 스트림을 보냅니다)으로 설정할 수 있습니다. *pass-through* |[라이브 스트리밍 개요](live-streaming-overview.md)<br/><br/>[라이브 이벤트 및 라이브 출력](live-events-outputs-concept.md)|
|이벤트 그리드로 모니터링|작업의 진행 률을 보려면 **이벤트 그리드를**사용합니다. 미디어 서비스는 라이브 이벤트 유형도 내보며, 라이브 이벤트 유형도 제공합니다. Event Grid를 사용하면 앱이 사용자 지정 원본뿐만 아니라 거의 모든 Azure 서비스의 이벤트에 대해 수신 대기하고 대응할 수 있습니다. |[Event Grid 이벤트 처리](reacting-to-media-services-events.md)<br/><br/>[스키마](media-services-event-schemas.md)|
|Azure 모니터로 모니터링|Azure Monitor를 통해 앱의 성능을 이해하는 데 도움이 되는 메트릭 및 진단 로그를 모니터링합니다.|[메트릭 및 진단 로그](media-services-metrics-diagnostic-logs.md)<br/><br/>[진단 로그 스키마](media-services-diagnostic-logs-schema.md)|
|플레이어 클라이언트|Azure Media Player를 사용하여 다양한 브라우저 및 장치에서 Media Services에서 스트리밍하는 미디어 콘텐츠를 재생할 수 있습니다. Azure Media Player는 HTML5, 미디어 소스 확장(MSE) 및 암호화된 미디어 확장(EME)과 같은 업계 표준을 사용하여 풍부한 적응형 스트리밍 환경을 제공합니다. |[Azure Media Player 개요](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [원격 파일 및 스트림 비디오 인코딩 – REST](stream-files-tutorial-with-rest.md)
* [업로드된 파일 및 스트림 비디오 인코딩 – .NET](stream-files-tutorial-with-api.md)
* [라이브 스트리밍 - .NET](stream-live-tutorial-with-api.md)
* [비디오 분석 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 동적 암호화 - .NET](protect-with-aes128.md)
* [다중 DRM으로 동적으로 암호화 - .NET](protect-with-drm.md)
