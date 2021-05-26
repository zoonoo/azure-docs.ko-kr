---
title: Kubernetes의 Azure Event Grid - Batch 이벤트 전달
description: 이 문서에서는 대상에 이벤트 일괄 처리를 제공하는 방법을 설명합니다.
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: a246ee493b89051ad35436cd9213462a09a5d1b0
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386997"
---
# <a name="event-grid-on-kubernetes---batch-event-delivery"></a>Kubernetes의 Event Grid - Batch 이벤트 전달 
Azure Arc를 사용하는 Kubernetes의 Event Grid에서는 하나의 전송 요청으로 둘 이상의 이벤트를 전달하도록 지원합니다. 이 기능을 사용하면 요청당 HTTP 오버헤드를 사용하지 않고도 전체 전송 처리량을 늘릴 수 있습니다. Batch 이벤트 전달은 기본적으로 해제되어 있으며 이벤트 구독 구성을 사용하여 설정할 수 있습니다. 

[!INCLUDE [event-grid-preview-feature-note.md](../../../includes/event-grid-preview-feature-note.md)]

> [!WARNING]
> 이벤트 처리기 코드가 잠재적으로 일괄 처리된 요청당 더 많은 작업을 수행해야 하는 경우에도 각 전송 요청 처리에 허용되는 최대 기간은 변경되지 않습니다. 전송 시간 제한은 기본적으로 60초로 지정됩니다.

## <a name="batch-event-delivery-policy"></a>Batch 이벤트 전달 정책
Kubernetes의 Event Grid에서 일괄 처리 이벤트 전달 동작은 다음 두 설정을 조정하여 이벤트 구독별로 사용자 지정할 수 있습니다.

- **일괄 처리당 최대 이벤트 수**
    
    이 설정은 일괄 처리 전송 요청에 추가할 수 있는 이벤트 수의 상한을 설정합니다.
- **기본 설정 Batch 크기(KB)**
    
    이 구성 항목은 전송 요청당 보낼 수 있는 최대 KB 수를 추가로 제어하는 데 사용됩니다.

## <a name="batch-event-delivery-behavior"></a>Batch 이벤트 전달 동작   

- **모두 성공 또는 실패**

    Kubernetes의 Event Grid는 모두 성공 또는 실패 의미 체계로 작동합니다. 일괄 처리 이벤트 전송의 부분적인 성공은 지원되지 않습니다. 이벤트 처리기는 합리적으로 60초 이내에 처리할 수 있는 만큼의 일괄 처리당 이벤트만 요청하도록 주의해야 합니다.
- **최적 일괄 처리**

    일괄 처리 정책 설정은 일괄 처리 동작을 엄격하게 제한하지 않으며 가장 효율적인 방식으로 적용됩니다. 이벤트율이 낮은 경우 일괄 처리 크기가 일괄 처리당 요청된 최대 이벤트 수보다 작은 경우가 많습니다.
- **일괄 처리 전달은 기본적으로 OFF로 설정됩니다.**

    기본적으로 Kubernetes의 Event Grid는 각 전송 요청에 하나의 이벤트만 추가합니다. 이벤트 전달을 일괄 처리로 설정하는 방법은 이벤트 구독 페이로드에서 이 문서 앞부분에 언급된 설정 중 하나를 지정하는 것입니다.
- **기본값**

    이벤트 구독을 만들 때 설정(일괄 처리당 최대 이벤트 수 및 대략적인 일괄 처리 크기(킬로바이트))을 둘 다 지정할 필요는 없습니다. 설정이 하나만 지정되면 Kubernetes의 Event Grid에서 (구성 가능한) 기본값을 사용합니다. 

## <a name="example"></a>예제
다음 예제에서는 일괄 처리를 사용하도록 설정하는 엔드포인트 속성에서 `maxEventsPerBatch` 및 `preferredBatchSizeInKilobytes`를 설정하는 방법을 보여줍니다. 

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="next-steps"></a>다음 단계
Kubernetes용 Azure Arc의 Event Grid에서 지원하는 대상 및 처리기에 대해 알아보려면 [Kubernetes의 Event Grid - 이벤트 처리기](event-handlers.md)를 참조하세요.