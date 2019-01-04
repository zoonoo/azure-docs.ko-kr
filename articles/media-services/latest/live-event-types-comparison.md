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
ms.date: 11/27/2018
ms.author: juliako
ms.openlocfilehash: 3ed38ce75e5ee7c9f05533d64b28171482bfec51
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725935"
---
# <a name="liveevent-types-comparison"></a>LiveEvent 형식 비교

Azure Media Services에서 [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents)는 라이브 인코딩 및 통과라는 두 가지 형식 중 하나일 수 있습니다. 

## <a name="types-comparison"></a>형식 비교 

다음 테이블에서는 두 가지 LiveEvent 형식의 기능을 비교합니다.

| 기능 | 통과 LiveEvent | 표준 LiveEvent |
| --- | --- | --- |
| 단일 비트 전송률 입력은 클라우드에서 다중 비트 전송률로 인코딩됩니다. |아니요 |예 |
| 기여 피드에 대한 최대 비디오 해상도 |4K(60프레임/초에서 4096x2160) |1080p(30프레임/초에서 1920x1088)|
| 기여 피드에 권장되는 최대 계층|최대 12개|하나의 오디오|
| 출력의 최대 계층| 입력과 같음|최대 7개|
| 기여 피드의 최대 집계 대역폭|60Mbps|해당 없음|
| 기여에서 단일 계층의 최대 전송률 |20Mbps|20Mbps|
| 여러 언어 오디오 트랙 지원|예|아니요|
| 지원되는 입력 비디오 코덱 |H.264/AVC 및 H.265/HEVC|H.264/AVC|
| 지원되는 출력 비디오 코덱|입력과 같음|H.264/AVC|
| 지원되는 비디오 비트 수준, 입력 및 출력|HDR 10/HLG를 포함하여 최대 10비트|8비트|
| 지원되는 입력 오디오 코덱|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| 지원되는 출력 오디오 코덱|입력과 같음|AAC-LC|
| 출력 비디오의 최대 비디오 해상도|입력과 같음|720p(초당 30프레임에서)|
| 입력 프로토콜|RTMP, fragmented-MP4(부드러운 스트리밍)|RTMP, fragmented-MP4(부드러운 스트리밍)|
| 가격|[가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조하고 "라이브 비디오" 탭 클릭|[가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조하고 "라이브 비디오" 탭 클릭|
| 최대 실행 시간|연중무휴 라이브 선형|연중 무휴|
| 포함된 CEA 608/708 캡션 데이터 전달 가능|예|예|
| 슬레이트 삽입 지원|아니요|아니요|
| API를 통한 광고 신호 지원| 아니요|아니요|
| SCTE-35 인밴드 메시지를 통한 광고 신호 지원|예|예|
| 기여 피드의 일시 정지에서 복구하는 기능|예|아니요(LiveEvent는 입력 데이터가 6초 이상 없으면 슬레이팅을 시작함)|
| 균일하지 않은 입력 GOP에 대한 지원|예|아니요 - 입력에 고정된 GOP 지속 기간이 있어야 함|
| 변수 프레임 속도 입력에 대한 지원|예|아니요 - 입력은 고정된 프레임 속도여야 함. 예를 들어 움직임이 많은 장면 중에는 사소한 차이가 허용됩니다. 그러나 기여 피드는 프레임 속도를 15프레임/초 등으로 늦출 수 없습니다.|
| 입력 피드가 손실될 경우 LiveEvent 자동 차단|아니요|12시간 동안 LiveOutput 실행이 없는 경우|

## <a name="next-steps"></a>다음 단계

[라이브 스트리밍 개요](live-streaming-overview.md)
