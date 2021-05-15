---
title: IoT Edge의 Azure Event Grid에서 출력 일괄 처리 | Microsoft Docs
description: IoT Edge의 Event Grid에서 출력 일괄 처리
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0ae2261f8278c4d5e1944b01a9731afd293df20b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171638"
---
# <a name="output-batching"></a>출력 일괄 처리

Event Grid에서는 하나의 전송 요청으로 둘 이상의 이벤트를 전달하도록 지원합니다. 이 기능을 사용하면 요청당 HTTP 오버헤드를 지불하지 않고도 전체 전송 처리량을 늘릴 수 있습니다. 일괄 처리는 기본적으로 해제되어 있으며 구독별로 설정할 수 있습니다.

> [!WARNING]
> 구독자 코드가 일괄 처리 요청당 더 많은 작업을 수행해야 하는 경우에도 각 전송 요청 처리에 허용되는 최대 기간은 변경되지 않습니다. 전송 시간 제한은 기본적으로 60초로 지정됩니다.

## <a name="batching-policy"></a>일괄 처리 정책

다음 두 설정을 조정하여 구독자별로 Event Grid의 일괄 처리 동작을 사용자 지정할 수 있습니다.

* 일괄 처리당 최대 이벤트 수

  이 설정은 일괄 처리 전송 요청에 추가할 수 있는 이벤트 수의 상한을 설정합니다.

* 기본 설정 일괄 처리 크기(KB)

  이 노브는 전송 요청당 보낼 수 있는 최대 KB 수를 추가로 제어하는 데 사용됩니다.

## <a name="batching-behavior"></a>일괄 처리 동작

* 모두 성공 또는 실패

  Event Grid는 모두 성공 또는 실패 의미 체계로 작동합니다. 일괄 처리 전송에서는 일부 성공이 지원되지 않습니다. 구독자는 합리적으로 60초 이내에 처리할 수 있는 만큼의 일괄 처리당 이벤트만 요청하도록 주의해야 합니다.

* 최적 일괄 처리

  일괄 처리 정책 설정은 일괄 처리 동작을 엄격하게 제한하지 않으며 가장 효율적인 방식으로 적용됩니다. 이벤트율이 낮은 경우 일괄 처리 크기가 일괄 처리당 요청된 최대 이벤트 수보다 작은 경우가 많습니다.

* 기본적으로 해제로 설정

  기본적으로 Event Grid는 전송 요청마다 이벤트를 하나만 추가합니다. 일괄 처리를 설정하는 방법은 이벤트 구독 JSON에서 이 문서 앞부분에 언급된 설정 중 하나를 지정하는 것입니다.

* 기본값

  이벤트 구독을 만들 때 설정(일괄 처리당 최대 이벤트 수 및 대략적인 일괄 처리 크기(킬로바이트))을 둘 다 지정할 필요는 없습니다. 설정이 하나만 지정되면 Event Grid에서 (구성 가능한) 기본값을 사용합니다. 기본값과 기본값을 재정의하는 방법은 다음 섹션을 참조하세요.

## <a name="turn-on-output-batching"></a>출력 일괄 처리 설정

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

## <a name="configuring-maximum-allowed-values"></a>허용되는 최대값 구성

다음 배포 시간 설정은 이벤트 구독을 만들 때 허용되는 최대값을 제어합니다.

| 속성 이름 | Description |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | `PreferredBatchSizeInKilobytes` 노브에 허용되는 최대값입니다. 기본값은 `1033`입니다.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch` 노브에 허용되는 최대값입니다. 기본값은 `50`입니다.

## <a name="configuring-runtime-default-values"></a>런타임 기본값 구성

다음 배포 시간 설정은 이벤트 구독에 지정되지 않은 경우 각 노브의 런타임 기본값을 제어합니다. 다시 말해, 일괄 처리 동작을 설정하려면 이벤트 구독에서 노브를 하나 이상 설정해야 합니다.

| 속성 이름 | Description |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | `MaxEventsPerBatch`만 지정된 경우의 최대 전송 요청 크기입니다. 기본값은 `1_058_576`입니다.
| `broker__defaultMaxEventsPerBatch` | `MaxBatchSizeInBytes`만 지정된 경우 일괄 처리에 추가할 최대 이벤트 수입니다. 기본값은 `10`입니다.
