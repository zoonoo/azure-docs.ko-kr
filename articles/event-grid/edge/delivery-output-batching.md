---
title: Azure Event Grid IoT Edge의 출력 일괄 처리 Microsoft Docs
description: IoT Edge Event Grid의 출력 일괄 처리
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0ae2261f8278c4d5e1944b01a9731afd293df20b
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171638"
---
# <a name="output-batching"></a>출력 일괄 처리

Event Grid는 단일 배달 요청에서 둘 이상의 이벤트를 전달 하도록 지원 합니다. 이 기능을 사용 하면 HTTP 요청당 오버 헤드를 지불 하지 않고도 전체 배달 처리량을 높일 수 있습니다. 일괄 처리는 기본적으로 해제 되어 있으며 구독 단위로 설정할 수 있습니다.

> [!WARNING]
> 구독자 코드가 일괄 처리 된 요청당 더 많은 작업을 수행 해야 하는 경우에도 각 배달 요청을 처리 하는 데 허용 되는 최대 기간은 변경 되지 않습니다. 배달 제한 시간은 기본값은 60 초입니다.

## <a name="batching-policy"></a>일괄 처리 정책

다음 두 설정을 조정 하 여 구독자 별로 Event Grid의 일괄 처리 동작을 사용자 지정할 수 있습니다.

* 일괄 처리당 최대 이벤트 수

  이 설정은 일괄 처리 된 배달 요청에 추가할 수 있는 이벤트 수의 상한을 설정 합니다.

* 기본 배치 크기 (Kb)

  이 노브는 배달 요청당 전송할 수 있는 최대 kb 수를 추가로 제어 하는 데 사용 됩니다.

## <a name="batching-behavior"></a>일괄 처리 동작

* 모두 또는 없음

  Event Grid는 모두 또는-없음 의미 체계와 함께 작동 합니다. 일괄 처리 배달의 부분 성공을 지원 하지 않습니다. 구독자는 60 초 내에 합리적으로 처리할 수 있으므로 일괄 처리당 많은 이벤트를 요청 하도록 주의 해야 합니다.

* 낙관적 일괄 처리

  일괄 처리 정책 설정은 일괄 처리 동작에서 엄격 하 게 제한 되지 않으며 가장 적합 한 방식으로 적용 됩니다. 낮은 이벤트 요금에서 일괄 처리 크기는 일괄 처리당 요청 된 최대 이벤트 보다 작은 경우가 많습니다.

* 기본값은 OFF로 설정 됩니다.

  기본적으로 Event Grid는 각 배달 요청에 하나의 이벤트만 추가 합니다. 일괄 처리를 켜는 방법은 이벤트 구독 JSON의 문서 앞부분에서 언급 한 설정 중 하나를 설정 하는 것입니다.

* 기본값

  이벤트 구독을 만들 때 설정 (일괄 처리당 최대 이벤트 수 및 대략적인 일괄 처리 크기 (킬로바이트))을 모두 지정할 필요는 없습니다. 설정이 하나만 설정 되어 있으면 Event Grid (구성 가능) 기본값을 사용 합니다. 기본값에 대 한 다음 섹션과이를 재정의 하는 방법을 참조 하세요.

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

## <a name="configuring-maximum-allowed-values"></a>허용 되는 최대 값 구성

다음 배포 시간 설정은 이벤트 구독을 만들 때 허용 되는 최대값을 제어 합니다.

| 속성 이름 | 설명 |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | 노브에 허용 되는 최대값 `PreferredBatchSizeInKilobytes` 입니다. 기본값 `1033` .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | 노브에 허용 되는 최대값 `MaxEventsPerBatch` 입니다. 기본값 `50` .

## <a name="configuring-runtime-default-values"></a>런타임 기본값 구성

다음 배포 시간 설정은 이벤트 구독에 지정 되지 않은 경우 각 노브의 런타임 기본값을 제어 합니다. 다시 말해, 일괄 처리 동작을 켜려면 이벤트 구독에서 하나 이상의 노브를 설정 해야 합니다.

| 속성 이름 | 설명 |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | 만 지정 된 경우 최대 배달 요청 크기 `MaxEventsPerBatch` 입니다. 기본값 `1_058_576` .
| `broker__defaultMaxEventsPerBatch` | 만 지정 된 경우 일괄 처리에 추가할 최대 이벤트 수 `MaxBatchSizeInBytes` 입니다. 기본값 `10` .
