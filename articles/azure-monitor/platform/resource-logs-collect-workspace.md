---
title: Log Analytics 작업 영역에서 Azure 리소스 로그 수집
description: Azure 리소스 로그를 Azure 모니터의 로그 분석 작업 영역으로 스트리밍하는 방법을 알아봅니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248594"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure 모니터의 로그 분석 작업 영역에서 Azure 플랫폼 로그 수집
Azure 활동 로그 및 리소스 로그를 포함한 [Azure의 플랫폼 로그는](platform-logs-overview.md) Azure 리소스 및 해당 플랫폼에 대한 자세한 진단 및 감사 정보를 제공합니다. 이 문서에서는 강력한 로그 쿼리를 사용하여 Azure Monitor Log에서 수집된 다른 모니터링 데이터로 분석하고 경고 및 및 과 같은 다른 Azure Monitor 기능을 활용할 수 있는 Log Analytics 작업 영역에서 리소스 로그 수집에 대해 설명합니다. 시각화. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>작업 영역에서 플랫폼 로그로 수행할 수 있는 작업
Log Analytics 작업 영역에 플랫폼 로그를 수집하면 모든 Azure 리소스의 로그를 함께 분석하고 다음을 포함하는 [Azure Monitor Log에서](data-platform-logs.md) 사용할 수 있는 모든 기능을 활용할 수 있습니다.

* **로그 쿼리** - 강력한 쿼리 언어를 사용하여 [로그 쿼리를](../log-query/log-query-overview.md) 만들어 진단 데이터를 신속하게 분석하고 통찰력을 얻고 Azure Monitor의 다른 소스에서 수집한 데이터로 분석합니다.
* **경고** - [Azure Monitor의 로그 경고를](alerts-log.md)사용하여 리소스 로그에서 식별된 중요한 조건 및 패턴에 대한 사전 알림을 받습니다.
* **시각화** - 로그 쿼리 결과를 Azure 대시보드에 고정하거나 대화형 보고서의 일부로 통합 문서에 포함합니다.

## <a name="prerequisites"></a>사전 요구 사항
아직 없는 경우 [새 작업 영역을 만들어야](../learn/quick-create-workspace.md) 합니다. 설정을 구성하는 사용자가 두 구독에 대한 적절한 RBAC 액세스 권한을 가지고 있는 한 작업 영역이 리소스 전송 로그와 동일한 구독에 있을 필요는 없습니다.

## <a name="create-a-diagnostic-setting"></a>진단 설정 만들기
Azure 리소스에 대한 진단 설정을 만들어 로그 분석 작업 영역 및 기타 대상으로 플랫폼 로그를 보냅니다. 자세한 내용은 [Azure에서 로그 및 메트릭을 수집하려면 진단 설정 만들기를](diagnostic-settings.md) 참조하십시오.


## <a name="activity-log-collection"></a>활동 로그 수집
단일 구독에서 최대 5개의 Log Analytics 작업 영역으로 활동 로그를 보낼 수 있습니다. Log Analytics 작업 영역에서 수집된 리소스 로그 데이터는 **AzureActivity** 테이블에 저장됩니다. 

## <a name="resource-log-collection-mode"></a>리소스 로그 수집 모드
Log Analytics 작업 영역에서 수집된 리소스 로그 데이터는 [Azure Monitor Log의 구조에](../log-query/logs-structure.md)설명된 대로 테이블에 저장됩니다. 리소스 로그에서 사용되는 테이블은 리소스가 사용하는 컬렉션 유형에 따라 달라집니다.

- Azure 진단 - 작성된 모든 데이터는 _AzureDiagnostics_ 테이블에 있습니다.
- 리소스별 - 리소스의 각 범주에 대한 개별 테이블에 데이터가 기록됩니다.

### <a name="azure-diagnostics-mode"></a>Azure 진단 모드 
이 모드에서는 [모든 진단 설정의](diagnostic-settings.md) 모든 데이터가 _AzureDiagnostics_ 테이블에 수집됩니다. 이 방법은 오늘날 대부분의 Azure 서비스에서 사용하는 레거시 메서드입니다.

여러 리소스 형식이 동일한 테이블로 데이터를 보내므로 해당 스키마는 수집되는 모든 다른 데이터 형식의 스키마의 상위 집합입니다.

다음 데이터 형식에 대해 동일한 작업 영역에서 진단 설정이 수집되는 다음 예제를 고려합니다.

- 서비스 1의 감사 로그(A, B 및 C 열로 구성된 스키마)  
- 서비스 1의 오류 로그(D, E 및 F 열로 구성된 스키마)  
- 서비스 2의 감사 로그(G, H 및 I 열로 구성된 스키마)  

AzureDiagnostics 테이블은 다음과 같습니다.  

| ResourceProvider    | Category     | A  | b  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| 마이크로소프트.서비스1 | AuditLogs    | x1 | y1 | Z1 |    |    |    |    |    |    |
| 마이크로소프트.서비스1 | 오류 로그    |    |    |    | q1 | w1 | e1 |    |    |    |
| 마이크로소프트.서비스2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| 마이크로소프트.서비스1 | 오류 로그    |    |    |    | q2 | w2 | e2 |    |    |    |
| 마이크로소프트.서비스2 | AuditLogs    |    |    |    |    |    |    | J3 | k3 | l3 |
| 마이크로소프트.서비스1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>리소스별
이 모드에서는 진단 설정에서 선택한 각 범주에 대해 선택한 작업 영역의 개별 테이블이 만들어집니다. 이 메서드는 로그 쿼리의 데이터 로큰롤 작업을 훨씬 쉽게 하고, 스키마와 그 구조를 더 잘 검색할 수 있으며, 수집 대기 시간 및 쿼리 시간 모두에서 성능을 향상시키고, RBAC 권한을 부여하는 기능을 훨씬 쉽게 할 수 있으므로 권장됩니다. 특정 테이블. 모든 Azure 서비스는 결국 리소스 별 모드로 마이그레이션됩니다. 

위의 예제에서는 세 개의 테이블이 만들어집니다.
 
- 표 *서비스1감사로그:*

    | 리소스 공급자 | Category | A | b | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | y1 | Z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- 표 *서비스1다음과* 같이 오류가 표시됩니다.  

    | 리소스 공급자 | Category | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | 오류 로그 |  q1 | w1 | e1 |
    | Service1 | 오류 로그 |  q2 | w2 | e2 |
    | ... |

- 표 *서비스2감사로그:*  

    | 리소스 공급자 | Category | G | H | I |
    | -- | -- | -- | -- | -- |
    | 서비스 2 | AuditLogs | j1 | k1 | l1|
    | 서비스 2 | AuditLogs | J3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>컬렉션 모드 선택
대부분의 Azure 리소스는 선택 사항 없이 **Azure 진단** 또는 리소스 별 **모드에서** 작업 영역에 데이터를 씁니다. 각 [서비스에 대한 설명서를](diagnostic-logs-schema.md) 참조하여 사용하는 모드에 대한 자세한 내용을 확인하십시오. 모든 Azure 서비스는 결국 리소스 관련 모드를 사용합니다. 이 전환의 일부로 일부 리소스를 사용하면 진단 설정에서 모드를 선택할 수 있습니다. 새 진단 설정에 대해 리소스별 모드를 지정해야 하므로 데이터를 더 쉽게 관리할 수 있으며 나중에 복잡한 마이그레이션을 방지하는 데 도움이 될 수 있습니다.
  
   ![진단 설정 모드 선택기](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> 현재 **Azure 진단** 및 **리소스 별** 모드는 Azure 포털에서 진단 설정을 구성할 때만 선택할 수 있습니다. CLI, PowerShell 또는 나머지 API를 사용하여 설정을 구성하는 경우 기본적으로 **Azure 진단.**

기존 진단 설정을 리소스별 모드로 수정할 수 있습니다. 이 경우 이미 수집된 데이터는 작업 영역에 대한 보존 설정에 따라 제거될 때까지 _AzureDiagnostics_ 테이블에 남아 있습니다. 새 데이터가 전용 테이블에 수집됩니다. 공용 [구조자](https://docs.microsoft.com/azure/kusto/query/unionoperator) 연산자를 사용하여 두 테이블에서 데이터를 쿼리합니다.

[리소스별](https://azure.microsoft.com/updates/) 모드를 지원하는 Azure 서비스에 대한 공지사항에 대한 Azure 업데이트 블로그를 계속 시청합니다.

### <a name="column-limit-in-azurediagnostics"></a>Azure진단의 열 제한
Azure 모니터 로그의 모든 테이블에 대해 500개의 속성 제한이 있습니다. 이 제한에 도달하면 처음 500 이외의 속성이 있는 데이터가 포함된 모든 행은 수집 시간에 삭제됩니다. *AzureDiagnostics* 테이블은 모든 Azure 서비스에 쓰기에 대한 속성을 포함하므로 이 제한에 특히 취약합니다.

여러 서비스에서 리소스 로그를 수집하는 경우 _AzureDiagnostics가_ 이 제한을 초과할 수 있으며 데이터가 누락될 수 있습니다. 모든 Azure 서비스가 리소스별 모드를 지원할 때까지 리소스를 여러 작업 영역에 쓸 수 있도록 구성하여 500개 열 제한에 도달할 가능성을 줄여야 합니다.

### <a name="azure-data-factory"></a>Azure 데이터 팩터리
Azure Data Factory는 매우 상세한 로그 집합으로 인해 많은 수의 열을 작성하고 _AzureDiagnostics가_ 제한을 초과할 수 있는 것으로 알려진 서비스입니다. 리소스 별 모드를 사용하도록 설정하기 전에 구성된 모든 진단 설정의 경우 모든 활동에 대해 고유하게 명명된 모든 사용자 매개 변수에 대해 새 열이 만들어집니다. 활동 입력 및 출력의 자세한 특성으로 인해 더 많은 열이 만들어집니다.
 
가능한 한 빨리 리소스 별 모드를 사용하려면 로그를 마이그레이션해야 합니다. 즉시 수행할 수 없는 경우 임시 대안은 Azure Data Factory 로그를 자체 작업 영역에 격리하여 이러한 로그가 작업 영역에서 수집되는 다른 로그 유형에 영향을 미칠 가능성을 최소화하는 것입니다.


## <a name="next-steps"></a>다음 단계

* [리소스 로그에 대해 자세히 알아보기.](platform-logs-overview.md)
* [진단 설정을 만들어 Azure 에서 로그 및 메트릭을 수집합니다.](diagnostic-settings.md)
