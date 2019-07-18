---
title: Azure Media Services LiveEvent 형식 | Microsoft Docs
description: 이 아티클에서 LiveEvent 형식을 비교 하 여 자세한 테이블에 설명 합니다.
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
ms.openlocfilehash: 93f01513841d1174fea796f1615ab05df0a41af4
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150375"
---
# <a name="live-event-types-comparison"></a>라이브 이벤트 유형 비교

Azure Media Services에서 [라이브 이벤트](https://docs.microsoft.com/rest/api/media/liveevents)는 라이브 인코딩 및 통과라는 두 가지 형식 중 하나일 수 있습니다. 

## <a name="types-comparison"></a>형식 비교 

다음 표에서 라이브 이벤트 형식의 기능을 비교 합니다. 형식을 사용 하 여 생성 하는 동안 설정 됩니다 [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** -온-프레미스 라이브 인코더를 여러 비트 전송률 스트림을 보냅니다. 수집 된 스트림은 어떠한 추가적인 처리 없이 라이브 이벤트를 통해 전달 합니다. 
* **LiveEventEncodingType.Standard** -온-프레미스 라이브 인코더는 라이브 이벤트 및 Media Services는 단일 비트 전송률 스트림을 다중 비트 전송률 스트림을 만드는 보냅니다. 기여 피드의 720p 또는 더 높은 해상도의 경우는 **Default720p** 사전 설정 (이 문서의 뒷부분에 따라 세부 정보) 6 해상도/비트 전송률 쌍의 집합을 인코딩할 됩니다.
* **LiveEventEncodingType.Premium1080p** -온-프레미스 라이브 인코더는 라이브 이벤트 및 Media Services는 단일 비트 전송률 스트림을 다중 비트 전송률 스트림을 만드는 보냅니다. Default1080p 사전 설정 확인/비트 전송률 쌍 (이 문서의 뒷부분에 따라 세부 정보)의 출력 집합을 지정 합니다. 

| 기능 | 통과 라이브 이벤트 | 표준 또는 Premium1080p 라이브 이벤트 |
| --- | --- | --- |
| 단일 비트 전송률 입력은 클라우드에서 다중 비트 전송률로 인코딩됩니다. |아닙니다. |예 |
| 기여 피드에 대한 최대 비디오 해상도 |4K(60프레임/초에서 4096x2160) |1080p(30프레임/초에서 1920x1088)|
| 기여 피드에 권장되는 최대 계층|최대 12개|하나의 오디오|
| 출력의 최대 계층| 입력과 같음|(시스템 사전 설정 아래 참조) 하는 6으로 올림|
| 기여 피드의 최대 집계 대역폭|60Mbps|N/A|
| 기여에서 단일 계층의 최대 전송률 |20Mbps|20Mbps|
| 여러 언어 오디오 트랙 지원|예|아닙니다.|
| 지원되는 입력 비디오 코덱 |H.264/AVC 및 H.265/HEVC|H.264/AVC|
| 지원되는 출력 비디오 코덱|입력과 같음|H.264/AVC|
| 지원되는 비디오 비트 수준, 입력 및 출력|HDR 10/HLG를 포함하여 최대 10비트|8비트|
| 지원되는 입력 오디오 코덱|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| 지원되는 출력 오디오 코덱|입력과 같음|AAC-LC|
| 출력 비디오의 최대 비디오 해상도|입력과 같음|표준-720p Premium1080p-1080p|
| 입력된 비디오의 최대 프레임 속도|초당 60 프레임|표준 또는 Premium1080p-30 프레임/초|
| 입력 프로토콜|RTMP, fragmented-MP4(부드러운 스트리밍)|RTMP, fragmented-MP4(부드러운 스트리밍)|
| 가격|[가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조하고 "라이브 비디오" 탭 클릭|[가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조하고 "라이브 비디오" 탭 클릭|
| 최대 실행 시간| 연중무휴 라이브 선형 | 최대 24 시간|
| 포함된 CEA 608/708 캡션 데이터 전달 가능|예|예|
| 슬레이트 삽입 지원|아닙니다.|아닙니다.|
| API를 통한 광고 신호 지원| 아닙니다.|아닙니다.|
| SCTE-35 인밴드 메시지를 통한 광고 신호 지원|예|예|
| 기여 피드의 일시 정지에서 복구하는 기능|예|부분|
| 균일하지 않은 입력 GOP에 대한 지원|예|아니요 - 입력에 고정된 GOP 지속 기간이 있어야 함|
| 변수 프레임 속도 입력에 대한 지원|예|아니요 - 입력은 고정된 프레임 속도여야 함. 예를 들어 움직임이 많은 장면 중에는 사소한 차이가 허용됩니다. 기여 피드의 프레임 속도 삭제할 수 없습니다 있지만 (예를 들어 15 개로 프레임/초)입니다.|
| 입력 피드가 손실될 경우 라이브 이벤트 자동 차단|아닙니다.|12시간 동안 LiveOutput 실행이 없는 경우|

## <a name="system-presets"></a>시스템 사전 설정

해상도 및 비트 전송률 라이브 인코더의 출력에 포함 된 기준 합니다 [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding)합니다. 사용 하는 경우는 **표준** 라이브 인코더 (LiveEventEncodingType.Standard) 해당 *Default720p* 사전 설정 아래에 설명 된 6 해상도/비트 전송률 쌍의 집합을 지정 합니다. 사용 하는 경우 그러지를 **Premium1080p** 라이브 인코더 (LiveEventEncodingType.Premium1080p) 해당 *Default1080p* 사전 설정 확인/비트 전송률 쌍의 출력 집합을 지정 합니다.

> [!NOTE]
> 표준 라이브 인코딩에 대해 설치 된 경우 오류가 발생 하면 라이브 이벤트에 사전 설정 Default1080p를 적용할 수 없습니다. Premium1080p 라이브 인코더 사전 설정을 Default720p 적용 하려고 하면 오류가 발생 하기도 합니다.

### <a name="output-video-streams-for-default720p"></a>Default720p에 대 한 출력 비디오 스트림

기여 피드의 720p 또는 더 높은 해상도의 경우는 **Default720p** 사전 설정은 다음 6 개 레이어로 피드를 인코딩합니다. 아래 표에서 비트 전송률 kbps는, MaxFPS 해당 최대 허용 된 프레임 속도 나타냅니다 (의 초당 프레임), 프로필을 사용 하는 H.264 프로필 나타냅니다.

| Bitrate | 너비 | 높이 | MaxFPS | 프로필 |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |높음 |
| 2200 |960 |540 |30 |높음 |
| 1350 |704 |396 |30 |높음 |
| 850 |512 |288 |30 |높음 |
| 550 |384 |216 |30 |높음 |
| 200 |340 |192 |30 |높음 |

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정 해야 하는 경우 Azure Portal을 통해 지원 티켓을 여세요. 원하는 해상도 및 비트 전송률 표를 지정해야 합니다. 720p에서 레이어가 하나만 있고, 6레이어 이하인지 확인합니다. 또한 수행할 표준 라이브 인코더에 대 한 사전 설정을 요청 하 지정 합니다.
> 비트 전송률 및 해상도의 특정 값은 시간이 지남에 따라 조정 될 수 있습니다.

### <a name="output-video-streams-for-default1080p"></a>Default1080p에 대 한 출력 비디오 스트림

기여 피드의 1080p 해상도의 경우는 **Default1080p** 사전 설정은 다음 6 개 레이어로 피드를 인코딩합니다.

| Bitrate | 너비 | 높이 | MaxFPS | 프로필 |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |높음 |
| 3000 |1280 |720 |30 |높음 |
| 1600 |960 |540 |30 |높음 |
| 800 |640 |360 |30 |높음 |
| 400 |480 |270 |30 |높음 |
| 200 |320 |180 |30 |높음 |

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정 해야 하는 경우 Azure Portal을 통해 지원 티켓을 여세요. 원하는 해상도 및 비트 전송률 표를 지정해야 합니다. 수행할 하나만 1080p, 계층 및 최대 6 계층 인지 확인 합니다. 또한 수행할 Premium1080p 라이브 인코더에 대 한 사전 설정을 요청 하 지정 합니다.
> 비트 전송률 및 해상도의 특정 값은 시간이 지남에 따라 조정 될 수 있습니다.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>출력 오디오 Stream Default720p 및 Default1080p

둘 다에 대해 *Default720p* 하 고 *Default1080p* 사전 설정, 오디오 128kbps 스테레오 AAC-LC로 인코딩됩니다. 샘플링 비율의 기여 피드에서 오디오 트랙을 따릅니다.

## <a name="implicit-properties-of-the-live-encoder"></a>라이브 인코더의 암시적 속성

이전 섹션에는 라이브 인코더 사전 설정을 통해-계층, 해상도 및 비트 전송률의 수와 같은 명시적으로 제어할 수 있는의 속성을 설명 합니다. 이 섹션에는 암시적 속성을 명확히 구분합니다.

### <a name="group-of-pictures-gop-duration"></a>그림 (GOP) 기간의 그룹

라이브 인코더 뒤의 [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) 출력 계층 GOP 기간이 동일 해야 합니다. 즉 기여도 피드-구조입니다. 따라서는 GOP 기간 (일반적으로 2 초)을 수정 하는 기여 피드를 생성 하기 위해 온-프레미스 인코더를 구성 하는 것이 좋습니다. 이렇게 하면 서비스에서 보내는 HLS 및 MPEG DASH 스트림도는 고정 된 GOP 기간입니다. GOP 기간이 사소한 차이 대부분의 장치에서 허용 될 가능성이 높습니다.

### <a name="frame-rate"></a>프레임 속도

또한 라이브 인코더에 대 한 기여도 피드-즉 출력 계층은 동일한 기간을 사용 하 여 프레임의 개별 비디오 프레임의 기간을 따릅니다. 따라서 것이 좋습니다는 프레임 속도 수정 하는 기여 피드를 생성 하기 위해 온-프레미스 인코더를 구성 하는 (최대 30 프레임/초)입니다. 이렇게 하면 서비스에서 보내는 HLS 및 MPEG DASH 스트림도는 고정 된 프레임 속도 기간. 대부분의 장치에서 용인 해서는 안 프레임 속도가 약간 변동 이지만 라이브 인코더는 올바르게 재생 되는 출력을 생성 하는 보장 되지 않습니다. 온-프레미스 라이브 인코더 됩니다 하지 수 프레임이 손실 (예: 배터리 부족 상황) 또는 어떤 방식으로든에서 프레임 속도 변경 합니다.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>작성 글의 해상도 피드 및 계층 출력

라이브 인코더 upconverting를 방지 하도록 구성 됩니다 기여 피드 합니다. 결과적으로 출력 계층의 최대 해상도 기여도 피드를 초과 하지 않습니다.

예를 들어 기여 피드에서 720p를 보내는 경우 Default1080p에 대해 구성 된 라이브 이벤트를 라이브 인코딩, 출력에서 720p 3Mbps 200kbps 1080p까지 이동부터 5 계층에만 포함 됩니다. 또는 표준에 대해 구성 된 라이브 이벤트로 라이브 인코딩 360p 피드에서 작성 글을 보내는 경우, 출력 3 계층 (288 p, 216 p 및 192 p 해상도)이 포함 됩니다. degenerate 사례에 표준 라이브 인코더를 예를 들어 160 x 90 픽셀의 기여도 피드를 보낼 경우 출력 기여 피드와 동일한 비트 전송률의 160 x 90 해상도 한 레이어를 포함 됩니다.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>비트 전송률 기여의 피드 및 계층 출력

라이브 인코더는 비트 전송률 사전 설정에서 기여 피드의 비트 전송률에 관계 없이 적용 하도록 구성 됩니다. 결과적으로 출력 계층의 비트 전송률 기여 피드의 초과할 수 있습니다. 예를 들어 기여 피드에서 1mbps에서 720p 해상도 보내는 경우 출력 계층 동일 하 게 에서처럼 합니다 [테이블](live-event-types-comparison.md#output-video-streams-for-default720p) 위에 있습니다.

## <a name="next-steps"></a>다음 단계

[라이브 스트리밍 개요](live-streaming-overview.md)
