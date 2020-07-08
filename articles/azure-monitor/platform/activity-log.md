---
title: Azure 활동 로그
description: Azure 활동 로그를 보고 Azure Monitor 로그, Azure Event Hubs 및 Azure Storage으로 보냅니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 77946694253ff0c1c6953d0b20836d3cb6733801
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86082304"
---
# <a name="azure-activity-log"></a>Azure 활동 로그
활동 로그는 구독 수준 이벤트에 대 한 통찰력을 제공 하는 Azure의 [플랫폼 로그](platform-logs-overview.md) 입니다. 여기에는 리소스를 수정 하거나 가상 컴퓨터를 시작할 때와 같은 정보가 포함 됩니다. Azure Portal에서 활동 로그를 보거나 PowerShell 및 CLI를 사용 하 여 항목을 검색할 수 있습니다. 추가 기능을 위해 활동 로그를 [Azure Monitor 로그](data-platform-logs.md), azure Event Hubs에서 azure로 전달 하거나 보관을 위해 Azure Storage 전송 하는 진단 설정을 만들어야 합니다. 이 문서에서는 활동 로그를 보고 다른 대상으로 보내는 방법에 대 한 세부 정보를 제공 합니다.

진단 설정 만들기에 대 한 자세한 내용은 [진단 설정 만들기를 참조 하 여 플랫폼 로그 및 메트릭을 다른 대상으로 보냅니다](diagnostic-settings.md) .

> [!NOTE]
> 활동 로그의 항목은 시스템에서 생성 되며 변경 하거나 삭제할 수 없습니다.

## <a name="view-the-activity-log"></a>활동 로그 보기
Azure Portal의 대부분 메뉴에서 활동 로그에 액세스할 수 있습니다. 에서 여는 메뉴는 초기 필터를 결정 합니다. **모니터** 메뉴에서 열면 유일한 필터가 구독에 표시 됩니다. 리소스의 메뉴에서 열 경우 필터가 해당 리소스로 설정 됩니다. 다른 모든 항목을 볼 수 있지만 언제 든 지 필터를 변경할 수 있습니다. 필터 **추가** 를 클릭 하 여 필터에 추가 속성을 추가 합니다.

![활동 로그 보기](./media/activity-logs-overview/view-activity-log.png)

활동 로그 범주에 대 한 설명은 [Azure 활동 로그 이벤트 스키마](activity-log-schema.md#categories)를 참조 하세요.

### <a name="view-change-history"></a>변경 기록 보기

일부 이벤트의 경우 해당 이벤트 시간에 발생 한 변경 내용을 보여 주는 변경 기록을 볼 수 있습니다. 자세히 알아보고자 하는 이벤트를 활동 로그에서 선택합니다. **변경 기록(미리 보기)** 탭을 선택하여 해당 이벤트와 연관된 변경 사항을 봅니다.

![이벤트에 대한 변경 기록 목록](media/activity-logs-overview/change-history-event.png)

이벤트와 관련된 변경 사항이 있는 경우 선택할 수 있는 변경 사항 목록이 있습니다. 그러면 **변경 기록(미리 보기)** 페이지가 열립니다. 이 페이지에서 리소스에 대한 변경 사항을 볼 수 있습니다. 다음 예제에서는 VM의 크기를 변경 하는 것 뿐만 아니라 이전 VM 크기를 변경 하기 전과 변경 된 내용을 볼 수 있습니다. 변경 기록에 대해 자세히 알아보려면 [리소스 변경 내용 가져오기](../../governance/resource-graph/how-to/get-resource-changes.md)를 참조 하세요.

![차이점을 보여주는 변경 기록 페이지](media/activity-logs-overview/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>활동 로그 이벤트를 검색 하는 다른 방법
다음 방법을 사용 하 여 활동 로그 이벤트에 액세스할 수도 있습니다.

- [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet을 사용하여 PowerShell에서 활동 로그를 검색합니다. [Azure Monitor PowerShell 샘플](../samples/powershell-samples.md#retrieve-activity-log)을 참조 하세요.
- [az monitor activity-log](https://docs.microsoft.com/cli/azure/monitor/activity-log)를 사용하여 CLI에서 활동 로그를 검색합니다.  [Azure Monitor CLI 샘플](../samples/cli-samples.md#view-activity-log)을 참조하세요.
- [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/)를 사용하여 REST 클라이언트에서 활동 로그를 검색합니다. 


## <a name="send-to-log-analytics-workspace"></a>Log Analytics 작업 영역으로 보내기
 활동 로그를 Log Analytics 작업 영역으로 보내 다음을 포함 하는 [Azure Monitor 로그](data-platform-logs.md) 의 기능을 사용 하도록 설정 합니다.

- 활동 로그 데이터를 Azure Monitor에 의해 수집 된 다른 모니터링 데이터와 상호 연결 합니다.
- 여러 Azure 구독 및 테 넌 트의 로그 항목을 분석을 위해 하나의 위치로 통합 합니다.
- 로그 쿼리를 사용 하 여 복잡 한 분석을 수행 하 고 활동 로그 항목에 대 한 심층 통찰력을 얻습니다.
- 활동 항목이 있는 로그 경고를 사용 하 여 보다 복잡 한 경고 논리를 허용 합니다.
- 활동 로그 항목을 90 일 보다 오래 저장 합니다.
- Log Analytics 작업 영역에 저장 된 활동 로그 데이터에 대 한 데이터 수집 또는 데이터 보존 요금이 없습니다.

작업 로그를 Log Analytics 작업 영역으로 보내는 [진단 설정을 만듭니다](diagnostic-settings.md) . 단일 구독에서 최대 5 개의 작업 영역으로 활동 로그를 보낼 수 있습니다. 테 넌 트 간에 로그를 수집 하려면 [Azure Lighthouse](/azure/lighthouse)가 필요 합니다.

Log Analytics 작업 영역의 활동 로그 데이터는 [Log Analytics](../log-query/get-started-portal.md)에서 [로그 쿼리](../log-query/log-query-overview.md) 를 사용 하 여 검색할 수 있는 *azureactivity* 라는 테이블에 저장 됩니다. 이 테이블의 구조는 [로그 항목의 범주](activity-log-schema.md)에 따라 달라 집니다. 테이블 속성에 대 한 설명은 [Azure Monitor 데이터 참조](https://docs.microsoft.com/azure/azure-monitor/reference/tables/azureactivity)를 참조 하세요.

예를 들어 각 범주에 대 한 활동 로그 레코드 수를 보려면 다음 쿼리를 사용 합니다.

```kusto
AzureActivity
| summarize count() by Category
```

관리 범주에 있는 모든 레코드를 검색 하려면 다음 쿼리를 사용 합니다.

```kusto
AzureActivity
| where Category == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>Azure Event Hubs에 보내기
Azure Event Hubs에 활동 로그를 보내 타사 SIEM 또는 다른 Log analytics 솔루션과 같은 Azure 외부에 항목을 보냅니다. Event hubs의 활동 로그 이벤트는 `records` 각 페이로드의 레코드를 포함 하는 요소와 함께 JSON 형식으로 사용 됩니다. 이 스키마는 범주에 따라 다르며 [저장소 계정 및 event hubs의 스키마](activity-log-schema.md)에 설명 되어 있습니다.

다음은 활동 로그에 대 한 Event Hubs의 샘플 출력 데이터입니다.

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>Azure storage로 보내기
감사, 정적 분석 또는 백업을 위해 90 일 보다 오래 된 로그 데이터를 유지 하려는 경우 활동 로그를 Azure Storage 계정으로 보냅니다. 90 일 이하로만 이벤트를 유지 해야 하는 경우 활동 로그 이벤트는 90 일 동안 Azure 플랫폼에서 유지 되므로 저장소 계정에 보관을 설정할 필요가 없습니다.

활동 로그를 Azure로 보내면 이벤트가 발생 하는 즉시 저장소 계정에 저장소 컨테이너가 만들어집니다. 컨테이너의 blob는 다음과 같은 명명 규칙을 사용 합니다.

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

예를 들어 특정 blob의 이름은 다음과 유사할 수 있습니다.

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

각 PT1H.json Blob은 Blob URL에 지정된 시간 내에서 발생한 이벤트의 JSON Blob을 포함합니다(예: h=12). 현재 시간 동안 이벤트는 발생하는 순서대로 PT1H.json 파일에 추가됩니다. 리소스 로그 이벤트는 시간당 개별 blob으로 분할 되므로 분 값 (m = 00)은 항상 00입니다.

각 이벤트는 공통 최상위 스키마를 사용 하지만 [활동 로그 스키마](activity-log-schema.md)에 설명 된 대로 각 범주에 대해 고유한 다음 형식의 PT1H.js파일에 저장 됩니다.

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>레거시 컬렉션 메서드
이 섹션에서는 진단 설정 전에 사용 된 활동 로그를 수집 하는 레거시 방법에 대해 설명 합니다. 이러한 방법을 사용 하는 경우 리소스 로그와 더 나은 기능 및 일관성을 제공 하는 진단 설정으로 전환 하는 것을 고려해 야 합니다.

### <a name="log-profiles"></a>로그 프로필
로그 프로필은 활동 로그를 Azure storage 또는 event hubs로 전송 하는 레거시 방법입니다. 다음 절차를 사용 하 여 로그 프로필 작업을 계속 하거나 진단 설정으로 마이그레이션하기 위한 준비에서 로그 프로필을 사용 하지 않도록 설정할 수 있습니다.

1. Azure Portal **Azure Monitor** 메뉴에서 **활동 로그**를 선택 합니다.
3. **진단 설정**을 클릭합니다.

   ![진단 설정](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 레거시 환경에 대해 자주색 배너를 클릭 합니다.

    ![레거시 환경](media/diagnostic-settings-subscription/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>PowerShell을 사용 하 여 로그 프로필 구성

로그 프로필이 이미 있는 경우 먼저 기존 로그 프로필을 제거한 다음 새 로그 프로필을 만들어야 합니다.

1. `Get-AzLogProfile`를 사용하여 로그 프로필이 있는지 확인합니다.  로그 프로필이 있는 경우 *name* 속성을 적어둡니다.

1. *name* 속성의 값을 사용하여 로그 프로필을 제거하려면 `Remove-AzLogProfile`을 사용합니다.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. `Add-AzLogProfile`를 사용하여 새 로그 프로필을 만듭니다.

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | 속성 | 필수 | 설명 |
    | --- | --- | --- |
    | 이름 |예 |로그 프로필의 이름입니다. |
    | StorageAccountId |No |활동 로그를 저장 해야 하는 저장소 계정의 리소스 ID입니다. |
    | serviceBusRuleId |No |이벤트 허브를 만들 Service Bus 네임스페이스의 Service Bus 규칙 ID입니다. 형식으로 된 문자열입니다 `{service bus resource ID}/authorizationrules/{key name}` . |
    | 위치 |Yes |활동 로그 이벤트를 수집할 쉼표로 구분된 지역 목록입니다. |
    | RetentionInDays |Yes |저장소 계정에서 이벤트를 보존 해야 하는 기간 (일)입니다 (1에서 365 사이). 0 값은 로그를 무기한 저장합니다. |
    | 범주 |No |수집할 쉼표로 구분된 이벤트 범주 목록입니다. 가능한 값은 _쓰기_, _삭제_및 _동작_입니다. |

### <a name="example-script"></a>예제 스크립트
다음은 저장소 계정 및 이벤트 허브 모두에 활동 로그를 기록 하는 로그 프로필을 만드는 샘플 PowerShell 스크립트입니다.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Azure CLI를 사용 하 여 로그 프로필 구성

로그 프로필이 이미 있으면 먼저 기존 로그 프로필을 제거한 다음, 새 로그 프로필을 생성해야 합니다.

1. `az monitor log-profiles list`를 사용하여 로그 프로필이 있는지 확인합니다.
2. *name* 속성의 값을 사용하여 로그 프로필을 제거하려면 `az monitor log-profiles delete --name "<log profile name>`을 사용합니다.
3. `az monitor log-profiles create`를 사용하여 새 로그 프로필을 만듭니다.

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | 속성 | 필수 | Description |
    | --- | --- | --- |
    | name |Yes |로그 프로필의 이름입니다. |
    | storage-account-id |Yes |활동 로그를 저장할 Storage 계정의 리소스 ID입니다. |
    | 위치 |예 |활동 로그 이벤트를 수집할 공백으로 구분된 지역 목록입니다. `az account list-locations --query [].name`을 사용하여 구독에 대한 모든 지역 목록을 볼 수 있습니다. |
    | 일 |Yes |이벤트를 유지 해야 하는 기간 (일)입니다 (1에서 365 사이). 0 값은 로그를 무기한(영원히) 저장합니다.  0 인 경우 enabled 매개 변수를 false로 설정 해야 합니다. |
    |사용 | Yes |True 또는 False입니다.  보존 정책을 사용하거나 비활성화하는 데 사용합니다.  True이면 일 매개 변수 0보다 큰 값이어야 합니다.
    | 범주 |Yes |수집해야 할 공백으로 구분된 이벤트 범주 목록입니다. 가능한 값은 쓰기, 삭제 및 작업입니다. |


### <a name="log-analytics-workspace"></a>Log Analytics 작업 영역
활동 로그를 Log Analytics 작업 영역으로 보내는 레거시 방법은 작업 영역 구성의 로그에 연결 하는 것입니다. 

1. Azure Portal **Log Analytics 작업 영역** 메뉴에서 작업 영역을 선택 하 여 활동 로그를 수집 합니다.
1. 작업 영역 메뉴의 **작업 영역 데이터 원본** 섹션에서 **Azure 활동 로그**를 선택 합니다.
1. 연결 하려는 구독을 클릭 합니다.

    ![작업 영역](media/activity-log-collect/workspaces.png)

1. **연결** 을 클릭 하 여 구독의 활동 로그를 선택한 작업 영역에 연결 합니다. 구독이 다른 작업 영역에 이미 연결 되어 있는 경우 먼저 **연결 끊기** 를 클릭 하 여 연결을 끊습니다.

    ![작업 영역 연결](media/activity-log-collect/connect-workspace.png)


설정을 사용 하지 않도록 설정 하려면 동일한 절차를 수행 하 고 **연결 끊기** 를 클릭 하 여 작업 영역에서 구독을 제거 합니다.

### <a name="data-structure-changes"></a>데이터 구조 변경
진단 설정은 *azureactivity* 테이블의 구조에 대 한 일부 변경 내용으로 활동 로그를 보내는 데 사용 되는 레거시 방법과 동일한 데이터를 보냅니다.

다음 표의 열은 업데이트 된 스키마에서 더 이상 사용 되지 않습니다. 이러한 작업은 여전히 *azureactivity* 에 있지만 데이터는 없습니다. 이러한 열에 대 한 대체는 새로운 것은 아니지만 사용 되지 않는 열과 동일한 데이터를 포함 합니다. 서로 다른 형식 이므로이를 사용 하는 로그 쿼리를 수정 해야 할 수도 있습니다. 

| 사용 되지 않는 열 | 대체 열 |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus 상태 | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> 경우에 따라 이러한 열의 값은 모두 대문자 일 수 있습니다. 이러한 열을 포함 하는 쿼리가 있는 경우 [= ~ 연산자](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) 를 사용 하 여 대/소문자를 구분 하지 않는 비교를 수행 해야 합니다.

다음 열이 업데이트 된 스키마의 *Azureactivity* 에 추가 되었습니다.

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>활동 로그 분석 모니터링 솔루션
Azure Log Analytics 모니터링 솔루션은 곧 사용 되지 않으며, Log Analytics 작업 영역에서 업데이트 된 스키마를 사용 하는 통합 문서로 대체 됩니다. 이미 사용 하도록 설정한 경우에도 솔루션을 사용할 수 있지만 레거시 설정을 사용 하 여 활동 로그를 수집 하는 경우에만 사용할 수 있습니다. 



### <a name="use-the-solution"></a>솔루션 사용
모니터링 솔루션은 Azure Portal의 **모니터** 메뉴에서 액세스할 수 있습니다. **Insights** 섹션에서 **자세히** 를 선택 하 여 솔루션 타일이 있는 **개요** 페이지를 엽니다. **Azure 활동 로그** 타일에는 작업 영역의 **azureactivity** 레코드 수 수가 표시 됩니다.

![Azure 활동 로그 타일](media/collect-activity-logs/azure-activity-logs-tile.png)


Azure **활동 로그** 타일을 클릭 하 여 **azure 활동 로그** 보기를 엽니다. 뷰에는 다음 표의 시각화 파트가 포함 되어 있습니다. 각 파트에는 지정 된 시간 범위에 대 한 해당 부분의 기준과 일치 하는 항목이 최대 10 개까지 나열 됩니다. 파트 아래쪽의 **모두 보기** 를 클릭 하 여 일치 하는 모든 레코드를 반환 하는 로그 쿼리를 실행할 수 있습니다.

![Azure 활동 로그 대시보드](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>새 구독에 대해 솔루션을 사용 하도록 설정
Azure Portal를 사용 하 여 구독에 활동 로그 분석 솔루션을 더 이상 추가할 수 없습니다. 리소스 관리자 템플릿에 다음 절차를 사용 하 여 추가할 수 있습니다. 

1. 다음 json을 *Activitylogtemplate*. json 이라는 파일에 복사 합니다.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. 다음 PowerShell 명령을 사용 하 여 템플릿을 배포 합니다.

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>다음 단계

* [플랫폼 로그 개요 읽기](platform-logs-overview.md)
* [활동 로그를 다른 대상으로 보내는 진단 설정 만들기](diagnostic-settings.md)
