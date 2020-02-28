---
title: Log Analytics 작업 영역에서 Azure 리소스 로그 수집
description: Azure Monitor에서 Log Analytics 작업 영역으로 Azure 리소스 로그를 스트리밍하는 방법에 대해 알아봅니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659002"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor의 Log Analytics 작업 영역에서 Azure 플랫폼 로그를 수집 합니다.
Azure 활동 로그 및 리소스 로그를 포함 하 여 azure의 [플랫폼 로그](platform-logs-overview.md) 는 azure 리소스 및 해당 리소스가 종속 된 azure 플랫폼에 대 한 자세한 진단 및 감사 정보를 제공 합니다. 이 문서에서는 Log Analytics 작업 영역에서 리소스 로그를 수집 하는 방법에 대해 설명 합니다 .이를 통해 강력한 로그 쿼리를 사용 하 여 Azure Monitor 로그에 수집 된 다른 모니터링 데이터를 분석 하 고 경고와 같은 기타 Azure Monitor 기능을 활용할 수 있습니다 가상화. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>작업 영역에서 플랫폼 로그로 수행할 수 있는 작업
Log Analytics 작업 영역에 플랫폼 로그를 수집 하 여 모든 Azure 리소스의 로그를 함께 분석 하 고 다음을 포함 하는 [Azure Monitor 로그](data-platform-logs.md) 에 사용할 수 있는 모든 기능을 활용할 수 있습니다.

* **로그 쿼리** -강력한 쿼리 언어를 사용 하 여 [로그 쿼리](../log-query/log-query-overview.md) 를 만들어 진단 데이터를 신속 하 게 분석 하 고 통찰력을 얻고 Azure Monitor의 다른 원본에서 수집 된 데이터로 분석할 수 있습니다.
* **경고** - [Azure Monitor의 로그 경고](alerts-log.md)를 사용 하 여 리소스 로그에서 식별 된 중요 한 조건 및 패턴에 대 한 사전 알림을 받습니다.
* **시각화** -로그 쿼리 결과를 Azure 대시보드에 고정 하거나 대화형 보고서의 일부로 통합 문서에 포함 합니다.

## <a name="prerequisites"></a>사전 요구 사항
아직 없는 경우 [새 작업 영역을 만들어야](../learn/quick-create-workspace.md) 합니다. 설정을 구성 하는 사용자에 게 두 구독에 대 한 적절 한 RBAC 액세스 권한이 있는 한,이 작업 영역은 로그를 보내는 리소스와 동일한 구독을가지고 있지 않아도 됩니다.

## <a name="create-a-diagnostic-setting"></a>진단 설정 만들기
Azure 리소스에 대 한 진단 설정을 만들어 Log Analytics 작업 영역 및 기타 대상에 플랫폼 로그를 보냅니다. 자세한 내용은 [Azure에서 로그 및 메트릭을 수집 하는 진단 설정 만들기를](diagnostic-settings.md) 참조 하세요.


## <a name="activity-log-collection"></a>활동 로그 수집
단일 구독에서 최대 5 개의 Log Analytics 작업 영역으로 활동 로그를 보낼 수 있습니다. Log Analytics 작업 영역에서 수집 된 리소스 로그 데이터는 **Azureactivity** 테이블에 저장 됩니다. 

## <a name="resource-log-collection-mode"></a>리소스 로그 수집 모드
Log Analytics 작업 영역에서 수집 된 리소스 로그 데이터는 [Azure Monitor 로그 구조](../log-query/logs-structure.md)에 설명 된 대로 테이블에 저장 됩니다. 리소스 로그에서 사용 하는 테이블은 리소스에서 사용 하는 컬렉션 유형에 따라 달라 집니다.

- Azure 진단-기록 된 모든 데이터는 _Azurediagnostics_ 테이블에 저장 됩니다.
- 리소스 관련 데이터는 리소스의 각 범주에 대 한 개별 테이블에 기록 됩니다.

### <a name="azure-diagnostics-mode"></a>Azure 진단 모드 
이 모드에서는 모든 [진단 설정](diagnostic-settings.md) 의 모든 데이터가 _azurediagnostics_ 테이블에 수집 됩니다. 이는 오늘날 대부분의 Azure 서비스에서 사용 하는 레거시 방법입니다.

여러 리소스 형식이 동일한 테이블에 데이터를 보내기 때문에 해당 스키마는 수집 되는 다른 모든 데이터 형식의 스키마 상위 집합입니다.

진단 설정이 다음 데이터 형식에 대해 동일한 작업 영역에서 수집 되는 다음 예를 살펴보세요.

- 서비스 1의 감사 로그 (A, B, C 열로 구성 된 스키마 포함)  
- 서비스 1의 오류 로그 (열 D, E 및 F 열로 구성 된 스키마 포함)  
- 서비스 2의 감사 로그 (G, H, I 열로 구성 된 스키마 포함)  

AzureDiagnostics 테이블은 다음과 같이 표시 됩니다.  

| ResourceProvider    | Category     | A  | b  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft Service1 | AuditLogs    | x1 | y1 | z1 |    |    |    |    |    |    |
| Microsoft Service1 | ErrorLogs    |    |    |    | q1 | w1 | e1 |    |    |    |
| Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>리소스 관련
이 모드에서는 진단 설정에서 선택한 각 범주에 대해 선택한 작업 영역의 개별 테이블이 생성 됩니다. 이 방법은 로그 쿼리에서 데이터 작업을 훨씬 쉽게 수행할 수 있도록 하 고, 스키마 및 해당 구조를 보다 효율적으로 검색 하 고, 수집 대기 시간과 쿼리 시간 모두에서 성능을 향상 시키고,에 대 한 RBAC 권한을 부여 하는 기능을 제공 하기 때문에 권장 됩니다. 특정 테이블. 모든 Azure 서비스는 궁극적으로 리소스 특정 모드로 마이그레이션됩니다. 

위의 예에서는 세 개의 테이블을 만듭니다.
 
- *Service1AuditLogs* 테이블은 다음과 같습니다.

    | 리소스 공급자 | Category | A | b | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- *Service1ErrorLogs* 테이블은 다음과 같습니다.  

    | 리소스 공급자 | Category | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | w1 | e1 |
    | Service1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- *Service2AuditLogs* 테이블은 다음과 같습니다.  

    | 리소스 공급자 | Category | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | k1 | l1|
    | Service2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>컬렉션 모드를 선택 합니다.
대부분의 Azure 리소스는 사용자를 제공 하지 않고 **Azure 진단** 또는 **리소스 특정 모드** 에서 작업 영역에 데이터를 기록 합니다. 사용 모드에 대 한 자세한 내용은 [각 서비스에 대 한 설명서](diagnostic-logs-schema.md) 를 참조 하세요. 모든 Azure 서비스는 궁극적으로 리소스 특정 모드를 사용 합니다. 이러한 전환의 일부로 일부 리소스는 진단 설정에서 모드를 선택할 수 있습니다. 모든 새 진단 설정에 대해 리소스 특정 모드를 지정 해야 합니다. 이렇게 하면 데이터를 더 쉽게 관리할 수 있으므로 나중에 복잡 한 마이그레이션을 방지할 수 있습니다.
  
   ![진단 설정 모드 선택기](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> 현재는 Azure Portal에서 진단 설정을 구성 하는 경우에만 **Azure 진단** 및 **리소스 특정** 모드를 선택할 수 있습니다. CLI, PowerShell 또는 Rest API를 사용 하 여 설정을 구성 하는 경우 **Azure 진단**이 기본값으로 사용 됩니다.

기존 진단 설정을 리소스 특정 모드로 수정할 수 있습니다. 이 경우 이미 수집 된 데이터는 작업 영역의 보존 설정에 따라 제거 될 때까지 _Azurediagnostics_ 테이블에 남아 있습니다. 새 데이터는에서 전용 테이블로 수집 됩니다. [Union](https://docs.microsoft.com/azure/kusto/query/unionoperator) 연산자를 사용 하 여 두 테이블에서 데이터를 쿼리 합니다.

리소스 특정 모드를 지 원하는 Azure 서비스에 대 한 공지를 보려면 [Azure 업데이트](https://azure.microsoft.com/updates/) 블로그를 계속 시청 하세요.

### <a name="column-limit-in-azurediagnostics"></a>AzureDiagnostics의 열 제한
Azure Monitor 로그의 모든 테이블에 대 한 500 속성 제한이 있습니다. 이 한도에 도달 하면 첫 번째 500 외부의 속성을 포함 하는 데이터를 포함 하는 모든 행이 수집 시 삭제 됩니다. *Azurediagnostics* 테이블은 모든 Azure 서비스에 대 한 속성을 포함 하므로 특히이 제한에 취약 합니다.

여러 서비스에서 리소스 로그를 수집 하는 경우 _Azurediagnostics_ 는이 제한을 초과할 수 있으며 데이터가 누락 됩니다. 모든 Azure 서비스에서 리소스 특정 모드를 지원할 때까지 여러 작업 영역에 쓰도록 리소스를 구성 하 여 500 열 제한에 도달할 가능성을 줄여야 합니다.

### <a name="azure-data-factory"></a>Azure 데이터 팩터리
Azure Data Factory은 매우 자세한 로그 집합으로 인해 많은 열을 작성 하는 것으로 알려진 서비스 이며, 잠재적으로 _Azurediagnostics_ 가 제한을 초과 하 게 됩니다. 리소스 특정 모드를 사용 하기 전에 구성 된 모든 진단 설정의 경우 모든 작업에 대해 고유 하 게 명명 된 사용자 매개 변수에 대해 새로 만들어진 새 열이 있습니다. 활동 입력 및 출력의 자세한 특성으로 인해 더 많은 열이 생성 됩니다.
 
가능한 한 빨리 리소스 특정 모드를 사용 하려면 로그를 마이그레이션해야 합니다. 즉시 수행할 수 없는 경우에는 작업 영역에서 수집 되는 다른 로그 유형에 영향을 주는 로그를 최소화 하기 위해 Azure Data Factory 로그를 자체 작업 영역으로 분리 하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

* [자세한 내용은 리소스 로그를 참조](platform-logs-overview.md)하세요.
* [Azure에서 로그 및 메트릭을 수집 하는 진단 설정을 만듭니다](diagnostic-settings.md).
