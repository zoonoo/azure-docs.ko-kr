---
title: 진단 설정을 사용하여 플랫폼 메트릭 및 로그를 수집하고 Azure에서
description: 진단 설정을 사용하여 Azure Monitor 플랫폼 메트릭 및 로그를 Azure 모니터 로그, Azure 저장소 또는 Azure 이벤트 허브로 보냅니다.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681232"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Azure에서 리소스 로그 및 메트릭을 수집하는 진단 설정 만들기

Azure 활동 로그 및 리소스 로그를 포함한 Azure의 [플랫폼 로그는](platform-logs-overview.md) Azure 리소스 및 해당 플랫폼에 대한 자세한 진단 및 감사 정보를 제공합니다. [플랫폼 메트릭은](data-platform-metrics.md) 기본적으로 수집되며 일반적으로 Azure Monitor 메트릭 데이터베이스에 저장됩니다.

이 문서에서는 플랫폼 메트릭 및 플랫폼 로그를 다른 대상으로 전송하기 위한 진단 설정을 만들고 구성하는 방법에 대한 세부 정보를 제공합니다.

> [!IMPORTANT]
> 활동 로그를 수집하는 진단 설정을 만들기 전에 먼저 레거시 구성을 사용하지 않도록 설정해야 합니다. 자세한 내용은 [레거시 설정이 있는 Azure 활동 로그 수집을](diagnostic-settings-legacy.md) 참조하십시오.

각 Azure 리소스에는 다음 기준을 정의하는 자체 진단 설정이 필요합니다.

- 설정에 정의된 대상으로 전송되는 로그 및 메트릭 데이터의 범주. 사용 가능한 범주는 리소스 유형에 따라 달라집니다.
- 로그를 보낼 하나 이상의 대상입니다. 현재 대상에는 Log Analytics 작업 영역, Event Hubs 및 Azure Storage가 포함됩니다.

단일 진단 설정은 각 대상 중 하나만 정의할 수 있습니다. 데이터를 2개 이상의 특정 대상 유형(예: 두 개의 다른 Log Analytics 작업 영역) 으로 보내려면 여러 개의 설정을 만듭니다. 각 리소스는 진단 설정을 5개까지 포함할 수 있습니다.

> [!NOTE]
> [플랫폼 메트릭은](metrics-supported.md) Azure 모니터 메트릭에 자동으로 [수집됩니다.](data-platform-metrics.md) 진단 설정은 [로그 쿼리를](../log-query/log-query-overview.md)사용하여 다른 모니터링 데이터와 함께 분석을 위해 특정 Azure 서비스에 대한 메트릭을 Azure Monitor Log로 수집하는 데 사용할 수 있습니다.

## <a name="destinations"></a>Destinations

플랫폼 로그 및 메트릭은 다음 표의 대상으로 보낼 수 있습니다. 해당 대상으로 데이터를 보내는 방법에 대한 자세한 내용은 다음 표의 각 링크를 따르십시오.

| 대상 | 설명 |
|:---|:---|
| [Log Analytics 작업 영역](resource-logs-collect-workspace.md) | 로그 분석 작업 영역에 로그 및 메트릭을 수집하면 강력한 로그 쿼리를 사용하여 Azure Monitor에서 수집한 다른 모니터링 데이터로 로그 및 메트릭을 분석하고 경고 및 시각화와 같은 다른 Azure Monitor 기능을 활용할 수 있습니다. |
| [이벤트 허브](resource-logs-stream-event-hubs.md) | 이벤트 허브로 로그 및 메트릭을 전송하면 타사 SIEM 및 기타 로그 분석 솔루션과 같은 외부 시스템으로 데이터를 스트리밍할 수 있습니다. |
| [Azure 저장소 계정](resource-logs-collect-storage.md) | Azure 저장소 계정에 로그 및 메트릭을 보관하는 것은 감사, 정적 분석 또는 백업에 유용합니다. Azure 모니터 로그 및 로그 분석 작업 영역에 비해 Azure 저장소는 비용이 적게 들며 로그를 무기한 보관할 수 있습니다. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Azure 포털에서 진단 설정 만들기

Azure 포털에서 Azure 모니터 메뉴 또는 리소스에 대한 메뉴에서 진단 설정을 구성할 수 있습니다.

1. Azure 포털에서 진단 설정을 구성하는 위치는 리소스에 따라 다릅니다.

   - 단일 리소스의 경우 리소스 메뉴에서 **모니터** 아래의 **진단 설정을** 클릭합니다.

        ![진단 설정](media/diagnostic-settings/menu-resource.png)

   - 하나 이상의 리소스에 대 한 Azure 모니터 메뉴의 **설정에서** **진단 설정을** 클릭 하 고 리소스를 클릭 합니다.

      ![진단 설정](media/diagnostic-settings/menu-monitor.png)

   - 활동 로그의 경우 **Azure 모니터** 메뉴에서 **활동 로그를 클릭한** 다음 **진단 설정을**클릭합니다. 활동 로그에 대한 레거시 구성을 비활성화해야 합니다. 자세한 내용은 [기존 설정 사용 안 함(설정)을](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) 참조하십시오.

        ![진단 설정](media/diagnostic-settings/menu-activity-log.png)

2. 선택한 리소스에 설정이 없는 경우, 설정을 만들라는 메시지가 표시됩니다. **진단 설정 추가**를 클릭합니다.

   ![진단 설정 추가 - 기존 설정 없음](media/diagnostic-settings/add-setting.png)

   리소스에 기존 설정이 있는 경우 이미 구성된 설정 목록이 표시됩니다. 진단 **추가 설정을** 클릭하여 새 설정을 추가하거나 **편집 설정을 클릭하여** 기존 설정을 편집합니다. 각 설정에는 각 대상 유형 중 하나만 있을 수 있습니다.

   ![진단 설정 추가 - 기존 설정](media/diagnostic-settings/edit-setting.png)

3. 아직 이름이 없는 경우 설정을 지정합니다.

    ![진단 설정 추가](media/diagnostic-settings/setting-new-blank.png)

4. **범주 세부 정보(라우팅 대상)** - 나중에 지정된 목적지로 보낼 각 데이터 범주에 대한 확인란을 선택합니다. 범주 목록은 Azure 서비스에 따라 다릅니다.

     - **AllMetrics는** 리소스의 플랫폼 메트릭을 Azure Logs 저장소에 라우팅하지만 로그 형식으로 라우팅합니다. 이러한 메트릭은 일반적으로 Azure Monitor 메트릭 타임시리즈 데이터베이스로만 전송됩니다. 로그 애널리틱스를 통해 검색할 수 있는 Azure Monitor Logs 저장소로 보내면 다른 로그를 검색하는 쿼리에 통합할 수 있습니다. 이 옵션은 모든 리소스 유형에 사용하지 못할 수 있습니다. 지원되는 경우 [Azure Monitor 지원메트릭은](metrics-supported.md) 어떤 리소스 유형에 대해 수집되는 메트릭을 나열합니다.

       > [!NOTE]
       > 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
       >
       > *예:* 블록 체인의 'IOReadBytes' 메트릭을 노드별 수준에서 탐색하고 차트로 만들 수 있습니다. 그러나 진단 설정을 통해 내보낼 때 내보낸 메트릭은 모든 노드에 대해 모든 읽기 바이트로 표시됩니다.

     - **로그에는** 리소스 유형에 따라 사용할 수 있는 다양한 범주가 나열됩니다. 목적지로 라우팅할 범주를 확인합니다.

5. **대상 세부 정보** - 각 목적지에 대한 확인란을 선택합니다. 각 확인란을 선택하면 추가 정보를 추가할 수 있는 옵션이 나타납니다.

      ![로그 분석 또는 이벤트 허브로 보내기](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **로그 분석** - 구독 및 작업 영역을 입력합니다.  작업 영역이 없는 경우 [를 계속하기 전에 작업](../learn/quick-create-workspace.md)영역을 만들어야 합니다.

    1. 이벤트 허브 - 다음 조건을 **지정합니다.**
       - 이벤트 허브의 일부인 구독
       - 이벤트 허브 네임스페이스 - 아직 이벤트 허브 네임스페이스가 없는 경우 이벤트 허브 네임스페이스를 [만들어야](../../event-hubs/event-hubs-create.md) 합니다.
       - 모든 데이터를 보낼 이벤트 허브 이름(선택 사항)입니다. 이름을 지정하지 않으면 각 로그 범주에 대해 이벤트 허브가 만들어집니다. 여러 범주를 보내는 경우 만든 이벤트 허브 수를 제한하는 이름을 지정할 수 있습니다. 자세한 내용은 [Azure 이벤트 허브 할당량 및 제한을](../../event-hubs/event-hubs-quotas.md) 참조하십시오.
       - 이벤트 허브 정책(선택 사항) 정책은 스트리밍 메커니즘에 있는 권한을 정의합니다. 자세한 내용은 [이벤트 허브 기능을](../../event-hubs/event-hubs-features.md#publisher-policy)참조하십시오.

    1. **저장소** - 구독, 저장소 계정 및 보존 정책을 선택합니다.

        ![저장소로 보내기](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > 보존 정책을 0으로 설정하고 나중에 혼동을 피하기 위해 예약된 작업을 사용하여 저장소에서 데이터를 수동으로 삭제하는 것이 좋습니다.
        >
        > 첫째, 보관을 위해 저장소를 사용하는 경우 일반적으로 365일 이상 데이터를 원합니다. 둘째, 0보다 큰 보존 정책을 선택하면 저장소 시 로그에 만료 날짜가 첨부됩니다. 저장된 로그의 날짜는 변경할 수 없습니다.
        >
        > 예를 들어 *WorkflowRuntime에* 대한 보존 정책을 180일로 설정한 다음 24시간 후에 365일로 설정하면 처음 24시간 동안 저장된 로그는 180일 후에 자동으로 삭제되고 해당 유형의 모든 후속 로그는 365일 후에 자동으로 삭제됩니다. 나중에 보존 정책을 변경해도 처음 24시간 동안 로그는 365일 동안 유지되지 않습니다.

6. **저장**을 클릭합니다.

잠시 후 새 설정이 이 리소스의 설정 목록에 나타나고 새 이벤트 데이터가 생성될 때 로그가 지정된 대상으로 스트리밍됩니다. 이벤트가 내보내지는 시점과 [로그 분석 작업 영역에 표시되는](data-ingestion-time.md)시점 사이에 최대 15분이 걸릴 수 있습니다.

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

- [Azure 플랫폼 로그에 대해 자세히 알아보기](platform-logs-overview.md)
