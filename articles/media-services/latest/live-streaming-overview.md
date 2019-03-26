---
title: Azure Media Services를 사용하는 라이브 스트리밍 개요 | Microsoft Docs
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
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: b8725dfcb2a337750c6e2a78ba7571114b8e3cd3
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407186"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Azure Media Services v3를 통한 라이브 스트리밍

Azure Media Services를 사용하면 Azure 클라우드에서 고객에게 라이브 이벤트를 전달할 수 있습니다. Media Services로 라이브 이벤트를 스트리밍하려면 다음이 필요합니다.  

- 라이브 이벤트를 캡처하는 데 사용되는 카메라.<br/>설치 아이디어는 [간단하고 이동 가능한 이벤트 비디오 기어 설정]( https://link.medium.com/KNTtiN6IeT)을 확인하세요.
- 카메라(또는 노트북과 같은 다른 디바이스)의 신호를 Media Services로 전송되는 기여 피드로 변환하는 라이브 비디오 인코더. 기여 피드에는 SCTE-35 마커와 같은 광고 관련 신호가 포함될 수 있습니다.<br/>권장 라이브 스트리밍 인코더 목록은 [라이브 스트리밍 인코더](recommended-on-premises-live-encoders.md)를 참조하세요. 또한 이 블로그를 확인하세요. [OBS를 사용한 라이브 스트리밍 프로덕션](https://link.medium.com/ttuwHpaJeT)
- Media Services의 구성 요소. 이러한 구성 요소를 사용하여 라이브 이벤트를 수집, 미리 보기, 패키지화, 기록, 암호화할 수 있으며 고객 또는 CDN(추가 배포를 위해)에 브로드캐스트할 수 있습니다.

이 문서에서는 개요 및 Media Services 및 다른 관련 된 문서에 대 한 링크를 사용 하 여 라이브 스트리밍의 지침을 제공 합니다.

> [!NOTE]
> 현재는 Azure Portal을 사용하여 v3 리소스를 관리할 수 없습니다. 사용 된 [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref), 또는 지원 되는 하나 [Sdk](developers-guide.md).

## <a name="dynamic-packaging"></a>동적 패키징

Media Services를 사용 하 여 미리 보기에서 라이브 스트림에서 브로드캐스트를 허용 하는 동적 Packaging](dynamic-packaging-overview.md) 활용을 걸릴 수 있습니다 [MPEG DASH, HLS 및 부드러운 스트리밍 형식](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) 기여에서 서비스에 전송 하는 피드입니다. 뷰어는 HLS, DASH 또는 부드러운 스트리밍이 호환되는 플레이어를 사용해 라이브 스트림을 재생할 수 있습니다. 웹 또는 모바일 애플리케이션에서 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html)를 사용하여 이러한 프로토콜 중 하나로 스트림을 전달할 수 있습니다.

## <a name="dynamic-encryption"></a>동적 암호화

동적 암호화를 사용 하면 AES-128 또는 세 가지 주요 디지털 rights management (DRM) 시스템 중 하나를 사용 하 여 라이브 또는 주문형 콘텐츠를 동적으로 암호화할 수 있습니다. 동적 암호화된 라이브 콘텐츠 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. 자세한 내용은 [동적 암호화](content-protection-overview.md)합니다.

## <a name="dynamic-manifest"></a>Dynamic Manifest

동적 필터링은 추적, 형식, 비트 전송률을 및 플레이어에 전송 되는 프레젠테이션 기간 수를 제어 하는 데 사용 됩니다. 자세한 내용은 [필터 및 동적 매니페스트](filters-dynamic-manifest-overview.md)합니다.

## <a name="live-event-types"></a>라이브 이벤트 유형

라이브 이벤트는 두 가지 유형 중 하나일 수 있습니다: 통과 및 라이브 인코딩. Media Services v3의 라이브 스트리밍에 대 한 자세한 내용은 참조 하세요 [라이브 이벤트 및 라이브 출력](live-events-outputs-concept.md)합니다.

### <a name="pass-through"></a>통과

![통과](./media/live-streaming/pass-through.svg)

통과 **라이브 이벤트**를 사용하는 경우 온-프레미스 라이브 인코더에 의존하여 다중 비트 전송률이 있는 비디오 스트림을 생성하고 이 스트림을 기여 피드로 하여 LiveEvent에 전송(RTMP 또는 조각난 MP4 프로토콜 사용)할 수 있습니다. 그러면 라이브 이벤트는 들어오는 비디오 스트림을 추가 처리 없이 제공합니다. 실행 시간이 긴 라이브 이벤트에 대 한 이러한 통과 라이브 이벤트를 최적화 하거나 24 x 365 선형 라이브 스트리밍. 

### <a name="live-encoding"></a>라이브 인코딩  

![라이브 인코딩](./media/live-streaming/live-encoding.svg)

Media Services에서 라이브 인코딩을 사용하는 경우 단일 비트 전송률 비디오를 기여 피드로 하여 라이브 이벤트에 전송(RTMP 또는 조각난 Mp4 프로토콜 사용)하도록 온-프레미스 라이브 인코더를 구성합니다. 라이브 이벤트는 들어오는 단일 비트 전송률 스트림을 [다중 비트 전송률이 있는 비디오 스트림](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)으로 인코딩하고 MPEG-DASH, HLS, 부드러운 스트리밍 등과 같은 프로토콜을 통해 디바이스를 재생하도록 이 스트림을 전달 가능하게 만듭니다. 

## <a name="live-streaming-workflow"></a>라이브 스트리밍 워크플로

Media Services v3의 라이브 스트리밍 워크플로 이해 하려면 먼저 검토 해야 하 고 다음 개념을 이해: 

- [스트리밍 엔드포인트](streaming-endpoint-concept.md)
- [라이브 이벤트 및 라이브 출력](live-events-outputs-concept.md)
- [스트리밍 로케이터](streaming-locators-concept.md)

### <a name="general-steps"></a>일반 단계

1. Media Services 계정에서 **스트리밍 엔드포인트**가 실행되고 있는지 확인합니다. 
2. [라이브 이벤트](live-events-outputs-concept.md)를 만듭니다. <br/>이벤트를 만들 때 자동 시작을 지정할 수 있습니다. 또는 스트리밍을 시작할 준비가 되었을 때 이벤트를 시작해도 됩니다.<br/> autostart를 true로 설정하면 Live Event가 생성 직후 시작됩니다. 라이브 이벤트가 실행되는 즉시 청구가 시작됩니다. 추가 청구를 중지하려면 라이브 이벤트 리소스에 대해 명시적으로 Stop을 호출해야 합니다. 자세한 내용은 [라이브 이벤트 상태 및 청구](live-event-states-billing.md)를 참조하세요.
3. 수집 URL을 가져오고, URL을 사용하여 기여 피드를 보내도록 온-프레미스 인코더를 구성합니다.<br/>[추천되는 라이브 인코더](recommended-on-premises-live-encoders.md)를 참조하세요.
4. 미리 보기 URL을 가져와서 인코더의 입력이 실제로 수신되고 있는지 확인하는 데 사용합니다.
5. 새 **자산** 개체를 만듭니다.
6. **라이브 출력**을 만들고 만든 자산 이름을 사용합니다.<br/>**라이브 출력**은 스트림을 **자산**에 보관합니다.
7. **스트리밍 정책** 유형이 기본 제공된 **스트리밍 로케이터**를 만듭니다.<br/>콘텐츠를 암호화하려면 [콘텐츠 보호 개요](content-protection-overview.md)를 검토하세요.
8. **스트리밍 로케이터**의 경로를 나열하여 사용할 URL(결정적)을 다시 가져옵니다.
9. 스트리밍을 내보낼 **스트리밍 엔드포인트**의 호스트 이름을 가져옵니다.
10. 8단계의 URL을 9단계의 호스트 이름과 결합하여 전체 URL을 구합니다.
11. **라이브 이벤트**를 더 이상 표시하지 않으려면 이벤트 스트리밍을 중지하고 **스트리밍 로케이터**를 삭제해야 합니다.

## <a name="other-important-articles"></a>다른 중요 문서

- [추천되는 라이브 인코더](recommended-on-premises-live-encoders.md)
- [클라우드 DVR 사용](live-event-cloud-dvr.md)
- [라이브 이벤트 유형의 기능 비교](live-event-types-comparison.md)
- [상태 및 청구](live-event-states-billing.md)
- [대기 시간](live-event-latency.md)

## <a name="next-steps"></a>다음 단계

* [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)
* [Media Services v2에서 v3로 이동하기 위한 마이그레이션 지침](migrate-from-v2-to-v3.md)
