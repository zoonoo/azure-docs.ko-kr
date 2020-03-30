---
title: Azure 미디어 서비스 v3로 라이브 스트리밍 개요 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Media Services v3를 사용하는 라이브 스트리밍 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: e2c4e5b6c10b06d82a1933962cb2d97e031876a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068016"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Azure Media Services v3를 통한 라이브 스트리밍

Azure Media Services를 사용하면 Azure 클라우드에서 고객에게 라이브 이벤트를 전달할 수 있습니다. Media Services로 라이브 이벤트를 스트리밍하려면 다음이 필요합니다.  

- 라이브 이벤트를 캡처하는 데 사용되는 카메라.<br/>설치 아이디어는 [간단하고 이동 가능한 이벤트 비디오 기어 설정]( https://link.medium.com/KNTtiN6IeT)을 확인하세요.

    카메라에 액세스할 수 없는 경우 [Telestream Wirecast와](https://www.telestream.net/wirecast/overview.htm) 같은 도구를 사용하여 비디오 파일에서 라이브 피드를 생성할 수 있습니다.
- 카메라(또는 노트북과 같은 다른 디바이스)의 신호를 Media Services로 전송되는 기여 피드로 변환하는 라이브 비디오 인코더. 기여 피드에는 SCTE-35 마커와 같은 광고 관련 신호가 포함될 수 있습니다.<br/>권장 라이브 스트리밍 인코더 목록은 [라이브 스트리밍 인코더](recommended-on-premises-live-encoders.md)를 참조하세요. 또한, 이 블로그를 체크 아웃: [OBS와 라이브 스트리밍 프로덕션](https://link.medium.com/ttuwHpaJeT).
- Media Services의 구성 요소. 이러한 구성 요소를 사용하여 라이브 이벤트를 수집, 미리 보기, 패키지화, 기록, 암호화할 수 있으며 고객 또는 CDN(추가 배포를 위해)에 브로드캐스트할 수 있습니다.

이 문서에서는 미디어 서비스와 함께 라이브 스트리밍에 대한 개요와 지침을 제공하고 다른 관련 문서에 대한 링크를 제공합니다.
 
> [!NOTE]
> [Azure 포털을](https://portal.azure.com/) 사용하여 v3 [라이브 이벤트를](live-events-outputs-concept.md)관리하고 v3 [자산을](assets-concept.md)보고 API 액세스에 대한 정보를 얻을 수 있습니다. 다른 모든 관리 작업(예: 변환 및 작업)의 경우 [REST API,](https://docs.microsoft.com/rest/api/media/) [CLI](https://aka.ms/ams-v3-cli-ref)또는 지원되는 [SDK](media-services-apis-overview.md#sdks)중 하나를 사용합니다.

## <a name="dynamic-packaging"></a>동적 패키징

미디어 서비스를 사용하면 서비스로 전송되는 기여 피드에서 [MPEG DASH, HLS 및 부드러운 스트리밍 형식으로](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) 라이브 스트림을 미리 보고 브로드캐스트할 수 있는 동적 [패키징을](dynamic-packaging-overview.md)활용할 수 있습니다. 뷰어는 HLS, DASH 또는 부드러운 스트리밍이 호환되는 플레이어를 사용해 라이브 스트림을 재생할 수 있습니다. 웹 또는 모바일 애플리케이션에서 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html)를 사용하여 이러한 프로토콜 중 하나로 스트림을 전달할 수 있습니다.

## <a name="dynamic-encryption"></a>동적 암호화

동적 암호화를 사용하면 AES-128 또는 세 가지 주요 디지털 권한 관리(DRM) 시스템(Microsoft PlayReady, Google 와이드바인 및 Apple FairPlay)을 사용하여 라이브 또는 주문형 콘텐츠를 동적으로 암호화할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. 자세한 내용은 [동적 암호화](content-protection-overview.md)를 참조하십시오.

> [!NOTE]
> Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="dynamic-manifest"></a>동적 매니페스트

동적 필터링은 플레이어에게 전송되는 트랙, 형식, 비트 전송률 및 프레젠테이션 시간 창의 수를 제어하는 데 사용됩니다. 자세한 내용은 [필터 및 동적 매니페스트를](filters-dynamic-manifest-overview.md)참조하십시오.

## <a name="live-event-types"></a>라이브 이벤트 유형

[라이브 이벤트](https://docs.microsoft.com/rest/api/media/liveevents)는 라이브 비디오 피드 수집 및 처리를 담당합니다. 라이브 이벤트는 통과(온-프레미스 라이브 인코더가 여러 비트 전송률 스트림을 전송) 또는 *라이브 인코딩(온-프레미스* 라이브 인코더가 단일 비트 레이트 스트림을 보냅니다)으로 설정할 수 있습니다. *pass-through* 미디어 서비스 v3의 라이브 스트리밍에 대한 자세한 내용은 [라이브 이벤트 및 라이브 출력을](live-events-outputs-concept.md)참조하십시오.

### <a name="pass-through"></a>통과

![통과](./media/live-streaming/pass-through.svg)

통과 **라이브 이벤트를**사용하는 경우 온-프레미스 라이브 인코더를 사용하여 여러 비트 전송률 비디오 스트림을 생성하고 라이브 이벤트에 대한 기여 피드로 전송합니다(RTMP 또는 조각형 MP4 입력 프로토콜 사용). 그런 다음 라이브 이벤트는 추가 트랜스코딩 없이 들어오는 비디오 스트림을 동적 패키지(스트리밍 엔드포인트)로 전달합니다. 이러한 패스스루 라이브 이벤트는 장기 실행 라이브 이벤트 또는 24x365 선형 라이브 스트리밍에 최적화되어 있습니다. 

### <a name="live-encoding"></a>라이브 인코딩  

![라이브 인코딩](./media/live-streaming/live-encoding.svg)

Media Services에서 클라우드 인코딩을 사용하는 경우 온-프레미스 라이브 인코더를 구성하여 단일 비트 전송률 비디오를 라이브 이벤트(RTMP 또는 조각형 MP4 입력 프로토콜 사용)에 기여 피드(최대 32Mbps 집계)로 보냅니다. 라이브 이벤트는 들어오는 단일 비트 전송률 스트림을 다양한 해상도로 [여러 비트 레이트 비디오 스트림으로](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) 변환하여 전송을 개선하고 MPEG-DASH, Apple HTTP 라이브 스트리밍(HLS) 및 Microsoft 스무스 스트리밍과 같은 업계 표준 프로토콜을 통해 디바이스를 재생할 수 있도록 합니다. 

### <a name="live-transcription-preview"></a>라이브 전사(미리보기)

라이브 전사는 통과 또는 라이브 인코딩인 라이브 이벤트와 함께 사용할 수 있는 기능입니다. 자세한 내용은 [라이브 전사를](live-transcription.md)참조하십시오. 이 기능을 사용하도록 설정하면 서비스는 인지 서비스의 [음성-텍스트](../../cognitive-services/speech-service/speech-to-text.md) 기능을 사용하여 들어오는 오디오의 음성 단어를 텍스트로 기록합니다. 그런 다음 이 텍스트는 MPEG-DASH 및 HLS 프로토콜의 비디오 및 오디오와 함께 배달할 수 있습니다.

> [!NOTE]
> 현재 라이브 전사는 미국 서부 2에서 미리 보기 기능으로 제공됩니다.

## <a name="live-streaming-workflow"></a>라이브 스트리밍 워크플로

Media Services v3의 라이브 스트리밍 워크플로우를 이해하려면 먼저 다음 개념을 검토하고 이해해야 합니다. 

- [스트리밍 엔드포인트](streaming-endpoint-concept.md)
- [라이브 이벤트 및 라이브 출력](live-events-outputs-concept.md)
- [스트리밍 로케이터](streaming-locators-concept.md)

### <a name="general-steps"></a>일반적인 단계

1. 미디어 서비스 계정에서 **스트리밍 끝점(원본)이** 실행되고 있는지 확인합니다. 
2. 라이브 [이벤트](live-events-outputs-concept.md)만들기. <br/>이벤트를 만들 때 자동 시작을 지정할 수 있습니다. 또는 스트리밍을 시작할 준비가 되었을 때 이벤트를 시작해도 됩니다.<br/> autostart를 true로 설정하면 Live Event가 생성 직후 시작됩니다. 라이브 이벤트가 실행되는 즉시 청구가 시작됩니다. 추가 청구를 중지하려면 라이브 이벤트 리소스에 대해 명시적으로 Stop을 호출해야 합니다. 자세한 내용은 [라이브 이벤트 상태 및 청구](live-event-states-billing.md)를 참조하세요.
3. 인제스트 URL을 얻고 온-프레미스 인코더를 구성하여 URL을 사용하여 기여 피드를 보냅니다.<br/>[추천되는 라이브 인코더](recommended-on-premises-live-encoders.md)를 참조하세요.
4. 미리 보기 URL을 가져와서 인코더의 입력이 실제로 수신되고 있는지 확인하는 데 사용합니다.
5. 새 **자산** 개체를 만듭니다. 

    각 라이브 출력은 연결된 Azure Blob 저장소 컨테이너에 비디오를 기록하는 데 사용하는 자산과 연결됩니다. 
6. 라이브 **출력을** 만들고 스트림을 자산에 보관할 수 있도록 만든 자산 이름을 사용합니다.

    라이브 출력은 생성과 동시에 시작되고 삭제되면 중지됩니다. 라이브 출력을 삭제하면 에셋의 기본 자산 및 콘텐츠를 삭제하지 않습니다.
7. 기본 제공 스트리밍 [정책 유형으로 스트리밍](streaming-policy-concept.md) **로케이터를** 만듭니다.

    라이브 출력을 게시하려면 연결된 자산에 대한 스트리밍 로케이터를 만들어야 합니다. 
8. **스트리밍 로케이터**의 경로를 나열하여 사용할 URL(결정적)을 다시 가져옵니다.
9. 스트리밍하려는 **스트리밍 끝점(원본)의** 호스트 이름을 가져옵니다.
10. 8단계의 URL을 9단계의 호스트 이름과 결합하여 전체 URL을 구합니다.
11. **라이브 이벤트**를 더 이상 표시하지 않으려면 이벤트 스트리밍을 중지하고 **스트리밍 로케이터**를 삭제해야 합니다.
12. 스트리밍 이벤트를 완료하고 이전에 프로비전된 리소스를 정리하려면 다음 절차를 수행합니다.

    * 인코더에서 스트림의 푸시를 중지합니다.
    * 라이브 이벤트를 중지합니다. 라이브 이벤트가 중지되면 채널 요금이 발생하지 않습니다. 채널을 다시 시작해야 하는 경우 채널의 수집 URL은 동일하므로 인코더를 다시 구성하지 않아도 됩니다.
    * 라이브 이벤트의 보관 파일을 주문형 스트림으로 계속 제공하지 않으려면 스트리밍 엔드포인트를 중지할 수 있습니다. 라이브 이벤트가 중지된 상태이면 채널 요금이 발생하지 않습니다.

라이브 출력이 보관되는 자산은 라이브 출력이 삭제될 때 자동으로 온디맨드 자산이 됩니다. 라이브 이벤트를 중지하려면 먼저 모든 라이브 출력을 삭제해야 합니다. 선택적 플래그 제거를 사용할 수 [있습니다OutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) 중지시 라이브 출력을 자동으로 제거합니다. 

> [!TIP]
> [라이브 스트리밍 자습서를](stream-live-tutorial-with-api.md)참조 , 이 문서에서는 위에서 설명한 단계를 구현하는 코드를 검사합니다.

## <a name="other-important-articles"></a>다른 중요 문서

- [권장되는 라이브 인코더](recommended-on-premises-live-encoders.md)
- [클라우드 DVR 사용](live-event-cloud-dvr.md)
- [라이브 이벤트 유형의 기능 비교](live-event-types-comparison.md)
- [상태 및 청구](live-event-states-billing.md)
- [대기 시간이](live-event-latency.md)

## <a name="frequently-asked-questions"></a>질문과 대답

자주 [묻는 질문](frequently-asked-questions.md#live-streaming) 문서를 참조하십시오.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [라이브 스트리밍 퀵스타트] (live-events-wirecast-quickstart.md)
* [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)
* [Media Services v2에서 v3로 이동하기 위한 마이그레이션 지침](migrate-from-v2-to-v3.md)
