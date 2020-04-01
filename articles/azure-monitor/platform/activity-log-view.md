---
title: Azure 모니터에서 Azure 활동 로그 이벤트 보기
description: Azure 모니터에서 Azure 활동 로그인을 보고 PowerShell, CLI 및 REST API를 사용 하 여 검색합니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: d2423d04ead9040cce53d847d24efe75be680d94
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397317"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Azure 활동 로그 이벤트 보기 및 검색

[Azure 활동 로그는](platform-logs-overview.md) Azure에서 발생한 구독 수준 이벤트에 대한 통찰력을 제공합니다. 이 문서에서는 활동 로그 이벤트를 보고 검색하기 위한 다양한 방법에 대한 세부 정보를 제공합니다.

## <a name="azure-portal"></a>Azure portal
Azure 포털의 **모니터** 메뉴에서 모든 리소스에 대한 활동 로그를 봅니다. 해당 리소스의 메뉴에서 활동 **로그** 옵션에서 특정 리소스에 대한 활동 로그를 봅니다.

![활동 로그 보기](./media/activity-logs-overview/view-activity-log.png)

다음 필드별로 활동 로그 이벤트를 필터링할 수 있습니다.

* **시간 범위**: 이벤트의 시작 및 종료 시간입니다.
* **범주**: [활동 로그의 범주에](activity-log-view.md#categories-in-the-activity-log)설명된 이벤트 범주입니다.
* **구독**: 하나 이상의 Azure 구독 이름입니다.
* **리소스 그룹**: 선택한 구독 내에서 하나 이상의 리소스 그룹입니다.
* **리소스 (이름)**: - 특정 리소스의 이름입니다.
* **리소스 유형**: 리소스 유형(예: _Microsoft.Compute/가상 시스템)_
* **작업 이름** - Azure 리소스 관리자 작업의 이름(예: _Microsoft.SQL/서버/쓰기)_
* **심각도**: 이벤트의 심각도 수준입니다. 사용 가능한 값은 _정보,_ _경고,_ _오류_, _중요_입니다.
* **:** 작업을 수행한 사용자입니다.
* **검색 열기**: 모든 이벤트의 모든 필드에서 해당 문자열을 검색하는 텍스트 검색 상자를 엽니다.

## <a name="categories-in-the-activity-log"></a>활동 로그의 범주
활동 로그의 각 이벤트에는 다음 표에 설명된 특정 범주가 있습니다. 이러한 범주의 스키마에 대한 전체 정보는 [Azure Activity Log 이벤트 스키마](activity-log-schema.md)를 참조하세요. 

| Category | Description |
|:---|:---|
| 관리 | 리소스 관리자를 통해 수행된 모든 만들기, 업데이트, 삭제 및 작업 작업의 레코드를 포함합니다. 관리 이벤트의 예로는 _가상 컴퓨터 만들기_ 및 네트워크 보안 그룹 _삭제_등이 있습니다.<br><br>리소스 관리자를 사용하여 사용자 또는 응용 프로그램에서 수행한 모든 작업은 특정 리소스 유형에 대한 작업으로 모델링됩니다. 작업 유형이 _쓰기,_ _삭제_또는 _작업인_경우 해당 작업의 시작 및 성공 또는 실패의 레코드가 관리 범주에 기록됩니다. 관리 이벤트에는 구독의 역할 기반 액세스 제어에 대한 변경 내용도 포함됩니다. |
| 서비스 상태 | Azure에서 발생한 서비스 상태 인시던트의 레코드를 포함합니다. 미국 동부의 서비스 상태 이벤트 SQL Azure의 예로 _인해 가동 중지 시간이 발생했습니다._ <br><br>서비스 상태 이벤트는 _조치 필요,_ _복구 보조,_ _사고,_ _유지 관리,_ _정보_또는 _보안의_여섯 가지 종류로 제공됩니다. 이러한 이벤트는 이벤트의 영향을 받는 구독에 리소스가 있는 경우에만 만들어집니다.
| 리소스 상태 | Azure 리소스에 발생한 리소스 상태 이벤트의 레코드를 포함합니다. 리소스 상태 이벤트의 예로는 _가상 시스템 상태를 사용할 수 없는 상태로 변경된 경우입니다._<br><br>리소스 상태 이벤트는 _사용 가능한_상태, _사용할 수 없음,_ _저하됨_및 _알 수 없음의_네 가지 상태 중 하나를 나타낼 수 있습니다. 또한 리소스 상태 이벤트는 _플랫폼을 시작하거나_ 사용자가 시작한 것으로 _분류할_수 있습니다. |
| 경고 | Azure 경고에 대한 활성화 레코드를 포함합니다. 경고 이벤트의 예는 _myVM의 CPU %가 지난 5분 동안 80을 넘었습니다._|
| 자동 크기 조정 | 구독에서 정의한 자동 크기 조정 설정에 따라 자동 크기 조정 엔진 의 작동과 관련된 모든 이벤트 레코드를 포함합니다. 자동 크기 조정 이벤트의 예로는 _자동 크기 조정 작업이 실패한_것입니다. |
| 권장 | Azure Advisor의 권장 이벤트가 포함되어 있습니다. |
| 보안 | Azure 보안 센터에서 생성된 모든 경고레코드를 포함합니다. 보안 이벤트의 예로는 _의심스러운 이중 확장파일이 실행되었습니다._ |
| 정책 | Azure Policy에서 수행하는 모든 효과 작업 작업의 레코드를 포함합니다. 정책 이벤트의 예로는 _감사_ 및 _거부_가 있습니다. Policy에서 수행하는 모든 작업은 리소스에 대한 작업으로 모델링됩니다. |

## <a name="view-change-history"></a>변경 기록 보기

활동 로그를 검토할 때 해당 이벤트 시간 동안 어떤 변경 사항이 있었는지 확인하는 데 도움이 될 수 있습니다. **변경 기록을**통해 이 정보를 볼 수 있습니다. 자세히 살펴볼 활동 로그에서 이벤트를 선택합니다. 해당 이벤트와 관련된 변경 내용을 보려면 **변경 기록(미리 보기)** 탭을 선택합니다.

![이벤트의 기록 목록 변경](media/activity-logs-overview/change-history-event.png)

이벤트에 관련된 변경 사항이 있는 경우 선택할 수 있는 변경 사항 목록이 표시됩니다. 그러면 **변경 기록(미리 보기)** 페이지가 열립니다. 이 페이지에서 리소스에 대한 변경 내용을 볼 수 있습니다. 다음 예제에서 볼 수 있듯이 VM의 크기가 변경된 것뿐만 아니라 이전 VM 크기가 변경되기 전의 크기와 변경된 내용을 확인할 수 있습니다.

![차이점을 보여주는 변경 기록 페이지](media/activity-logs-overview/change-history-event-details.png)

변경 기록에 대한 자세한 내용은 [리소스 변경 받기를](../../governance/resource-graph/how-to/get-resource-changes.md)참조하십시오.






## <a name="powershell"></a>PowerShell
[Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet을 사용하여 PowerShell에서 활동 로그를 검색합니다. 다음은 몇 가지 일반적인 예입니다.

> [!NOTE]
> `Get-AzLog` 명령은 15일 간의 기록만 제공합니다. **-MaxEvents** 매개 변수를 사용하여 15일을 초과하는 마지막 N 이벤트를 쿼리합니다. 15일이 지난 이벤트에 액세스하려면 REST API 또는 SDK를 사용합니다. **StartTime**을 포함하지 않으면 **EndTime**에서 1시간을 뺀 값이 기본값입니다. **EndTime**을 포함하지 않으면 현재 시간이 기본값입니다. 모든 시간은 UTC입니다.


특정 날짜 시간 이후에 로그 항목을 생성합니다.

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

날짜 시간 범위 간에 로그 항목 가져옵니다.

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

특정 리소스 그룹에서 로그 항목을 가져옵니다.

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

날짜 시간 범위 간에 특정 리소스 공급자로부터 로그 항목을 가져옵니다.

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

특정 호출자로 로그 항목을 가져옵니다.

```powershell
Get-AzLog -Caller 'myname@company.com'
```

마지막 1000개의 이벤트 받기:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
[AZ 모니터 활동 로그를](cli-samples.md#view-activity-log-for-a-subscription) 사용하여 CLI에서 활동 로그를 검색합니다. 다음은 몇 가지 일반적인 예입니다.


사용 가능한 모든 옵션을 봅니다.

```azurecli
az monitor activity-log list -h
```

특정 리소스 그룹에서 로그 항목을 가져옵니다.

```azurecli
az monitor activity-log list --resource-group <group name>
```

특정 호출자로 로그 항목을 가져옵니다.

```azurecli
az monitor activity-log list --caller myname@company.com
```

날짜 범위 내에서 리소스 유형에 대한 호출자에 의한 로그 받기:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
Azure [모니터 REST API를](https://docs.microsoft.com/rest/api/monitor/) 사용하여 REST 클라이언트에서 활동 로그를 검색합니다. 다음은 몇 가지 일반적인 예입니다.

필터를 통해 활동 로그 받기:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

필터를 통해 활동 로그를 얻고 다음을 선택합니다.

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

다음을 선택하여 활동 로그 받기:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

필터 없이 활동 로그를 얻거나 다음을 선택합니다.

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>다음 단계

* [플랫폼 로그 개요 읽기](platform-logs-overview.md)
* [다른 대상으로 활동 로그를 보낼 진단 설정 만들기](diagnostic-settings.md)
