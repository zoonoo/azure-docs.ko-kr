---
title: Azure Media Services LiveEvent 형식 | Microsoft Docs
description: 이 문서에서는 LiveEvent 유형을 비교하는 자세한 표를 보여 줍니다.
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
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: bd4899374c06246ddd4d5fa81d0f6e3a6a1e7017
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075015"
---
# <a name="live-event-types-comparison"></a>라이브 이벤트 유형 비교

Azure Media Services에서 [라이브 이벤트](https://docs.microsoft.com/rest/api/media/liveevents)는 라이브 인코딩 및 통과라는 두 가지 형식 중 하나일 수 있습니다. 

## <a name="types-comparison"></a>형식 비교 

다음 표에서 라이브 이벤트 형식의 기능을 비교 합니다.

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

해상도 및 비트 전송률 라이브 인코더의 출력에 포함 하 여 결정 됩니다 합니다 [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding)합니다. 사용 하는 경우는 **표준** 라이브 인코더 (LiveEventEncodingType.Standard) 해당 *Default720p* 사전 설정 아래에 설명 된 6 해상도/비트 비율 쌍의 집합을 지정 합니다. 사용 하는 경우 그러지를 **Premium1080p** 라이브 인코더 (LiveEventEncodingType.Premium1080p) 해당 *Default1080p* 확인/비트 비율 쌍의 집합을 출력 하는 사전 설정을된 지정 합니다. 

### <a name="output-video-streams-for-default720p"></a>Default720p에 대 한 출력 비디오 스트림

**Default720p** 는 다음 6 개 레이어로 입력된 비디오를 인코딩합니다.

| 비트 전송률 | 너비 | 높이 | MaxFPS | 프로필 | 출력 스트림 이름 |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |높음 |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |높음 |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |높음 |Video_704x396_1350kbps |
| 850 |512 |288 |30 |높음 |Video_512x288_850kbps |
| 550 |384 |216 |30 |높음 |Video_384x216_550kbps |
| 200 |340 |192 |30 |높음 |Video_340x192_200kbps |

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정 해야 하는 경우 Azure Portal을 통해 지원 티켓을 여세요. 원하는 해상도 및 비트 전송률 표를 지정해야 합니다. 720p에서 레이어가 하나만 있고, 6레이어 이하인지 확인합니다. 또한 수행할 표준 라이브 인코더에 대 한 사전 설정을 요청 하 지정 합니다.

### <a name="output-video-streams-for-default1080p"></a>Default1080p에 대 한 출력 비디오 스트림

**Default1080p** 는 다음 6 개 레이어로 입력된 비디오를 인코딩합니다.

| 비트 전송률 | 너비 | 높이 | MaxFPS | 프로필 | 출력 스트림 이름 |
| --- | --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |높음 |Video_1920x1080_5500kbps |
| 3000 |1280 |720 |30 |높음 |Video_1280x720_3000kbps |
| 1600 |960 |540 |30 |높음 |Video_960x540_1600kbps |
| 800 |640 |360 |30 |높음 |Video_640x360_800kbps |
| 400 |480 |270 |30 |높음 |Video_480x270_400kbps |
| 200 |320 |180 |30 |높음 |Video_320x180_200kbps |

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정 해야 하는 경우 Azure Portal을 통해 지원 티켓을 여세요. 원하는 해상도 및 비트 전송률 표를 지정해야 합니다. 수행할 하나만 1080p, 계층 및 최대 6 계층 인지 확인 합니다. 또한 수행할 Premium1080p 라이브 인코더에 대 한 사전 설정을 요청 하 지정 합니다.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>출력 오디오 Stream Default720p 및 Default1080p

둘 다에 대해 *Default720p* 하 고 *Default1080p* 사전 설정, 오디오 스테레오 AAC-LC 128kbps, 샘플링 속도의 48 kHz로 인코딩됩니다.

## <a name="next-steps"></a>다음 단계

[라이브 스트리밍 개요](live-streaming-overview.md)
