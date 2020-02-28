---
title: Azure 활동 로그 내보내기
description: Azure 활동 로그를 저장소로 내보내서 azure 외부에서 내보내기 위해 azure Event Hubs를 보관 합니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: edaa585ffb3448a80b021aa924a9d654ac829931
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668964"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Azure 활동 로그를 storage 또는 Azure Event Hubs로 내보내기

> [!IMPORTANT]
> Azure 활동 로그를 Azure Storage 및 Azure Event Hubs 전송 하는 메서드가 [진단 설정](diagnostic-settings.md)으로 변경 되었습니다. 이 문서에서는 더 이상 사용 되지 않는 레거시 방법을 설명 합니다. 비교를 보려면 [Azure 활동 로그 수집 및 내보내기](diagnostic-settings-legacy.md) 에 대 한 업데이트를 참조 하세요.


Azure [활동 로그](platform-logs-overview.md) 는 azure 구독에서 발생 한 구독 수준 이벤트에 대 한 통찰력을 제공 합니다. Azure Portal에서 활동 로그를 보거나 Azure Monitor에서 수집 된 다른 데이터를 사용 하 여 분석할 수 있는 Log Analytics 작업 영역으로 복사 하는 것 외에도 활동 로그를 Azure storage 계정에 보관 하는 로그 프로필을 만들거나  이벤트 허브.

## <a name="archive-activity-log"></a>활동 로그 보관
저장소 계정에 활동 로그를 보관 하는 것은 감사, 정적 분석 또는 백업을 위해 90 일 (보존 정책에 대 한 모든 권한 포함) 보다 오래 된 로그 데이터를 보존 하려는 경우에 유용 합니다. 90 일 이하로만 이벤트를 유지 해야 하는 경우 활동 로그 이벤트는 90 일 동안 Azure 플랫폼에서 유지 되므로 저장소 계정에 보관을 설정할 필요가 없습니다.

## <a name="stream-activity-log-to-event-hub"></a>활동 로그를 이벤트 허브로 스트림
[Azure Event Hubs](/azure/event-hubs/) 는 초당 수백만 개의 이벤트를 수신 하 고 처리할 수 있는 데이터 스트리밍 플랫폼 및 이벤트 수집 서비스입니다. 이벤트 허브로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. 활동 로그의 스트리밍 기능을 사용할 수 있는 두 가지 방법은 다음과 같습니다.
* **타사 로깅 및 원격 분석 시스템으로 스트림**: 시간이 지나면서 Azure Event Hubs 스트리밍은 활동 로그를 타사 SIEM 및 로그 분석 솔루션으로 파이핑하기 위한 메커니즘이 되고 있습니다.
* **사용자 지정 원격 분석 및 로깅 플랫폼 빌드**: 사용자 지정 빌드 원격 분석 플랫폼이 이미 있거나 플랫폼 빌드에 대해 생각하고 있는 경우 Event Hubs의 확장성 높은 게시-구독 특성을 통해 활동 로그를 유연하게 수집할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="storage-account"></a>스토리지 계정
활동 로그를 보관 하는 경우 [저장소 계정을 만들어야](../../storage/common/storage-account-create.md) 합니다 (아직 없는 경우). 모니터링 데이터에 대 한 액세스를 더 잘 제어할 수 있도록 다른 모니터링 되지 않는 데이터가 저장 되어 있는 기존 저장소 계정을 사용해 서는 안 됩니다. 그러나 저장소 계정에 로그 및 메트릭을 보관 하는 경우에도 동일한 저장소 계정을 사용 하 여 모든 모니터링 데이터를 중앙 위치에 유지 하도록 선택할 수 있습니다.

설정을 구성하는 사용자가 두 구독에 대한 적절한 RBAC 액세스를 가진 경우 스토리지 계정은 로그를 내보내는 구독과 동일한 구독을 가지고 있지 않아도 됩니다. 

> [!TIP]
> 보안 가상 네트워크 뒤의 저장소 계정에 대 한 액세스를 제공 하려면 [Azure Storage 방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) 을 참조 하세요.

### <a name="event-hubs"></a>Event Hubs
활동 로그를 event hub에 전송 하는 경우에는 [이벤트 허브를 만들어야](../../event-hubs/event-hubs-create.md) 합니다 (아직 없는 경우). 이전에 활동 로그 이벤트를이 Event Hubs 네임 스페이스로 스트리밍하는 경우 해당 이벤트 허브가 다시 사용 됩니다.

공유 액세스 정책은 스트리밍 메커니즘에서 보유하는 권한을 정의합니다. Event Hubs로 스트리밍하려면 관리, 보내기 및 수신 권한이 필요 합니다. Event Hubs 네임 스페이스의 구성 탭에 있는 Azure Portal에서 Event Hubs 네임 스페이스에 대 한 공유 액세스 정책을 만들거나 수정할 수 있습니다.

스트리밍을 포함 하도록 활동 로그 로그 프로필을 업데이트 하려면 해당 Event Hubs 권한 부여 규칙에 대 한 ListKey 권한이 있어야 합니다. 설정을 구성하는 사용자에게 구독 모두에 액세스할 수 있는 적절한 RBAC 액세스 권한이 있다면 Event Hubs 네임스페이스가 로그를 내보내는 구독과 동일한 구독에 위치하지 않아도 됩니다. 구독은 모두 동일한 AAD 테넌트에 위치합니다.

[로그 프로필을 만들어](#create-a-log-profile)활동 로그를 이벤트 허브로 스트리밍합니다.

## <a name="create-a-log-profile"></a>로그 프로필 만들기
**로그 프로필**을 사용 하 여 Azure 활동 로그를 내보내는 방법을 정의 합니다. 각 Azure 구독에는 로그 프로필이 하나만 있을 수 있습니다. 이러한 설정은 포털의 활동 로그 블레이드에서 **내보내기** 옵션을 통해 구성할 수 있습니다. [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell cmdlet 또는 CLI를 사용하여 프로그래밍 방식으로 구성할 수도 있습니다.

로그 프로필은 다음을 정의 합니다.

**활동 로그를 보내야 하는 위치입니다.** 현재 사용할 수 있는 옵션은 저장소 계정 또는 Event Hubs입니다.

**보낼 이벤트 범주입니다.** 로그 프로필 및 활동 로그 이벤트에서 *범주의* 의미는 다릅니다. 로그 프로필에서 *범주* 는 작업 유형 (쓰기, 삭제, 작업)을 나타냅니다. 활동 로그 이벤트에서 *category*"* 속성은 이벤트의 원본 또는 유형을 나타냅니다 (예: 관리, Servicehealth 및 Alert).

**내보내려는 영역 (위치)입니다.** 활동 로그의 많은 이벤트가 글로벌 이벤트 이므로 모든 위치를 포함 해야 합니다.

**저장소 계정에 활동 로그를 보존 해야 하는 기간입니다.** 보존 기간이 0일이라는 것은 로그가 영원히 보관된다는 의미입니다. 그렇지 않으면 값은 1에서 365 사이의 일 수 있습니다.

보존 정책이 설정 되었지만 저장소 계정에 로그를 저장 하지 않은 경우 보존 정책은 적용 되지 않습니다. 보존 정책은 매일 적용되므로 하루의 마지막에(UTC) 보존 정책이 지난 날의 로그가 삭제됩니다. 예를 들어, 하루의 보존 정책이 있는 경우 오늘 날짜가 시작될 때 하루 전의 로그가 삭제됩니다. 삭제 프로세스는 자정(UTC)에 시작되지만, 스토리지 계정에서 로그가 삭제될 때까지 최대 24시간이 걸릴 수 있습니다.


> [!IMPORTANT]
> Microsoft Insights 리소스 공급자가 등록 되지 않은 경우 로그 프로필을 만들 때 오류가 나타날 수 있습니다. 이 공급자를 등록 하려면 [Azure 리소스 공급자 및 형식](../../azure-resource-manager/management/resource-providers-and-types.md) 을 참조 하세요.


### <a name="create-log-profile-using-the-azure-portal"></a>Azure Portal를 사용 하 여 로그 프로필 만들기

Azure Portal에서 **이벤트 허브로 내보내기** 옵션을 사용 하 여 로그 프로필을 만들거나 편집 합니다.

1. Azure Portal **Azure Monitor** 메뉴에서 **활동 로그**를 선택 합니다.
3. **진단 설정**을 클릭합니다.

   ![진단 설정](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 레거시 환경에 대해 자주색 배너를 클릭 합니다.

    ![레거시 환경](media/diagnostic-settings-subscription/legacy-experience.png)

3. 표시 되는 블레이드에서 다음을 지정 합니다.
   * 내보낼 이벤트가 있는 지역입니다. 활동 로그는 전역 (비 지역) 로그 이므로 키 이벤트를 누락 하지 않도록 모든 지역을 선택 하 여 대부분의 이벤트에 연결 된 지역이 없도록 해야 합니다.
   * 저장소 계정에 쓰려면 다음과 같이 합니다.
       * 이벤트를 저장할 저장소 계정입니다.
       * 이러한 이벤트를 저장소에 유지 하려는 일 수입니다. 0일로 설정하면 로그를 계속 유지합니다.
   * 이벤트 허브에 기록 하려면:
       * 이러한 이벤트 스트리밍을 위해 이벤트 허브를 만들 Service Bus 네임 스페이스입니다.

     ![활동 로그 내보내기 블레이드](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. **저장**을 클릭하여 이러한 설정을 저장합니다. 해당 설정이 구독에 즉시 적용됩니다.


### <a name="configure-log-profile-using-powershell"></a>PowerShell을 사용 하 여 로그 프로필 구성

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

로그 프로필이 이미 있는 경우 먼저 기존 로그 프로필을 제거한 다음 새 로그 프로필을 만들어야 합니다.

1. `Get-AzLogProfile`를 사용하여 로그 프로필이 있는지 확인합니다.  로그 프로필이 있는 경우 *name* 속성을 적어둡니다.

1. `Remove-AzLogProfile`name*속성의 값을 사용하여 로그 프로필을 제거하려면*을 사용합니다.

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
    | 속성 |yes |로그 프로필의 이름입니다. |
    | StorageAccountId |예 |활동 로그를 저장 해야 하는 저장소 계정의 리소스 ID입니다. |
    | serviceBusRuleId |예 |이벤트 허브를 만들 Service Bus 네임스페이스의 Service Bus 규칙 ID입니다. `{service bus resource ID}/authorizationrules/{key name}`형식의 문자열입니다. |
    | 위치 |yes |활동 로그 이벤트를 수집할 쉼표로 구분된 지역 목록입니다. |
    | RetentionInDays |yes |저장소 계정에서 이벤트를 보존 해야 하는 기간 (일)입니다 (1에서 365 사이). 0 값은 로그를 무기한 저장합니다. |
    | Category |예 |수집할 쉼표로 구분된 이벤트 범주 목록입니다. 가능한 값은 _쓰기_, _삭제_및 _동작_입니다. |

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
2. `az monitor log-profiles delete --name "<log profile name>`name*속성의 값을 사용하여 로그 프로필을 제거하려면*을 사용합니다.
3. `az monitor log-profiles create`를 사용하여 새 로그 프로필을 만듭니다.

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | 속성 | 필수 | Description |
    | --- | --- | --- |
    | name |yes |로그 프로필의 이름입니다. |
    | storage-account-id |yes |활동 로그를 저장할 Storage 계정의 리소스 ID입니다. |
    | 위치 |yes |활동 로그 이벤트를 수집할 공백으로 구분된 지역 목록입니다. `az account list-locations --query [].name`을 사용하여 구독에 대한 모든 지역 목록을 볼 수 있습니다. |
    | 일 |yes |이벤트를 유지 해야 하는 기간 (일)입니다 (1에서 365 사이). 0 값은 로그를 무기한(영원히) 저장합니다.  0 인 경우 enabled 매개 변수를 false로 설정 해야 합니다. |
    |사용 | yes |True 또는 False입니다.  보존 정책을 사용하거나 비활성화하는 데 사용합니다.  True이면 일 매개 변수 0보다 큰 값이어야 합니다.
    | 범주 |yes |수집해야 할 공백으로 구분된 이벤트 범주 목록입니다. 가능한 값은 쓰기, 삭제 및 작업입니다. |



## <a name="next-steps"></a>다음 단계

* [활동 로그에 대한 자세한 내용](../../azure-resource-manager/management/view-activity-logs.md)
* [Azure Monitor 로그에 활동 로그 수집](activity-log-collect.md)
