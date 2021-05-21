---
title: Azure Front Door에서 메트릭 및 로그 모니터링 | Microsoft Docs
description: 이 문서에서는 Azure Front Door가 지원하는 다양한 메트릭 및 액세스 로그에 대해 설명합니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: a972123604cafbfc4c6753c1fe3ad21147b53b2d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550662"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Azure Front Door에서 메트릭 및 로그 모니터링

Azure Front Door를 사용하여 다음과 같은 방법으로 리소스를 모니터링할 수 있습니다.

- **메트릭**. Azure Front Door에는 현재 성능 카운터를 볼 수 있는 메트릭이 8개 있습니다.
- **로그**. 활동 및 진단 로그를 사용하면 모니터링을 위해 리소스의 성능, 액세스 및 기타 데이터를 저장하거나 사용할 수 있습니다.

##  <a name="metrics"></a><a name="metrics"></a>메트릭

메트릭은 포털에서 성능 카운터를 볼 수 있는 특정 Azure 리소스에 대한 기능입니다. 사용 가능한 Front Door 메트릭은 다음과 같습니다.

| 메트릭 | 메트릭 표시 이름 | 단위 | 차원 | Description |
| --- | --- | --- | --- | --- |
| RequestCount | 요청 수 | 개수 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 제공하는 클라이언트 요청 수  |
| RequestSize | 요청 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 클라이언트에서 Front Door로, 요청으로 전송된 바이트 수 |
| ResponseSize | 응답 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 클라이언트로, 응답으로 전송된 바이트 수 |
| TotalLatency | 총 대기 시간 | 밀리초 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door가 클라이언트 요청을 수신한 시점부터 AFD에서 클라이언트로 마지막 응답 바이트가 전송된 시점까지 총 소요 시간입니다. |
| BackendRequestCount | 백 엔드 요청 수 | 개수 | HttpStatus</br>HttpStatusGroup</br>백 엔드 | Front Door에서 백 엔드로 전송된 요청 수 |
| BackendRequestLatency | 백 엔드 요청 대기 시간 | 밀리초 | 백 엔드 | Front Door에서 백 엔드의 마지막 응답 바이트를 받을 때까지 Front Door에서 백 엔드로 요청이 전송될 때 계산된 시간 |
| BackendHealthPercentage | 백 엔드 상태 비율 | 백분율 | 백 엔드</br>BackendPool | Front Door에서 백 엔드로 성공한 상태 프로브의 비율 |
| WebApplicationFirewallRequestCount | 웹 애플리케이션 방화벽 요청 수 | 개수 | PolicyName</br>RuleName</br>작업 | Front Door의 애플리케이션 계층 보안에 의해 처리된 클라이언트 요청 수 |

## <a name="activity-logs"></a><a name="activity-log"></a>활동 로그

활동 로그는 Front Door에서 수행된 작업에 대한 정보를 제공합니다. 또한 Front Door에서 수행된 모든 쓰기 작업(put, post 또는 delete)의 대상, 주체 및 시기를 보여 줍니다.

>[!NOTE]
>활동 로그에는 읽기(get) 작업이 포함되지 않습니다. 또한 Azure Portal 또는 원래 관리 API를 사용하여 수행하는 작업은 포함되지 않습니다.

Front Door의 활동 로그 또는 Azure Monitor에서 Azure 리소스의 모든 로그에 액세스합니다. 활동 로그를 보려면

1. Front Door 인스턴스를 선택합니다.
2. **활동 로그** 를 선택합니다.

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="활동 로그":::

3. 필터링 범위를 선택한 다음, **적용** 을 선택합니다.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>진단 로그
진단 로그는 감사 및 문제 해결에 중요한 작업 및 오류에 관한 풍부한 정보를 제공합니다. 진단 로그는 활동 로그와 다릅니다.

활동 로그는 Azure 리소스에서 수행된 작업에 대한 인사이트를 제공합니다. 진단 로그는 리소스가 수행한 작업에 대한 정보를 제공합니다. 자세한 내용은 [Azure Monitor 진단 로그](../azure-monitor/essentials/platform-logs-overview.md)를 참조하세요.

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="진단 로그":::

Front Door에 대한 진단 로그를 구성하려면 다음을 수행합니다.

1. Azure Front Door를 선택합니다.

2. **진단 설정** 을 선택합니다.

3. **진단 켜기** 를 선택합니다. 진단 로그를 메트릭과 함께 스토리지 계정에 보관하고, 이벤트 허브로 스트림하고, Azure Monitor 로그로 보냅니다.

Front Door는 현재 진단 로그를 제공합니다. 원시 로그는 각 항목에 다음 스키마가 포함된 개별 API 요청을 제공합니다.

| 속성  | Description |
| ------------- | ------------- |
| BackendHostname | 요청이 백 엔드에 전달되는 경우 이 필드는 백 엔드의 호스트 이름을 나타냅니다. 요청이 리디렉션되거나 지역 캐시(라우팅 규칙에 캐싱이 설정됨)로 전달되는 경우 이 필드는 비어 있습니다. |
| CacheStatus | 캐싱 시나리오의 경우 이 필드는 POP에서 캐시 적중/누락을 정의합니다. |
| ClientIp | 요청한 클라이언트의 IP 주소입니다. 요청에 X-Forwarded-For 헤더가 있는 경우 클라이언트 IP는 동일한 곳에서 선택됩니다. |
| ClientPort | 요청한 클라이언트의 IP 포트입니다. |
| HttpMethod | 요청에서 사용된 HTTP 메서드 |
| HttpStatusCode | 프록시에서 반환된 HTTP 상태 코드입니다. |
| HttpStatusDetails | 요청의 결과 상태입니다. 이 문자열 값의 의미는 상태 참조 테이블에서 찾을 수 있습니다. |
| HttpVersion | 요청 또는 연결의 유형입니다. |
| POP | 요청이 도달한 에지의 약식 이름입니다. |
| RequestBytes | 요청 헤더 및 요청 본문을 포함하여 HTTP 요청 메시지의 크기(바이트)입니다. |
| RequestUri | 받은 요청의 URI |
| ResponseBytes | 백 엔드 서버에서 응답으로 보낸 바이트 수입니다.  |
| RoutingRuleName | 요청과 일치하는 라우팅 규칙의 이름입니다. |
| RulesEngineMatchNames | 요청과 일치하는 규칙의 이름입니다. |
| SecurityProtocol | 요청에 사용되는 TLS/SSL 프로토콜 버전이거나, 암호화가 없는 경우 null입니다. |
| SentToOriginShield </br> (사용되지 않음) * **다음 섹션에서 사용 중단에 대한 참고 사항을 참조하세요.**| True이면 요청이 에지 POP가 아닌 원본 보호 캐시에서 응답되었음을 의미합니다. 원본 보호는 캐시 적중률을 향상하는 데 사용되는 부모 캐시입니다. |
| isReceivedFromClient | True인 경우 요청이 클라이언트에서 나온 것을 의미합니다. False인 경우 요청이 에지(자식 POP)에서 누락되고 원본 보호(부모 POP)에서 응답됩니다. |
| TimeTaken | 요청의 첫 번째 바이트부터 마지막 응답 바이트까지의 시간(초)입니다. |
| TrackingReference | Front Door에서 제공하는 요청을 식별하는 고유한 참조 문자열로, 클라이언트에 X-Azure-Ref 헤더로 전송됩니다. 특정 요청의 액세스 로그에서 세부 정보를 검색하는 데 필요합니다. |
| UserAgent | 클라이언트에서 사용한 브라우저 유형입니다. |
| ErrorInfo | 이 필드에는 추가 문제 해결을 위한 구체적인 오류 유형이 포함됩니다. </br> 가능한 값은 다음과 같습니다. </br> **NoError**: 발견된 오류가 없음을 나타냅니다. </br> **CertificateError**: 제네릭 SSL 인증서 오류입니다.</br> **CertificateNameCheckFailed**: SSL 인증서의 호스트 이름이 잘못되었거나 일치하지 않습니다. </br> **ClientDisconnected**: 클라이언트 네트워크 연결 때문에 요청이 실패했습니다. </br> **UnspecifiedClientError**: 제네릭 클라이언트 오류입니다. </br> **InvalidRequest**: 요청이 잘못되었습니다. 이 오류는 잘못된 형식의 헤더, 본문 및 URL 때문에 발생할 수 있습니다. </br> **DNSFailure**: DNS 오류가 발생했습니다. </br> **DNSNameNotResolved**: 서버 이름이나 주소를 확인할 수 없습니다. </br> **OriginConnectionAborted**: 원본과의 연결이 갑자기 중지되었습니다. </br> **OriginConnectionError**: 제네릭 원본 연결 오류입니다. </br> **OriginConnectionRefused**: 원본과의 연결을 설정할 수 없습니다. </br> **OriginError**: 제네릭 원본 오류입니다. </br> **OriginInvalidResponse**: 원본이 잘못되었거나 인식할 수 없는 응답을 반환했습니다. </br> **OriginTimeout**: 원본 요청에 대한 시간 제한이 만료되었습니다. </br> **ResponseHeaderTooBig**: 원본에서 너무 많은 응답 헤더를 반환했습니다. </br> **RestrictedIP**: 제한된 IP로 인해 요청이 차단되었습니다. </br> **SSLHandshakeError**: SSL 핸드셰이크 오류로 인해 원본과의 연결을 설정할 수 없습니다. </br> **UnspecifiedError**: 테이블의 오류에 맞지 않는 오류가 발생했습니다. |

### <a name="sent-to-origin-shield-deprecation"></a>원본 보호로 전송됨 사용 중단
원시 로그 속성 **isSentToOriginShield** 는 더 이상 사용되지 않으며 새 필드 **isReceivedFromClient** 로 바뀌었습니다. 사용 중단된 필드를 이미 사용하고 있는 경우 새 필드를 사용하세요. 

원시 로그는 CDN 에지(자식 POP)와 원본 보호 모두에서 생성된 로그를 포함합니다. 원본 보호는 전 세계에 전략적으로 배치되는 부모 노드를 말합니다. 이러한 노드는 원본 서버와 통신하여 원본에 대한 트래픽 부하를 줄입니다. 

원본 보호로 이동하는 모든 요청에는 두 가지 로그 항목이 있습니다.

* 에지 노드에 대한 항목 하나
* 원본 보호에 대한 항목 하나 

에지 노드 및 원본 보호에 대한 송신 또는 응답을 구분하려면 **isReceivedFromClient** 필드를 사용하여 올바른 데이터를 가져올 수 있습니다. 

값이 false인 경우 요청이 원본 보호에서 에지 노드로 응답되는 것을 의미합니다. 이 방법은 원시 로그를 청구 데이터와 비교하는 데 효과적입니다. 원본 보호에서 에지 노드로의 송신에는 요금이 부과되지 않습니다. 에지 노드에서 클라이언트로의 송신에는 요금이 부과됩니다. 

**Log Analytics에서 원본 보호에 생성된 로그를 제외하는 Kusto 쿼리 샘플**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> 다양한 라우팅 구성 및 트래픽 동작의 경우 backendHostname, cacheStatus, isReceivedFromClient 및 POP 필드와 같은 일부 필드는 서로 다른 값으로 응답할 수 있습니다. 아래 표에서는 다양한 시나리오에서 이러한 필드에 포함되는 다양한 값을 설명합니다.

| 시나리오 | 로그 항목 수 | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 캐싱을 사용하지 않는 라우팅 규칙 | 1 | 에지 POP 코드 | 요청이 전달된 백 엔드 | True | CONFIG_NOCACHE |
| 캐싱을 사용하는 라우팅 규칙 에지 POP에서 캐시 적중 | 1 | 에지 POP 코드 | Empty | True | HIT |
| 캐싱을 사용하는 라우팅 규칙 에지 POP에서 캐시가 누락되지만 부모 캐시 POP에서 캐시 적중 | 2 | 1. 에지 POP 코드</br>2. 부모 캐시 POP 코드 | 1. 부모 캐시 POP 호스트 이름</br>2. Empty | 1. True</br>2. False | 1. MISS</br>2. HIT |
| 캐싱을 사용하는 라우팅 규칙 에지 POP에서 캐시가 누락되지만 부모 캐시 POP에서 부분 캐시 적중 | 2 | 1. 에지 POP 코드</br>2. 부모 캐시 POP 코드 | 1. 부모 캐시 POP 호스트 이름</br>2. 캐시를 채우는 데 도움이 되는 백 엔드 | 1. True</br>2. False | 1. MISS</br>2. PARTIAL_HIT |
| 캐싱을 사용하는 라우팅 규칙 에지 POP에서 부분 적중되지만 부모 캐시 POP에서 캐시 적중 | 2 | 1. 에지 POP 코드</br>2. 부모 캐시 POP 코드 | 1. 에지 POP 코드</br>2. 부모 캐시 POP 코드 | 1. True</br>2. False | 1. PARTIAL_HIT</br>2. HIT |
| 캐싱을 사용하는 라우팅 규칙 에지 및 부모 캐시 POP에서 캐시 누락 | 2 | 1. 에지 POP 코드</br>2. 부모 캐시 POP 코드 | 1. 에지 POP 코드</br>2. 부모 캐시 POP 코드 | 1. True</br>2. False | 1. MISS</br>2. MISS |

> [!NOTE]
> 캐싱 시나리오에서 요청의 일부 바이트가 Front Door 에지 또는 원본 보호 캐시에서 처리되고, 일부 바이트가 대규모 개체의 원본에서 처리되는 경우 캐시 상태 값은 partial_hit가 됩니다.

Front Door는 개체 청크라는 기술을 사용합니다. 대용량 파일이 요청되면 Front Door가 원본에서 파일의 더 작은 파일 조각을 검색합니다. Front Door POP 서버가 요청된 파일의 전체 또는 바이트 범위를 수신하면 Front Door 에지 서버가 원본에서 8MB 청크로 파일을 요청합니다.

청크가 Front Door 에지에 도착하면 캐시되고 사용자에게 즉시 제공됩니다. 그런 다음, Front Door가 다음 청크를 동시에 프리페치합니다. 이 프리페치를 사용하면 콘텐츠가 사용자보다 먼저 하나의 청크로 유지되도록 하여 대기 시간을 줄일 수 있습니다. 이 프로세스는 전체 파일을 다운로드하거나(요청된 경우), 모든 바이트 범위를 사용할 수 있거나(요청된 경우), 클라이언트에서 연결을 종료할 때까지 계속됩니다. 바이트 범위 요청에 대한 자세한 내용은 RFC 7233을 참조하세요. Front Door는 받은 청크를 모두 캐시합니다. Front Door 캐시에서 전체 파일을 캐시할 필요는 없습니다. 파일 또는 바이트 범위에 대한 요청은 Front Door 캐시에서 처리됩니다. 모든 청크가 Front Door에 캐시되지 않으면 프리페치를 사용하여 원본에서 청크를 요청합니다. 이 최적화는 원본 서버에서 바이트 범위 요청을 지원하는 기능을 사용합니다. 원본 서버에서 바이트 범위 요청을 지원하지 않으면 이 최적화가 적용되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Front Door 프로필 만들기](quickstart-create-front-door.md)
- [Front Door 작동 원리](front-door-routing-architecture.md)
