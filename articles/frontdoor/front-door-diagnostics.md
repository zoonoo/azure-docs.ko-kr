---
title: Azure Front 도어에서 메트릭 및 로그 모니터링 | Microsoft Docs
description: 이 문서에서는 Azure Front 도어가 지 원하는 다양 한 메트릭 및 액세스 로그에 대해 설명 합니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: d533b8fed47b1790cc35429613179f440f1fac51
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961751"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Azure 전면 도어에서 메트릭 및 로그 모니터링

Azure Front 도어를 사용 하 여 다음과 같은 방법으로 리소스를 모니터링할 수 있습니다.

- **메트릭**. Azure 전면 도어에는 현재 성능 카운터를 볼 수 있는 8 개의 메트릭이 있습니다.
- **로그**. 활동 및 진단 로그를 사용 하면 모니터링 목적으로 리소스에서 성능, 액세스 및 기타 데이터를 저장 하거나 사용할 수 있습니다.

### <a name="metrics"></a>메트릭

메트릭은 포털에서 성능 카운터를 볼 수 있는 특정 Azure 리소스에 대 한 기능입니다. 다음은 사용 가능한 전방 도어 메트릭입니다.

| 메트릭 | 메트릭 표시 이름 | 단위 | 차원 | Description |
| --- | --- | --- | --- | --- |
| RequestCount | 요청 수 | 개수 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 제공하는 클라이언트 요청 수  |
| RequestSize | 요청 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 클라이언트에서 Front Door로, 요청으로 전송된 바이트 수 |
| ResponseSize | 응답 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 클라이언트로, 응답으로 전송된 바이트 수 |
| TotalLatency | 총 대기 시간 | 밀리초 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 마지막 응답 바이트가 AFD에서 클라이언트로 전송 될 때까지 Front 도어가 받은 클라이언트 요청의 총 시간입니다. |
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

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="활동 로그":::

3. 필터링 범위를 선택한 다음 **적용**을 선택 합니다.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>진단 로그
진단 로그는 감사 및 문제 해결에 중요 한 작업 및 오류에 대 한 다양 한 정보를 제공 합니다. 진단 로그는 활동 로그와 다릅니다.

활동 로그는 Azure 리소스에서 수행 된 작업에 대 한 통찰력을 제공 합니다. 진단 로그는 리소스에서 수행한 작업에 대 한 통찰력을 제공 합니다. 자세한 내용은 [Azure Monitor 진단 로그](../azure-monitor/platform/platform-logs-overview.md)를 참조 하세요.

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="활동 로그":::

프런트 도어에 대 한 진단 로그를 구성 하려면:

1. Azure Front 도어를 선택 합니다.

2. **진단 설정**을 선택 합니다.

3. **진단 켜기**를 선택합니다. 진단 로그를 메트릭과 함께 저장소 계정에 보관 하거나, 이벤트 허브로 스트림 하거나 Azure Monitor 로그에 보냅니다.

앞 도어는 현재 진단 로그 (일괄 처리 시간)를 제공 합니다. 진단 로그는 각 항목에 대해 다음 스키마를 갖는 개별 API 요청을 제공 합니다.

| 속성  | Description |
| ------------- | ------------- |
| BackendHostname | 요청을 백 엔드에 전달 하는 경우이 필드는 백 엔드의 호스트 이름을 나타냅니다. 요청이 리디렉션 되거나 지역 캐시로 전달 되는 경우이 필드는 비어 있습니다 (라우팅 규칙에 캐싱이 설정 된 경우). |
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
| SentToOriginShield </br> (사용 되지 않음) * **다음 섹션에서 사용 중단에 대 한 참고를 참조 하세요.**| True이면 요청이 에지 POP가 아닌 원본 보호 캐시에서 응답되었음을 의미합니다. 원본 보호는 캐시 적중률을 향상하는 데 사용되는 부모 캐시입니다. |
| isReceivedFromClient | True 이면 요청이 클라이언트에서 제공 되었음을 의미 합니다. False 이면 요청이에 지 (자식 POP)에서 누락 되 고 원본 방패 (부모 POP)에서 응답 합니다. 
| TimeTaken | 요청의 첫 번째 바이트부터 마지막 응답 바이트 까지의 시간 (초)입니다. |
| TrackingReference | Front Door에서 제공하는 요청을 식별하는 고유한 참조 문자열로, 클라이언트에 X-Azure-Ref 헤더로 전송됩니다. 특정 요청의 액세스 로그에서 세부 정보를 검색하는 데 필요합니다. |
| UserAgent | 클라이언트에서 사용한 브라우저 유형입니다. |

### <a name="sent-to-origin-shield-deprecation"></a>원본 방패 사용 중단으로 전송
원시 로그 속성 **isSentToOriginShield** 는 더 이상 사용 되지 않으며 새 필드인 **isReceivedFromClient**로 바뀌었습니다. 사용 되지 않는 필드를 이미 사용 하 고 있는 경우 새 필드를 사용 합니다. 

원시 로그는 CDN에 지 (자식 POP)와 원본 방패에서 생성 된 로그를 포함 합니다. 원본 방패는 전 세계에서 전략적으로 배치 되는 부모 노드를 나타냅니다. 이러한 노드는 원본 서버와 통신 하 여 원본에서의 트래픽 부하를 줄입니다. 

원본 방패로 이동 하는 모든 요청에 대해 2 개의 로그 항목이 있습니다.

* 에 지 노드에 대 한 하나
* 원본 방패를 위한 것입니다. 

에 지 노드 및 원본 방패의 송신 또는 응답을 구분 하기 위해 **isReceivedFromClient** 필드를 사용 하 여 올바른 데이터를 가져올 수 있습니다. 

값이 false 이면 요청은 원본 방패에서에 지 노드로 응답 하는 것을 의미 합니다. 이 방법은 원시 로그를 청구 데이터와 비교 하는 데 효과적입니다. 원본 실드에서에 지 노드로의 송신에 대 한 요금이 발생 하지 않습니다. 에 지 노드에서 클라이언트로의 송신에 대 한 요금이 부과 됩니다. 

**Log Analytics에서 원본 방패에 생성 된 로그를 제외 하는 kusto 쿼리 샘플입니다.**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> 다양 한 라우팅 구성 및 트래픽 동작의 경우 backendHostname, cacheStatus, isReceivedFromClient 및 POP 필드와 같은 일부 필드는 다른 값으로 응답할 수 있습니다. 아래 표에서는 다양 한 시나리오에 대해 이러한 필드에 포함 되는 다양 한 값을 설명 합니다.

| 시나리오 | 로그 항목 수 | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 캐싱을 사용 하지 않는 라우팅 규칙 | 1 | Edge POP 코드 | 요청이 전달 된 백 엔드 | 참 | CONFIG_NOCACHE |
| 캐싱이 설정 된 라우팅 규칙입니다. 가장자리 POP에서 캐시 적중 | 1 | Edge POP 코드 | 비어 있음 | 참 | 리 |
| 캐싱이 설정 된 라우팅 규칙입니다. Edge POP에서 캐시 누락이 있지만 부모 캐시 POP에서 캐시 적중 | 2 | 1. Edge POP 코드</br>2. 부모 캐시 POP 코드 | 1. 부모 캐시 POP 호스트 이름</br>2. 비어 있음 | 1. True</br>2. False | 1. 누락</br>2. 적중 |
| 캐싱이 설정 된 라우팅 규칙입니다. Edge POP의 캐시 누락 (부모 캐시 POP에서 부분 캐시 적중) | 2 | 1. Edge POP 코드</br>2. 부모 캐시 POP 코드 | 1. 부모 캐시 POP 호스트 이름</br>2. 캐시를 채우는 데 도움이 되는 백 엔드 | 1. True</br>2. False | 1. 누락</br>2. PARTIAL_HIT |
| 캐싱이 설정 된 라우팅 규칙입니다. Edge POP의 캐시 PARTIAL_HIT, 부모 캐시의 캐시 적중 | 2 | 1. Edge POP 코드</br>2. 부모 캐시 POP 코드 | 1. Edge POP 코드</br>2. 부모 캐시 POP 코드 | 1. True</br>2. False | 1. PARTIAL_HIT</br>2. 적중 |
| 캐싱이 설정 된 라우팅 규칙입니다. Edge 및 부모 cache POPP에서 캐시 누락 | 2 | 1. Edge POP 코드</br>2. 부모 캐시 POP 코드 | 1. Edge POP 코드</br>2. 부모 캐시 POP 코드 | 1. True</br>2. False | 1. 누락</br>2. 누락 |

> [!NOTE]
> 캐싱 시나리오의 경우 요청에 대 한 일부 바이트가 Front 도어 edge 또는 원본 방패 캐시에서 제공 될 때 캐시 상태 값이 partial_hit 됩니다. 반면 일부 바이트는 대량 개체의 원본에서 제공 됩니다.

Front Door는 개체 청크라는 기술을 사용합니다. 대형 파일이 요청 되 면 Front 문이 원본에서 파일의 더 작은 부분을 검색 합니다. 프런트 도어 POP 서버는 요청 된 파일의 전체 또는 바이트 범위를 수신 하 고 나면 프런트 도어 edge 서버는 원본에서 8mb 청크를 기준으로 파일을 요청 합니다.

청크가 프런트 도어 가장자리에 도착 하면 캐시 되어 사용자에 게 즉시 제공 됩니다. 그러면 Front 도어가 다음 청크를 병렬로 사전 인출 합니다. 이 프리페치를 사용 하면 콘텐츠가 사용자 보다 먼저 한 청크로 유지 되므로 대기 시간이 줄어듭니다. 이 프로세스는 전체 파일이 다운로드 될 때까지 계속 됩니다 (요청 된 경우). 모든 바이트 범위를 사용할 수 있거나 (요청한 경우) 클라이언트에서 연결을 닫을 때까지 계속 됩니다. 바이트 범위 요청에 대한 자세한 내용은 RFC 7233을 참조하세요. 프런트 도어는 수신 되는 모든 청크를 캐시 합니다. 전체 파일을 Front 도어 캐시에 캐시할 필요가 없습니다. 파일 또는 바이트 범위에 대 한 결과 요청은 Front 도어 캐시에서 제공 됩니다. 모든 청크가 앞 도어에서 캐시 되지 않는 경우 프리페치는 원본에서 청크를 요청 하는 데 사용 됩니다. 이 최적화는 원본 서버에서 바이트 범위 요청을 지원하는 기능을 사용합니다. 원본 서버에서 바이트 범위 요청을 지원하지 않으면 이 최적화가 적용되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Front Door 프로필 만들기](quickstart-create-front-door.md)
- [프런트 도어 작동 방법](front-door-routing-architecture.md)
