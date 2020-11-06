---
title: Azure 앱 구성 REST API-제한
description: Azure 앱 구성을 사용할 때 제한을 이해 하기 위한 참조 페이지 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 26464138b7739bb476d9d0dd1078194473ecd23e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424269"
---
# <a name="throttling"></a>스로틀

구성 저장소에는 제공 되는 요청에 대 한 제한이 있습니다. 구성 저장소에 할당 된 할당량을 초과 하는 모든 요청은 HTTP 429 (너무 많은 요청) 응답을 수신 합니다.

제한은 다른 할당량 정책으로 나뉩니다.

- **총 요청** 수-총 요청 수
- **총 대역폭** -아웃 바운드 데이터 (바이트)
- **저장소** -사용자 데이터의 총 저장소 크기 (바이트)

## <a name="handling-throttled-responses"></a>제한 된 응답 처리

지정 된 할당량에 대 한 요율 한도에 도달 하면 서버는 _429_ 상태 코드를 사용 하 여 해당 유형의 추가 요청에 응답 합니다. _429_ 응답에는 클라이언트에 게 권장 되는 대기 시간 (밀리초)을 제공 하 여 요청 할당량을 스레드 단위으로 허용 하는 _다시 시도-ms_ 헤더가 포함 됩니다.

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

위의 예제에서 클라이언트는 허용 되는 할당량을 초과 했으며, 추가 요청을 시도 하기 전에 속도를 높이는 것이 좋습니다. 클라이언트는 점진적 백오프 고려해 야 합니다.

## <a name="other-retry"></a>기타 다시 시도

서비스는 클라이언트가 다시 시도 해야 하는 제한 이외의 상황을 식별할 수 있습니다 (예: 503 서비스를 사용할 수 없음). 이러한 모든 경우에 `retry-after-ms` 응답 헤더가 제공 됩니다. 견고성을 높이려면 클라이언트에서 제안 된 간격을 따르고 재시도를 수행 하는 것이 좋습니다.

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
