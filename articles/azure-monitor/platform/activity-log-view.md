---
title: Azure Monitor에서 Azure 활동 로그 이벤트 보기
description: Azure Monitor에서 Azure 활동 로그를 보고 PowerShell, CLI 및 REST API를 사용하여 검색합니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 3327f0bdaff641055cf84ab205d847f0fb73bfe8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834614"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Azure 활동 로그 이벤트를 보고 검색하기

[Azure 활동 로그](platform-logs-overview.md)는 Azure에서 발생한 구독 수준 이벤트에 대한 정보를 제공합니다. 이 문서에서는 활동 로그 이벤트를 보고 검색하는 다양한 방법에 대한 세부 정보를 제공합니다.

## <a name="azure-portal"></a>Azure portal
Azure Portal의 **모니터** 메뉴에서 모든 리소스에 대한 활동 로그를 확인합니다. 해당 리소스의 메뉴에 있는 **활동 로그** 옵션에서 특정 리소스에 대한 활동 로그를 확인합니다.

![활동 로그 보기](./media/activity-logs-overview/view-activity-log.png)

다음 필드를 사용하여 활동 로그 이벤트를 필터링할 수 있습니다.

* **Timespan**: 이벤트에 대한 시작 및 종료 시간입니다.
* **범주**: [활동 로그의 범주](activity-log-view.md#categories-in-the-activity-log)에서 설명하는 이벤트 범주입니다.
* **구독**: 하나 이상의 Azure 구독 이름입니다.
* **리소스 그룹**: 해당 구독 내에서 하나 이상의 리소스 그룹입니다.
* **리소스(이름)** : - 특정 리소스의 이름입니다.
* **리소스 종류**: 리소스의 종류입니다(예: _Microsoft.Compute/virtualmachines_).
* **작업 이름** - Azure Resource Manager 작업의 이름입니다(예: _Microsoft.SQL/servers/Write_).
* **심각도**: 이벤트의 심각도 수준입니다. 사용 가능한 값은 _정보_, _경고_, _오류_, _위험_입니다.
* **이벤트를 시작한 사람**: 작업을 수행하는 사용자입니다.
* **검색 열기**: 모든 이벤트의 모든 필드에서 해당 문자열을 검색하는 열려 있는 텍스트 검색 상자입니다.

## <a name="categories-in-the-activity-log"></a>활동 로그의 범주
활동 로그의 각 이벤트에는 다음 표에서 설명하는 특정 범주가 있습니다. 이러한 범주의 스키마에 대한 전체 정보는 [Azure Activity Log 이벤트 스키마](activity-log-schema.md)를 참조하세요. 

| Category | Description |
|:---|:---|
| 관리 | Resource Manager를 통해 수행한 모든 만들기, 업데이트, 삭제 및 동작 작업의 레코드가 포함되어 있습니다. 관리 이벤트의 예로는 가상 머신 만들기 및 네트워크 보안 그룹 삭제가 있습니다.<br><br>Resource Manager를 사용하여 사용자 또는 애플리케이션에서 수행하는 모든 작업은 특정 리소스 종류에 대한 작업으로 모델링됩니다. 작업 유형이 쓰기, 삭제 또는 동작이면 해당 작업의 시작 및 성공이나 실패 레코드가 모두 관리 범주에 기록됩니다. 관리 이벤트에는 구독의 역할 기반 액세스 제어 변경 내용도 포함됩니다. |
| 서비스 상태 | Azure에서 발생한 모든 서비스 상태 관련 인시던트의 레코드가 포함됩니다. 서비스 상태 이벤트 예제로 미국 동부의 SQL Azure에서 가동 중지 시간 발생이 있습니다. <br><br>서비스 상태 이벤트는 6가지 형태로 제공됩니다. 작업 필요, 복구 지원, 인시던트, 유지 관리, 정보 또는 보안입니다. 이러한 이벤트는 이벤트의 영향을 받는 구독에 리소스가 있는 경우에만 생성됩니다.
| 리소스 상태 | Azure 리소스에 발생한 모든 리소스 상태 이벤트의 레코드가 포함됩니다. 리소스 상태 이벤트의 예제로 가상 머신 상태가 사용 불가능으로 변경됨이 있습니다.<br><br>리소스 상태 이벤트는 상태 중 하나를 나타낼 수 있습니다. 사용 가능, 사용 불가능, 저하됨, 알 수 없음입니다. 또한 리소스 상태 이벤트는 플랫폼 시작 또는 사용자 시작으로 분류될 수 있습니다. |
| 경고 | Azure 경고에 대한 활성화 레코드를 포함합니다. 경고 이벤트의 예로는 지난 5분 동안 myVM의 CPU %가 80 이상이었음이 있습니다.|
| 자동 크기 조정 | 구독에서 정의한 자동 크기 조정 설정을 기준으로 하는 자동 크기 조정 엔진 작업 관련 이벤트의 레코드가 포함됩니다. 자동 크기 조정 이벤트로 예로는 자동 크기 조정 이벤트 실패가 있습니다. |
| 권장 | Azure Advisor의 권장 사항 이벤트가 포함됩니다. |
| 보안 | Azure Security Center에서 경고가 생성한 모든 레코드가 포함됩니다. 보안 이벤트의 예로는 의심스러운 이중 확장 파일이 실행됨이 있습니다. |
| 정책 | Azure Policy에서 수행하는 모든 적용 작업의 레코드가 포함됩니다. 정책 이벤트의 예로는 감사 및 거부가 있습니다. Policy에서 수행하는 모든 작업은 리소스에 대한 작업으로 모델링됩니다. |

## <a name="view-change-history"></a>변경 기록 보기

활동 로그를 검토할 때 해당 이벤트 시간 동안 발생한 변경 사항을 볼 수 있습니다. **변경 기록**을 사용하여 이 정보를 볼 수 있습니다. 자세히 알아보고자 하는 이벤트를 활동 로그에서 선택합니다. **변경 기록(미리 보기)** 탭을 선택하여 해당 이벤트와 연관된 변경 사항을 봅니다.

![이벤트에 대한 변경 기록 목록](media/activity-logs-overview/change-history-event.png)

이벤트와 관련된 변경 사항이 있는 경우 선택할 수 있는 변경 사항 목록이 있습니다. 그러면 **변경 기록(미리 보기)** 페이지가 열립니다. 이 페이지에서 리소스에 대한 변경 사항을 볼 수 있습니다. 다음 예제에서 볼 수 있듯이 VM의 크기를 변경하는 것뿐만 아니라 이전 VM 크기를 변경하기 전과 변경된 사항을 볼 수 있습니다.

![차이점을 보여주는 변경 기록 페이지](media/activity-logs-overview/change-history-event-details.png)

변경 기록에 대한 자세한 내용은 [리소스 변경 가져오기](../../governance/resource-graph/how-to/get-resource-changes.md)를 참조하세요.






## <a name="powershell"></a>PowerShell
[Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet을 사용하여 PowerShell에서 활동 로그를 검색합니다. 다음은 몇 가지 일반적인 예입니다.

> [!NOTE]
> `Get-AzLog` 명령은 15일 간의 기록만 제공합니다. **-MaxRecord** 매개 변수를 사용하여 15일 이후의 N개 이벤트를 쿼리합니다. 15일이 지난 이벤트에 액세스하려면 REST API 또는 SDK를 사용합니다. **StartTime**을 포함하지 않으면 **EndTime**에서 1시간을 뺀 값이 기본값입니다. **EndTime**을 포함하지 않으면 현재 시간이 기본값입니다. 모든 시간은 UTC입니다.


특정 날짜 시간 이후에 만들어진 로그 항목을 가져옵니다.

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

날짜 시간 범위의 로그 항목을 가져옵니다.

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

특정 리소스 그룹에서 로그 항목을 가져옵니다.

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

특정 리소스 공급자에서 이 날짜 시간 범위의 로그 항목을 가져옵니다.

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

특정 호출자의 로그 항목을 가져옵니다.

```powershell
Get-AzLog -Caller 'myname@company.com'
```

마지막 1,000개의 이벤트를 가져옵니다.

```powershell
Get-AzLog -MaxRecord 1000
```


## <a name="cli"></a>CLI
[az monitor activity-log](../samples/cli-samples.md#view-activity-log-for-a-subscription)를 사용하여 CLI에서 활동 로그를 검색합니다. 다음은 몇 가지 일반적인 예입니다.


사용 가능한 모든 옵션을 봅니다.

```azurecli
az monitor activity-log list -h
```

특정 리소스 그룹에서 로그 항목을 가져옵니다.

```azurecli
az monitor activity-log list --resource-group <group name>
```

특정 호출자의 로그 항목을 가져옵니다.

```azurecli
az monitor activity-log list --caller myname@company.com
```

날짜 범위 내 리소스 종류에 대한 호출자별 로그를 가져옵니다.

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
[Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/)를 사용하여 REST 클라이언트에서 활동 로그를 검색합니다. 다음은 몇 가지 일반적인 예입니다.

필터를 사용하여 활동 로그를 가져옵니다.

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

필터 및 선택을 사용하여 활동 로그를 가져옵니다.

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

선택을 사용하여 활동 로그를 가져옵니다.

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

필터 또는 선택을 사용하여 활동 로그를 가져옵니다.

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>다음 단계

* [플랫폼 로그 개요 읽기](platform-logs-overview.md)
* [활동 로그를 다른 대상으로 보내는 진단 설정 만들기](diagnostic-settings.md)
