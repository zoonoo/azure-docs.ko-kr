---
title: Azure Monitor에서 Azure 활동 로그 이벤트 보기
description: Azure Monitor에서 Azure 활동 로그를 보고 PowerShell, CLI 및 REST API를 사용 하 여 검색 합니다.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 54a1d3e503ddb8b11109596decde94a2834dbf47
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529969"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Azure 활동 로그 이벤트 보기 및 검색

[Azure 활동 로그](activity-logs-overview.md) 는 azure에서 발생 한 구독 수준 이벤트에 대 한 통찰력을 제공 합니다. 이 문서에서는 활동 로그 이벤트를 보고 검색 하는 다양 한 방법에 대 한 세부 정보를 제공 합니다.

## <a name="azure-portal"></a>Azure Portal
Azure Portal **모니터** 메뉴의 모든 리소스에 대 한 활동 로그를 봅니다. 해당 리소스의 메뉴에 있는 **활동 로그** 옵션에서 특정 리소스에 대 한 활동 로그를 봅니다.

![활동 로그 보기](./media/activity-logs-overview/view-activity-log.png)

다음 필드를 기준으로 활동 로그 이벤트를 필터링 할 수 있습니다.

* **Timespan**: 이벤트의 시작 및 종료 시간입니다.
* **Category**: [활동 로그의 범주](activity-log-view.md#categories-in-the-activity-log)에 설명 된 이벤트 범주입니다.
* **구독**: 하나 이상의 Azure 구독 이름입니다.
* **리소스 그룹**: 선택한 구독 내의 하나 이상의 리소스 그룹입니다.
* **리소스 (이름)** :-특정 리소스의 이름입니다.
* **리소스 종류**: 리소스의 유형 (예: _virtualmachines/_ )입니다.
* **작업 이름** -Azure Resource Manager 작업의 이름입니다 (예: _Microsoft .sql/servers/Write_).
* **심각도**: 이벤트의 심각도 수준입니다. 사용 가능한 값은 _정보 제공_, _경고_, _오류_, _중요_합니다.
* **이벤트를 시작한 사람**: 작업을 수행한 사용자입니다.
* **검색 열기**: 모든 이벤트의 모든 필드에서 해당 문자열을 검색 하는 텍스트 검색 상자를 엽니다.

## <a name="categories-in-the-activity-log"></a>활동 로그의 범주
활동 로그의 각 이벤트에는 다음 표에 설명 된 특정 범주가 있습니다. 이러한 범주의 스키마에 대한 전체 정보는 [Azure Activity Log 이벤트 스키마](activity-log-schema.md)를 참조하세요. 

| 범주 | Description |
|:---|:---|
| 관리 | 리소스 관리자을 통해 수행 되는 모든 만들기, 업데이트, 삭제 및 동작 작업의 레코드를 포함 합니다. 관리 이벤트의 예로는 _가상 머신 만들기_ 및 _네트워크 보안 그룹 삭제_가 있습니다.<br><br>리소스 관리자를 사용 하 여 사용자 또는 응용 프로그램에서 수행 하는 모든 작업은 특정 리소스 종류에 대 한 작업으로 모델링 됩니다. 작업 유형이 _쓰기_, _삭제_또는 _작업_인 경우 해당 작업의 시작 및 성공 또는 실패의 레코드가 관리 범주에 기록 됩니다. 관리 이벤트에는 구독에서 역할 기반 액세스 제어에 대 한 변경 내용도 포함 됩니다. |
| 서비스 상태 | Azure에서 발생 한 모든 서비스 상태 인시던트의 레코드를 포함 합니다. 미국 동부에서 Service Health 이벤트 SQL Azure의 예로는 _가동 중지 시간이 발생_합니다. <br><br>Service Health 이벤트는 _작업 필요_, _지원 복구_, _인시던트_, _유지 관리_, _정보_또는 _보안_의 여섯 가지 형태로 제공 됩니다. 이러한 이벤트는 이벤트의 영향을 받는 구독에 리소스가 있는 경우에만 생성 됩니다.
| 리소스 상태 | Azure 리소스에 발생 한 리소스 상태 이벤트의 레코드를 포함 합니다. Resource Health 이벤트의 예는 _가상 컴퓨터 상태를 사용할 수 없음으로 변경 하_는 것입니다.<br><br>Resource Health 이벤트는 _사용 가능, 사용_ _불가_, _저하_됨 및 _알 수 없음_의 네 가지 상태 중 하나를 나타낼 수 있습니다. 또한 Resource Health 이벤트는 _플랫폼에서 시작_ 되거나 _사용자가 시작한_것으로 분류 될 수 있습니다. |
| 경고 | Azure 경고에 대 한 활성화 레코드를 포함 합니다. 경고 이벤트의 예로는 _myVM의 CPU%가 지난 5 분 동안 80을 초과_했습니다.|
| 자동 크기 조정 | 구독에서 정의한 자동 크기 조정 설정에 따라 자동 크기 조정 엔진의 작업과 관련 된 모든 이벤트의 레코드를 포함 합니다. 자동 크기 조정 이벤트의 예로는 _자동 크기 조정 확장 작업이 실패 했습니다_. |
| 권장 | Azure Advisor의 권장 사항 이벤트를 포함 합니다. |
| 보안 | Azure Security Center에서 생성 된 모든 경고의 레코드를 포함 합니다. 보안 이벤트의 예로 _의심 스러운 이중 확장 파일이 실행_됩니다. |
| 정책 | Azure Policy에서 수행 하는 모든 효과 동작 작업의 레코드를 포함 합니다. 정책 이벤트의 예로 _감사_ 및 _거부_를 들 수가 있습니다. Policy에서 수행하는 모든 작업은 리소스에 대한 작업으로 모델링됩니다. |

## <a name="view-change-history"></a>변경 기록 보기

활동 로그를 검토할 때 해당 이벤트 시간 동안 변경 된 내용을 확인 하는 데 도움이 될 수 있습니다. **변경 기록과**함께이 정보를 볼 수 있습니다. 자세히 보려는 활동 로그에서 이벤트를 선택 합니다. **변경 내용 (미리 보기)** 탭을 선택 하 여 해당 이벤트와 관련 된 변경 내용을 확인 합니다.

![이벤트에 대 한 변경 내용 목록](media/activity-logs-overview/change-history-event.png)

이벤트와 관련 된 변경 내용이 있는 경우 선택할 수 있는 변경 내용의 목록이 표시 됩니다. 그러면 **변경 내용 (미리 보기)** 페이지가 열립니다. 이 페이지에서 리소스에 대 한 변경 내용을 볼 수 있습니다. 다음 예제에서 볼 수 있듯이 VM의 크기를 변경 하는 것 뿐만 아니라 이전 VM 크기를 변경 하기 전과 변경 된 내용을 볼 수 있습니다.

![차이점을 보여 주는 변경 기록 페이지](media/activity-logs-overview/change-history-event-details.png)

변경 기록에 대해 자세히 알아보려면 [리소스 변경 내용 가져오기](../../governance/resource-graph/how-to/get-resource-changes.md)를 참조 하세요.






## <a name="powershell"></a>PowerShell
[AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet을 사용 하 여 PowerShell에서 활동 로그를 검색 합니다. 다음은 몇 가지 일반적인 예입니다.

> [!NOTE]
> `Get-AzLog` 명령은 15일 간의 기록만 제공합니다. **-Maxevents** 매개 변수를 사용 하 여 15 일을 초과 하는 마지막 N 개 이벤트를 쿼리 합니다. 15 일 보다 오래 된 이벤트에 액세스 하려면 REST API 또는 SDK를 사용 합니다. **StartTime**을 포함하지 않으면 **EndTime**에서 1시간을 뺀 값이 기본값입니다. **EndTime**을 포함하지 않으면 현재 시간이 기본값입니다. 모든 시간은 UTC입니다.


특정 날짜 시간 이후에 만들어진 로그 항목 가져오기:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

날짜 시간 범위 간의 로그 항목을 가져옵니다.

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

특정 리소스 그룹에서 로그 항목을 가져옵니다.

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

특정 리소스 공급자에서 날짜/시간 범위의 로그 항목을 가져옵니다.

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

특정 호출자를 사용 하 여 로그 항목을 가져옵니다.

```powershell
Get-AzLog -Caller 'myname@company.com'
```

마지막 1000 이벤트를 가져옵니다.

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
[Az monitor activity-log](cli-samples.md#view-activity-log-for-a-subscription) 를 사용 하 여 CLI에서 활동 로그를 검색 합니다. 다음은 몇 가지 일반적인 예입니다.


사용 가능한 모든 옵션을 표시 합니다.

```azurecli
az monitor activity-log list -h
```

특정 리소스 그룹에서 로그 항목을 가져옵니다.

```azurecli
az monitor activity-log list --resource-group <group name>
```

특정 호출자를 사용 하 여 로그 항목을 가져옵니다.

```azurecli
az monitor activity-log list --caller myname@company.com
```

날짜 범위 내에서 리소스 유형에 대 한 호출자의 로그 가져오기:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
[Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) 를 사용 하 여 REST 클라이언트에서 활동 로그를 검색 합니다. 다음은 몇 가지 일반적인 예입니다.

필터를 사용 하 여 활동 로그 가져오기:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

필터를 사용 하 여 활동 로그 가져오기 및 다음을 선택 합니다.

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Select를 사용 하 여 활동 로그 가져오기:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

필터를 사용 하지 않고 활동 로그 가져오기 또는 선택:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="activity-logs-analytics-monitoring-solution"></a>활동 로그 분석 모니터링 솔루션
Azure Log Analytics 모니터링 솔루션에는 Log Analytics 작업 영역에서 활동 로그 레코드를 분석 하기 위한 여러 로그 쿼리 및 뷰가 포함 되어 있습니다.

### <a name="prerequisites"></a>필수 조건
구독에 대 한 활동 로그를 Log Analytics 작업 영역에 보내려면 진단 설정을 만들어야 합니다. [Azure Monitor의 Log Analytics 작업 영역에서 Azure platform Logs 수집을](resource-logs-collect-workspace.md)참조 하세요.

### <a name="install-the-solution"></a>솔루션 설치
**활동 로그 분석** 솔루션을 설치 하려면 [모니터링 솔루션 설치](../insights/solutions.md#install-a-monitoring-solution) 의 절차를 따르십시오. 추가 구성은 필요 하지 않습니다.

### <a name="use-the-solution"></a>솔루션 사용
**활동 로그** 페이지의 맨 위에서 **로그** 를 클릭 하 여 구독에 대 한 [활동 로그 분석 모니터링 솔루션](activity-log-collect.md) 을 엽니다. 또는 Azure Portal의 구독 **모니터** 메뉴에 있는 모든 모니터링 솔루션에 액세스 합니다. **Insights** 섹션에서 **자세히** 를 선택 하 여 솔루션 타일이 있는 **개요** 페이지를 엽니다. **Azure 활동 로그** 타일에는 작업 영역의 **azureactivity** 레코드 수 수가 표시 됩니다.

![Azure 활동 로그 타일](media/collect-activity-logs/azure-activity-logs-tile.png)


Azure **활동 로그** 타일을 클릭 하 여 **azure 활동 로그** 보기를 엽니다. 뷰에는 다음 표의 시각화 파트가 포함 되어 있습니다. 각 파트에는 지정 된 시간 범위에 대 한 해당 부분의 기준과 일치 하는 항목이 최대 10 개까지 나열 됩니다. 파트 아래쪽의 **모두 보기** 를 클릭 하 여 일치 하는 모든 레코드를 반환 하는 로그 쿼리를 실행할 수 있습니다.

![Azure 활동 로그 대시보드](media/collect-activity-logs/activity-log-dash.png)

| 시각화 요소 | Description |
| --- | --- |
| Azure 활동 로그 항목 | 선택한 날짜 범위에 대 한 상위 Azure 활동 로그 항목 레코드 합계의 가로 막대형 차트를 표시 하 고 상위 10 개 활동 호출자의 목록을 표시 합니다. 가로 막대형 차트를 클릭하면 `AzureActivity`에 대한 로그 검색이 실행됩니다. 호출자 항목을 클릭 하면 해당 항목에 대 한 모든 활동 로그 항목을 반환 하는 로그 검색이 실행 됩니다. |
| 상태별 활동 로그 | 선택한 날짜 범위에 대 한 Azure 활동 로그 상태와 상위 10 개 상태 레코드의 목록에 대 한 도넛형 차트를 표시 합니다. 차트를 클릭 하 `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`에 대 한 로그 쿼리를 실행 합니다. 상태 항목을 클릭 하면 해당 상태 레코드에 대 한 모든 활동 로그 항목을 반환 하는 로그 검색이 실행 됩니다. |
| 리소스별 활동 로그 | 활동 로그를 포함 하는 총 리소스 수를 표시 하 고 각 리소스에 대 한 레코드 수를 포함 하는 상위 10 개 리소스를 나열 합니다. 전체 영역을 클릭하여 `AzureActivity | summarize AggregatedValue = count() by Resource`에 대해 로그 검색을 실행하면 솔루션에 사용할 수 있는 모든 Azure 리소스가 표시됩니다. 리소스를 클릭 하 여 해당 리소스에 대 한 모든 활동 레코드를 반환 하는 로그 쿼리를 실행 합니다. |
| 리소스 공급자별 활동 로그 | 활동 로그를 생성 하는 리소스 공급자의 총 수를 표시 하 고 상위 10 개를 나열 합니다. 전체 영역을 클릭 하 여 모든 Azure 리소스 공급자를 표시 하는 `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`에 대 한 로그 쿼리를 실행 합니다. 리소스 공급자를 클릭 하 여 공급자에 대 한 모든 활동 레코드를 반환 하는 로그 쿼리를 실행 합니다. |




## <a name="next-steps"></a>다음 단계

* [플랫폼 로그 개요 읽기](platform-logs-overview.md)
* [활동 로그를 다른 대상으로 보내는 진단 설정 만들기](diagnostic-settings.md)
