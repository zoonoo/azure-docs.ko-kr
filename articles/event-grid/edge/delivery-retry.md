---
title: 배달 및 재시도 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: IoT 에지의 이벤트 그리드에서 배달 및 재시도.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7df283b12a0d04d2b785c13a2f12b03115581e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841715"
---
# <a name="delivery-and-retry"></a>배달 및 다시 시도

Event Grid는 지속성이 있는 배달을 제공합니다. 일치하는 각 구독에 대해 각 메시지를 한 번 이상 즉시 배달하려고 시도합니다. 구독자의 끝점이 이벤트 수신을 승인하지 않거나 오류가 있는 경우 Event Grid는 **고정된 재시도 일정** 및 **재시도 정책에**따라 배달을 다시 시도합니다.  기본적으로 이벤트 그리드 모듈은 구독자에게 한 번에 하나의 이벤트를 제공합니다. 그러나 페이로드는 단일 이벤트가 있는 배열입니다. 출력 일괄 처리 기능을 활성화하여 모듈이 한 번에 두 개 이상의 이벤트를 제공하도록 할 수 있습니다. 이 기능에 대한 자세한 내용은 [출력 일괄 처리를](delivery-output-batching.md)참조하십시오.  

> [!IMPORTANT]
>이벤트 데이터에 대한 지속성 지원은 없습니다. 즉, Event Grid 모듈을 다시 배포하거나 다시 시작하면 아직 배달되지 않은 이벤트가 손실됩니다.

## <a name="retry-schedule"></a>재시도 일정

이벤트 그리드는 메시지를 전달한 후 응답을 위해 최대 60초 동안 대기합니다. 구독자의 끝점이 응답을 ACK하지 않으면 메시지가 후속 재시도를 위해 다시 대기열 중 하나에 큐에 큐에 대기됩니다.

재시도를 시도할 일정을 결정하는 미리 구성된 백오프 큐가 두 개 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

| 일정 | 설명 |
| ---------| ------------ |
| 1분 | 여기에 끝나는 메시지는 매 분마다 시도됩니다.
| 10분 | 여기서 끝나는 메시지는 10분마다 시도됩니다.

### <a name="how-it-works"></a>작동 방법

1. 메시지가 이벤트 그리드 모듈에 도착합니다. 즉시 배달을 시도합니다.
1. 배달에 실패하면 메시지가 1분 대기열로 큐에 대기되고 1분 후에 다시 시도됩니다.
1. 배달이 계속 실패하면 메시지가 10분 대기열로 큐에 대기되고 10분마다 다시 시도됩니다.
1. 배달은 성공 또는 다시 시도 정책 제한에 도달할 때까지 시도됩니다.

## <a name="retry-policy-limits"></a>정책 제한 다시 시도

재시도 정책을 결정하는 두 가지 구성이 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

* 최대 시도 횟수
* 이벤트 시간(TTL)

재시도 정책의 제한 중 하나에 도달하면 이벤트가 삭제됩니다. 재시도 일정 자체는 재시도 일정 섹션에 설명되어 있습니다. 이러한 제한의 구성은 모든 구독자 또는 구독 기준별로 수행할 수 있습니다. 다음 섹션에서는 각 섹션에 대해 자세히 설명합니다.

## <a name="configuring-defaults-for-all-subscribers"></a>모든 구독자에 대한 기본값 구성

두 가지 속성이 있습니다: `brokers__defaultMaxDeliveryAttempts` 모든 `broker__defaultEventTimeToLiveInSeconds` 구독자에 대 한 다시 시도 정책 기본값을 제어 하는 Event Grid 배포의 일부로 구성할 수 있습니다.

| 속성 이름 | 설명 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 이벤트를 전달하는 최대 시도 횟수입니다. 기본값: 30.
| `broker__defaultEventTimeToLiveInSeconds` | 이벤트 TTL 이후에 이벤트가 전달되지 않으면 삭제됩니다. 기본값: **7200초**

## <a name="configuring-defaults-per-subscriber"></a>구독자당 기본값 구성

구독별로 다시 시도 정책 제한을 지정할 수도 있습니다.
구독자당 기본값을 구성하는 방법에 대한 자세한 내용은 [API 설명서를](api.md) 참조하십시오. 구독 수준 기본값은 모듈 수준 구성을 재정의합니다.

## <a name="examples"></a>예

다음 예제에서는 maxNumberOfTries = 3 및 이벤트 TTL30분으로 이벤트 그리드 모듈에서 재시도 정책을 설정합니다.

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

다음 예제는 maxNumberOfTries = 3 및 이벤트 TTL 30분으로 웹 후크 구독을 설정합니다.

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
