---
title: Azure에서 로그 및 메트릭을 수집 하는 진단 설정 만들기 | Microsoft Docs
description: Azure 플랫폼 로그를 Azure Monitor 로그, Azure storage 또는 Azure Event Hubs에 전달 하는 진단 설정을 만듭니다.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: b90e5ccf38e95d33c4b5b6f3b8da0e91a4facb5a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023737"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Azure에서 플랫폼 로그 및 메트릭을 수집 하는 진단 설정 만들기
Azure의 [플랫폼 로그](resource-logs-overview.md) 는 azure 리소스 및 해당 리소스가 종속 된 azure 플랫폼에 대 한 자세한 진단 및 감사 정보를 제공 합니다. 이 문서에서는 다양 한 대상에 플랫폼 로그를 수집 하는 진단 설정을 만들고 구성 하는 방법에 대해 자세히 설명 합니다.

각 Azure 리소스에는 자체 진단 설정이 필요 합니다. 진단 설정은 해당 리소스에 대해 다음을 정의 합니다.

- 설정에 정의 된 대상에 전송 된 로그 및 메트릭 데이터의 범주입니다. 사용 가능한 범주는 리소스 유형에 따라 달라 집니다.
- 로그를 보낼 하나 이상의 대상입니다. 현재 대상 Log Analytics 작업 영역, Event Hubs 및 Azure Storage를 포함 합니다.
- Azure Storage에 저장 된 데이터에 대 한 보존 정책입니다.
 
단일 진단 설정은 각 대상 중 하나를 정의할 수 있습니다. 데이터를 특정 대상 유형 (예: 두 개의 다른 Log Analytics 작업 영역) 중 하나 이상으로 전송 하려면 여러 설정을 만듭니다. 각 리소스는 진단 설정을 5 개까지 포함할 수 있습니다.

> [!NOTE]
> 활동 로그는 다른 플랫폼 로그와 동일한 대상으로 전달 될 수 있지만 진단 설정으로 구성 되지 않습니다. 자세한 내용은 [Azure의 플랫폼 로그 개요](platform-logs-overview.md#destinations) 를 참조 하세요.

> [!NOTE]
> [플랫폼 메트릭은](metrics-supported.md) [메트릭을 Azure Monitor](data-platform-metrics.md)하기 위해 자동으로 수집 됩니다. 진단 설정을 사용 하면 [로그 쿼리](../log-query/log-query-overview.md)를 사용 하 여 다른 모니터링 데이터를 분석 하기 위해 특정 Azure 서비스에 대 한 메트릭을 Azure Monitor 로그에 수집할 수 있습니다.

## <a name="destinations"></a>Destinations 
다음 표의 대상에 플랫폼 로그를 보낼 수 있습니다. 각 대상에 대 한 구성은이 문서에 설명 된 진단 설정을 만드는 동일한 프로세스를 사용 하 여 수행 됩니다. 대상에 데이터를 보내는 방법에 대 한 자세한 내용은 다음 표의 각 링크를 따르세요.

| 대상 | 설명 |
|:---|:---|
| [Log Analytics 작업 영역](resource-logs-collect-workspace.md) | Log Analytics 작업 영역에 로그를 수집 하면 강력한 로그 쿼리를 사용 하 여 Azure Monitor 수집 된 다른 모니터링 데이터로 분석 하 고 경고 및 시각화와 같은 기타 Azure Monitor 기능을 활용할 수 있습니다. |
| [Event Hubs](resource-logs-stream-event-hubs.md) | Event Hubs로 로그를 전송 하면 타사 SIEMs 및 기타 log analytics 솔루션과 같은 외부 시스템으로 데이터를 스트리밍할 수 있습니다. |
| [Azure Storage 계정](resource-logs-collect-storage.md) | Azure 저장소 계정에 로그를 보관 하는 것은 감사, 정적 분석 또는 백업에 유용 합니다. |


> [!IMPORTANT]
> Azure Data Lake Storage Gen2 계정은 현재 Azure Portal에서 유효한 옵션으로 나열 될 수 있지만 진단 설정의 대상으로 지원 되지 않습니다.

## <a name="create-diagnostic-settings-in-azure-portal"></a>Azure Portal에서 진단 설정 만들기
Azure Monitor 메뉴 또는 리소스의 메뉴에서 Azure Portal 진단 설정을 구성할 수 있습니다.

1. Azure Portal의 Azure Monitor 메뉴에서 **설정** 아래의 **진단 설정** 을 클릭 한 다음 리소스를 클릭 합니다.

    ![진단 설정](media/diagnostic-settings/menu-monitor.png)

    또는 Azure Portal의 리소스 메뉴에서 **모니터**아래에 있는 **진단 설정** 을 클릭 합니다.

    ![진단 설정](media/diagnostic-settings/menu-resource.png)

2. 선택한 리소스에 설정이 없는 경우, 설정을 만들라는 메시지가 표시됩니다. **진단 켜기**를 클릭합니다.

   ![진단 설정 추가 - 기존 설정 없음](media/diagnostic-settings/add-setting.png)

   리소스에 기존 설정이 있는 경우 이미 구성 된 설정 목록이 표시 됩니다. **진단 설정 추가** 를 클릭 하 여 새 설정을 추가 하거나 **설정을 편집** 하 여 기존 항목을 편집 합니다. 각 설정에는 대상 유형 중 하나만 지정할 수 있습니다.

   ![진단 설정 추가 - 기존 설정](media/diagnostic-settings/edit-setting.png)

3. 아직 없는 경우 설정 이름을 지정 합니다.
4. 로그를 보낼 각 대상에 대 한 확인란을 선택 합니다. **구성** 을 클릭 하 여 다음 표에 설명 된 대로 설정을 지정 합니다.

    | 설정 | 설명 |
    |:---|:---|
    | Log Analytics 작업 영역 | 작업 영역의 이름입니다. |
    | Storage 계정 | 저장소 계정의 이름입니다. |
    | 이벤트 허브 네임스페이스 | 이벤트 허브가 생성 되는 네임 스페이스 (처음으로 로그를 스트리밍하는 경우) 또는 스트리밍되는 경우 (해당 로그 범주를이 네임 스페이스로 스트리밍하는 리소스가 이미 있는 경우).
    | 이벤트 허브 이름 | 선택적으로 이벤트 허브 이름을 지정 하 여 모든 데이터를 설정으로 보냅니다. 이름을 지정 하지 않으면 각 로그 범주에 대해 이벤트 허브가 생성 됩니다. 여러 범주를 전송 하는 경우 생성 되는 이벤트 허브 수를 제한 하는 이름을 지정할 수 있습니다. 자세한 내용은 [Azure Event Hubs 할당량 및 제한을](../../event-hubs/event-hubs-quotas.md) 참조 하세요. |
    | 이벤트 허브 정책 이름 | 스트리밍 메커니즘이 포함 하는 사용 권한을 정의 합니다. |

    ![진단 설정 추가 - 기존 설정](media/diagnostic-settings/setting-details.png)

5. 지정 된 대상에 보낼 데이터의 각 범주에 대 한 확인란을 선택 합니다. **저장소 계정에 보관**하는 옵션을 선택한 경우에는 [보존 기간](resource-logs-collect-storage.md#data-retention)도 지정 해야 합니다.



> [!NOTE]
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.

4. **저장**을 클릭합니다.

잠시 후 새 설정이이 리소스에 대 한 설정 목록에 표시 되 고, 새 이벤트 데이터가 생성 될 때 로그가 지정 된 대상으로 스트리밍됩니다. 이벤트가 내보내지는 시간과 [Log Analytics 작업 영역에 표시](data-ingestion-time.md)되는 시간 사이에는 최대 15 분이 걸릴 수 있습니다.



## <a name="create-diagnostic-settings-using-powershell"></a>PowerShell을 사용 하 여 진단 설정 만들기
[Azure PowerShell](powershell-quickstart-samples.md)를 사용 하 여 진단 설정을 만들려면 [AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) cmdlet을 사용 합니다. 매개 변수에 대 한 설명은이 cmdlet에 대 한 설명서를 참조 하세요.

다음은 세 가지 대상을 모두 사용 하 여 진단 설정을 만드는 예제 PowerShell cmdlet입니다.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -RetentionEnabled $true -RetentionInDays 7 -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Azure CLI를 사용 하 여 진단 설정 만들기
[Az monitor 진단-설정 만들기](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) 명령을 사용 하 여 [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)를 사용 하 여 진단 설정을 만듭니다. 매개 변수에 대 한 설명은이 명령의 설명서를 참조 하세요.

다음은 세 개의 대상을 모두 사용 하 여 진단 설정을 만드는 예제 CLI 명령입니다.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--metrics '[{"category": "AllMetrics","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>REST API를 사용 하 여 진단 설정 구성
[Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/)를 사용 하 여 진단 설정을 만들거나 업데이트 하려면 [진단 설정](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) 을 참조 하세요.


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 진단 설정 구성
리소스 관리자 템플릿을 사용 하 여 진단 설정을 만들거나 업데이트 하려면 [리소스 관리자 템플릿을 사용 하 여 리소스를 만들 때 자동으로 진단 설정 사용](diagnostic-settings-template.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure platform Logs에 대해 자세히 알아보세요.](resource-logs-overview.md)