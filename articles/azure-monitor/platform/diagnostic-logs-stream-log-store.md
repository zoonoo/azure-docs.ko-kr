---
title: Log Analytics로 Azure 진단 로그 스트리밍
description: Azure 진단 로그를 Log Analytics 작업 영역으로 스트리밍하는 방법을 알아봅니다.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: bd760fca20a602127e7d33913547dcb2c6bc95f6
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351564"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Log Analytics로 Azure 진단 로그 스트리밍

**[Azure 진단 로그](diagnostic-logs-overview.md)** 는 Portal, PowerShell cmdlet 또는 Azure CLI를 사용하여 거의 실시간으로 Azure Log Analytics로 스트리밍할 수 있습니다.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Log Analytics에서 진단 로그로 수행할 수 있는 작업

Azure Log Analytics는 Azure 리소스에서 생성된 원시 로그 데이터를 깊이 있게 이해할 수 있도록 도와주는 유연한 로그 검색 및 분석 도구입니다. 일부 기능은 다음과 같습니다.

* **로그 검색** - 로그 데이터에 대해 고급 쿼리를 작성하고, 다양한 소스의 로그 간 상관 관계를 파악하고, Azure 대시보드에 고정될 수 있는 차트를 생성합니다.
* **경고** - 하나 이상의 이벤트가 특정 쿼리와 일치할 경우를 감지하고 전자 메일 또는 웹후크 호출을 통해 알림을 받습니다.
* **솔루션** - 미리 작성된 뷰와 대시보드를 사용하여 로그 데이터를 바로 이해할 수 있습니다.
* **고급 분석** - Machine Learning 및 패턴 일치 알고리즘을 적용하여 로그를 통해 확인된 가능한 문제점을 식별합니다.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Log Analytics로 진단 로그 스트리밍

프로그래밍 방식으로 포털을 통하거나 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)를 사용하여 진단 로그의 스트리밍을 사용하도록 설정할 수 있습니다. 어느 경우든, Log Analytics 작업 영역을 지정하는 진단 설정과 해당 작업 영역으로 전송하려는 로그 범주 및 메트릭을 만듭니다. 진단 **로그 범주**는 리소스가 제공할 수 있는 로그 유형입니다.

설정을 구성하는 사용자가 두 구독에 대한 적절한 RBAC 액세스를 가진 경우 Log Analytics 작업 영역은 로그를 내보내는 리소스와 동일한 구독을 가지고 있지 않아도 됩니다.

> [!NOTE]
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>포털을 사용하여 진단 로그 스트림
1. 포털에서 Azure Monitor로 이동하고 **진단 설정**을 클릭합니다.

    ![Azure Monitor의 모니터링 섹션](media/diagnostic-logs-stream-log-store/diagnostic-settings-blade.png)

2. 필요에 따라 리소스 그룹 또는 리소스 종류를 기준으로 목록을 필터링합니다. 그런 다음 진단 설정을 지정하려는 리소스를 클릭합니다.

3. 선택한 리소스에 설정이 없는 경우, 설정을 만들라는 메시지가 표시됩니다. “진단 켜기”를 클릭합니다.

   ![진단 설정 추가 - 기존 설정 없음](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   리소스에 기존 설정이 있는 경우 이 리소스에 이미 구성된 설정의 목록이 표시됩니다. “진단 설정 추가”를 클릭합니다.

   ![진단 설정 추가 - 기존 설정](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. 설정에 이름을 지정하고 **Log Analytics에 보내기** 확인란을 선택한 후 Log Analytics 작업 영역을 선택합니다.

   ![진단 설정 추가 - 기존 설정](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. **저장**을 클릭합니다.

몇 분 후 새 설정이 이 리소스에 대한 설정 목록에 표시되고, 새 이벤트 데이터가 생성되는 즉시 진단 로그가 해당 작업 영역에 스트리밍됩니다. 이벤트를 내보낸 시간과 Log Analytics에 이벤트가 나타난 시간 사이에는 최대 15분의 차이가 있을 수 있습니다.

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

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Log Analytics에서 데이터를 쿼리하려면 어떻게 하나요?

Portal의 로그 검색 블레이드 또는 Log Analytics에 속하는 고급 분석에서 AzureDiagnostics 테이블 아래에 제공되는 로그 관리 솔루션을 통해 진단 로그를 쿼리할 수 있습니다. 또한 Log Analytics로 전송하는 로그 데이터를 즉시 이해하는 데 도움이 되는 [Azure 리소스의 몇 가지 솔루션](../../azure-monitor/insights/solutions.md)을 설치할 수 있습니다.

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>알려진 제한: AzureDiagnostics 열 제한
다양 한 리소스 데이터 형식 모두 같은 테이블에 전송 됩니다 보내기 때문에 (_AzureDiagnostics_),이 테이블의 스키마는 수집 되 고 모든 다른 데이터 형식의 스키마의 상위 집합입니다. 예를 들어, 다음 데이터 형식 컬렉션에 대 한 진단 설정을 만든 경우 동일한 작업 영역에 전송 되 고 모든:
- 감사 로그 (A, B 및 C 열으로 구성 된 스키마가) 리소스 1  
- (D, E 및 F 열으로 구성 된 스키마가) 리소스 2의 오류 로그  
- 리소스 3 (G 열, H, 및 I로 구성 된 스키마가) 데이터 흐름 로그  
 
AzureDiagnostics 테이블은 몇 가지 샘플 데이터를 사용 하 여 다음과 같이 표시 됩니다.  
 
| ResourceProvider | Category | A | b | C | D | E | F | G | H | I |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
| Microsoft.Resource2 | ErrorLogs | | | | q1 | w1 | e1 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
| Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
| Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
| ... |
 
500 개 이상의 열을가지고 있지 않은 경우 지정된 된 Azure 로그 테이블의 명시적 제한은.입니다. 에 도달 하면 수집 시 처음 500 개 외부 열을 사용 하 여 데이터를 포함 하는 행이 삭제 됩니다. AzureDiagnostics 테이블 되도록이 제한의 영향을 특히 취약 되었습니다. 일반적으로 동일한 작업 영역으로 전송 되는 다양 한 데이터 원본 동일한 작업 영역으로 전송 되기 때문에 두 개 이상의 아주 자세한 데이터 원본에 발생 합니다. 
 
#### <a name="azure-data-factory"></a>Azure 데이터 팩터리  
매우 자세한 로그를 집합으로 인해 azure Data Factory는이 제한에 의해 영향을 받지 특히 알고 있는 리소스입니다. 특히 다음과 같습니다.  
- *파이프라인의 모든 활동에 대해 정의 된 사용자 매개 변수에*: 모든 작업에 대 한 모든 사용자 고유 하 게 명명 된 매개 변수에 대해 만든 새 열이 됩니다. 
- *작업 입력 및 출력*: 이러한 작업-작업을 변경 하 고 많은 양의 자세한 특성으로 인해 열을 생성 합니다. 
 
으로 아래 광범위 한 해결 방법 제안 된 것이 좋습니다 이러한 로그를 작업 영역에 수집 되 고 다른 로그 형식에 영향을 줄 가능성을 최소화 하기 위해 자신의 작업 영역으로 ADF 로그 격리 합니다. 4 월 중순 2019에서 사용할 수 있는 Azure Data Factory에 대 한 로그 조정 될 예정입니다.
 
#### <a name="workarounds"></a>해결 방법
짧은 기간 동안 500 개 열만 다시 정의 될 때까지, 것이 좋습니다 자세한 데이터 형식 제한에 도달의 가능성을 줄이기 위해 별도 작업 영역으로 구분 합니다.
 
장기적인 Azure 진단 이동 하는 통합 된, 스파스 스키마에서 각 데이터 형식으로 당 개별 테이블로 동적 형식에 대 한 지원을 사용 하 여 쌍을 이루는 이렇게 Azure 진단 메커니즘을 통해 Azure 로그에 들어오는 데이터의 사용 편의성을 크게 향상 됩니다. 이미이 확인할 수 있습니다 선택 Azure 리소스 종류에 대 한 예를 들어 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) 하거나 [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) 로그 합니다. 큐 레이 팅 된 이러한 로그에 지원 되는 Azure에서 새 리소스 종류에 대 한 뉴스를 찾아보십시오 합니다 [Azure 업데이트](https://azure.microsoft.com/updates/) 블로그!


## <a name="next-steps"></a>다음 단계

* [Azure 진단 로그에 대해 자세히 알아보기](diagnostic-logs-overview.md)

