---
title: Azure Event Grid 배달 및 다시 시도
description: Azure Event Grid에서 이벤트를 배달하는 방법 및 배달되지 않은 메시지를 처리하는 방법을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: spelluru
ms.openlocfilehash: dda2fd98c4c0d330059156a5ec00baa97ffaf627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921065"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid 메시지 배달 및 다시 시도

이 문서에서는 배달이 승인되지 않는 경우 Azure Event Grid에서 이벤트를 처리하는 방법을 설명합니다.

Event Grid는 지속성이 있는 배달을 제공합니다. 각 메시지를 각 구독에 대해 최소 한 번 배달합니다. 이벤트는 각 구독에 등록된 엔드포인트로 즉시 전송됩니다. 엔드포인트가 이벤트 수신을 확인하지 않는 경우 Event Grid는 이벤트 전달을 재시도합니다.

## <a name="batched-event-delivery"></a>일괄 처리된 이벤트 배달

이벤트 그리드는 기본적으로 각 이벤트를 구독자에게 개별적으로 전송합니다. 구독자는 단일 이벤트로 배열을 받습니다. 처리량이 높은 시나리오에서 HTTP 성능을 향상시키기 위해 이벤트를 일괄 처리하도록 이벤트 그리드를 구성할 수 있습니다.

일괄 처리된 배달에는 두 가지 설정이 있습니다.

* **일괄 처리당 최대 이벤트** - 이벤트 그리드가 일괄 처리당 제공하는 최대 이벤트 수입니다. 이 숫자는 초과되지 않습니다, 그러나 더 적은 이벤트는 게시 시 사용할 수 있는 다른 이벤트가 없는 경우 제공 될 수 있습니다. 이벤트 그리드는 사용 가능한 이벤트가 적을 경우 일괄 처리를 만들기 위해 이벤트를 지연시키지 않습니다. 1에서 5,000 사이여야 합니다.
* **기본 배치 크기(킬로바이트) - 킬로바이트** 크기의 배치 크기에 대한 목표 천장입니다. 최대 이벤트와 마찬가지로 게시 시 더 많은 이벤트를 사용할 수 없는 경우 일괄 처리 크기가 작을 수 있습니다. 단일 이벤트가 기본 크기보다 큰 *경우* 일괄 처리가 기본 배치 크기보다 클 수 있습니다. 예를 들어 기본 설정 크기가 4KB이고 10KB 이벤트가 이벤트 그리드로 푸시되는 경우 10KB 이벤트는 삭제되지 않고 자체 일괄 처리로 계속 배달됩니다.

포털, CLI, PowerShell 또는 SDK를 통해 이벤트별 구독 단위로 구성된 일괄 처리된 배달입니다.

### <a name="azure-portal"></a>Azure Portal: 
![일괄 처리 배달 설정](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
이벤트 구독을 만들 때 다음 매개 변수를 사용합니다. 

- **일괄 처리당 최대 이벤트** - 일괄 처리의 최대 이벤트 수입니다. 숫자는 1에서 5000 사이여야 합니다.
- **기본 배치 크기-킬로바이트** - 기본 배치 크기로 1KB로 기본 배치 크기입니다. 숫자는 1에서 1024 사이여야 합니다.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

이벤트 그리드를 사용하여 Azure CLI를 사용하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 저장소 이벤트를 웹 끝점으로 라우팅참조.](../storage/blobs/storage-blob-event-quickstart.md)

## <a name="retry-schedule-and-duration"></a>예약 및 기간 재시도

이벤트 그리드는 메시지를 전달한 후 응답을 위해 30초 동안 대기합니다. 30초 후 끝점이 응답하지 않으면 메시지가 다시 시도될 때까지 큐에 대기됩니다. Event Grid는 이벤트 배달에 대해 지수 백오프 재시도 정책을 사용합니다. 이벤트 그리드는 최선의 노력을 기울여 다음 일정에 따라 배달을 다시 시도합니다.

- 10초
- 30초
- 1분
- 5분
- 10분
- 30분
- 1시간
- 최대 24시간 시간 동안 시간당

끝점이 3분 이내에 응답하는 경우 Event Grid는 최선의 노력을 기울여 재시도 큐에서 이벤트를 제거하려고 시도하지만 중복은 계속 수신될 수 있습니다.

Event Grid는 모든 재시도 단계에 작은 무작위화를 추가하며 끝점이 일관되게 비정상이거나, 일정 기간 동안 다운되거나, 과부하가 걸린 것처럼 보이는 경우 특정 재시도를 기회적으로 건너뛸 수 있습니다.

결정적인 동작의 경우 [구독 다시 시도 정책에서](manage-event-delivery.md)이벤트 시간을 라이브 및 최대 배달 시도로 설정합니다.

기본적으로 Event Grid는 24시간 이내에 배달되지 않는 모든 이벤트를 만료합니다. 이벤트 구독을 만들 때 [재시도 정책을 사용자 지정](manage-event-delivery.md)할 수 있습니다. 최대 배달 시도 횟수(기본값: 30)와 이벤트 TTL(Time to Live, 기본값: 1440분)을 제공합니다.

## <a name="delayed-delivery"></a>지연된 배송

끝점에서 배달 오류가 발생하면 Event Grid는 해당 끝점으로의 이벤트 배달 및 재시도를 지연하기 시작합니다. 예를 들어, 끝점에 게시된 처음 10개의 이벤트가 실패하는 경우 Event Grid는 끝점에 문제가 있다고 가정하고 일부 시간 동안 모든 후속 재시도 *및 새* 배달을 지연시요( 경우에 따라 최대 몇 시간).

지연된 배달의 기능적 목적은 이벤트 그리드 시스템뿐만 아니라 비정상 엔드포인트를 보호하는 것입니다. 비정상 엔드포인트로의 전송을 백오프하고 지연하지 않으면 Event Grid의 재시도 정책 및 볼륨 기능이 시스템을 쉽게 압도할 수 있습니다.

## <a name="dead-letter-events"></a>배달 못한 편지 이벤트

Event Grid에서 이벤트를 전송할 수 없는 경우, 전송되지 않은 이벤트를 스토리지 계정으로 보낼 수 있습니다. 이 프로세스를 배달 못한 편지라고 합니다. 기본적으로 Event Grid에서는 배달 못한 편지를 켜지 않습니다. 이 기능을 사용하려면 이벤트 구독을 만들 때 전송되지 않은 이벤트를 보유할 스토리지 계정을 지정해야 합니다. 이 스토리지 계정에서 이벤트를 끌어와 전송을 해결합니다.

Event Grid가 모든 재시도 시도 횟수를 시도한 경우 이벤트를 배달 못한 편지로 보냅니다. Event Grid가 400(잘못된 요청) 또는 413(요청 엔터티가 너무 큼) 응답 코드를 수신하는 경우, 즉시 이벤트를 배달 못한 편지 엔드포인트로 보냅니다. 이 응답 코드는 이벤트 전달이 실패하지 않음을 나타냅니다.

이벤트를 배달하기 위한 마지막 시도 및 배달 못한 편지 위치로 이벤트를 배달하는 경우, 그 사이에 5분의 지연이 발생합니다. 이 지연은 Blob Storage 작업 수를 줄이기 위함입니다. 배달 못한 편지 위치를 4시간 동안 사용할 수 없는 경우 이벤트가 삭제됩니다.

배달 못한 편지 위치를 설정하기 전에 컨테이너를 포함하는 스토리지 계정이 있어야 합니다. 이벤트 구독을 만들 때 이 컨테이너에 대한 엔드포인트를 제공합니다. 엔드포인트는 다음과 같은 형식입니다. `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

이벤트가 배달 못한 편지 위치로 전송된 경우 알림을 받을 수 있습니다. Event Grid를 사용하여 전송되지 않은 이벤트에 응답하려면 배달 못한 편지 Blob Storage에 대한 [이벤트 구독을 만듭니다](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 배달 못한 편지 Blob Storage가 전송되지 않은 이벤트를 수신할 때마다 Event Grid에서 처리기에 알립니다. 처리기는 전송되지 않은 이벤트를 조정하기 위해 수행할 작업으로 응답합니다.

배달 못한 편지 위치를 설정하는 예제는 [배달 못한 편지 및 재시도 정책](manage-event-delivery.md)을 참조하세요.

## <a name="message-delivery-status"></a>메시지 배달 상태

Event Grid는 HTTP 응답 코드를 사용하여 이벤트의 수신을 승인합니다. 

### <a name="success-codes"></a>성공 코드

Event Grid는 다음 HTTP 응답 **코드만** 성공적인 배달로 간주합니다. 다른 모든 상태 코드는 실패한 배달로 간주되며 적절하게 재시도되거나 deadletterd로 표시됩니다. 성공적인 상태 코드를 받으면 Event Grid는 배달이 완료된 것으로 간주합니다.

- 200 정상
- 201 생성됨
- 202 수락됨
- 203 신뢰할 수 없는 정보
- 204 콘텐츠 없음

### <a name="failure-codes"></a>실패 코드

위의 집합(200-204)에 없는 다른 모든 코드는 실패로 간주되며 다시 시도됩니다. 일부는 아래에 설명된 특정 재시도 정책을 가지고 있으며, 다른 모든 정책은 표준 지수 백오프 모델을 따릅니다. Event Grid 아키텍처의 병렬 처리특성이 높기 때문에 재시도 동작은 결정적이지 않습니다. 

| 상태 코드 | 다시 시도 동작 |
| ------------|----------------|
| 400 잘못된 요청 | 5분 이상 후에 다시 시도(데드레터 를 설정한 경우 즉시 데드레터) |
| 401 권한 없음 | 5분 이상 후에 다시 시도 |
| 403 사용할 수 없음 | 5분 이상 후에 다시 시도 |
| 404 찾을 수 없음 | 5분 이상 후에 다시 시도 |
| 408 요청 시간 초과 | 2분 이상 후 재시도 |
| 413 요청 엔터티가 너무 큼 | 10초 이상 후에 다시 시도(데드레터를 설정한 경우 즉시 데드레터) |
| 503 서비스를 사용할 수 없음 | 30초 이상 후 재시도 |
| 나머지 | 10초 이상 후에 다시 시도 |


## <a name="next-steps"></a>다음 단계

* 이벤트 배달 상태를 보려면 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* 이벤트 전달 옵션을 사용자 지정하려면 [전송 못한 편지 및 재시도 정책](manage-event-delivery.md)을 참조하세요.
