---
title: Azure 미디어 서비스 라이브 이벤트 오류 코드 | 마이크로 소프트 문서
description: 이 문서에는 라이브 이벤트 오류 코드가 나열되어 있습니다.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: f9067562f67190b8bc04392f33078d4d3262f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654582"
---
# <a name="media-services-live-event-error-codes"></a>미디어 서비스 라이브 이벤트 오류 코드

이 섹션의 테이블에는 [라이브 이벤트](live-events-outputs-concept.md) 오류 코드가 나열되어 있습니다.

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

라이브 이벤트에 대한 [이벤트 그리드](https://docs.microsoft.com/azure/event-grid/) 이벤트를 구독하면 [LiveEventConnection거부](media-services-event-schemas.md#liveeventconnectionrejected) 이벤트에서 다음 오류 중 하나가 표시될 수 있습니다.

| 결과 코드 | 설명 |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | 잘못된 수집 URL입니다. |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | 인코더 IP가 IP 허용 목록에 구성되어 있지 않습니다. |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | RTMP 인코더가 setDataFrame 명령을 보내지 않았습니다. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | 지정된 코덱이 지원되지 않습니다. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |실제 미디어 데이터가 전달되기 전에 미디어 설명 정보가 수신되지 않았습니다.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |오디오 또는 비디오 유형에 대한 품질 수가 허용된 최대 제한을 초과했습니다.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |라이브 이벤트 또는 채널 서비스의 총 수신 비트레이가 허용된 최대 제한을 초과했습니다.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | 비디오 또는 오디오 FLVTag에 대한 타임스탬프가 RTMP 인코더에서 유효하지 않습니다. |
| MPE_INGEST_FRAMERATE_EXCEEDED | 프레임 속도가 있는 수신 인코더가 인코딩된 스트림이 라이브 이벤트/채널을 인코딩할 때 허용되는 최대 30fps를 초과했습니다.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | 수신 인코더 인제스트 스트림은 라이브 이벤트/채널 인코딩을 위한 1920x1088, 통과 라이브 이벤트/채널의 경우 4096 x 2160을 초과했습니다.|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

[LiveEventEncoder 연결 해제](media-services-event-schemas.md#liveeventencoderdisconnected) 된 이벤트에서 다음 오류 중 하나를 볼 수 있습니다.

|결과 코드|설명|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|허용된 시간 한도 동안 유휴 상태가 된 후 RTMP 세션 시간이 초과되었습니다.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|비디오 또는 오디오 FLVTag에 대한 타임스탬프가 RTMP 인코더에서 유효하지 않습니다.|
|MPE_CAPACITY_LIMIT_REACHED|데이터를 너무 빨리 보내는 인코더입니다.|
|알 수 없는 오류 코드|이러한 오류 코드는 메모리 오류에서 해시 맵의 중복 항목에 이르기까지 다양할 수 있습니다.|


## <a name="see-also"></a>참조

[스트리밍 끝점(원점) 오류 코드](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>다음 단계

[튜토리얼: 미디어 서비스로 라이브 스트리밍](stream-live-tutorial-with-api.md)
