---
title: Azure 이벤트 그리드 IoT 에지의 출력 일괄 처리 | 마이크로 소프트 문서
description: IoT 에지의 이벤트 그리드에서 출력 일괄 처리.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841750"
---
# <a name="output-batching"></a>출력 일괄 처리

Event Grid는 단일 배달 요청에서 두 개 이상의 이벤트를 전달할 수 있는 지원을 제공합니다. 이 기능을 사용하면 요청당 HTTP 오버헤드를 지불하지 않고도 전체 배달 처리량을 늘릴 수 있습니다. 일괄 처리는 기본적으로 꺼져 있으며 구독당 켜질 수 있습니다.

> [!WARNING]
> 구독자 코드가 일괄 처리된 요청당 더 많은 작업을 수행해야 할 수 있더라도 각 배달 요청을 처리하는 최대 허용 기간은 변경되지 않습니다. 배달 시간 설정은 기본값으로 60초입니다.

## <a name="batching-policy"></a>일괄 처리 정책

다음 두 설정을 조정하여 이벤트 Grid의 일괄 처리 동작을 구독자별로 사용자 지정할 수 있습니다.

* 일괄 처리당 최대 이벤트

  이 설정은 일괄 처리된 배달 요청에 추가할 수 있는 이벤트 수에 대한 상한을 설정합니다.

* 킬로바이트로 선호하는 배치 크기

  이 노브는 배달 요청당 전송할 수 있는 최대 킬로바이트 수를 추가로 제어하는 데 사용됩니다.

## <a name="batching-behavior"></a>일괄 처리 동작

* 전부 또는 없음

  이벤트 그리드는 전부 또는 없음 의미 체계로 작동합니다. 일괄 처리 배달의 부분적인 성공을 지원하지 는 않습니다. 구독자는 60초 안에 합리적으로 처리할 수 있는 만큼의 일괄 처리당 이벤트만 요청해야 합니다.

* 낙관적 일괄 처리

  일괄 처리 정책 설정은 일괄 처리 동작에 대한 엄격한 경계가 아니며 최선의 노력을 기울여 존중됩니다. 낮은 이벤트 속도에서는 배치 크기가 일괄 처리당 요청된 최대 이벤트보다 작다는 것을 관찰할 수 있습니다.

* 기본값은 꺼짐으로 설정됩니다.

  기본적으로 이벤트 그리드는 각 배달 요청에 하나의 이벤트만 추가합니다. 일괄 처리를 설정하는 방법은 이벤트 구독 JSON의 문서의 앞에서 언급한 설정 중 하나를 설정하는 것입니다.

* 기본값

  이벤트 구독을 만들 때 설정(일괄 처리당 최대 이벤트 및 킬로바이트별 대략적인 일괄 처리 크기)을 모두 지정할 필요는 없습니다. 하나의 설정만 설정된 경우 이벤트 그리드는 (구성 가능) 기본값을 사용합니다. 기본값과 이를 재정의하는 방법에 대한 다음 섹션을 참조하십시오.

## <a name="turn-on-output-batching"></a>출력 일괄 처리 켜기

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

## <a name="configuring-maximum-allowed-values"></a>허용되는 최대 값 구성

다음 배포 시간 설정은 이벤트 구독을 만들 때 허용되는 최대 값을 제어합니다.

| 속성 이름 | 설명 |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | 손잡이에 `PreferredBatchSizeInKilobytes` 허용되는 최대값입니다. 기본값 `1033`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | 손잡이에 `MaxEventsPerBatch` 허용되는 최대값입니다. 기본값 `50`.

## <a name="configuring-runtime-default-values"></a>런타임 기본값 구성

다음 배포 시간 설정은 이벤트 구독에 지정되지 않은 경우 각 노브의 런타임 기본값을 제어합니다. 일괄 처리 동작을 설정하려면 이벤트 구독에서 하나 이상의 노브를 설정해야 합니다.

| 속성 이름 | 설명 |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | 지정된 경우에만 최대 `MaxEventsPerBatch` 배달 요청 크기입니다. 기본값 `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | 지정한 경우에만 일괄 처리에 추가할 `MaxBatchSizeInBytes` 최대 이벤트 수입니다. 기본값 `10`.
