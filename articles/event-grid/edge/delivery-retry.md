---
title: 전송 및 다시 시도 - IoT Edge의 Azure Event Grid | Microsoft Docs
description: IoT Edge의 Event Grid에서 전송하고 다시 시도합니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: aa0b3a05fb26f6be951b697145d7b22e03b7792d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171621"
---
# <a name="delivery-and-retry"></a>배달 및 다시 시도

Event Grid는 지속성이 있는 배달을 제공합니다. 일치하는 구독마다 즉각적으로 각 메시지를 한 번 이상 전송하려고 시도합니다. 구독자 엔드포인트에서 이벤트 수신을 승인하지 않거나 오류가 있는 경우 Event Grid는 고정된 **다시 시도 일정** 및 **재시도 정책** 에 따라 전송을 다시 시도합니다.  기본적으로 Event Grid 모듈은 한 번에 하나의 이벤트를 구독자에게 전송합니다. 하지만 페이로드는 단일 이벤트가 포함된 배열입니다. 출력 일괄 처리 기능을 사용하도록 설정하여 모듈에서 한 번에 둘 이상의 이벤트를 전송하도록 할 수 있습니다. 이 기능에 대한 자세한 내용은 [출력 일괄 처리](delivery-output-batching.md)를 참조하세요.  

> [!IMPORTANT]
>이벤트 데이터에 대한 지속성은 지원되지 않습니다. 즉, Event Grid 모듈을 다시 배포하거나 다시 시작하면 아직 전송되지 않은 이벤트는 모두 손실됩니다.

## <a name="retry-schedule"></a>다시 시도 일정

Event Grid는 메시지를 전송한 후 최대 60초 동안 응답을 대기합니다. 구독자 엔드포인트에서 응답을 승인하지 않으면 나중에 다시 시도하는 데 사용되도록 백오프 큐 중 하나에 메시지를 넣습니다.

다시 시도할 일정을 결정하는 미리 구성된 백오프 큐는 두 개가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

| 예약 | Description |
| ---------| ------------ |
| 1분 | 여기에 배치되는 메시지는 1분마다 전달이 다시 시도됩니다.
| 10분 | 여기에 배치되는 메시지는 10분마다 전달이 다시 시도됩니다.

### <a name="how-it-works"></a>작동 방법

1. 메시지가 Event Grid 모듈에 도달합니다. 이 메시지를 즉시 전송하려고 시도합니다.
1. 전송이 실패하면 메시지를 1분 큐에 넣고 1분 후 전송을 다시 시도합니다.
1. 전송이 계속 실패하면 메시지를 10분 큐에 넣고 10분마다 전송을 다시 시도합니다.
1. 전송이 성공하거나 재시도 정책 한도에 도달할 때까지 전송을 시도합니다.

## <a name="retry-policy-limits"></a>재시도 정책 한도

재시도 정책을 결정하는 구성은 두 가지가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

* 최대 시도 횟수
* 이벤트 TTL(Time to Live)

재시도 정책 한도 중 하나에 도달하면 이벤트가 삭제됩니다. 다시 시도 일정은 다시 시도 일정 섹션에 설명되어 있습니다. 해당 한도 구성은 모든 구독자 또는 개별 구독에 대해 수행할 수 있습니다. 다음 섹션에서 각 항목에 대해 자세히 설명합니다.

## <a name="configuring-defaults-for-all-subscribers"></a>모든 구독자에 대한 기본값 구성

모든 구독자에 대한 재시도 정책 기본값을 제어하며 Event Grid 배포의 일부로 구성할 수 있는 속성은 `brokers__defaultMaxDeliveryAttempts` 및 `broker__defaultEventTimeToLiveInSeconds` 두 개입니다.

| 속성 이름 | Description |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 이벤트 전송 작업의 최대 시도 횟수입니다. 기본값은 30입니다.
| `broker__defaultEventTimeToLiveInSeconds` | 이벤트가 전송되지 않는 경우 이벤트를 삭제하려면 경과되어야 하는 이벤트 TTL(초)입니다. 기본값은 **7200** 초입니다.

## <a name="configuring-defaults-per-subscriber"></a>구독자별 기본값 구성

개별 구독에 대해 재시도 정책 한도를 지정할 수도 있습니다.
구독자별 기본값 구성 방법에 대한 자세한 내용은 [API 설명서](api.md)를 참조하세요. 구독 수준 기본값은 모듈 수준 구성을 재정의합니다.

## <a name="examples"></a>예

다음 예제에서는 maxNumberOfAttempts는 3, 이벤트 TTL은 30분을 사용하여 Event Grid 모듈에서 재시도 정책을 설정합니다.

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

다음 예제에서는 maxNumberOfAttempts는 3, 이벤트 TTL은 30분을 사용하여 웹후크 구독을 설정합니다.

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
