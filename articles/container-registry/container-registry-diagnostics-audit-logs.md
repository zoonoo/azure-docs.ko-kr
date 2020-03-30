---
title: 리소스 로그 분석 & 수집
description: 인증, 이미지 푸시 및 이미지 끌어오기와 같은 Azure 컨테이너 레지스트리에 대한 리소스 로그 이벤트를 기록하고 분석합니다.
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409646"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>진단 평가 및 감사를 위한 Azure 컨테이너 레지스트리 로그

이 문서에서는 Azure Monitor 의 기능을 사용하여 Azure 컨테이너 레지스트리에 대한 로그 데이터를 수집하는 방법을 [설명합니다.](../azure-monitor/overview.md) Azure Monitor는 레지스트리의 사용자 기반 이벤트에 대한 [리소스 로그(이전의](../azure-monitor/platform/platform-logs-overview.md) *진단 로그)를 수집합니다.* 다음 요구 사항을 충족하기 위해 이 데이터를 수집하고 사용합니다.

* 레지스트리 인증 이벤트를 감사하여 보안 및 규정 준수를 보장합니다. 

* 레지스트리의 운영 문제를 진단할 수 있도록 끌어오기 및 끌어오기 이벤트와 같은 레지스트리 아티팩트에 대한 전체 활동 추적을 제공합니다. 

Azure Monitor를 사용하여 리소스 로그 데이터를 수집할 경우 추가 비용이 발생할 수 있습니다. [Azure 모니터 가격 책정을](https://azure.microsoft.com/pricing/details/monitor/)참조하십시오. 

## <a name="repository-events"></a>리포지토리 이벤트

이미지 및 기타 아티팩트에 대한 다음 리포지토리 수준 이벤트는 현재 기록됩니다.

* **푸시 이벤트**
* **이벤트 끌어오기**
* **이벤트 태그 해제**
* **이벤트** 삭제(리포지토리 삭제 이벤트 포함)

현재 기록되지 않은 리포지토리 수준 이벤트: 제거 이벤트입니다.

## <a name="registry-resource-logs"></a>레지스트리 리소스 로그

리소스 로그에는 내부 작업을 설명하는 Azure 리소스에서 내보낸 정보가 포함되어 있습니다. Azure 컨테이너 레지스트리의 경우 로그에는 다음 테이블에 저장된 인증 및 리포지토리 수준 이벤트가 포함됩니다. 

* **컨테이너레지스트리로그인이벤트** - 들어오는 ID 및 IP 주소를 포함한 레지스트리 인증 이벤트 및 상태
* **컨테이너레지스트리리포지토리이벤트** - 레지스트리 리포지토리의 이미지 및 기타 아티팩트에 대한 푸시 및 끌어오기와 같은 작업
* **AzureMetrics** - 컨테이너 집계 된 푸시 및 끌어오기[개수와](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) 같은 레지스트리 메트릭입니다.

작업의 경우 로그 데이터에는 다음이 포함됩니다.
  * 성공 또는 실패 상태
  * 시작 및 종료 타임스탬프

리소스 로그 외에도 Azure는 컨테이너 레지스트리의 생성 또는 삭제와 같은 Azure 관리 이벤트의 단일 구독 수준 레코드인 [활동 로그를](../azure-monitor/platform/platform-logs-overview.md)제공합니다.

## <a name="enable-collection-of-resource-logs"></a>리소스 로그 수집 사용

컨테이너 레지스트리에 대한 리소스 로그 컬렉션은 기본적으로 활성화되어 있지 않습니다. 모니터링하려는 각 레지스트리에 대한 진단 설정을 명시적으로 활성화합니다. 진단 설정을 사용하도록 설정하는 옵션은 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기를](../azure-monitor/platform/diagnostic-settings.md)참조하십시오.

예를 들어 Azure Monitor에서 거의 실시간으로 컨테이너 레지스트리에 대한 로그 및 메트릭을 보려면 로그 분석 작업 영역에서 리소스 로그를 수집합니다. Azure 포털을 사용하여 이 진단 설정을 사용하려면 다음을 수행하십시오.

1. 아직 작업 영역이 없는 경우 [Azure 포털을](../azure-monitor/learn/quick-create-workspace.md)사용하여 작업 영역을 만듭니다. 데이터 수집의 대기 시간을 최소화하려면 작업 영역이 컨테이너 레지스트리와 **동일한 지역에** 있는지 확인합니다.
1. 포털에서 레지스트리를 선택하고 **진단 추가 설정을 > 진단 > 모니터링 설정을 선택합니다.**
1. 설정의 이름을 입력하고 **로그 분석으로 보내기를**선택합니다.
1. 레지스트리 진단 로그의 작업 영역을 선택합니다.
1. 수집할 로그 데이터를 선택하고 **저장을**클릭합니다.

다음 이미지는 포털을 사용하여 레지스트리에 대한 진단 설정을 만드는 것을 보여 주며, 이 에 대한 진단 설정을 보여 주면 됩니다.

![진단 설정을 사용하도록 설정](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> 필요한 데이터만 수집하여 비용과 모니터링 요구 사항을 조정합니다. 예를 들어 인증 이벤트를 감사해야 하는 경우 **컨테이너레지스트리로그인이벤트** 로그만 선택합니다. 

## <a name="view-data-in-azure-monitor"></a>Azure 모니터의 데이터 보기

Log Analytics에서 진단 로그 컬렉션을 사용하도록 설정하면 Azure Monitor에 데이터가 표시되는 데 몇 분 정도 걸릴 수 있습니다. 포털에서 데이터를 보려면 레지스트리를 선택하고 **> 로그 모니터링을 선택합니다.** 레지스트리에 대한 데이터가 포함된 테이블 중 하나를 선택합니다. 

쿼리를 실행하여 데이터를 봅니다. 여러 샘플 쿼리가 제공되거나 직접 실행됩니다. 예를 들어 다음 쿼리는 **컨테이너레지스트리RepositoryEvents** 테이블에서 가장 최근 24시간 의 데이터를 검색합니다.

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

다음 이미지는 샘플 출력을 보여 주며,

![로그 데이터 쿼리](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Azure 포털에서 로그 분석 사용에 대한 자습서는 [Azure 모니터 로그 분석 시작하기](../azure-monitor/log-query/get-started-portal.md)를 참조하거나 로그 분석 데모 [환경을](https://portal.loganalytics.io/demo)사용해 보십시오. 

로그 쿼리에 대한 자세한 내용은 [Azure Monitor 의 로그 쿼리 개요를](../azure-monitor/log-query/log-query-overview.md)참조하십시오.

### <a name="additional-query-examples"></a>추가 쿼리 예제

#### <a name="100-most-recent-registry-events"></a>100개의 최신 레지스트리 이벤트

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>추가 로그 대상

로그 애널리틱스로 로그를 보내는 것 외에도 또는 다른 방법으로 Azure Storage 계정을 로그 대상으로 선택하는 것이 일반적입니다. Azure Storage에 로그를 보관하려면 진단 설정을 통해 보관을 활성화하기 전에 저장소 계정을 만듭니다.

진단 로그 이벤트를 Azure [이벤트 허브로](../event-hubs/event-hubs-what-is-event-hubs.md)스트리밍할 수도 있습니다. Event Hubs는 초당 수백 건의 이벤트를 수집하여 모든 실시간 분석 공급자를 통해 변환 및 저장할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [로그 분석](../azure-monitor/log-query/get-started-portal.md) 사용 및 [로그 쿼리](../azure-monitor/log-query/get-started-queries.md)만들기에 대해 자세히 알아봅니다.
* Azure 의 여러 계층에서 사용할 수 있는 플랫폼 로그에 대해 알아보려면 [Azure 플랫폼 로그 개요를](../azure-monitor/platform/platform-logs-overview.md) 참조하세요.

