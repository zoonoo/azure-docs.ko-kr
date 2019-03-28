---
title: Log Analytics 작업 영역에 Azure 서비스 로그 및 메트릭 수집 | Microsoft Docs
description: 로그 및 메트릭을 Azure Monitor에서 Log Analytics 작업 영역에 쓰도록 Azure 리소스에 대 한 진단을 구성 합니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: 5a619b768d61875a03e53a613dfb9a3fb01dd7aa
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540180"
---
# <a name="collect-azure-service-logs-and-metrics-into-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor에서 Log Analytics 작업 영역에 Azure 서비스 로그 및 메트릭 수집

Azure 서비스에 대한 로그 및 메트릭을 수집하는 방법에는 다음 네 가지가 있습니다.

1. Azure Monitor에서 Log Analytics 작업 영역에 직접 azure 진단 (*진단* 표에)
2. Azure Monitor에서 Log Analytics 작업 영역에 Azure storage에 azure 진단 (*저장소* 표에)
3. Azure 서비스에 대한 커넥터(다음 표에서 *커넥터*)
4. 수집 하 고 그런 다음 Azure Monitor (공백 다음 테이블에 나열 되지 않은 서비스에 대 한)에서 Log Analytics 작업 영역에 데이터를 게시 하는 스크립트


| 서비스                 | 리소스 종류                           | 로그        | 메트릭     | 해결 방법 |
| --- | --- | --- | --- | --- |
| 애플리케이션 게이트웨이    | Microsoft.Network/applicationGateways   | 진단 | 진단 | [Azure Application Gateway 분석](../../azure-monitor/insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Application insights    |                                         | 커넥터   | 커넥터   | [Application Insights 커넥터](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)(미리 보기) |
| Automation 계정     | Microsoft.Automation/AutomationAccounts | 진단 |             | [자세한 정보](../../automation/automation-manage-send-joblogs-log-analytics.md)|
| Batch 계정          | Microsoft.Batch/batchAccounts           | 진단 | 진단 | |
| 클래식 Cloud Services  |                                         | Storage     |             | [자세한 정보](azure-storage-iis-table.md) |
| Cognitive Services      | Microsoft.CognitiveServices/accounts    |             | 진단 | |
| Data Lake Analytics     | Microsoft.DataLakeAnalytics/accounts    | 진단 |             | |
| Data Lake Store         | Microsoft.DataLakeStore/accounts        | 진단 |             | |
| 이벤트 허브 네임스페이스     | Microsoft.EventHub/namespaces           | 진단 | 진단 | |
| IoT Hub                | Microsoft.Devices/IotHubs               |             | 진단 | |
| Key Vault               | Microsoft.KeyVault/vaults               | 진단 |             | [KeyVault 분석](../../azure-monitor/insights/azure-key-vault.md) |
| 부하 분산 장치          | Microsoft.Network/loadBalancers         | 진단 |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | 진단 | 진단 | |
| 네트워크 보안 그룹 | Microsoft.Network/networksecuritygroups | 진단 |             | [Azure 네트워크 보안 그룹 분석](../../azure-monitor/insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| 복구 자격 증명         | Microsoft.RecoveryServices/vaults       |             |             | [Azure Recovery Services 분석(미리 보기)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Search 서비스         | Microsoft.Search/searchServices         | 진단 | 진단 | |
| Service Bus 네임스페이스   | Microsoft.ServiceBus/namespaces         | 진단 | 진단 | [Service Bus 분석(미리 보기)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Storage     |             | [Service Fabric 분석(미리 보기)](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL(v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | 진단 | [Azure SQL Analytics(미리 보기)](../../azure-monitor/insights/azure-sql.md) |
| Storage                 |                                         |             | 스크립트      | [Azure Storage 분석(미리 보기)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtual Machines        | Microsoft.Compute/virtualMachines       | 내선 번호   | 내선 번호 <br> 진단  | |
| Virtual Machines 확장 집합 | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | 진단 | |
| 웹 서버 팜        | Microsoft.Web/serverfarms               |             | 진단 | |
| 웹 사이트               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | 진단 | [Azure Web Apps 분석(미리 보기)](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> Azure 가상 머신(Linux 및 Windows 모두)를 모니터링하려면 [Log Analytics VM 확장](../../azure-monitor/learn/quick-collect-azurevm.md)을 설치하는 것이 좋습니다. 이 에이전트는 가상 머신 내에서 수집된 통찰력을 제공합니다. 또한 가상 머신 확장 집합에 대한 확장을 사용할 수도 있습니다.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Azure 진단에서 Log Analytics로 직접
많은 Azure 리소스 진단 로그 및 메트릭을 Azure Monitor에서 Log Analytics 작업 영역에 직접 쓸 수 및 분석을 위해 데이터를 수집 하는 기본 방법이 있습니다. 작업 영역으로 데이터가 즉시 기록 Azure 진단을 사용 하는 경우 이며 먼저 저장소에 데이터를 쓸 필요가 없습니다.

Azure 리소스를 지 원하는 [Azure monitor](../../azure-monitor/overview.md) 해당 로그 및 메트릭을 Log Analytics 작업 영역에 직접 보낼 수 있습니다.

> [!NOTE]
> Log Analytics 작업 영역 진단 설정 통한 다차원 메트릭 보내기는 현재 지원 되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
>

* 사용 가능한 메트릭에 대한 자세한 내용은 [Azure Monitor에서 지원되는 메트릭](../../azure-monitor/platform/metrics-supported.md)을 참조하세요.
* 사용 가능한 로그에 대한 자세한 내용은 [진단 로그에 지원되는 서비스 및 스키마](../../azure-monitor/platform/diagnostic-logs-schema.md)를 참조하세요.

### <a name="enable-diagnostics-with-powershell"></a>PowerShell에서 진단 사용

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

다음 PowerShell 예제를 사용 하는 방법을 보여 줍니다 [집합 AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) 네트워크 보안 그룹에서 진단을 사용 하도록 설정 합니다. 지원되는 모든 리소스에 대해 같은 방법을 사용할 수 있습니다. `$resourceId`를 진단을 사용하도록 설정할 리소스의 리소스 ID로 설정하기만 하면 됩니다.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Resource Manager 템플릿을 사용하여 진단 사용

리소스가 생성될 때 진단을 사용하도록 설정하고 진단이 Log Analytics 작업 영역에 진단이 전송되도록 하려면 아래와 비슷한 템플릿을 사용할 수 있습니다. 이 예제는 Automation 계정에 대한 것이지만 지원되는 모든 리소스 형식에 작동합니다.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Azure 진단 -> Azure Storage -> Log Analytics

일부 리소스 내에서 로그를 수집 하는 것에 대 한 Azure storage에 로그 보내기 다음 저장소에서 로그를 읽도록 Log Analytics 작업 영역을 구성 하는 것이 같습니다.

Azure Monitor에는 다음 리소스 및 로그에 대 한 Azure storage에서 진단을 수집 하려면이 방법을 사용할 수 있습니다.:

| 리소스 | 로그 |
| --- | --- |
| Service Fabric |ETWEvent <br> 작업 데이터 <br> 신뢰할 수 있는 행위자 이벤트 <br> 신뢰할 수 있는 서비스 이벤트 |
| Virtual Machines |Linux Syslog <br> Windows 이벤트 <br> IIS 로그 <br> Windows ETWEvent |
| 웹 역할  <br> 작업자 역할 |Linux Syslog <br> Windows 이벤트 <br> IIS 로그 <br> Windows ETWEvent |

> [!NOTE]
> Log Analytics 작업 영역 저장소 계정에서 데이터를 읽을 때 및 저장소 계정에 진단을 보내는 경우 저장소 및 트랜잭션에 대해 일반 Azure 데이터 요금이 청구 됩니다.
>
>

참조 [이벤트에 대 한 IIS와 table storage에 blob storage 사용](azure-storage-iis-table.md) Azure Monitor에서 이러한 로그를 수집할 수는 방법에 대해 자세히 알아보려면 합니다.

## <a name="connectors-for-azure-services"></a>Azure 서비스용 커넥터

Log Analytics 작업 영역을 전송할 수 있도록 Application Insights에서 수집 된 데이터를 허용 하는 Application Insights 용 커넥터가 있습니다.

[Application Insights 커넥터](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)에 대해 자세히 알아보세요.

## <a name="scripts-to-collect-and-post-data-to-log-analytics-workspace"></a>수집 하 고 Log Analytics 작업 영역에 데이터를 게시 하는 스크립트

로그 및 메트릭을 Log Analytics 작업 영역을 보낼 수 있는 직접적인 방법을 제공 하지 않는 Azure 서비스에 대 한 로그 및 메트릭을 수집 하는 Azure Automation 스크립트를 사용할 수 있습니다. 스크립트를 사용 하 여 작업 영역에 데이터를 보낼 수 있습니다는 [데이터 수집기 API](../../azure-monitor/platform/data-collector-api.md)

Azure 템플릿 갤러리에 [Azure Automation을 사용 하는 예가](https://azure.microsoft.com/resources/templates/?term=OMS) 서비스에서 데이터를 수집 하 여 Azure Monitor로 전송 합니다.

## <a name="next-steps"></a>다음 단계

* [이벤트에 대해 IIS 및 테이블 스토리지에 Blob Storage를 사용하여](azure-storage-iis-table.md) Table Storage에 진단을 기록하는 Azure 서비스나 Blob Storage에 기록된 IIS 로그에 대해 로그를 읽을 수 있습니다.
* [솔루션을 사용하도록 설정](../../azure-monitor/insights/solutions.md) 하여 데이터에 대한 정보를 제공합니다.
* [검색 쿼리를 사용](../../azure-monitor/log-query/log-query-overview.md) 하여 데이터를 분석합니다.
