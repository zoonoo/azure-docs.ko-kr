---
title: Azure Media Services를 사용하는 라이브 스트리밍 개요 | Microsoft Docs
description: 이 항목에서는 Azure Media Services v3를 사용하는 라이브 스트리밍 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: e9ecf1ba3022ca057fa09bad2413aa19d902ae23
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972182"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Azure Media Services v3를 통한 라이브 스트리밍

Azure Media Services를 사용하여 라이브 스트리밍 이벤트를 제공할 때 다음 구성 요소가 일반적으로 포함됩니다.

* 이벤트를 브로드캐스트하는 데 사용되는 카메라
* 카메라(또는 노트북과 같은 다른 장치)에서 Media Services 라이브 스트리밍 서비스로 보내는 스트림으로 신호를 변환하는 라이브 비디오 인코더입니다. 신호에는 SCTE-35 및 Ad-cues 광고가 포함될 수도 있습니다. 
* Media Services 라이브 스트리밍 서비스를 사용하면 콘텐츠를 수집, 미리 보기, 패키지화, 기록, 암호화할 수 있으며 고객 또는 CDN(추가 배포를 위해)에 브로드캐스트할 수 있습니다.

이 문서에서는 Media Services의 라이브 스트리밍과 관련된 주요 구성 요소의 다이어그램과 자세한 개요를 제공합니다.

## <a name="overview-of-main-components"></a>미디어 구성 요소 개요

[LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents)는 Media Services에서 라이브 스트리밍 콘텐츠 처리를 담당합니다. LiveEvent는 온-프레미스 라이브 인코더에 제공할 입력 엔드포인트(수집 URL)를 제공합니다. LiveEvent는 라이브 인코더에서 RTMP 또는 부드러운 스트리밍 형식으로 라이브 입력 스트림을 수신하여 하나 이상의 [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints)를 통해 스트리밍하는 데 사용할 수 있도록 합니다. [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs)을 사용하면 라이브 스트림의 게시, 기록 및 DVR 창 설정을 제어할 수 있습니다. 또한 LiveEvent는 스트림을 추가로 처리하고 배달하기 전에 미리 보고 유효성을 검색하는 데 사용되는 미리 보기 엔드포인트(미리 보기 URL)를 제공합니다. 

Media Services는 **동적 패키징**을 제공하며 이는 콘텐츠를 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍)으로 수동으로 다시 패키지하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 합니다. HLS, DASH 또는 Smooth 호환 플레이어로 재생할 수 있습니다. [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html)를 사용하여 스트림을 테스트할 수도 있습니다.

Media Services를 사용하면 AES-128(Advanced Encryption Standard) 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나로 **동적 암호화**된 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다.

원하는 경우 **동적 필터링**을 적용하여 플레이어에 전송되는 트랙 번호, 형식, 비트 전송률을 제어할 수 있습니다. Media Services는 광고 삽입도 지원합니다.


## <a name="liveevent-types"></a>LiveEvent 형식

[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents)는 라이브 인코딩 및 통과라는 두 가지 형식 중 하나일 수 있습니다. 

### <a name="live-encoding-with-media-services"></a>Media Services를 사용하여 라이브 인코딩

![라이브 인코딩](./media/live-streaming/live-encoding.png)

온-프레미스 라이브 인코더는 단일 비트 전송률 스트림을 Media Services를 통해 RTMP 또는 부드러운 스트리밍(조각화된 MP4) 프로토콜 중 하나로 라이브 인코딩할 수 있는 LiveEvent에 전송합니다. 그러면 LiveEvent는 들어오는 단일 비트 전송률 스트림을 다중 비트 전송률(적응) 비디오 스트림으로 라이브 인코딩합니다. 요청된 경우 Media Services는 고객에게 스트림을 배달합니다.

이 형식의 LiveEvent를 만들 경우 **기본**(LiveEventEncodingType.Basic)을 지정합니다.

### <a name="pass-through"></a>통과

![통과](./media/live-streaming/pass-through.png)

통과는 온-프레미스 라이브 인코더를 사용한 장기 실행 라이브 스트림 또는 연중 무휴 선형 라이브 인코딩에 최적화되어 있습니다. 온-프레미스 인코더가 다중 비트 전송률 **RTMP** 또는 **부드러운 스트리밍**(조각화된 MP4)을 **통과** 전달을 위해 구성된 LiveEvent에 보냅니다. 어떠한 추가적인 처리 없이 수집된 스트림이 **LiveEvent**를 통과하는 경우를 **통과** 전달이라고 합니다. 

통과 LiveEvent는 최대 4K 해상도를 지원할 수 있으며 HEVC는 부드러운 스트리밍 수집 프로토콜과 함께 사용될 때 통과합니다. 

이 형식의 LiveEvent를 만들 경우 **없음**(LiveEventEncodingType.None)을 지정합니다.

> [!NOTE]
> 통과 방법을 사용하면 긴 기간 동안 여러 이벤트를 수행하고 온-프레미스 인코더에 이미 투자한 경우 라이브 스트리밍을 수행하는 가장 경제적인 방법입니다. [가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 세부 정보를 참조하세요.
> 

## <a name="liveevent-types-comparison"></a>LiveEvent 형식 비교 

다음 테이블에서는 두 가지 LiveEvent 형식의 기능을 비교합니다.

| 기능 | 통과 LiveEvent | 기본 LiveEvent |
| --- | --- | --- |
| 단일 비트 전송률 입력은 클라우드에서 다중 비트 전송률로 인코딩됩니다. |아니오 |예 |
| 최대 해상도, 계층 수 |4Kp30  |720p, 6계층, 30fps |
| 입력 프로토콜 |RTMP, 부드러운 스트리밍 |RTMP, 부드러운 스트리밍 |
| 가격 |[가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조하고 "라이브 비디오" 탭 클릭 |[가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) |
| 최대 실행 시간 |연중 무휴 |연중 무휴 |
| 슬레이트 삽입 지원 |아니오 |예 |
| API를 통한 광고 신호 지원|아니오 |예 |
| SCTE35 인밴드를 통한 광고 신호 지원|예 |예 |
| 통과 CEA 608/708 캡션 |예 |예 |
| 기여 피드의 일시 정지에서 복구하는 기능 |예 |아니요(LiveEvent는 입력 데이터가 6초 이상 없으면 슬레이팅을 시작함)|
| 균일하지 않은 입력 GOP에 대한 지원 |예 |아니요 - 입력은 고정된 2초 GOP여야 함 |
| 변수 프레임 속도 입력에 대한 지원 |예 |아니요 - 입력은 고정된 프레임 속도여야 함.<br/>예를 들어 움직임이 많은 장면 중에는 사소한 차이가 허용됩니다. 하지만 인코더는 10프레임/초까지 떨어질 수 없습니다. |
| 입력 피드가 손실될 경우 LiveEvent 자동 차단 |아니오 |12시간 동안 LiveOutput 실행이 없는 경우 |

## <a name="liveevent-states"></a>LiveEvent 상태 

LiveEvent의 현재 상태입니다. 가능한 값은 다음과 같습니다.

|시스템 상태|설명|
|---|---|
|**중지**| 이는 LiveEvent를 만든 후 초기 상태입니다(지정된 위치에서 자동 시작을 선택하지 않은 경우). 이 상태에서는 요금이 청구되지 않습니다. 이 상태에서 LiveEvent 속성을 업데이트할 수 있지만 스트리밍은 허용되지 않습니다.|
|**시작 중**| LiveEvent가 시작됩니다. 이 상태에서는 요금이 청구되지 않습니다. 이 상태에서는 업데이트 또는 스트리밍이 허용되지 않습니다. 오류가 발생하면 LiveEvent가 중지 상태로 돌아갑니다.|
|**실행 중**| 라이브 스트림 처리에 LiveEvent를 사용할 수 있습니다. 이제 사용 요금이 청구됩니다. 추가 요금 청구를 방지하기 위해 LiveEvent를 중지해야 합니다.|
|**중지 중**| LiveEvent가 중지됩니다. 이 일시적인 상태에서는 요금이 청구되지 않습니다. 이 상태에서는 업데이트 또는 스트리밍이 허용되지 않습니다.|
|**삭제 중**| LiveEvent가 삭제됩니다. 이 일시적인 상태에서는 요금이 청구되지 않습니다. 이 상태에서는 업데이트 또는 스트리밍이 허용되지 않습니다.|

## <a name="liveoutput"></a>LiveOutput

[LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs)을 사용하면 라이브 스트림의 게시, 기록 및 DVR 창 설정을 제어할 수 있습니다. LiveEvent 및 LiveOutput 관계는 기존 미디어와 유사하여 채널(LiveEvent)에는 일정한 콘텐츠 스트림이 있고 프로그램(LiveOutput) 범위는 해당 LiveEvent에 있는 일부 시간 제한 이벤트로 지정됩니다.
**ArchiveWindowLength** 속성을 설정하여 LiveOutput에 대해 기록된 콘텐츠를 유지할 시간을 지정할 수 있습니다. **ArchiveWindowLength**는 보관 창 길이(디지털 비디오 레코더 또는 DVR)의 ISO 8601 timespan 기간입니다. 이 값은 최소 5분에서 최대 25시간 사이로 설정할 수 있습니다. 

또한 **ArchiveWindowLength**는 클라이언트가 현재 라이브 위치에서 이전 시간을 검색할 수 있는 최대 시간을 나타냅니다. LiveOutput은 지정된 시간 동안 실행되지만 기간 길이보다 늦는 콘텐츠는 계속 삭제됩니다. 또한 이 속성의 값은 클라이언트 매니페스트가 증가할 수 있는 길이를 결정합니다.

각 LiveOutput은 [자산](https://docs.microsoft.com/rest/api/media/assets)과 연결되어 있으며 Media Services 계정에 연결된 Azure 저장소의 컨테이너에 데이터를 기록합니다. LiveOutput을 게시하려면 연결된 자산에 대한 [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만들어야 합니다. 이 로케이터가 있으면 클라이언트에 제공할 수 있는 스트리밍 URL을 작성할 수 있습니다.

LiveEvent는 동시 실행 LiveOutput을 최대 세 개까지 지원하므로 동일한 수신 스트림의 보관 파일을 여러 개 만들 수 있습니다. 따라서 이벤트의 여러 부분을 필요에 따라 게시하고 보관할 수 있습니다. 예를 들어 비즈니스 요구 사항은 연중 무휴 라이브 선형 피드를 브로드캐스트하는 것이지만, 하루 동안의 프로그램 "기록"을 만들어 다시 보기 용도의 주문형 콘텐츠를 고객에게 제공하고 싶습니다.  이 시나리오에서는 먼저 고객이 기본 라이브 스트림으로 튜닝할 수 있도록 1시간 이하의 짧은 보관 창을 사용하여 기본 LiveOutput을 만듭니다. 이 LiveOutput용 StreamingLocator를 만들어 응용 프로그램 또는 웹 사이트에 “라이브” 피드로 게시합니다.  피드가 실행되면, 쇼 시작 부분에 두 번째 동시 LiveOutput을 프로그래밍 방식으로 만들 수 있습니다(또는 나중에 잘라낼 핸들을 제공하기 위해 5분 일찍). 이 두 번째 LiveOutput은 프로그램이나 이벤트가 끝나고 5분 후에 중지될 수 있습니다. 그러면 새 StreamingLocator를 만들어 이 프로그램을 응용 프로그램 카탈로그의 주문형 자산으로 게시할 수 있습니다.  첫 번째 LiveOutput의 “라이브” 피드가 선형 피드를 계속 브로드캐스트하는 동안, 주문 시 즉시 공유하려는 다른 프로그램 경계 또는 하이라이트에 대해 이 프로세스를 여러 번 반복할 수 있습니다.  또한 동적 필터 지원을 활용하여 프로그램 간 “다중 보안”을 위해 도입한 LiveOutput에서 보관의 시작과 끝 부분을 잘라내어 보다 정확하게 콘텐츠의 시작과 끝을 보관할 수 있습니다. 보관된 콘텐츠는 다른 서비스로의 배포로 사용할 여러 출력 형식으로 정확히 서브클리핑되는 인코딩 또는 프레임을 위해 [변환](https://docs.microsoft.com/rest/api/media/transforms)에 제출될 수도 있습니다.

## <a name="streamingendpoint"></a>StreamingEndpoint

LiveEvent로 들어오는 스트림이 있으면 자산, LiveOutput 및 StreamingLocator를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 이렇게 하면 스트림이 보관되고 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints)를 통해 시청자가 스트림을 사용할 수 있게 됩니다.

Azure Media Services 계정이 만들어지면, 기본 스트리밍 엔드포인트가 중지됨 상태의 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 실행 상태에 있어야 합니다.

## <a name="billing"></a>결제

LiveEvent는 상태가 “실행 중”으로 전환되면 그 즉시 청구되기 시작됩니다. LiveEvent가 청구되지 않도록 하려면 LiveEvent를 중지해야 합니다.

> [!NOTE]
> [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents)에서 **LiveEventEncodingType**이 기본으로 설정되면 Media Services는 입력 피드가 손실된 후 12시간 동안 실행 중인 LiveOutput이 없으면 "실행 중" 상태인 모든 LiveEvent를 자동 차단합니다. 그러나 LiveEvent가 “실행 중” 상태였던 시간에 대한 요금은 청구됩니다.
>

다음 표에서는 LiveEvent 상태가 청구 모드에 매핑되는 방식을 보여 줍니다.

| LiveEvent 상태 | 요금이 청구됩니까? |
| --- | --- |
| 시작 중 |없음(일시적인 상태) |
| 실행 중 |예 |
| 중지 중 |없음(일시적인 상태) |
| 중지됨 |아니오 |

## <a name="next-steps"></a>다음 단계

[라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)
