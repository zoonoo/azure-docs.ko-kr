---
title: Azure App Configuration REST API - 제한
description: Azure App Configuration REST API를 사용하는 경우 제한을 이해하기 위한 참조 페이지
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b4472fdcacfe5b62c0cded089224c6d41e32f47d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932441"
---
# <a name="throttling"></a>제한

구성 저장소에는 제공할 수 있는 요청에 대한 제한이 있습니다. 구성 저장소에 대해 할당된 할당량을 초과하는 모든 요청은 HTTP 429(너무 많은 요청) 응답을 받습니다.

제한은 여러 가지 할당량 정책으로 나뉩니다.

- **총 요청** - 총 요청 수
- **총 대역폭** - 아웃바운드 데이터(바이트)
- **스토리지** - 사용자 데이터의 총 스토리지 크기(바이트)

## <a name="handling-throttled-responses"></a>제한된 응답 처리

지정된 할당량의 속도 제한에 도달하면 서버는 _429_ 상태 코드를 사용하여 해당 유형의 추가 요청에 응답합니다. _429_ 응답에는 요청 할당량을 보충하도록 허용하는 제안된 대기 시간(밀리초)을 클라이언트에 제공하는 _retry-after-ms_ 헤더가 포함됩니다.

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

위 예제에서 클라이언트는 허용된 할당량을 초과했으며 추가 요청을 시도하기 전에 속도를 늦추고 10밀리초 동안 대기하는 것이 좋습니다. 클라이언트는 점진적 백오프도 고려해야 합니다.

## <a name="other-retry"></a>기타 재시도

서비스는 클라이언트 재시도가 필요한 제한 이외의 상황을 식별할 수 있습니다(예: 503 서비스를 사용할 수 없음). 이 경우에 모두 `retry-after-ms` 응답 헤더가 제공됩니다. 견고성을 높이기 위해 클라이언트는 제안된 간격을 따르고 재시도를 수행하는 것이 좋습니다.

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
