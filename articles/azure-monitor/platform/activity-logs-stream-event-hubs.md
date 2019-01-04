---
title: Azure 활동 로그를 Event Hubs로 스트림
description: Azure 활동 로그를 Event Hubs로 스트림하는 방법에 대해 알아봅니다.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: b58b7c7ebc3547153f805f762c4caf3511a5a709
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717504"
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Azure 활동 로그를 Event Hubs로 스트림
다음을 수행하여 모든 애플리케이션에 거의 실시간으로 [Azure 활동 로그](../../azure-monitor/platform/activity-logs-overview.md)를 스트리밍할 수 있습니다.

* 포털에서 기본 제공 **내보내기** 옵션 사용
* Azure PowerShell cmdlet 또는 Azure CLI를 통해 로그 프로필에서 Azure Service Bus 규칙 ID를 사용하도록 설정

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>활동 로그 및 Event Hubs에서 수행할 수 있는 작업
활동 로그의 스트리밍 기능을 사용할 수 있는 두 가지 방법은 다음과 같습니다.

* **타사 로깅 및 원격 분석 시스템으로 스트리밍**: 시간이 지나면서 Azure Event Hubs 스트리밍은 활동 로그를 타사 SIEM 및 로그 분석 솔루션으로 파이핑하기 위한 메커니즘이 되고 있습니다.
* **사용자 지정 원격 분석 및 로깅 플랫폼 빌드**: 사용자 지정 빌드 원격 분석 플랫폼이 이미 있거나 플랫폼 빌드에 대해 생각하고 있는 경우 Event Hubs의 확장성 높은 게시-구독 특성을 통해 활동 로그를 유연하게 수집할 수 있습니다. 자세한 내용은 [글로벌 확장 원격 분석 플랫폼에 Event Hubs 사용에 대한 Dan Rosanova의 비디오](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)를 참조하세요.

## <a name="enable-streaming-of-the-activity-log"></a>활동 로그의 스트리밍 사용
프로그래밍 방식이나 포털을 통해 활동 로그의 스트리밍을 사용하도록 설정할 수 있습니다. 두 방법 모두, 해당 네임스페이스에 대한 Event Hubs 네임스페이스 및 공유 액세스 정책을 선택합니다. 첫 번째 새 활동 로그 이벤트가 발생하면 이름이 해당 네임스페이스에서 insights-logs-operationallogs인 이벤트 허브가 생성됩니다. 

Event Hubs 네임스페이스가 없는 경우 먼저 만들어야 합니다. 이전에 활동 로그 이벤트를 이 Event Hubs 네임스페이스로 스트리밍한 경우 이전에 만든 이벤트 허브를 다시 사용합니다. 

공유 액세스 정책은 스트리밍 메커니즘에서 보유하는 권한을 정의합니다. 현재, Event Hubs로 스트리밍하려면 **관리**, **보내기** 및 **수신** 권한이 필요합니다. Azure Portal에서 Event Hubs 네임스페이스에 대한 **구성** 탭 아래에서 Event Hubs 네임스페이스에 대한 공유 액세스 정책을 만들거나 수정할 수 있습니다. 

스트리밍을 포함할 활동 로그의 로그 프로필을 업데이트하려면 변경하는 사용자에게 Event Hubs 권한 부여 규칙에 대한 ListKey 권한이 있어야 합니다. 설정을 구성하는 사용자에게 구독 모두에 액세스할 수 있는 적절한 RBAC 액세스 권한이 있다면 Event Hubs 네임스페이스가 로그를 내보내는 구독과 동일한 구독에 위치하지 않아도 됩니다. 구독은 모두 동일한 AAD 테넌트에 위치합니다.

### <a name="via-the-azure-portal"></a>Azure Portal을 통해
1. 포털 왼쪽에 있는 **모든 서비스** 검색을 사용하여 **활동 로그** 섹션으로 이동합니다.
   
   ![포털의 서비스의 목록에서 활동 로그 선택](./media/activity-logs-stream-event-hubs/activity-logs-portal-navigate-v2.png)
2. 로그 맨 위에서 **이벤트 허브로 내보내기** 단추를 선택합니다.
   
   ![포털의 내보내기 단추](./media/activity-logs-stream-event-hubs/activity-logs-portal-export-v2.png)

   이전 보기에서 활동 로그를 볼 때 적용한 필터 설정은 내보내기 설정에 영향을 주지 않습니다. 포털에서 활동 로그를 찾아보는 동안 표시되는 내용만 필터링됩니다.
3. 표시되는 섹션에서 **모든 지역**을 선택합니다. 특정 지역을 선택하지 마세요.
   
   ![내보내기 섹션](./media/activity-logs-stream-event-hubs/export-audit.png)

   > [!WARNING]  
   > **모든 지역** 이외의 지역을 선택하면 수신해야 하는 중요 이벤트가 누락될 수 있습니다. 활동 로그는 글로벌(비지역) 로그이므로 대부분의 이벤트에는 연결된 지역이 없습니다. 
   >

4. **Azure Event Hubs** 옵션을 클릭하고, 로그를 보내야 하는 Event Hubs 네임스페이스를 선택한 다음, **확인**을 클릭합니다.
5. **저장**을 선택하여 이러한 설정을 저장합니다. 해당 설정이 구독에 즉시 적용됩니다.
6. 여러 구독이 있는 경우 이 작업을 반복하고 모든 데이터를 동일한 Event Hub로 보냅니다.

### <a name="via-powershell-cmdlets"></a>PowerShell cmdlet을 통해
로그 프로필이 이미 있으면 먼저 기존 로그 프로필을 제거한 다음, 새 로그 프로필을 생성해야 합니다.

1. `Get-AzureRmLogProfile`를 사용하여 로그 프로필이 있는지 확인합니다.  로그 프로필이 존재하는 경우 *이름* 속성에 있습니다.
2. *name* 속성의 값을 사용하여 로그 프로필을 제거하려면 `Remove-AzureRmLogProfile`을 사용합니다.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzureRmLogProfile -Name "default"
    ```
3. `Add-AzureRmLogProfile`를 사용하여 새 로그 프로필을 만듭니다.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>Azure CLI를 통해
로그 프로필이 이미 있으면 먼저 기존 로그 프로필을 제거한 다음, 새 로그 프로필을 생성해야 합니다.

1. `az monitor log-profiles list`를 사용하여 로그 프로필이 있는지 확인합니다.
2. *name* 속성의 값을 사용하여 로그 프로필을 제거하려면 `az monitor log-profiles delete --name "<log profile name>`을 사용합니다.
3. `az monitor log-profiles create`를 사용하여 새 로그 프로필을 만듭니다.

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>Event Hubs에서 로그 데이터 사용
활동 로그에 대한 스키마는 [Azure 활동 로그로 구독 활동 모니터링](../../azure-monitor/platform/activity-logs-overview.md)에서 사용할 수 있습니다. 각 이벤트는 *레코드*라는 JSON Blob의 배열입니다.

## <a name="next-steps"></a>다음 단계
* [저장소 계정에 활동 로그 보관](../../azure-monitor/platform/archive-activity-log.md)
* [Azure 활동 로그 개요 알아보기](../../azure-monitor/platform/activity-logs-overview.md)
* [활동 로그 이벤트를 기반으로 경고 설정](../../azure-monitor/platform/alerts-log-webhook.md)

