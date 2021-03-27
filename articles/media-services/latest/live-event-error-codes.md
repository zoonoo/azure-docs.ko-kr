---
title: Azure Media Services 라이브 이벤트 오류 코드
description: 이 문서에는 라이브 이벤트 오류 코드가 나열 되어 있습니다.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 7c30649fe3486f812569cb51f609356a6cbfd58f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627543"
---
# <a name="media-services-live-event-error-codes"></a>Media Services 라이브 이벤트 오류 코드

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

다음 표에서는 [라이브 이벤트](live-events-outputs-concept.md) 오류 코드를 나열 합니다.

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

라이브 이벤트에 대 한 [Event Grid](../../event-grid/index.yml) 이벤트를 구독할 때 [LiveEventConnectionRejected](monitoring/media-services-event-schemas.md\#liveeventconnectionrejected) 이벤트에서 다음 오류 중 하나가 표시 될 수 있습니다.
> [!div class="mx-tdCol2BreakAll"]
>| Error | 정보 |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|설명 | 잘못된 수집 URL입니다. |
>|추천 솔루션| APPID는 RTMP 수집 URL의 GUID 토큰입니다. API의 수집 URL과 일치 하는지 확인 합니다. |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| 설명 |인코더 IP가 구성 된 IP 허용 목록에 없습니다. |
>| 추천 솔루션| 인코더의 IP가 IP 허용 목록에 있는지 확인 합니다. *Whoismyip* 또는 *CIDR 계산기* 와 같은 온라인 도구를 사용 하 여 적절 한 값을 설정 합니다.  인코더가 실제 라이브 이벤트 전에 서버에 연결할 수 있는지 확인 합니다. |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| 설명|RTMP 인코더가 명령을 보내지 않았습니다 `setDataFrame` . |
>| 추천 솔루션|대부분의 상용 인코더는 스트림 메타 데이터를 전송 합니다. 단일 비트 전송률 수집을 푸시하는 인코더의 경우에는이 문제가 발생 하지 않을 수 있습니다. 라이브는 스트림 메타 데이터가 없을 때 들어오는 비트 전송률을 계산할 수 있습니다.  PassThru 채널 또는 이중 푸시 시나리오에 대 한 다중 비트 전송률 수집의 경우 수집 URL에 ' videodatarate ' 및 ' audiodatarate '를 사용 하 여 쿼리 문자열을 추가 해 볼 수 있습니다. 대략적인 값은 사용할 수 있습니다. 단위가 Kbit입니다. 예: `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| 설명|지정 된 코덱이 지원 되지 않습니다.|
>| 추천 솔루션| 라이브에서 지원 되지 않는 코덱을 받았습니다. 예를 들어 RTMP 수집, 라이브는 비 AVC 비디오 코덱을 받았습니다.  인코더 사전 설정을 확인 합니다. |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| 설명 |실제 미디어 데이터를 배달 하기 전에 미디어 설명 정보를 받지 못했습니다. |
>| 추천 솔루션|라이브은 인코더에서 스트림 설명 (헤더 또는 FLV 태그)을 받지 않습니다. 이는 프로토콜 위반입니다. 인코더 공급 업체에 문의 하세요. |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| 설명|오디오 또는 비디오 형식의 품질 수가 허용 된 최대 한도를 초과 했습니다. |
>| 추천 솔루션|라이브 이벤트 모드가 Live Encoding 되 면 인코더는 비디오와 오디오의 단일 비트 전송률을 푸시합니다.  동일한 비트 전송률에서 중복 푸시를 사용할 수 있습니다. 인코더 사전 설정 또는 출력 설정을 확인 하 여 단일 비트 전송률 스트림을 출력 하는지 확인 합니다. |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| 설명|라이브 이벤트 또는 채널 서비스의 총 수신 비트 전송률이 허용 된 최대 한도를 초과 했습니다. |
>| 추천 솔루션|인코더가 들어오는 최대 비트 전송률을 초과 했습니다. 이 제한은 참여 하는 인코더에서 들어오는 모든 데이터를 집계 합니다. 비트 전송률을 줄이기 위해 인코더 사전 설정 또는 출력 설정을 확인 합니다. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| 설명|비디오 또는 오디오 FLVTag의 타임 스탬프는 RTMP 인코더에서 유효 하지 않습니다. |
>| 추천 솔루션|더 이상 사용되지 않습니다. |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| 설명|프레임 속도가 프레임 속도가 수집 들어오는 인코더는 라이브 이벤트/채널 인코딩에 허용 되는 최대 30fps를 초과 했습니다. |
>| 추천 솔루션|인코더 사전 설정을 확인 하 여 프레임 속도를 36 fps 미만으로 낮춥니다. |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| 설명|들어오는 인코더 수집 스트림에서 라이브 이벤트/채널 인코딩에 대 한 1920x1088 및 통과 라이브 이벤트/채널에 대 한 4096 x 2160의 허용 되는 해상도를 초과 했습니다. |
>| 추천 솔루션|인코더 사전 설정을 선택 하 여 비디오 해상도를 낮춰 제한을 초과 하지 않도록 합니다. |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| 설명|라이브 이벤트가 한 번에 많은 양의 오디오 데이터를 받았거나 키 프레임이 없는 많은 양의 비디오 데이터가 수신 되었습니다. 올바른 데이터로 다시 시도할 수 있는 기회를 제공 하기 위해 인코더의 연결을 끊었습니다. |
>| 추천 솔루션|인코더가 모든 GOP (키 프레임 간격)에 대해 키 프레임을 전송 하는지 확인 합니다.  "상수 비트 전송률 (CBR)" 또는 "키 프레임 맞춤"과 같은 설정을 사용 하도록 설정 합니다. 경우에 따라 참여 하는 인코더를 다시 설정 하면 도움이 될 수 있습니다. 도움이 되지 않으면 인코더 공급 업체에 문의 하세요. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

[LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md\#liveeventencoderdisconnected) 이벤트에서 다음 오류 중 하나가 표시 될 수 있습니다.

> [!div class="mx-tdCol2BreakAll"]
>| Error | 정보 |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| 설명|허용된 시간 한도 동안 유휴 상태가 된 후 RTMP 세션 시간이 초과되었습니다. |
>|추천 솔루션|이는 일반적으로 인코더에서 입력 피드의 수신을 중지 하 여 푸시할 데이터가 없으므로 세션이 유휴 상태가 될 때 발생 합니다. 인코더 또는 입력 피드 상태가 정상 상태 인지 확인 합니다. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|설명| 비디오 또는 오디오 FLVTag의 타임 스탬프가 RTMP encoder에서 유효 하지 않습니다. |
>| 추천 솔루션| 더 이상 사용되지 않습니다. |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| 설명|데이터를 너무 빨리 보내는 인코더입니다. |
>| 추천 솔루션|인코더는 짧은 기간 동안 많은 조각 집합을 버스트 하는 경우에 발생 합니다.  이론적으로는 네트워크 문제로 인해 인코더에서 데이터를 푸시할 수 없고 네트워크를 사용할 수 있을 때 데이터를 버스트 할 수 없는 경우에 발생할 수 있습니다. 인코더 로그 또는 시스템 로그에서 이유를 찾습니다. |
>|**알 수 없는 오류 코드** |
>| 설명| 이러한 오류 코드는 메모리 오류에서 해시 맵의 중복 항목에 이르기까지 다양할 수 있습니다. 인코더에서 짧은 기간 동안 많은 조각 집합을 전송 하는 경우이 문제가 발생할 수 있습니다.  네트워크 문제로 인해 인코더가 데이터를 푸시 하지 못한 경우에도이 문제가 발생할 수 있습니다. 그런 다음 네트워크를 사용할 수 있게 되 면 지연 된 모든 조각을 한 번에 보냅니다. |
>|추천 솔루션| 인코더 로그를 확인 합니다.|

## <a name="other-error-codes"></a>기타 오류 코드

> [!div class="mx-tdCol2BreakAll"]
>| Error | 정보 |거부/연결 끊김 이벤트|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||예|
>| Description|일반 오류입니다. ||
>|추천 솔루션| 없음||
>|**MPI_SYSTEM_MAINTENANCE** ||예|
>| Description|서비스 업데이트 또는 시스템 유지 관리로 인해 인코더의 연결을 끊었습니다. ||
>|추천 솔루션|인코더가 ' 자동 연결 '을 사용 하도록 설정 했는지 확인 합니다. 인코더를 유지 관리 하지 않는 중복 라이브 이벤트 끝점에 다시 연결할 수 있습니다. ||
>|**MPE_BAD_URL_SYNTAX** ||예|
>| Description|수집 URL의 형식이 잘못 되었습니다. ||
>|추천 솔루션|수집 URL의 형식이 올바르게 지정 되었는지 확인 합니다. RTMP의 경우 `rtmp[s]://hostname:port/live/GUID_APPID/streamname` ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||예|
>| Description|인코더에서 세션의 연결을 끊었습니다.  ||
>|추천 솔루션|이는 오류가 아닙니다. 정상 연결 끊기를 포함 하 여 인코더가 연결 끊기를 시작 했습니다. 예기치 않은 연결 끊기 인 경우 인코더 로그를 확인 합니다. |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||예|
>| Description|들어오는 데이터 비율이 예상 비트 전송률이 일치 하지 않습니다. ||
>|추천 솔루션|이 경고는 들어오는 데이터 속도가 너무 느리거나 빠를 때 발생 하는 경고입니다. 인코더 로그 또는 시스템 로그를 확인 하십시오.||
>|**MPE_INGEST_DISCONTINUITY** ||예|
>| Description| 들어오는 데이터에 discontinuty가 있습니다.||
>|추천 솔루션| 이는 네트워크 문제나 시스템 리소스 문제로 인해 인코더가 데이터를 삭제 한다는 경고입니다. 인코더 로그 또는 시스템 로그를 확인 하십시오. 시스템 리소스 (CPU, 메모리 또는 네트워크)도 모니터링 합니다. 시스템 CPU가 너무 높으면 비트 전송률을 낮추고 시스템 그래픽 카드에서 H/W 인코더 옵션을 사용 하십시오.||

## <a name="see-also"></a>참조

[스트리밍 끝점 (원본) 오류 코드](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>다음 단계

[자습서: Media Services로 라이브 스트리밍](stream-live-tutorial-with-api.md)
