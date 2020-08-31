---
title: Azure Front 도어에서 메트릭 및 로그 모니터링 | Microsoft Docs
description: 이 문서에서는 Azure Front 도어가 지 원하는 다양 한 메트릭 및 액세스 로그에 대해 설명 합니다.
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
ms.openlocfilehash: 249b2406f048709fd7e4f76f8272b3158708e5bb
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056434"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Azure 전면 도어에서 메트릭 및 로그 모니터링

Azure Front 도어를 사용 하 여 다음과 같은 방법으로 리소스를 모니터링할 수 있습니다.

- **메트릭**. Azure 전면 도어에는 현재 성능 카운터를 볼 수 있는 7 개의 메트릭이 있습니다.
- **로그**. 활동 및 진단 로그를 사용 하면 모니터링 목적으로 리소스에서 성능, 액세스 및 기타 데이터를 저장 하거나 사용할 수 있습니다.

### <a name="metrics"></a>메트릭

메트릭은 포털에서 성능 카운터를 볼 수 있는 특정 Azure 리소스에 대 한 기능입니다. 다음은 사용 가능한 전방 도어 메트릭입니다.

| 메트릭 | 메트릭 표시 이름 | 단위 | 차원 | Description |
| --- | --- | --- | --- | --- |
| RequestCount | 요청 수 | 개수 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 제공하는 클라이언트 요청 수  |
| RequestSize | 요청 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 클라이언트에서 Front Door로, 요청으로 전송된 바이트 수 |
| ResponseSize | 응답 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 클라이언트로, 응답으로 전송된 바이트 수 |
| TotalLatency | 총 대기 시간 | 밀리초 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 프런트 도어에서 클라이언트가 마지막 응답 바이트를 승인할 때까지 Front 도어가 받은 클라이언트 요청에서 계산 된 시간입니다. |
| BackendRequestCount | 백 엔드 요청 수 | 개수 | HttpStatus</br>HttpStatusGroup</br>백 엔드 | Front Door에서 백 엔드로 전송된 요청 수 |
| BackendRequestLatency | 백 엔드 요청 대기 시간 | 밀리초 | 백 엔드 | Front Door에서 백 엔드의 마지막 응답 바이트를 받을 때까지 Front Door에서 백 엔드로 요청이 전송될 때 계산된 시간 |
| BackendHealthPercentage | 백 엔드 상태 비율 | 백분율 | 백 엔드</br>BackendPool | Front Door에서 백 엔드로 성공한 상태 프로브의 비율 |
| WebApplicationFirewallRequestCount | 웹 애플리케이션 방화벽 요청 수 | 개수 | PolicyName</br>RuleName</br>작업 | Front Door의 애플리케이션 계층 보안에 의해 처리된 클라이언트 요청 수 |

## <a name="activity-logs"></a><a name="activity-log"></a>활동 로그

활동 로그는 Front 문에 대해 수행 된 작업에 대 한 정보를 제공 합니다. 또한 전방 도어에서 수행 되는 모든 쓰기 작업 (put, post 또는 delete)에 대 한 내용, 사용자 및 시기를 결정 합니다.

>[!NOTE]
>활동 로그에는 읽기 (get) 작업이 포함 되지 않습니다. 또한 Azure Portal 또는 원래 관리 API를 사용 하 여 수행 하는 작업을 포함 하지 않습니다.

Azure Monitor에서 Azure 리소스의 앞 문이나 모든 로그에 활동 로그에 액세스 합니다. 활동 로그를 보려면

1. 프런트 도어 인스턴스를 선택 합니다.
2. **활동 로그**를 선택합니다.

    ![활동 로그](./media/front-door-diagnostics/activity-log.png)

3. 필터링 범위를 선택한 다음 **적용**을 선택 합니다.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>진단 로그
진단 로그는 감사 및 문제 해결에 중요 한 작업 및 오류에 대 한 다양 한 정보를 제공 합니다. 진단 로그는 활동 로그와 다릅니다.

활동 로그는 Azure 리소스에서 수행 된 작업에 대 한 통찰력을 제공 합니다. 진단 로그는 리소스에서 수행한 작업에 대 한 통찰력을 제공 합니다. 자세한 내용은 [Azure Monitor 진단 로그](../azure-monitor/platform/platform-logs-overview.md)를 참조 하세요.

![진단 로그](./media/front-door-diagnostics/diagnostic-log.png)

프런트 도어에 대 한 진단 로그를 구성 하려면:

1. Azure Front 도어를 선택 합니다.

2. **진단 설정**을 선택 합니다.

3. **진단 켜기**를 선택합니다. 진단 로그를 메트릭과 함께 저장소 계정에 보관 하거나, 이벤트 허브로 스트림 하거나 Azure Monitor 로그에 보냅니다.

앞 도어는 현재 진단 로그 (일괄 처리 시간)를 제공 합니다. 진단 로그는 각 항목에 대해 다음 스키마를 갖는 개별 API 요청을 제공 합니다.

| 속성  | 설명 |
| ------------- | ------------- |
| BackendHostname | 요청을 백 엔드에 전달 하는 경우이 필드는 백 엔드의 호스트 이름을 나타냅니다. 요청이 리디렉션 되었거나 지역 캐시로 전달 된 경우 (라우팅 규칙에 캐싱이 설정 된 경우)이 필드는 비어 있습니다. |
| CacheStatus | 캐싱 시나리오의 경우이 필드는 POP에서 캐시 적중/누락을 정의 합니다. |
| ClientIp | 요청한 클라이언트의 IP 주소입니다. 요청에 X로 전달 된 헤더가 있는 경우 클라이언트 IP는 동일한에서 선택 됩니다. |
| ClientPort | 요청을 수행한 클라이언트의 IP 포트입니다. |
| HttpMethod | 요청에서 사용된 HTTP 메서드 |
| HttpStatusCode | 프록시에서 반환된 HTTP 상태 코드입니다. |
| HttpStatusDetails | 요청의 결과 상태입니다. 이 문자열 값의 의미는 상태 참조 테이블에서 찾을 수 있습니다. |
| HttpVersion | 요청 또는 연결의 유형입니다. |
| POP | 요청이 배치는 가장자리의 짧은 이름입니다. |
| RequestBytes | 요청 헤더 및 요청 본문을 포함하여 HTTP 요청 메시지의 크기(바이트)입니다. |
| RequestUri | 받은 요청의 URI |
| ResponseBytes | 백 엔드 서버에서 응답으로 보낸 바이트 수입니다.  |
| RoutingRuleName | 요청이 일치 하는 라우팅 규칙의 이름입니다. |
| RulesEngineMatchNames | 요청에서 일치 하는 규칙의 이름입니다. |
| SecurityProtocol | 요청에 사용되는 TLS/SSL 프로토콜 버전이거나, 암호화가 없는 경우 null입니다. |
| SentToOriginShield | 첫 번째 환경에 캐시 누락이 있고 요청이 지역 캐시로 전송 되었는지 여부를 나타내는 부울 필드입니다. 라우팅 규칙이 리디렉션 이거나 캐싱이 설정 되지 않은 경우이 필드를 무시 합니다. |
| TimeTaken | 요청의 첫 번째 바이트부터 마지막 응답 바이트 까지의 시간 (초)입니다. |
| TrackingReference | Front Door에서 제공하는 요청을 식별하는 고유한 참조 문자열로, 클라이언트에 X-Azure-Ref 헤더로 전송됩니다. 특정 요청의 액세스 로그에서 세부 정보를 검색하는 데 필요합니다. |
| UserAgent | 클라이언트에서 사용한 브라우저 유형입니다. |

**참고:** 다양 한 라우팅 구성 및 트래픽 동작의 경우 backendHostname, cacheStatus, sentToOriginShield 및 POP 필드와 같은 일부 필드는 다른 값으로 응답할 수 있습니다. 아래 표에서는 다양 한 값을 설명 하 고 이러한 필드는 다양 한 시나리오에 대해 설명 합니다.

| 시나리오 | 로그 항목 수 | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 캐싱을 사용 하지 않는 라우팅 규칙 | 1 | Edge POP 코드 | 요청이 전달 된 백 엔드 | 거짓 | CONFIG_NOCACHE |
| 캐싱이 설정 된 라우팅 규칙입니다. 가장자리 POP에서 캐시 적중 | 1 | Edge POP 코드 | Empty | 거짓 | 리 |
| 캐싱이 설정 된 라우팅 규칙입니다. Edge POP에서 캐시 누락이 있지만 부모 캐시 POP에서 캐시 적중 | 2 | 1. Edge POP 코드</br>2. 부모 캐시 POP 코드 | 1. 부모 캐시 POP 호스트 이름</br>2. 비어 있음 | 1. True</br>2. False | 1. 누락</br>2. PARTIAL_HIT |
| 캐싱이 설정 된 라우팅 규칙입니다. Edge 및 부모 캐시 POP에서 캐시 누락 | 2 | 1. Edge POP 코드</br>2. 부모 캐시 POP 코드 | 1. 부모 캐시 POP 호스트 이름</br>2. 캐시를 채우는 데 도움이 되는 백 엔드 | 1. True</br>2. False | 1. 누락</br>2. 누락 |


## <a name="next-steps"></a>다음 단계

- [Front Door 프로필 만들기](quickstart-create-front-door.md)
- [프런트 도어 작동 방법](front-door-routing-architecture.md)
