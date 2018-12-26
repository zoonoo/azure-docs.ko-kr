---
title: Azure Front Door Service - 메트릭 및 기록 | Microsoft Docs
description: 이 문서는 Azure Front Door Service가 지원하는 다양한 메트릭 및 액세스 로그를 이해하는 데 도움이 됩니다.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 643ae03a9350868b172d99b2af7ce23e34fedf47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998001"
---
# <a name="monitoring-metrics-for-front-door"></a>Front Door에 대한 메트릭 모니터링

Azure Front Door Service를 사용하면 주요 메트릭을 모니터링하여 Front Door에 대한 사용 현황, 상태 및 성능과 함께 Front Door 구성의 유효성을 검사할 수 있습니다.

## <a name="metrics"></a>메트릭

메트릭은 포털에서 성능 카운터를 볼 수 있는 특정 Azure 리소스에 대한 기능입니다. Front Door의 경우 다음 메트릭을 사용할 수 있습니다.

| 메트릭 | 메트릭 표시 이름 | 단위 | 차원 | 설명 |
| --- | --- | --- | --- | --- |
| RequestCount | 요청 수 | 개수 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 제공하는 클라이언트 요청 수  |
| RequestSize | 요청 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 클라이언트에서 Front Door로, 요청으로 전송된 바이트 수 |
| ResponseSize | 응답 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 클라이언트로, 응답으로 전송된 바이트 수 |
| TotalLatency | 총 대기 시간 | 밀리초 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 클라이언트가 Front Door의 마지막 응답 바이트를 승인할 때까지 클라이언트 요청이 Front Door에서 수신될 때 계산된 시간 |
| BackendRequestCount | 백 엔드 요청 수 | 개수 | HttpStatus</br>HttpStatusGroup</br>백 엔드 | Front Door에서 백 엔드로 전송된 요청 수 |
| BackendRequestLatency | 백 엔드 요청 대기 시간 | 밀리초 | 백 엔드 | Front Door에서 백 엔드의 마지막 응답 바이트를 받을 때까지 Front Door에서 백 엔드로 요청이 전송될 때 계산된 시간 |
| BackendHealthPercentage | 백 엔드 상태 비율 | 백분율 | 백 엔드</br>BackendPool | Front Door에서 백 엔드로 성공한 상태 프로브의 비율 |
| WebApplicationFirewallRequestCount | 웹 응용 프로그램 방화벽 요청 수 | 개수 | PolicyName</br>RuleName</br>조치 | Front Door의 응용 프로그램 계층 보안에 의해 처리된 클라이언트 요청 수 |


## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는 방법](quickstart-create-front-door.md)을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
