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
ms.date: 12/26/2018
ms.author: juliako
ms.openlocfilehash: 3a2b3752926a3a4391ae9479ba636694533c97a8
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788211"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Azure Media Services v3를 통한 라이브 스트리밍

Azure Media Services를 사용하면 Azure 클라우드에서 고객에게 라이브 이벤트를 전달할 수 있습니다. Media Services로 라이브 이벤트를 스트리밍하려면 다음이 필요합니다.  

- 라이브 이벤트를 캡처하는 데 사용되는 카메라.
- 카메라(또는 노트북과 같은 다른 디바이스)의 신호를 Media Services로 전송되는 기여 피드로 변환하는 라이브 비디오 인코더. 기여 피드에는 SCTE-35 마커와 같은 광고 관련 신호가 포함될 수 있습니다.
- Media Services의 구성 요소. 이러한 구성 요소를 사용하여 라이브 이벤트를 수집, 미리 보기, 패키지화, 기록, 암호화할 수 있으며 고객 또는 CDN(추가 배포를 위해)에 브로드캐스트할 수 있습니다.

이 문서에서는 Media Services의 라이브 스트리밍과 관련된 기본 구성 요소의 다이어그램과 자세한 개요 및 지침을 제공합니다.

## <a name="overview-of-main-components"></a>미디어 구성 요소 개요

Media Services를 사용하여 주문형 또는 라이브 스트림을 제공하려면 최소한 하나의 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints)가 있어야 합니다. Media Services 계정이 만들어지면 **기본** StreamingEndpoint가 계정에 **중지됨** 상태로 추가됩니다. 뷰어에 콘텐츠를 스트리밍할 StreamingEndpoint를 시작해야 합니다. 기본 **StreamingEndpoint**를 사용하거나 원하는 구성 및 CDN 설정으로 사용자 지정된 다른 **StreamingEndpoint**를 만들 수 있습니다. 각각이 서로 다른 CDN을 대상으로 하는 여러 개의 StreamingEndpoint를 사용하도록 지정하고 콘텐츠 전달을 위한 고유한 호스트 이름을 제공할 수도 있습니다. 

Media Services에서 [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents)는 라이브 비디오 피드 수집 및 처리를 담당합니다. LiveEvent를 만들면 원격 인코더에서 라이브 신호를 보내는 데 사용할 수 있는 입력 엔드포인트가 생성됩니다. 원격 라이브 인코더는 [RTMP](https://www.adobe.com/devnet/rtmp.html) 또는 [부드러운 스트리밍](https://msdn.microsoft.com/library/ff469518.aspx)(조각난 MP4) 프로토콜을 사용하여 이 입력 엔드포인트에 기여 피드를 전송합니다. 부드러운 스트리밍 수집 프로토콜의 경우 지원되는 URL 체계는 `http://` 또는 `https://`입니다. RTMP 수집 프로토콜의 경우 지원되는 URL 체계는 `rtmp://` 또는 `rtmps://`입니다. 자세한 내용은 [권장 라이브 스트리밍 인코더](recommended-on-premises-live-encoders.md)를 참조하세요.

**LiveEvent**가 기여 피드를 수신하기 시작하면 미리 보기 엔드포인트(미리 보기 URL)를 사용하여 추가 게시 전에 라이브 스트림을 수신하고 있음을 미리 보고 확인할 수 있습니다. 미리 보기 스트림이 양호한지 확인한 후에는 LiveEvent를 사용하여 하나 이상의 (미리 생성된) **StreamingEndpoint**를 통해 라이브 스트림을 전달 가능하도록 만들 수 있습니다. 이를 수행하려면 **LiveEvent**에 새 [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs)을 만듭니다. 

**LiveOutput** 개체는 테이프 레코더처럼 라이브 스트림을 포착하고 Media Services 계정의 자산에 기록합니다. 기록된 콘텐츠는 계정에 연결된 Azure Storage 계정뿐 아니라 자산 리소스에서 정의한 컨테이너에서도 유지됩니다.  **LiveOuput**을 사용하면 보관 기록에 유지되는 스트림의 양(예: 클라우드 DVR의 용량)을 비롯하여, 나가는 라이브 스트림의 일부 속성을 제어할 수도 있습니다. 디스크의 보관은 **LiveOutput**의 **archiveWindowLength** 속성에 지정된 양의 콘텐츠만 보유하는 순환식 보관 “기간”입니다. 이 기간을 벗어나는 콘텐츠는 스토리지 컨테이너에서 자동으로 삭제되며 복구할 수 없습니다. LiveEvent에서 보관 기간 및 설정이 서로 다른 여러 개의 LiveOutput(최대 3개)을 만들 수 있습니다.  

Media Services를 사용하면 기여 피드에서 서비스로 전송하는 라이브 스트림을 미리 보고 [MPEG DASH, HLS 및 부드러운 스트리밍 형식](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)으로 브로드캐스트할 수 있는 **동적 패키징**을 활용할 수 있습니다. 뷰어는 HLS, DASH 또는 부드러운 스트리밍이 호환되는 플레이어를 사용해 라이브 스트림을 재생할 수 있습니다. 웹 또는 모바일 애플리케이션에서 [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html)를 사용하여 이러한 프로토콜 중 하나로 스트림을 전달할 수 있습니다.

Media Services를 사용하면 AES-128(Advanced Encryption Standard) 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나로 **동적 암호화**된 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에 AES 키 및 DRM 라이선스를 전달하는 서비스를 제공합니다. Media Services로 콘텐츠를 암호화하는 방법에 대한 자세한 내용은 [콘텐츠 보호 개요](content-protection-overview.md)를 참조하세요.

원하는 경우 동적 필터링을 적용하여 플레이어에 전송되는 트랙 번호, 형식, 비트 전송률 및 프레젠테이션 창을 제어할 수 있습니다. 자세한 내용은 [필터 및 동적 매니페스트](filters-dynamic-manifest-overview.md)를 참조하세요.

### <a name="new-capabilities-for-live-streaming-in-v3"></a>v3의 라이브 스트리밍을 위한 새로운 기능

Media Services의 v3 API를 사용하면 다음과 같은 새로운 기능을 활용할 수 있습니다.

- 새로운 짧은 대기 시간 모드 자세한 내용은 [대기 시간](live-event-latency.md)을 참조하세요.
- RTMP 지원 향상(향상된 안정성 및 더 많은 소스 인코더 지원)
- RTMPS 보안 수집<br/>LiveEvent를 만들면 수집 URL이 4개 생성됩니다. 4개의 수집 URL은 거의 동일하며 스트리밍 토큰(AppId)이 동일하고 포트 번호 부분만 다릅니다. URL 중 두 개는 RTMPS에 대한 기본 및 백업용입니다.   
- 단일 비트 전송률 기여 피드를 다중 비트 전송률이 있는 출력 스트림으로 코드 변환하기 위해 Media Services를 사용할 때 최대 24시간 분량의 라이브 이벤트를 스트리밍할 수 있습니다. 

## <a name="liveevent-types"></a>LiveEvent 형식

[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents)는 통과 및 라이브 인코딩의 두 가지 형식 중 하나일 수 있습니다. 

### <a name="pass-through"></a>통과

![통과](./media/live-streaming/pass-through.png)

통과 LiveEvent를 사용하는 경우 온-프레미스 라이브 인코더에 의존하여 다중 비트 전송률이 있는 비디오 스트림을 생성하고 이 스트림을 기여 피드로 하여 LiveEvent에 전송(RTMP 또는 조각난 MP4 프로토콜 사용)할 수 있습니다. 그러면 LiveEvent는 들어오는 비디오 스트림을 추가 처리 없이 제공합니다. 이러한 통과 LiveEvent는 장기 실행 라이브 이벤트 또는 연중무휴 선형 라이브 스트리밍에 최적화되어 있습니다. 이 형식의 LiveEvent를 만들 경우 None(LiveEventEncodingType.None)을 지정합니다.

H.264/AVC 또는 H.265/HEVC 비디오 코덱 및 AAC(AAC-LC, HE-AACv1 또는 HE-AACv2) 오디오 코덱을 사용하여 최대 4K의 해상도 및 초당 60프레임의 프레임 속도로 기여 피드를 전송할 수 있습니다.  자세한 내용은 [LiveEvent types comparison and limitations](live-event-types-comparison.md)(LiveEvent 형식 비교 및 제한 사항) 문서를 참조하세요.

> [!NOTE]
> 통과 방법을 사용하면 긴 기간 동안 여러 이벤트를 수행하고 온-프레미스 인코더에 이미 투자한 경우 라이브 스트리밍을 수행하는 가장 경제적인 방법입니다. [가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 세부 정보를 참조하세요.
> 

[MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126)의 예제를 참조하세요.

### <a name="live-encoding"></a>라이브 인코딩  

![라이브 인코딩](./media/live-streaming/live-encoding.png)

Media Services에서 라이브 인코딩을 사용하는 경우 단일 비트 전송률 비디오를 기여 피드로 하여 LiveEvent에 전송(RTMP 또는 조각난 Mp4 프로토콜 사용)하도록 온-프레미스 라이브 인코더를 구성합니다. LiveEvent는 들어오는 단일 비트 전송률 스트림을 [다중 비트 전송률이 있는 비디오 스트림](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)으로 인코딩하고 MPEG-DASH, HLS, 부드러운 스트리밍 등과 같은 프로토콜을 통해 디바이스를 재생하도록 이 스트림을 전달 가능하게 만듭니다. 이 형식의 LiveEvent를 만들 경우 인코딩 형식을 **표준**(LiveEventEncodingType.Standard)으로 지정합니다.

H.264/AVC 비디오 코덱과 AAC(AAC-LC, HE-AACv1 또는 HE-AACv2) 오디오 코덱을 사용하여 최대 1080p의 해상도 및 초당 30프레임의 프레임 속도로 기여 피드를 전송할 수 있습니다. 자세한 내용은 [LiveEvent types comparison and limitations](live-event-types-comparison.md)(LiveEvent 형식 비교 및 제한 사항) 문서를 참조하세요.

## <a name="liveevent-types-comparison"></a>LiveEvent 형식 비교

다음 문서에는 두 LiveEvent 형식의 기능을 비교하는 표가 나와 있습니다. [비교](live-event-types-comparison.md).

## <a name="liveoutput"></a>LiveOutput

[LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs)을 통해 기록되는 스트림 양(예: 클라우드 DVR의 용량), 시청자가 라이브 스트림을 보기 시작할 수 있는지 여부 등, 보내는 라이브 스트림의 속성을 제어할 수 있습니다. **LiveEvent**와 **LiveOutput** 간 관계는 채널(**LiveEvent**)이 일정한 비디오 스트림을 나타내고 기록(**LiveOutput**)이 특정 시간 구간으로 한정(예: 오후 6시 30분부터 7시까지 저녁 뉴스)된다는 점에서 기존 TV 방송과 유사합니다. 디지털 비디오 레코더(DVR)를 사용하여 TV를 기록할 수 있으며, LiveEvent에서는 이와 동등한 기능이 ArchiveWindowLength 속성을 통해 관리됩니다. 이 속성은 DVR의 용량을 지정하는 ISO-8601 타임스팬 기간(예: PTHH:MM:SS)이며, 최소 3분에서 최대 25시간까지 설정할 수 있습니다.


> [!NOTE]
> **LiveOutput**은 생성 시 시작하고 삭제되면 중지합니다. **LiveOutput**을 삭제할 경우 기본 **자산**과 자산의 콘텐츠는 삭제되지 않습니다.  

자세한 내용은 [클라우드 DVR 사용](live-event-cloud-dvr.md)을 참조하세요.

## <a name="streamingendpoint"></a>StreamingEndpoint

**LiveEvent**로 들어오는 스트림이 있으면 **자산**, **LiveOutput** 및 **StreamingLocator**를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 이렇게 하면 스트림이 보관되고 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints)를 통해 시청자가 스트림을 사용할 수 있게 됩니다.

Azure Media Services 계정이 만들어지면, 기본 스트리밍 엔드포인트가 중지됨 상태의 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 실행 상태에 있어야 합니다.

## <a name="a-idbilling-liveevent-states-and-billing"></a><a id="billing" />LiveEvent 상태 및 청구

LiveEvent는 상태가 **실행 중**으로 전환되면 그 즉시 청구되기 시작됩니다. LiveEvent가 청구되지 않도록 하려면 LiveEvent를 중지해야 합니다.

자세한 내용은 [상태 및 청구](live-event-states-billing.md)를 참조하세요.

## <a name="latency"></a>대기 시간

LiveEvents 대기 시간에 대한 자세한 내용은 [대기 시간](live-event-latency.md)을 참조하세요.

## <a name="live-streaming-workflow"></a>라이브 스트리밍 워크플로

라이브 스트리밍 워크플로의 단계는 다음과 같습니다.

1. LiveEvent를 만듭니다.
2. 새 자산 개체를 만듭니다.
3. LiveOutput을 만들고 만든 자산 이름을 사용합니다.
4. DRM을 사용하여 콘텐츠를 암호화하려는 경우 스트리밍 정책 및 콘텐츠 키를 만듭니다.
5. DRM을 사용하지 않는 경우에는 스트리밍 정책 유형이 내장된 스트리밍 로케이터를 만듭니다.
6. 스트리밍 정책의 경로를 나열하여 사용할 URL(이 URL은 결정적임)을 다시 가져옵니다.
7. 스트리밍을 내보낼 스트리밍 엔드포인트의 호스트 이름을 가져옵니다. 
8. 6단계의 URL을 7단계의 호스트 이름과 결합하여 전체 URL을 구합니다.

자세한 내용은 라이브 [Live .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live) 샘플을 기반으로 하는 [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [LiveEvent 형식 비교](live-event-types-comparison.md)
- [상태 및 청구](live-event-states-billing.md)
- [대기 시간](live-event-latency.md)
