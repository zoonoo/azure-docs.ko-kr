---
title: Azure 활동 로그 내보내기
description: Azure 활동 로그를 보관용 저장소로 내보내거나 Azure 외부로 내보내기 위한 Azure 이벤트 허브를 내보냅니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 12c750f96b8852cdd6a6039ebfa750c2ee792a6b
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396713"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>저장소 또는 Azure 이벤트 허브로 Azure 활동 로그 내보내기

> [!IMPORTANT]
> Azure 활동 로그를 Azure 저장소 및 Azure 이벤트 허브로 보내는 메서드가 [진단 설정으로](diagnostic-settings.md)변경되었습니다. 이 문서에서는 더 이상 사용되지 않는 프로세스에 있는 레거시 메서드에 대해 설명합니다. 비교를 위해 [Azure 모니터의 Azure 활동 로그 수집 및 분석용](activity-log-collect.md) 업데이트를 참조하십시오.


[Azure 활동 로그는](platform-logs-overview.md) Azure 구독에서 발생한 구독 수준 이벤트에 대한 통찰력을 제공합니다. Azure Portal에서 활동 로그를 보거나 Azure Monitor에서 수집한 다른 데이터로 분석할 수 있는 로그 분석 작업 영역으로 복사하는 것 외에도 로그 프로필을 만들어 활동 로그를 Azure 저장소 계정에 보관하거나 이벤트 허브로 스트리밍할 수 있습니다.

## <a name="archive-activity-log"></a>아카이브 활동 로그
활동 로그를 저장소 계정에 보관하면 감사, 정적 분석 또는 백업을 위해 로그 데이터를 90일 이상(보존 정책을 완전히 제어)으로 유지하려는 경우에 유용합니다. 활동 로그 이벤트는 90일 동안 Azure 플랫폼에 유지되므로 90일 이하의 이벤트만 유지하면 저장소 계정에 보관을 설정할 필요가 없습니다.

## <a name="stream-activity-log-to-event-hub"></a>이벤트 허브로 활동 로그 스트리밍
[Azure Event Hubs는](/azure/event-hubs/) 초당 수백만 개의 이벤트를 수신하고 처리할 수 있는 데이터 스트리밍 플랫폼 및 이벤트 수집 서비스입니다. 이벤트 허브로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. 활동 로그에 스트리밍 기능을 사용할 수 있는 두 가지 방법은 다음과 같습니다.
* **타사 로깅 및 원격 분석 시스템으로 스트림**: 시간이 지나면서 Azure Event Hubs 스트리밍은 활동 로그를 타사 SIEM 및 로그 분석 솔루션으로 파이핑하기 위한 메커니즘이 되고 있습니다.
* **사용자 지정 원격 분석 및 로깅 플랫폼 빌드**: 사용자 지정 빌드 원격 분석 플랫폼이 이미 있거나 플랫폼 빌드에 대해 생각하고 있는 경우 Event Hubs의 확장성 높은 게시-구독 특성을 통해 활동 로그를 유연하게 수집할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="storage-account"></a>스토리지 계정
활동 로그를 보관하는 경우 아직 [보관하지](../../storage/common/storage-account-create.md) 않은 경우 저장소 계정을 만들어야 합니다. 모니터링 데이터에 대한 액세스를 더 잘 제어할 수 있도록 모니터링되지 않는 다른 데이터가 저장된 기존 저장소 계정을 사용해서는 안 됩니다. 하지만 저장소 계정에 로그 및 메트릭을 보관하는 경우 동일한 저장소 계정을 사용하여 모든 모니터링 데이터를 중앙 위치에 유지하도록 선택할 수 있습니다.

설정을 구성하는 사용자가 두 구독에 대한 적절한 RBAC 액세스를 가진 경우 스토리지 계정은 로그를 내보내는 구독과 동일한 구독을 가지고 있지 않아도 됩니다. 

> [!TIP]
> 보안 가상 네트워크 뒤에 있는 저장소 계정에 대한 액세스를 제공하려면 [Azure 저장소 방화벽 및 가상 네트워크 구성을](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) 참조하십시오.

### <a name="event-hubs"></a>Event Hubs
활동 로그를 이벤트 허브로 보내는 경우 이벤트 허브가 아직 없는 경우 [이벤트 허브를 만들어야](../../event-hubs/event-hubs-create.md) 합니다. 이전에 활동 로그 이벤트를 이 이벤트 허브 네임스페이스로 스트리밍한 경우 해당 이벤트 허브가 다시 사용됩니다.

공유 액세스 정책은 스트리밍 메커니즘에서 보유하는 권한을 정의합니다. 이벤트 허브로 스트리밍하려면 관리, 보내기 및 수신 권한이 필요합니다. Azure Portal에서 Event Hubs 네임스페이스에 대한 구성 탭 아래에서 Event Hubs 네임스페이스에 대한 공유 액세스 정책을 만들거나 수정할 수 있습니다.

활동 로그 프로필을 업데이트하여 스트리밍을 포함하려면 해당 이벤트 허브 권한 부여 규칙에 대한 ListKey 권한이 있어야 합니다. 설정을 구성하는 사용자에게 구독 모두에 액세스할 수 있는 적절한 RBAC 액세스 권한이 있다면 Event Hubs 네임스페이스가 로그를 내보내는 구독과 동일한 구독에 위치하지 않아도 됩니다. 구독은 모두 동일한 AAD 테넌트에 위치합니다.

[로그 프로필을 만들어](#create-a-log-profile)활동 로그를 이벤트 허브로 스트리밍합니다.

## <a name="create-a-log-profile"></a>로그 프로필 만들기
**로그 프로필을**사용하여 Azure 활동 로그를 내보내는 방법을 정의합니다. 각 Azure 구독에는 하나의 로그 프로필만 있을 수 있습니다. 이러한 설정은 포털의 활동 로그 블레이드에서 **내보내기** 옵션을 통해 구성할 수 있습니다. [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell cmdlet 또는 CLI를 사용하여 프로그래밍 방식으로 구성할 수도 있습니다.

로그 프로필은 다음을 정의합니다.

**활동 로그를 보내야 하는 위치입니다.** 현재 사용 가능한 옵션은 저장소 계정 또는 이벤트 허브입니다.

**보낼 이벤트 범주입니다.** 로그 프로필 및 활동 로그 이벤트의 *범주의* 의미는 다릅니다. 로그 프로필에서 *범주는* 작업 유형(쓰기, 삭제, 작업)을 나타냅니다. 활동 로그 이벤트에서 *범주*"* 속성은 이벤트의 소스 또는 유형(예: 관리, ServiceHealth 및 경고)을 나타냅니다.

**내보낼 지역(위치)입니다.** 활동 로그에 있는 많은 이벤트가 전역 이벤트이므로 모든 위치를 포함해야 합니다.

**활동 로그를 Storage 계정에 유지해야 하는 기간** 보존 기간이 0일이라는 것은 로그가 영원히 보관된다는 의미입니다. 그렇지 않으면 값은 1에서 365 사이의 일 수일 수일 수 있습니다.

보존 정책이 설정되어 있지만 저장소 계정에 로그를 저장하지 않는 경우 보존 정책은 영향을 주지 않습니다. 보존 정책은 매일 적용되므로 하루의 마지막에(UTC) 보존 정책이 지난 날의 로그가 삭제됩니다. 예를 들어, 하루의 보존 정책이 있는 경우 오늘 날짜가 시작될 때 하루 전의 로그가 삭제됩니다. 삭제 프로세스는 자정(UTC)에 시작되지만, 스토리지 계정에서 로그가 삭제될 때까지 최대 24시간이 걸릴 수 있습니다.


> [!IMPORTANT]
> Microsoft.Insights 리소스 공급자가 등록되지 않은 경우 로그 프로필을 만들 때 오류가 발생할 수 있습니다. 이 [공급자를](../../azure-resource-manager/management/resource-providers-and-types.md) 등록하는 Azure 리소스 공급자 및 형식을 참조하십시오.


### <a name="create-log-profile-using-the-azure-portal"></a>Azure 포털을 사용하여 로그 프로필 만들기

Azure 포털에서 **이벤트 허브로 내보내기** 옵션을 사용하여 로그 프로필을 만들거나 편집합니다.

1. Azure 포털의 **Azure 모니터** 메뉴에서 **활동 로그를**선택합니다.
3. **진단 설정을**클릭합니다.

   ![진단 설정](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 레거시 환경을 보려면 보라색 배너를 클릭합니다.

    ![레거시 경험](media/diagnostic-settings-subscription/legacy-experience.png)

3. 표시되는 블레이드에서 다음을 지정합니다.
   * 내보낼 이벤트가 있는 지역입니다. 활동 로그가 전역(비지역) 로그이므로 대부분의 이벤트에 연결된 지역이 없기 때문에 주요 이벤트를 놓치지 않도록 모든 지역을 선택해야 합니다.
   * 저장소 계정에 쓰려면 다음을 수행하십시오.
       * 이벤트를 저장하려는 저장소 계정입니다.
       * 이러한 이벤트를 저장소에 유지하려는 일 수입니다. 0일로 설정하면 로그를 계속 유지합니다.
   * 이벤트 허브에 쓰려면 다음을 수행하십시오.
       * 이러한 이벤트를 스트리밍하기 위해 이벤트 허브를 만들려는 서비스 버스 네임스페이스입니다.

     ![활동 로그 내보내기 블레이드](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. **저장**을 클릭하여 이러한 설정을 저장합니다. 해당 설정이 구독에 즉시 적용됩니다.


### <a name="configure-log-profile-using-powershell"></a>PowerShell을 사용하여 로그 프로파일 구성

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

로그 프로필이 이미 있는 경우 먼저 기존 로그 프로필을 제거한 다음 새 로그 프로필을 만들어야 합니다.

1. `Get-AzLogProfile`를 사용하여 로그 프로필이 있는지 확인합니다.  로그 프로필이 있는 경우 *name* 속성을 기록합니다.

1. *name* 속성의 값을 사용하여 로그 프로필을 제거하려면 `Remove-AzLogProfile`을 사용합니다.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. `Add-AzLogProfile`를 사용하여 새 로그 프로필을 만듭니다.

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | 속성 | 필수 | Description |
    | --- | --- | --- |
    | 속성 |예 |로그 프로필의 이름입니다. |
    | StorageAccountId |예 |활동 로그를 저장해야 하는 저장소 계정의 리소스 ID입니다. |
    | serviceBusRuleId |예 |이벤트 허브를 만들 Service Bus 네임스페이스의 Service Bus 규칙 ID입니다. 이 형식은 다음과 같은 `{service bus resource ID}/authorizationrules/{key name}`문자열입니다. |
    | 위치 |예 |활동 로그 이벤트를 수집할 쉼표로 구분된 지역 목록입니다. |
    | RetentionInDays |예 |1에서 365 사이의 저장소 계정에 이벤트를 유지해야 하는 일 수입니다. 0 값은 로그를 무기한 저장합니다. |
    | Category |예 |수집할 쉼표로 구분된 이벤트 범주 목록입니다. 가능한 값은 _쓰기,_ _삭제_및 _작업_입니다. |

### <a name="example-script"></a>예제 스크립트
다음은 활동 로그를 저장소 계정과 이벤트 허브에 모두 기록하는 로그 프로필을 만드는 샘플 PowerShell 스크립트입니다.

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


### <a name="configure-log-profile-using-azure-cli"></a>Azure CLI를 사용하여 로그 프로필 구성

로그 프로필이 이미 있으면 먼저 기존 로그 프로필을 제거한 다음, 새 로그 프로필을 생성해야 합니다.

1. `az monitor log-profiles list`를 사용하여 로그 프로필이 있는지 확인합니다.
2. *name* 속성의 값을 사용하여 로그 프로필을 제거하려면 `az monitor log-profiles delete --name "<log profile name>`을 사용합니다.
3. `az monitor log-profiles create`를 사용하여 새 로그 프로필을 만듭니다.

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | 속성 | 필수 | Description |
    | --- | --- | --- |
    | name |예 |로그 프로필의 이름입니다. |
    | storage-account-id |예 |활동 로그를 저장할 Storage 계정의 리소스 ID입니다. |
    | 위치 |예 |활동 로그 이벤트를 수집할 공백으로 구분된 지역 목록입니다. `az account list-locations --query [].name`을 사용하여 구독에 대한 모든 지역 목록을 볼 수 있습니다. |
    | 일 |예 |이벤트를 보존해야 하는 일 수(1일에서 365일 사이)입니다. 0 값은 로그를 무기한(영원히) 저장합니다.  0이면 활성화된 매개 변수를 false로 설정해야 합니다. |
    |사용 | 예 |True 또는 False입니다.  보존 정책을 사용하거나 비활성화하는 데 사용합니다.  True이면 일 매개 변수 0보다 큰 값이어야 합니다.
    | 범주 |예 |수집해야 할 공백으로 구분된 이벤트 범주 목록입니다. 가능한 값은 쓰기, 삭제 및 작업입니다. |



## <a name="next-steps"></a>다음 단계

* [활동 로그에 대해 자세히 알아보기](../../azure-resource-manager/management/view-activity-logs.md)
* [Azure 모니터 로그에 활동 로그 수집](activity-log-collect.md)
