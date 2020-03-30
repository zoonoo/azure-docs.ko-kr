---
title: Azure 정문에서 메트릭 및 로그 모니터링| 마이크로 소프트 문서
description: 이 문서에서는 Azure Front Door가 지원하는 다양한 메트릭 및 액세스 로그에 대해 설명합니다.
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
ms.openlocfilehash: b935355cce36a6e26b168db286ab40248f8f0f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471730"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Azure 정문에서 메트릭 및 로그 모니터링

Azure 정문을 사용하여 다음과 같은 방법으로 리소스를 모니터링할 수 있습니다.

- **메트릭 .** Azure 정문에는 현재 성능 카운터를 볼 수 있는 7가지 메트릭이 있습니다.
- **로그**. 활동 및 진단 로그를 사용하면 모니터링을 위해 리소스에서 성능, 액세스 및 기타 데이터를 저장하거나 사용할 수 있습니다.

### <a name="metrics"></a>메트릭

메트릭은 포털에서 성능 카운터를 볼 수 있는 특정 Azure 리소스의 기능입니다. 다음은 사용할 수 있는 정문 메트릭입니다.

| 메트릭 | 메트릭 표시 이름 | 단위 | 차원 | 설명 |
| --- | --- | --- | --- | --- |
| RequestCount | 요청 수 | 개수 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 제공하는 클라이언트 요청 수  |
| RequestSize | 요청 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 클라이언트에서 Front Door로, 요청으로 전송된 바이트 수 |
| ResponseSize | 응답 크기 | 바이트 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door에서 클라이언트로, 응답으로 전송된 바이트 수 |
| TotalLatency | 총 대기 시간 | 밀리초 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 클라이언트가 정문에서 마지막 응답 바이트를 승인할 때까지 정문에서 받은 클라이언트 요청에서 계산된 시간입니다. |
| BackendRequestCount | 백 엔드 요청 수 | 개수 | HttpStatus</br>HttpStatusGroup</br>백 엔드 | Front Door에서 백 엔드로 전송된 요청 수 |
| BackendRequestLatency | 백 엔드 요청 대기 시간 | 밀리초 | 백 엔드 | Front Door에서 백 엔드의 마지막 응답 바이트를 받을 때까지 Front Door에서 백 엔드로 요청이 전송될 때 계산된 시간 |
| BackendHealthPercentage | 백 엔드 상태 비율 | 백분율 | 백 엔드</br>BackendPool | Front Door에서 백 엔드로 성공한 상태 프로브의 비율 |
| WebApplicationFirewallRequestCount | 웹 애플리케이션 방화벽 요청 수 | 개수 | PolicyName</br>RuleName</br>작업 | Front Door의 애플리케이션 계층 보안에 의해 처리된 클라이언트 요청 수 |

## <a name="activity-logs"></a><a name="activity-log"></a>활동 로그

활동 로그는 정문에서 수행된 작업에 대한 정보를 제공합니다. 또한 정문에서 수행한 쓰기 작업(게시, 게시 또는 삭제)에 대해 무엇을, 누가, 언제 사용할지 결정합니다.

>[!NOTE]
>활동 로그에는 읽기(get) 작업이 포함되지 않습니다. 또한 Azure 포털 또는 원래 관리 API를 사용하여 수행하는 작업은 포함되지 않습니다.

정문또는 Azure 모니터의 Azure 리소스의 모든 로그에 액세스합니다. 활동 로그를 보려면

1. 정문 인스턴스를 선택합니다.
2. **활동 로그를 선택합니다.**

    ![활동 로그](./media/front-door-diagnostics/activity-log.png)

3. 필터링 범위를 선택한 다음 **적용을**선택합니다.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>진단 로그
진단 로그는 감사 및 문제 해결에 중요한 작업 및 오류에 대한 풍부한 정보를 제공합니다. 진단 로그는 활동 로그와 다릅니다.

활동 로그는 Azure 리소스에서 수행된 작업에 대한 통찰력을 제공합니다. 진단 로그는 리소스가 수행한 작업에 대한 통찰력을 제공합니다. 자세한 내용은 [Azure 모니터 진단 로그를](../azure-monitor/platform/platform-logs-overview.md)참조하십시오.

![진단 로그](./media/front-door-diagnostics/diagnostic-log.png)

정문에 대한 진단 로그를 구성하려면 다음을 수행하십시오.

1. Azure 정문을 선택합니다.

2. **진단 설정을**선택합니다.

3. **진단 켜기**를 선택합니다. 메트릭과 함께 진단 로그를 저장소 계정으로 보관하거나 이벤트 허브로 스트리밍하거나 Azure Monitor 로그로 보냅니다.

Front Door는 현재 진단 로그를 제공합니다(시간당 일괄 처리). 진단 로그는 다음 스키마를 갖는 각 항목에 개별 API 요청을 제공합니다.

| 속성  | 설명 |
| ------------- | ------------- |
| 백엔드호스트이름 | 요청이 백 엔드로 전달되는 경우 이 필드는 백 엔드의 호스트 이름을 나타냅니다. 요청이 리디렉션되거나 지역 캐시로 전달된 경우(라우팅 규칙에 대한 캐싱이 활성화된 경우) 이 필드는 비어 있습니다. |
| 캐시 상태 | 캐싱 시나리오의 경우 이 필드는 POP에서 캐시 적중/누락을 정의합니다. |
| ClientIp | 요청한 클라이언트의 IP 주소입니다. 요청에 X-전달-For 헤더가 있는 경우 클라이언트 IP가 동일한 헤더에서 선택됩니다. |
| 클라이언트 포트 | 요청을 한 클라이언트의 IP 포트입니다. |
| HttpMethod | 요청에서 사용된 HTTP 메서드 |
| HttpStatusCode | 프록시에서 반환된 HTTP 상태 코드입니다. |
| Http상태 세부 정보 | 요청의 결과 상태입니다. 이 문자열 값의 의미는 상태 참조 테이블에서 찾을 수 있습니다. |
| HttpVersion | 요청 또는 연결의 유형입니다. |
| POP | 요청이 도착한 가장자리의 짧은 이름입니다. |
| 요청 바이트 | 요청 헤더 및 요청 본문을 포함하여 바이트내의 HTTP 요청 메시지 크기입니다. |
| 요청우리 | 받은 요청의 URI |
| 응답바이트 | 백 엔드 서버에서 응답으로 전송하는 바이트입니다.  |
| 라우팅규칙 이름 | 요청이 일치하는 라우팅 규칙의 이름입니다. |
| 보안 프로토콜 | 암호화가 없는 경우 요청 또는 null에서 사용하는 TLS/SSL 프로토콜 버전입니다. |
| 센트토오리진쉴드 | 첫 번째 환경에 캐시 누락이 있고 요청이 지역 캐시로 전송되었는지 나타내는 부울 필드입니다. 라우팅 규칙이 리디렉션이거나 캐싱을 사용하도록 설정하지 않은 경우 이 필드를 무시합니다. |
| TimeTaken | 작업이 걸린 시간(밀리초)입니다. |
| 추적 참조 | 정문에서 제공하는 요청을 식별하는 고유한 참조 문자열로 X-Azure-Ref 헤더로 클라이언트에 전송됩니다. 특정 요청에 대한 액세스 로그에서 세부 정보를 검색하는 데 필요합니다. |
| UserAgent | 클라이언트가 사용한 브라우저 유형입니다. |

**참고:** 다양한 라우팅 구성 및 트래픽 동작의 경우 백엔드Hostname, cacheStatus, sentToOriginShield 및 POP 필드와 같은 일부 필드는 다른 값으로 응답할 수 있습니다. 아래 표는 다양한 시나리오에 대해 이러한 필드가 가질 수 있는 다양한 값을 설명합니다.

| 시나리오 | 로그 항목 수 | POP | 백엔드호스트이름 | 센트토오리진쉴드 | 캐시 상태 |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 캐싱을 사용하도록 설정하지 않은 라우팅 규칙 | 1 | 에지 POP 코드 | 요청이 전달된 백 엔드 | False | CONFIG_NOCACHE |
| 캐싱을 사용하도록 설정한 라우팅 규칙입니다. 에지 POP에서 캐시 적중 | 1 | 에지 POP 코드 | Empty | False | 히트 |
| 캐싱을 사용하도록 설정한 라우팅 규칙입니다. 에지 POP에서 캐시 누락하지만 상위 캐시 POP에서 캐시 히트 | 2 | 1. 에지 POP 코드</br>2. 부모 캐시 POP 코드 | 1. 부모 캐시 POP 호스트 이름</br>2. 비어 있음 | 1화 사실</br>2. 거짓 | 1. 미스</br>2. PARTIAL_HIT |
| 캐싱을 사용하도록 설정한 라우팅 규칙입니다. 에지 및 상위 캐시 POP 모두에서 캐시 누락 | 2 | 1. 에지 POP 코드</br>2. 부모 캐시 POP 코드 | 1. 부모 캐시 POP 호스트 이름</br>2. 캐시를 채우는 데 도움이되는 백 엔드 | 1화 사실</br>2. 거짓 | 1. 미스</br>2. 미스 |


## <a name="next-steps"></a>다음 단계

- [Front Door 프로필 만들기](quickstart-create-front-door.md)
- [정문 작동 방식](front-door-routing-architecture.md)
