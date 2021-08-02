---
title: Azure App Configuration 모니터링 데이터 참조
description: App Configuration을 모니터링할 때 필요한 중요 참조 자료
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 05/05/2021
ms.openlocfilehash: ec6515403574af29c47b03b55eb3cbc42ecd4a7a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968345"
---
# <a name="monitoring-app-configuration-data-reference"></a>App Configuration 모니터링 데이터 참조

이 문서는 App Configuration에서 수집한 모니터링 데이터에 대한 참조입니다. App Configuration 모니터링 데이터를 수집하고 분석하는 방법에 관한 연습은 [App Configuration 모니터링](monitor-app-configuration.md)을 참조하세요.

## <a name="metrics"></a>메트릭 
리소스 공급자 및 종류: [App Configuration 플랫폼 메트릭](../azure-monitor/essentials/metrics-supported.md#microsoftappconfigurationconfigurationstores)

| 메트릭 | 단위 | 설명 |
|-------|-----| ----- |
| HTTP 들어오는 요청 수   | 개수 | 들어오는 HTTP 요청의 총수 |
|HTTP 들어오는 요청 기간 | 밀리초 | HTTP 요청의 서버 쪽 기간 |
| 제한된 HTTP 요청 수 | 개수 |    제한된 요청은 429 상태 코드(너무 많은 요청)를 반환하는 HTTP 요청입니다. |

보다 자세한 정보는 [Azure Monitor에서 지원되는 모든 플랫폼 메트릭](../azure-monitor/essentials/metrics-supported.md)을 참조하세요.


## <a name="metric-dimensions"></a>메트릭 차원
App Configuration에는 해당 메트릭과 관련된 다음 차원이 있습니다.

| 메트릭 이름 | 차원 설명 |
|-------|-----|
| HTTP 들어오는 요청 수 | 총 HTTP 요청 수입니다. 지원되는 차원은 각 요청의 **HttpStatusCode** 또는 **AuthenticationScheme** 입니다. **AuthenticationScheme** 은 AAD 또는 HMAC 인증별로 필터링할 수 있습니다.   |
| HTTP 들어오는 요청 기간 | 각 요청의 서버 쪽 기간입니다. 지원되는 차원은 각 요청의 **HttpStatusCode** 또는 **AuthenticationScheme** 사용입니다. **AuthenticationScheme** 은 AAD 또는 HMAC 인증별로 필터링할 수 있습니다. |
| 제한된 HTTP 요청 수 | 이 메트릭에는 차원이 없습니다. |

 메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)을 참조하세요.

## <a name="resource-logs"></a>리소스 로그
이 섹션에서는 App Configuration에 대해 수집된 리소스 로그의 범주 유형을 나열합니다. 

| 리소스 로그 유형 | 추가 정보|
|-------|-----|
| HttpRequest | [App Configuration 리소스 로그 범주 정보](../azure-monitor/essentials/resource-logs-categories.md) |

자세한 내용은 [Azure Monitor에서 지원되는 모든 리소스 로그 범주 유형](../azure-monitor/essentials/resource-logs-schema.md) 목록을 참조하세요.
 
## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블

이 섹션은 App Configuration과 관련되고 Log Analytics의 쿼리에 사용할 수 있는 모든 Azure Monitor Logs Kusto 테이블을 참조합니다.

|리소스 유형 | 메모 |
|-------|-----|
| [AACHttpRequest](/azure/azure-monitor/reference/tables/aachttprequest) | 선택한 앱 구성 리소스로 전송된 모든 HTTP 요청의 항목입니다. |
| [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) | Azure에서 발생한 구독 수준 또는 관리 그룹 수준 이벤트에 대한 인사이트를 제공하는 Azure 활동 로그의 항목입니다. |

모든 Azure Monitor Logs/Log Analytics 테이블에 대한 참조는 [Azure Monitor 로그 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype)를 확인하세요.

### <a name="diagnostics-tables"></a>진단 테이블

App Configuration은 [AACHttpRequest 테이블](/azure/azure-monitor/reference/tables/aachttprequest)을 사용하여 리소스 로그 정보를 저장합니다.

**HTTP 요청**

|속성 | Type | Description |
|-------|-----| ----- |
|범주   |문자열 |이벤트의 로그 범주로, 항상 HttpRequest입니다. 
|ClientIPAddress |  문자열| 요청을 보낸 클라이언트의 IP 주소입니다.
|ClientRequestId|   문자열| 클라이언트에서 제공한 요청 ID입니다.
|CorrelationId| 문자열| 상관 관계가 있는 로그의 GUID입니다.
|DurationMs|    int |밀리초 단위의 작업 기간입니다.
|메서드 문자열| HTTP| HTTP 요청 메서드(get 또는 post)입니다.
|RequestId| 문자열| 서버에서 생성된 고유한 요청 ID입니다.
|RequestLength| int |HTTP 요청의 길이(바이트)입니다.
|RequestURI|    문자열| 요청의 URI로, 키 및 레이블 이름을 포함할 수 있습니다. 
|_ResourceId|   문자열| 레코드가 연결된 리소스의 고유 식별자입니다.
|ResponseLength|    int|    HTTP 응답의 길이(바이트)입니다.
|SourceSystem| 문자열|  
|StatusCode|    int |요청의 HTTP 상태 코드입니다.
|TenantId|  문자열  |요청의 WorkspaceId입니다. 
|TimeGenerated| Datetime|   요청이 전송되었기 때문에 로그가 생성된 타임스탬프(UTC)입니다.
|Type   |문자열|    테이블의 이름입니다.
|UserAgent| 문자열| 클라이언트에서 제공하는 사용자 에이전트입니다.


## <a name="see-also"></a>참고 항목

* Azure App Configuration 모니터링에 관한 자세한 내용은 [Azure App Configuration 모니터링](monitor-app-configuration.md)을 참조하세요.
* Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.