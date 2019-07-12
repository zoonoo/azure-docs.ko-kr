---
title: Azure Event Grid 배달 및 다시 시도
description: Azure Event Grid에서 이벤트를 배달하는 방법 및 배달되지 않은 메시지를 처리하는 방법을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: spelluru
ms.openlocfilehash: 0945b06f78ac34500f0b16a4a419cff12d1a4734
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812917"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid 메시지 배달 및 다시 시도

이 문서에서는 배달이 승인되지 않는 경우 Azure Event Grid에서 이벤트를 처리하는 방법을 설명합니다.

Event Grid는 지속성이 있는 배달을 제공합니다. 각 메시지를 각 구독에 대해 최소 한 번 배달합니다. 이벤트는 각 구독에 등록된 엔드포인트로 즉시 전송됩니다. 엔드포인트가 이벤트 수신을 확인하지 않는 경우 Event Grid는 이벤트 전달을 재시도합니다.

현재 Event Grid는 각 이벤트를 개별적으로 구독자에게 보냅니다. 구독자는 단일 이벤트로 배열을 받습니다.

## <a name="retry-schedule-and-duration"></a>예약 및 기간 재시도

Event Grid 메시지 배달 한 후 응답에 30 초를 대기 합니다. 30 초 후 끝점이 응답 하지 않은 경우 재시도 대 한 메시지 대기 됩니다. Event Grid는 이벤트 배달에 대해 지수 백오프 재시도 정책을 사용합니다. Event Grid를 최상의 기준으로 다음 일정에 따라 배달을 다시 시도합니다.

- 10초
- 30초
- 1분
- 5분
- 10분
- 30분
- 1시간
- 최대 24 시간 동안 1 시간 마다

끝점 3 분 이내에 응답 하는 경우 Event Grid는 이벤트를 최상의 기준으로 재시도 큐에서 제거 하려고 하지만 중복 항목을 계속 수신할 수 있습니다.

Event Grid는 약간의 불규칙을 모든 재시도 단계를 추가 및 끝점 긴 기간 동안 다운 일관 되 게 정상 되지 않거나 무력화 될 것 이므로 경우에 선택적으로 특정 재시도 건너뛸 수 있습니다.

결정적 동작에 대 한 라이브 이벤트 시간을 설정 하 고 최대 배달 시도에 [구독 다시 시도 정책](manage-event-delivery.md)합니다.

기본적으로 Event Grid는 24시간 이내에 배달되지 않는 모든 이벤트를 만료합니다. 이벤트 구독을 만들 때 [재시도 정책을 사용자 지정](manage-event-delivery.md)할 수 있습니다. 최대 배달 시도 횟수(기본값: 30)와 이벤트 TTL(Time to Live, 기본값: 1440분)을 제공합니다.

## <a name="delayed-delivery"></a>지연 된 배달

배달 오류가 발생 하는 끝점, Event Grid를 제공 하 고 해당 끝점에 이벤트를 다시 시도 지연 시작 됩니다. 예를 들어, 끝점에 게시 된 처음 10 개의 이벤트 실패 하는 경우 Event Grid 것으로 간주 하는 끝점 문제가 발생 한 모든 후속 다시 시도 지연 됩니다 *및 새* -경우에 따라 몇 시간까지 일정 시간 동안 배달 .

지연 된 배달 기능 목적은 비정상 끝점 뿐만 아니라 이벤트 그리드 시스템을 보호 하는 것입니다. 백오프 지연 비정상 끝점에 배달의 경우와 Event Grid의 다시 시도 정책 및 볼륨 기능 수에 쉽게 과부하가 걸릴 시스템입니다.

## <a name="dead-letter-events"></a>배달 못한 편지 이벤트

Event Grid에서 이벤트를 전송할 수 없는 경우, 전송되지 않은 이벤트를 저장소 계정으로 보낼 수 있습니다. 이 프로세스를 배달 못한 편지라고 합니다. 기본적으로 Event Grid에서는 배달 못한 편지를 켜지 않습니다. 이 기능을 사용하려면 이벤트 구독을 만들 때 전송되지 않은 이벤트를 보유할 저장소 계정을 지정해야 합니다. 이 저장소 계정에서 이벤트를 끌어와 전송을 해결합니다.

Event Grid가 모든 재시도 시도 횟수를 시도한 경우 이벤트를 배달 못한 편지로 보냅니다. Event Grid가 400(잘못된 요청) 또는 413(요청 엔터티가 너무 큼) 응답 코드를 수신하는 경우, 즉시 이벤트를 배달 못한 편지 엔드포인트로 보냅니다. 이 응답 코드는 이벤트 전달이 실패하지 않음을 나타냅니다.

이벤트를 배달하기 위한 마지막 시도 및 배달 못한 편지 위치로 이벤트를 배달하는 경우, 그 사이에 5분의 지연이 발생합니다. 이 지연은 Blob Storage 작업 수를 줄이기 위함입니다. 배달 못한 편지 위치를 4시간 동안 사용할 수 없는 경우 이벤트가 삭제됩니다.

배달 못한 편지 위치를 설정하기 전에 컨테이너를 포함하는 저장소 계정이 있어야 합니다. 이벤트 구독을 만들 때 이 컨테이너에 대한 엔드포인트를 제공합니다. 엔드포인트는 다음과 같은 형식입니다. `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

이벤트가 배달 못한 편지 위치로 전송된 경우 알림을 받을 수 있습니다. Event Grid를 사용하여 전송되지 않은 이벤트에 응답하려면 배달 못한 편지 Blob Storage에 대한 [이벤트 구독을 만듭니다](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 배달 못한 편지 Blob Storage가 전송되지 않은 이벤트를 수신할 때마다 Event Grid에서 처리기에 알립니다. 처리기는 전송되지 않은 이벤트를 조정하기 위해 수행할 작업으로 응답합니다.

배달 못한 편지 위치를 설정하는 예제는 [배달 못한 편지 및 재시도 정책](manage-event-delivery.md)을 참조하세요.

## <a name="message-delivery-status"></a>메시지 배달 상태

Event Grid는 HTTP 응답 코드를 사용하여 이벤트의 수신을 승인합니다. 

### <a name="success-codes"></a>성공 코드

Event Grid 고려 **만** 성공적으로 배달으로 다음 HTTP 응답 코드입니다. 다른 모든 상태 코드 배달 실패 한 것으로 간주 됩니다 및 다시 시도 됩니다 또는 deadlettered 적절 하 게 합니다. 성공 상태 코드를 받으면 Event Grid 배달 완료 고려 합니다.

- 200 정상
- 201 생성됨
- 202 수락됨
- 203 신뢰할 수 없는 정보
- 204 콘텐츠 없음

### <a name="failure-codes"></a>실패 코드

위의 집합 (200-204)에 없는 다른 모든 코드는 오류로 간주 됩니다 및 다시 시도 합니다. 일부는 아래에 설명 된를 연결 하는 특정 재시도 정책, 표준 지 수 백오프 모델에 따라 다른 모든 사용자. Event Grid 아키텍처의 고도로 병렬화 된 특성상 재시도 동작 인지 명확 하지 않은 염두에서에 두는 것이 반드시 합니다. 

| status code | 다시 시도 동작 |
| ------------|----------------|
| 400 잘못된 요청 | 5 분 이상 후에 다시 시도 (배달 못 한 편지 경우 즉시 배달 못 한 편지 설치) |
| 401 권한 없음 | 5 분 이상 후에 다시 시도 |
| 403 사용할 수 없음 | 5 분 이상 후에 다시 시도 |
| 404 찾을 수 없음 | 5 분 이상 후에 다시 시도 |
| 408 요청 시간 초과 | 2 분 이상 후에 다시 시도 |
| 413 요청 엔터티가 너무 큼 | 10 초 이상 후에 다시 시도 (배달 못 한 편지 경우 즉시 배달 못 한 편지 설치) |
| 503 서비스를 사용할 수 없음 | 30 초 이상 후에 다시 시도 |
| 다른 모든 사용자 | 10 초 이상 후에 다시 시도 |


## <a name="next-steps"></a>다음 단계

* 이벤트 배달 상태를 보려면 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* 이벤트 전달 옵션을 사용자 지정하려면 [전송 못한 편지 및 재시도 정책](manage-event-delivery.md)을 참조하세요.
