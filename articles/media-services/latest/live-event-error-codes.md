---
title: Azure Media Services 라이브 이벤트 오류 코드 | Microsoft Docs
description: 이 문서에는 라이브 이벤트 오류 코드가 나열 되어 있습니다.
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
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654582"
---
# <a name="media-services-live-event-error-codes"></a>Media Services 라이브 이벤트 오류 코드

이 섹션의 표에는 [라이브 이벤트](live-events-outputs-concept.md) 오류 코드가 나열 되어 있습니다.

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

라이브 이벤트에 대 한 [Event Grid](https://docs.microsoft.com/azure/event-grid/) 이벤트를 구독할 때 [LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected) 이벤트에서 다음 오류 중 하나가 표시 될 수 있습니다.

| 결과 코드 | Description |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | 잘못된 수집 URL입니다. |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | 인코더 IP가 IP 허용 목록에 구성되어 있지 않습니다. |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | RTMP 인코더가 setDataFrame 명령을 보내지 않았습니다. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | 지정된 코덱이 지원되지 않습니다. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |실제 미디어 데이터를 배달 하기 전에 미디어 설명 정보를 받지 못했습니다.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |오디오 또는 비디오 형식의 품질 수가 허용 된 최대 한도를 초과 했습니다.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |라이브 이벤트 또는 채널 서비스의 총 수신 비트 전송률이 허용 된 최대 한도를 초과 했습니다.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | 비디오 또는 오디오 FLVTag에 대한 타임스탬프가 RTMP 인코더에서 유효하지 않습니다. |
| MPE_INGEST_FRAMERATE_EXCEEDED | Framerate를 사용 하 여 들어오는 인코더 수집 스트림이 라이브 이벤트/채널 인코딩에 허용 되는 최대 30fps를 초과 했습니다.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | 들어오는 인코더 수집 스트림에서 라이브 이벤트/채널 인코딩에 대 한 1920x1088 및 통과 라이브 이벤트/채널에 대 한 4096 x 2160의 허용 되는 해상도를 초과 했습니다.|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

[LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) 이벤트에서 다음 오류 중 하나가 표시 될 수 있습니다.

|결과 코드|Description|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|허용된 시간 한도 동안 유휴 상태가 된 후 RTMP 세션 시간이 초과되었습니다.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|비디오 또는 오디오 FLVTag에 대한 타임스탬프가 RTMP 인코더에서 유효하지 않습니다.|
|MPE_CAPACITY_LIMIT_REACHED|데이터를 너무 빨리 보내는 인코더입니다.|
|알 수 없는 오류 코드|이러한 오류 코드는 메모리 오류에서 해시 맵의 중복 항목에 이르기까지 다양할 수 있습니다.|


## <a name="see-also"></a>참고 항목

[스트리밍 끝점 (원본) 오류 코드](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>다음 단계

[자습서: Media Services로 라이브 스트리밍](stream-live-tutorial-with-api.md)
