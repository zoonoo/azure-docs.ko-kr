---
title: Azure Media Services LiveEvent 형식 | Microsoft Docs
description: Azure Media Services에서 라이브 이벤트는 *통과* 또는 *라이브 인코딩으로*설정할 수 있습니다. 이 문서에서는 라이브 이벤트 유형을 비교하는 자세한 표를 보여 주며, 이 에 대한 자세한 설명입니다.
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: a28d4d96f643c12eeb6aa542db2c6af06f4fd954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244634"
---
# <a name="live-event-types-comparison"></a>라이브 이벤트 유형 비교

Azure Media Services에서 [라이브 이벤트는](https://docs.microsoft.com/rest/api/media/liveevents) *통과(온-프레미스* 라이브 인코더가 여러 비트 전송률 스트림을 전송) 또는 라이브 *인코딩(온-프레미스* 라이브 인코더가 단일 비트 레이트 스트림을 보냅니다)으로 설정할 수 있습니다. 

이 문서에서는 라이브 이벤트 유형의 기능을 비교합니다.

## <a name="types-comparison"></a>형식 비교 

다음 표에서는 라이브 이벤트 유형의 기능을 비교합니다. 형식은 [LiveEventEncodingType을](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)사용 하 여 만드는 동안 설정 됩니다.

* **LiveEventEncodingType.None** - 온-프레미스 라이브 인코더는 여러 비트 전송률 스트림을 보냅니다. 인제스트 스트림은 추가 처리 없이 라이브 이벤트를 통과합니다. 통과 라이브 이벤트라고도 합니다.
* **LiveEventEncodingType.Standard** - 온-프레미스 라이브 인코더는 단일 비트 레이트 스트림을 라이브 이벤트로 보내고 미디어 서비스는 여러 비트 전송률 스트림을 만듭니다. 기여 피드가 720p 이상인 경우 **Default720p** 사전 설정은 6개의 해상도/비트레이트 쌍 세트를 인코딩합니다(자세한 내용은 문서의 후반부참조).
* **LiveEventEncodingType.Premium1080p** - 온-프레미스 라이브 인코더는 라이브 이벤트에 단일 비트 레이트 스트림을 보내고 미디어 서비스는 여러 비트 레이트 스트림을 만듭니다. Default1080p 사전 설정은 해상도/비트 레이트 쌍의 출력 집합을 지정합니다(자세한 내용은 문서의 다음 참조). 

| 기능 | 통과 라이브 이벤트 | 표준 또는 프리미엄1080p 라이브 이벤트 |
| --- | --- | --- |
| 단일 비트 전송률 입력은 클라우드에서 다중 비트 전송률로 인코딩됩니다. |예 |yes |
| 기여 피드에 대한 최대 비디오 해상도 |4K(60프레임/초에서 4096x2160) |1080p(30프레임/초에서 1920x1088)|
| 기여 피드에 권장되는 최대 계층|최대 12개|하나의 오디오|
| 출력의 최대 계층| 입력과 같음|최대 6개(아래 시스템 사전 설정 참조)|
| 기여 피드의 최대 집계 대역폭|60Mbps|해당 없음|
| 기여에서 단일 계층의 최대 전송률 |20Mbps|20Mbps|
| 여러 언어 오디오 트랙 지원|yes|예|
| 지원되는 입력 비디오 코덱 |H.264/AVC 및 H.265/HEVC|H.264/AVC|
| 지원되는 출력 비디오 코덱|입력과 같음|H.264/AVC|
| 지원되는 비디오 비트 수준, 입력 및 출력|HDR 10/HLG를 포함하여 최대 10비트|8비트|
| 지원되는 입력 오디오 코덱|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| 지원되는 출력 오디오 코덱|입력과 같음|AAC-LC|
| 출력 비디오의 최대 비디오 해상도|입력과 같음|표준 - 720p, 프리미엄1080p - 1080p|
| 입력 비디오의 최대 프레임 속도|초당 60프레임|표준 또는 프리미엄1080p - 초당 30프레임|
| 입력 프로토콜|RTMP, fragmented-MP4(부드러운 스트리밍)|RTMP, fragmented-MP4(부드러운 스트리밍)|
| Price|[가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조하고 "라이브 비디오" 탭 클릭|[가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조하고 "라이브 비디오" 탭 클릭|
| 최대 실행 시간| 연중무휴 라이브 선형 | 24시간 x 365일, 라이브 리니어(미리보기)|
| 포함된 CEA 608/708 캡션 데이터 전달 가능|yes|yes|
| 라이브 전사를 켜는 기능|yes|yes|
| 슬레이트 삽입 지원|예|예|
| API를 통한 광고 신호 지원| 예|예|
| SCTE-35 인밴드 메시지를 통한 광고 신호 지원|yes|yes|
| 기여 피드의 일시 정지에서 복구하는 기능|yes|Partial|
| 균일하지 않은 입력 GOP에 대한 지원|yes|아니요 - 입력에 고정된 GOP 지속 기간이 있어야 함|
| 변수 프레임 속도 입력에 대한 지원|yes|아니요 - 입력은 고정된 프레임 속도여야 함. 예를 들어 움직임이 많은 장면 중에는 사소한 차이가 허용됩니다. 그러나 기여 피드는 프레임 속도를 떨어뜨릴 수 없습니다(예: 초당 15프레임).|
| 입력 피드가 손실될 경우 라이브 이벤트 자동 차단|예|12시간 동안 LiveOutput 실행이 없는 경우|

## <a name="system-presets"></a>시스템 사전 설정

라이브 인코더의 출력에 포함된 해상도 및 비트 비율은 [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding)에 의해 결정됩니다. **표준** 라이브 인코더(LiveEventEncodingType.Standard)를 사용하는 경우 *Default720p* 사전 설정은 아래에 설명된 6개의 해상도/비트레이트 쌍 집합을 지정합니다. 그렇지 않으면 **Premium1080p** 라이브 인코더(LiveEventEncodingType.Premium1080p)를 사용하는 경우 *Default1080p* 사전 설정은 해상도/비트레이트 쌍의 출력 집합을 지정합니다.

> [!NOTE]
> 표준 라이브 인코딩에 대해 설정된 경우 Default1080p 사전 설정을 라이브 이벤트에 적용할 수 없습니다. Premium1080p 라이브 인코더에 Default720p 사전 설정을 적용하려고 하면 오류가 발생합니다.

### <a name="output-video-streams-for-default720p"></a>기본값720p에 대한 비디오 스트림 출력

기여 피드가 720p 이상인 경우 **Default720p** 사전 설정은 피드를 다음 6개 레이어로 인코딩합니다. 아래 표에서 비트레이트(Bitrate)는 kbps, MaxFPS는 허용되는 최대 프레임 속도(프레임/초)를 나타내며, 프로필은 사용된 H.264 프로파일을 나타냅니다.

| Bitrate | 너비 | 높이 | MaxFPS | 프로필 |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |높음 |
| 2200 |960 |540 |30 |높음 |
| 1350 |704 |396 |30 |높음 |
| 850 |512 |288 |30 |높음 |
| 550 |384 |216 |30 |높음 |
| 200 |340 |192 |30 |높음 |

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정해야 하는 경우 Azure Portal을 통해 지원 티켓을 열십시오. 원하는 해상도 및 비트 전송률 표를 지정해야 합니다. 720p에서 레이어가 하나만 있고, 6레이어 이하인지 확인합니다. 또한 표준 라이브 인코더에 대한 사전 설정을 요청하도록 지정합니다.
> 비트 레이트와 해상도의 특정 값은 시간이 지남에 따라 조정 될 수 있습니다.

### <a name="output-video-streams-for-default1080p"></a>기본1080p에 대한 출력 비디오 스트림

기여 피드가 1080p 해상도인 경우 **Default1080p** 사전 설정은 피드를 다음 6개 레이어로 인코딩합니다.

| Bitrate | 너비 | 높이 | MaxFPS | 프로필 |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |높음 |
| 3000 |1280 |720 |30 |높음 |
| 1600 |960 |540 |30 |높음 |
| 800 |640 |360 |30 |높음 |
| 400 |480 |270 |30 |높음 |
| 200 |320 |180 |30 |높음 |

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정해야 하는 경우 Azure Portal을 통해 지원 티켓을 열십시오. 원하는 해상도 및 비트 전송률 표를 지정해야 합니다. 1080p에서 하나의 레이어와 6개의 레이어만 있는지 확인합니다. 또한 Premium1080p 라이브 인코더에 대한 사전 설정을 요청하도록 지정합니다.
> 비트 레이트와 해상도의 특정 값은 시간이 지남에 따라 조정 될 수있다.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>기본720p 및 기본1080p용 출력 오디오 스트림

*Default720p* 및 *Default1080p* 프리셋 모두에서 오디오는 128kbps의 스테레오 AAC-LC로 인코딩됩니다. 샘플링 속도는 기여 피드의 오디오 트랙의 샘플링 속도를 따릅니다.

## <a name="implicit-properties-of-the-live-encoder"></a>라이브 인코더의 암시적 속성

이전 섹션에서는 레이어 수, 해상도 및 비트 레이트와 같은 사전 설정을 통해 명시적으로 제어할 수 있는 라이브 인코더의 특성에 대해 설명합니다. 이 섹션에서는 암시적 속성을 명확히 합니다.

### <a name="group-of-pictures-gop-duration"></a>사진 그룹(GOP) 지속 시간

라이브 인코더는 기여 피드의 [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) 구조를 따릅니다. 따라서 GOP 지속 시간(일반적으로 2초)이 고정된 기여 피드를 생성하도록 온-프레미스 인코더를 구성하는 것이 좋습니다. 이렇게 하면 서비스에서 나가는 HLS 및 MPEG DASH 스트림에도 GOP 기간이 고정되어 있습니다. GOP 기간의 작은 변화는 대부분의 장치에서 허용될 수 있습니다.

### <a name="frame-rate"></a>프레임 속도

라이브 인코더는 기여 피드에서 개별 비디오 프레임의 지속 시간을 따릅니다. 따라서 온-프레미스 인코더를 구성하여 고정 프레임 속도(최대 30프레임/초)의 기여 피드를 생성하는 것이 좋습니다. 이렇게 하면 서비스에서 나가는 HLS 및 MPEG DASH 스트림에도 고정된 프레임 속도 기간이 있습니다. 프레임 속도의 작은 변화는 대부분의 장치에서 허용될 수 있지만 라이브 인코더가 올바르게 재생되는 출력을 생성한다는 보장은 없습니다. 온-프레미스 라이브 인코더는 프레임을 삭제해서는 안 됩니다(예: 배터리 부족 조건에서 또는 어떤 식으로든 프레임 속도를 변화시가지 않습니다.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>기여 피드 및 출력 레이어 의 해상도

라이브 인코더는 기여 피드를 업convert변환하지 않도록 구성됩니다. 따라서 출력 레이어의 최대 해상도는 기여 피드의 해상도를 초과하지 않습니다.

예를 들어 720p에서 기여 피드를 Default1080p 라이브 인코딩용으로 구성된 라이브 이벤트에 보내는 경우 출력에는 3Mbps에서 720p로 시작하여 200kbps에서 1080p로 내려가는 5개의 레이어만 있습니다. 또는 360p에서 기여 피드를 표준 라이브 인코딩용으로 구성된 라이브 이벤트로 보내면 출력에 3개의 레이어(해상도 288p, 216p 및 192p)가 포함됩니다. 퇴화된 경우 표준 라이브 인코더에 160x90 픽셀의 기여 피드를 보내면 출력에는 기여 피드와 동일한 비트 레이트에서 160x90 해상도의 한 레이어가 포함됩니다.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>기여 피드 및 출력 계층의 비트레이트

라이브 인코더는 기여 피드의 비트 레이트에 관계없이 사전 설정의 비트 레이트 설정을 준수하도록 구성됩니다. 결과적으로 출력 레이어의 비트 레이트는 기여 피드의 비트 레이트(bitrate)를 초과할 수 있습니다. 예를 들어 기여 피드를 1Mbps에서 720p의 해상도로 보내는 경우 출력 계층은 위의 [표와](live-event-types-comparison.md#output-video-streams-for-default720p) 동일하게 유지됩니다.

## <a name="next-steps"></a>다음 단계

[라이브 스트리밍 개요](live-streaming-overview.md)
