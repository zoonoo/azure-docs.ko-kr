---
title: Azure Monitor에서 Log Analytics 작업 영역에 Stream Azure 진단 로그
description: Azure Monitor에서 Log Analytics 작업 영역에 Azure 진단 로그를 스트림 하는 방법에 알아봅니다.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: e8e6276a38f06b5c6ebb24c89f3733b9fd7220f7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612837"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor에서 Log Analytics 작업 영역에 Stream Azure 진단 로그

**[Azure 진단 로그](diagnostic-logs-overview.md)**  포털, PowerShell cmdlet 또는 Azure CLI를 사용 하 여 Azure Monitor에서 Log Analytics 작업 영역에 거의 실시간으로에서 스트리밍할 수 있습니다.

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>Log Analytics 작업 영역에서 로그 진단을 사용 하 여 수행할 수 있는 작업

Azure Monitor는 Azure 리소스에서 생성 된 원시 로그 데이터에 대 한 정보를 얻을 수 있는 유연한 로그 쿼리 및 분석 도구를 제공 합니다. 일부 기능은 다음과 같습니다.

* **로그 쿼리** -쓰기 로그 데이터를 다양 한 상관 관계를 지정 로그 원본과 생성 over 차트는 고급 쿼리를 Azure 대시보드에 고정할 수 있습니다.
* **경고** -하나 이상의 이벤트가 특정 쿼리와 일치할 및 Azure Monitor 경고를 사용 하 여 전자 메일 또는 웹 후크 호출을 사용 하 여 알림을 받을 시기를 감지 합니다.
* **고급 분석** - 기계 학습 및 패턴 일치 알고리즘을 적용하여 로그를 통해 확인된 가능한 문제점을 식별합니다.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>Log Analytics 작업 영역에 진단 로그의 스트리밍을 사용 하도록 설정

프로그래밍 방식으로 포털을 통하거나 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)를 사용하여 진단 로그의 스트리밍을 사용하도록 설정할 수 있습니다. 어느 경우든, Log Analytics 작업 영역을 지정하는 진단 설정과 해당 작업 영역으로 전송하려는 로그 범주 및 메트릭을 만듭니다. 진단 **로그 범주**는 리소스가 제공할 수 있는 로그 유형입니다.

설정을 구성하는 사용자가 두 구독에 대한 적절한 RBAC 액세스를 가진 경우 Log Analytics 작업 영역은 로그를 내보내는 리소스와 동일한 구독을 가지고 있지 않아도 됩니다.

> [!NOTE]
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>포털을 사용하여 진단 로그 스트림
1. 포털에서 Azure Monitor로 이동 하 고 클릭 **진단 설정** 에 **설정** 메뉴.


2. 필요에 따라 리소스 그룹 또는 리소스 종류를 기준으로 목록을 필터링합니다. 그런 다음 진단 설정을 지정하려는 리소스를 클릭합니다.

3. 선택한 리소스에 설정이 없는 경우, 설정을 만들라는 메시지가 표시됩니다. “진단 켜기”를 클릭합니다.

   ![진단 설정 추가 - 기존 설정 없음](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   리소스에 기존 설정이 있는 경우 이 리소스에 이미 구성된 설정의 목록이 표시됩니다. “진단 설정 추가”를 클릭합니다.

   ![진단 설정 추가 - 기존 설정](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. 설정에 이름을 지정하고 **Log Analytics에 보내기** 확인란을 선택한 후 Log Analytics 작업 영역을 선택합니다.

   ![진단 설정 추가 - 기존 설정](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. **Save**을 클릭합니다.

몇 분 후 새 설정이 이 리소스에 대한 설정 목록에 표시되고, 새 이벤트 데이터가 생성되는 즉시 진단 로그가 해당 작업 영역에 스트리밍됩니다. 이벤트는 내보내집니다 하는 경우 Log Analytics에서 표시 되 면 사이의 최대 15 분 있을 수 있습니다.

### <a name="via-powershell-cmdlets"></a>PowerShell Cmdlet을 통해

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Azure PowerShell Cmdlet](../../azure-monitor/platform/powershell-quickstart-samples.md)을 통해 스트리밍을 사용하도록 설정하려면 다음 매개 변수와 함께 `Set-AzDiagnosticSetting` cmdlet을 사용하면 됩니다.

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

workspaceID 속성은 Log Analytics에 표시되는 작업 영역 ID/키가 아니라 작업 영역의 전체 Azure 리소스 ID를 사용합니다.

### <a name="via-azure-cli"></a>Azure CLI를 통해

[Azure CLI](../../azure-monitor/platform/cli-samples.md)를 통해 스트리밍을 사용하도록 설정하려면 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) 명령을 사용합니다.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

`--logs` 매개 변수로 전달된 JSON 배열에 사전을 추가하여 진단 로그에 추가적인 범주를 추가할 수 있습니다.

`--resource-group` 인수는 `--workspace`가 개체 ID가 아닌 경우에만 필요합니다.

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>Log Analytics 작업 영역에서 데이터를 쿼리하려면 어떻게 해야 하나요?

Azure Monitor 포털에서 로그 블레이드에서 AzureDiagnostics 테이블 아래에 로그 관리 솔루션의 일부로 진단 로그를 쿼리할 수 있습니다. 이 밖에도 [Azure 리소스에 대 한 몇 가지 모니터링 솔루션](../../azure-monitor/insights/solutions.md) 로그 데이터를 Azure Monitor로 전송 하는 즉각적인 통찰 얻기를 설치할 수 있습니다.

### <a name="examples"></a>예

```Kusto
// Resources that collect diagnostic logs into this Log Analytics workspace, using Diagnostic Settings
AzureDiagnostics
| distinct _ResourceId
```
```Kusto
// Resource providers collecting diagnostic logs into this Log Analytics worksapce, with log volume per category
AzureDiagnostics
| summarize count() by ResourceProvider, Category
```
```Kusto
// Resource types collecting diagnostic logs into this Log Analytics workspace, with number of resources onboarded
AzureDiagnostics
| summarize ResourcesOnboarded=dcount(_ResourceId) by ResourceType
```
```Kusto
// Operations logged by specific resource provider, in this example - KeyVault
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.KEYVAULT"
| distinct OperationName
```

## <a name="azure-diagnostics-vs-resource-specific"></a>Azure 진단 및 리소스별  
Log Analytics 대상 Azure 진단 구성에서 활성화 되 면 두 가지 고유한 데이터 작업 영역에 표시 되는:  
- **Azure 진단** -대부분의 Azure 서비스에서 현재 사용 되는 일반적인 방법입니다. 이 모드에서는 모든 진단 설정의 데이터를 지정된 된 작업 영역을 가리키는 모든 결국에 _AzureDiagnostics_ 테이블입니다. 
<br><br>다양 한 리소스는 같은 테이블에 데이터를 보내기 때문에 (_AzureDiagnostics_),이 테이블의 스키마는 수집 되 고 모든 다른 데이터 형식의 스키마의 상위 집합입니다. 예를 들어, 다음 데이터 형식 컬렉션에 대 한 진단 설정을 만든 경우 동일한 작업 영역에 전송 되 고 모든:
    - 감사 로그 (A, B 및 C 열으로 구성 된 스키마가) 리소스 1  
    - (D, E 및 F 열으로 구성 된 스키마가) 리소스 2의 오류 로그  
    - 리소스 3 (G 열, H, 및 I로 구성 된 스키마가) 데이터 흐름 로그  

    AzureDiagnostics 테이블은 몇 가지 샘플 데이터를 사용 하 여 다음과 같이 표시 됩니다.  

    | ResourceProvider | 범주 | 변수를 잠그기 위한 | B | C | D | E | F | G | H | I |
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
    | Microsoft.Resource2 | ErrorLogs | | | | q1 | w1 | e1 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
    | Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
    | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- **리소스별** -이 모드에서는 선택한 작업 영역에서 개별 테이블 진단 설정 구성에서 선택한 각 범주 마다 생성 됩니다. 이 최신 메서드를 사용 하면 정확 하 게 찾으려는 명시적 중요 한 부분의 분리를 통해 찾기가 훨씬 쉬워집니다: 모든 범주에 대 한 테이블입니다. 또한 동적 형식에 대 한 지원에서 혜택을 제공합니다. 확인할 수 있습니다이 모드는 Azure 리소스 종류 선택 예를 들어 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) 하거나 [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) 로그 합니다. 궁극적으로 모든 데이터 형식 리소스별 모드로 마이그레이션할 예정입니다. 

    위의 예제에서 만들어지는 세 테이블에 따라서: 
    - 테이블 _AuditLogs_ 다음과 같습니다.

        | ResourceProvider | 범주 | 변수를 잠그기 위한 | B | C |
        | -- | -- | -- | -- | -- |
        | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
        | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
        | ... |

    - 테이블 _ErrorLogs_ 다음과 같습니다.  

        | ResourceProvider | 범주 | D | E | F |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource2 | ErrorLogs | q1 | w1 | e1 |
        | Microsoft.Resource2 | ErrorLogs | q2 | w2 | e2 |
        | ... |

    - 테이블 _DataFlowLogs_ 다음과 같습니다.  

        | ResourceProvider | 범주 | G | H | I |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource3 | DataFlowLogs | j1 | k1 | l1|
        | Microsoft.Resource3 | DataFlowLogs | j3 | k3 | l3|
        | ... |

    리소스 전용 모드를 사용 하는 기타 이점은 수집 대기 시간 및 쿼리 시간, 스키마 및 해당 구조, 특정 테이블에 대 한 RBAC 권한을 부여 하는 기능, 더 나은 검색 기능에서 성능 향상된을 포함 합니다.

### <a name="selecting-azure-diagnostic-vs-resource-specific-mode"></a>Azure 진단 및 리소스 특정 모드를 선택합니다.
대부분의 Azure 리소스에 대 한 있습니다 선택 된 Azure 진단 또는 리소스별 모드를 사용할지 여부를 데이터는 리소스가 사용 하도록 선택 하는 메서드를 통해 자동으로 흐릅니다. 모드 채택 하 고 세부 정보에 대 한 Log Analytics에 데이터를 보내도록 설정한 리소스에서 제공 되는 설명서를 참조 하세요. 

이전 섹션에서 설명 했 듯이 궁극적으로의 목표는 Azure Monitor 리소스 특정 모드를 사용 하 여 Azure에서 모든 서비스 있어야 합니다. 이 전환을 용이 하 게 데이터 손실 후 Log Analytics에 온 보 딩 하면 다양 한 모드를 사용 하 여 하는 경우 일부 Azure 서비스의 일부로 인지 확인 하려면:  
   ![진단 설정 모드 선택기](media/diagnostic-logs-stream-log-store/diagnostic-settings-mode-selector.png)

우리가 **강력한** 는 조만간 잠재적으로 어려운 마이그레이션을 방지 하려면 새로 만든 진단 설정을 사용 하 여 리소스 중심 모드는 것이 좋습니다.  

특정 Azure 리소스에 의해 활성화 되 면 기존 진단 설정을 소급 해 서 Azure 진단에서 모드로 전환 하려면 리소스별 할 수 있습니다. 이전에 수집 된 데이터에서 사용할 수 있도록 계속 합니다 _AzureDiagnostics_ 작업 영역에서 보존 설정에 구성 된 대로 에이징 하 하지만 전용된 테이블에 모든 새 데이터를 보낼 때까지 테이블입니다. 이 즉,에 대 한 쿼리는 이전 데이터에 걸쳐 있는 및 새 (될 때까지 이전 데이터를 완전히 노후), [union](https://docs.microsoft.com/azure/kusto/query/unionoperator) 쿼리에서 연산자가 두 데이터 집합을 결합 해야 합니다.

새 Azure에 대 한 뉴스에서 리소스 관련 모드에서 지원 로그 서비스에 대 한 살펴보십시오 합니다 [Azure 업데이트](https://azure.microsoft.com/updates/) 블로그!

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>알려진 제한: AzureDiagnostics 열 제한
500 개 이상의 열을가지고 있지 않은 경우 지정된 된 Azure 로그 테이블의 명시적 제한은.입니다. 에 도달 하면 수집 시 처음 500 개 외부 열을 사용 하 여 데이터를 포함 하는 행이 삭제 됩니다. AzureDiagnostics 테이블 되도록이 제한의 영향을 특히 취약 되었습니다. 일반적으로 동일한 작업 영역으로 전송 되는 다양 한 데이터 원본 동일한 작업 영역으로 전송 되기 때문에 두 개 이상의 자세한 데이터 원본에 발생 합니다. 

#### <a name="azure-data-factory"></a>Azure 데이터 팩터리  
Azure Data Factory는 매우 자세한 로그를 집합으로 인해이 제한에 의해 영향을 받지 특히 알고 있는 리소스입니다. 특히는 특정 리소스에 적용 하기 전에 구성 된 모든 진단 설정 모드는 사용 하도록 설정 하거나 명시적으로 역방향 호환성을 위해 리소스 특정 모드를 사용 하도록 선택 했습니다.  
- *파이프라인의 모든 활동에 대해 정의 된 사용자 매개 변수에*: 모든 작업에 대 한 모든 사용자 고유 하 게 명명 된 매개 변수에 대해 만든 새 열이 됩니다. 
- *작업 입력 및 출력*: 작업-이러한 다르며의 자세한 특성으로 인해 많은 수의 열을 생성 합니다. 
 
으로 아래 광범위 한 해결 방법 제안 된 것이 좋습니다 가능한 한 빨리 리소스별 모드를 사용 하 여 로그를 마이그레이션. 바로 이렇게 할 수 없는 경우 중간 또는 작업 영역에 수집 되 고 다른 로그 형식에 영향을 주는 이러한 로그의 가능성을 최소화 하기 위해 자신의 작업 영역으로 ADF 로그를 격리 하는 것입니다. 
 
#### <a name="workarounds"></a>해결 방법
단기에 모든 Azure 서비스 리소스 특정 모드에서는 모든 서비스에 대 한 사용은 아직 리소스 특정 모드를 지 원하는 것이 좋습니다 줄이기 위해 자세한 데이터 형식을 서로 다른 작업 영역에 이러한 서비스에 게시 분리 될 때까지 합니다 한도 도달 될 가능성이 있습니다.  
 
장기적인 Azure 진단 리소스 특정 모드를 지 원하는 모든 Azure 서비스 쪽으로 이동 됩니다. 이 모드의이 500 열 제한의 영향을 방지 하려면 가능한 한 빨리 이동을 좋습니다.  


## <a name="next-steps"></a>다음 단계

* [Azure 진단 로그에 대해 자세히 알아보기](diagnostic-logs-overview.md)

