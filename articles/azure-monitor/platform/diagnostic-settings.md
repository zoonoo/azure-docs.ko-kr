---
title: Azure에서 로그 및 메트릭을 수집하는 진단 설정 만들기
description: Azure 플랫폼 로그를 Azure 모니터 로그, Azure 저장소 또는 Azure 이벤트 허브로 전달하는 진단 설정을 만듭니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fb2f9ff5af68575d9f9d29e9a6aca83d603395b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672415"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>진단 설정을 만들어 Azure에서 플랫폼 로그 및 메트릭을 수집합니다.
Azure 활동 로그 및 리소스 로그를 포함한 [Azure의 플랫폼 로그는](platform-logs-overview.md) Azure 리소스 및 해당 플랫폼에 대한 자세한 진단 및 감사 정보를 제공합니다. 이 문서에서는 플랫폼 로그를 다른 대상으로 보내기 위한 진단 설정을 만들고 구성하는 방법에 대한 세부 정보를 제공합니다.

> [!IMPORTANT]
> 활동 로그를 수집하는 진단 설정을 만들기 전에 먼저 레거시 구성을 사용하지 않도록 설정해야 합니다. 자세한 내용은 [레거시 설정이 있는 Azure 활동 로그 수집을](diagnostic-settings-legacy.md) 참조하십시오.

각 Azure 리소스에는 다음을 정의하는 자체 진단 설정이 필요합니다.

- 설정에 정의된 대상으로 전송되는 로그 및 메트릭 데이터의 범주. 사용 가능한 범주는 리소스 유형에 따라 달라집니다.
- 로그를 보낼 하나 이상의 대상입니다. 현재 대상에는 Log Analytics 작업 영역, Event Hubs 및 Azure Storage가 포함됩니다.
 
단일 진단 설정은 각 대상 중 하나만 정의할 수 있습니다. 데이터를 2개 이상의 특정 대상 유형(예: 두 개의 다른 Log Analytics 작업 영역) 으로 보내려면 여러 개의 설정을 만듭니다. 각 리소스는 진단 설정을 5개까지 포함할 수 있습니다.


> [!NOTE]
> [플랫폼 메트릭은](metrics-supported.md) Azure 모니터 메트릭에 자동으로 [수집됩니다.](data-platform-metrics.md) 진단 설정은 [로그 쿼리를](../log-query/log-query-overview.md)사용하여 다른 모니터링 데이터와 함께 분석을 위해 특정 Azure 서비스에 대한 메트릭을 Azure Monitor Log로 수집하는 데 사용할 수 있습니다.

## <a name="destinations"></a>Destinations 
플랫폼 로그는 다음 표의 대상으로 보낼 수 있습니다. 각 대상에 대한 구성은 이 문서에서 설명하는 진단 설정을 만들기 위해 동일한 프로세스를 사용하여 수행됩니다. 해당 대상으로 데이터를 보내는 방법에 대한 자세한 내용은 다음 표의 각 링크를 따르십시오.

| 대상 | 설명 |
|:---|:---|
| [로그 분석 작업 영역](resource-logs-collect-workspace.md) | 로그 분석 작업 영역에 로그를 수집하면 강력한 로그 쿼리를 사용하여 Azure Monitor에서 수집한 다른 모니터링 데이터로 로그를 분석하고 경고 및 시각화와 같은 다른 Azure Monitor 기능을 활용할 수 있습니다. |
| [이벤트 허브](resource-logs-stream-event-hubs.md) | 이벤트 허브로 로그를 전송하면 타사 SIEM 및 기타 로그 분석 솔루션과 같은 외부 시스템으로 데이터를 스트리밍할 수 있습니다. |
| [Azure 저장소 계정](resource-logs-collect-storage.md) | Azure 저장소 계정에 로그를 보관하는 것은 감사, 정적 분석 또는 백업에 유용합니다. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Azure 포털에서 진단 설정 만들기
Azure 포털에서 Azure 모니터 메뉴 또는 리소스에 대한 메뉴에서 진단 설정을 구성할 수 있습니다.

1. Azure 포털에서 진단 설정을 구성하는 위치는 리소스에 따라 다릅니다.

   - 단일 리소스의 경우 리소스 메뉴에서 **모니터** 아래의 **진단 설정을** 클릭합니다.

        ![진단 설정](media/diagnostic-settings/menu-resource.png)

    - 하나 이상의 리소스에 대 한 Azure 모니터 메뉴의 **설정에서** **진단 설정을** 클릭 하 고 리소스를 클릭 합니다.
    
        ![진단 설정](media/diagnostic-settings/menu-monitor.png)

    - 활동 로그의 경우 **Azure 모니터** 메뉴에서 **활동 로그를 클릭한** 다음 **진단 설정을**클릭합니다. 활동 로그에 대한 레거시 구성을 비활성화해야 합니다. 자세한 내용은 [기존 설정 사용 안 함(설정)을](diagnostic-settings-legacy.md#disable-existing-settings) 참조하십시오.

        ![진단 설정](media/diagnostic-settings/menu-activity-log.png)

2. 선택한 리소스에 설정이 없는 경우, 설정을 만들라는 메시지가 표시됩니다. **진단 설정 추가**를 클릭합니다.

   ![진단 설정 추가 - 기존 설정 없음](media/diagnostic-settings/add-setting.png)

   리소스에 기존 설정이 있는 경우 이미 구성된 설정 목록이 표시됩니다. 진단 **추가 설정을** 클릭하여 새 설정을 추가하거나 **편집 설정을 클릭하여** 기존 설정을 편집합니다. 각 설정에는 각 대상 유형 중 하나만 있을 수 있습니다.

   ![진단 설정 추가 - 기존 설정](media/diagnostic-settings/edit-setting.png)

3. 아직 이름이 없는 경우 설정을 지정합니다.
4. 로그를 보낼 각 대상에 대한 확인란을 선택합니다. 다음 표에 설명된 대로 설정을 지정하려면 **구성을** 클릭합니다.

    | 설정 | 설명 |
    |:---|:---|
    | Log Analytics 작업 영역 | 작업 영역의 이름입니다. |
    | 스토리지 계정 | 저장소 계정의 이름입니다. |
    | 이벤트 허브 네임스페이스 | 이벤트 허브가 만들어지거나(이 경우 처음으로 스트리밍 로그를 하는 경우) 또는 스트리밍된 네임스페이스(해당 로그 범주를 이 네임스페이스로 스트리밍하는 리소스가 이미 있는 경우)입니다.
    | 이벤트 허브 이름 | 선택적으로 설정의 모든 데이터를 보낼 이벤트 허브 이름을 지정합니다. 이름을 지정하지 않으면 각 로그 범주에 대해 이벤트 허브가 만들어집니다. 여러 범주를 보내는 경우 만든 이벤트 허브 수를 제한하는 이름을 지정할 수 있습니다. 자세한 내용은 [Azure 이벤트 허브 할당량 및 제한을](../../event-hubs/event-hubs-quotas.md) 참조하십시오. |
    | 이벤트 허브 정책 이름 | 스트리밍 메커니즘에 있는 권한을 정의합니다. |

    ![진단 설정 추가 - 기존 설정](media/diagnostic-settings/setting-details.png)

5. 지정된 대상으로 보낼 각 데이터 범주에 대한 확인란을 선택합니다. 범주 목록은 Azure 서비스에 따라 다릅니다.

   > [!NOTE]
   > 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
   >
   > *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.

6. **저장**을 클릭합니다.

잠시 후 새 설정이 이 리소스의 설정 목록에 나타나고 새 이벤트 데이터가 생성될 때 로그가 지정된 대상으로 스트리밍됩니다. 이벤트가 내보내지는 시점과 [로그 분석 작업 영역에 표시되는](data-ingestion-time.md)시점 사이에는 최대 15분이 있을 수 있습니다.



## <a name="create-diagnostic-settings-using-powershell"></a>PowerShell을 사용하여 진단 설정 만들기
[설정-AzDiagnosticSet](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) cmdlet을 사용 하 여 [Azure PowerShell](powershell-quickstart-samples.md)을 사용 하 여 진단 설정을 만듭니다. 매개 변수에 대한 설명은 이 cmdlet에 대한 설명서를 참조하십시오.

> [!IMPORTANT]
> Azure 활동 로그에 이 메서드를 사용할 수 없습니다. 대신 [리소스 관리자 템플릿을 사용하여 Azure 모니터에서 진단 만들기 설정을](diagnostic-settings-template.md) 사용하여 리소스 관리자 템플릿을 만들고 PowerShell으로 배포합니다.

다음은 세 가지 대상을 모두 사용하여 진단 설정을 만드는 PowerShell cmdlet의 예입니다.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Azure CLI를 사용하여 진단 설정 만들기
az [모니터 진단-만들기](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) 명령을 사용하여 [Azure CLI를](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)사용하여 진단 설정을 만듭니다. 매개 변수에 대한 설명은 이 명령의 설명서를 참조하십시오.

> [!IMPORTANT]
> Azure 활동 로그에 이 메서드를 사용할 수 없습니다. 대신 [리소스 관리자 템플릿을 사용하여 Azure 모니터에서 진단 만들기 설정을](diagnostic-settings-template.md) 사용하여 리소스 관리자 템플릿을 만들고 CLI로 배포합니다.

다음은 세 가지 대상을 모두 사용하여 진단 설정을 만드는 CLI 명령예제입니다.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>REST API를 사용하여 진단 설정 구성
[Azure 모니터 REST API를](https://docs.microsoft.com/rest/api/monitor/)사용하여 진단 설정을 만들거나 업데이트하려면 [진단 설정을](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) 참조하십시오.


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>리소스 관리자 템플릿을 사용하여 진단 설정 구성
[리소스 관리자 템플릿을 사용하여 Azure 모니터에서 진단 설정 만들기를](diagnostic-settings-template.md) 참조하여 리소스 관리자 템플릿을 사용하여 진단 설정을 만들거나 업데이트합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 플랫폼 로그에 대해 자세히 알아보기](platform-logs-overview.md)
