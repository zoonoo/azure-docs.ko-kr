---
title: 네트워크 보안 그룹 이벤트 및 규칙 카운터 Azure 진단 로그 | Microsoft Docs
description: Azure 네트워크 보안 그룹에 대한 이벤트 및 규칙 카운터 진단 로그를 활성화하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: jdial
ms.openlocfilehash: c3f4a64c9e11d17899987bbe818506f61c415e3f
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757062"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>네트워크 보안 그룹에 대한 진단 로깅

NSG(네트워크 보안 그룹)는 가상 네트워크 서브넷, 네트워크 인터페이스 또는 둘 다에 대한 트래픽을 허용하거나 거부하는 규칙을 포함합니다. NSG에 대한 진단 로깅을 활성화하면 다음과 같은 범주의 정보를 기록할 수 있습니다.

* **이벤트:** 항목은 MAC 주소에 따라 VM에 적용되는 NSG 규칙에 대해 기록됩니다. 이러한 규칙에 대한 상태는 60초마다 수집됩니다.
* **규칙 카운터:** 트래픽을 허용하거나 거부하기 위해 각 NSG 규칙이 적용된 횟수에 대한 항목을 포함합니다.

진단 로그는 Azure Resource Manager 배포 모델을 통해 배포된 NSG에 대해서만 사용할 수 있습니다. 클래식 배포 모델을 통해 배포된 NSG에 대해 진단 로깅을 사용할 수 없습니다. 두 모델의 이해를 돕기 위해 [Azure 배포 모델 이해](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

진단 로깅은 진단 데이터를 수집하려는 *각* NSG에 대해 별도로 활성화됩니다. 운영 또는 활동, 로그에 대신 관심이 있는 경우 Azure [활동 로깅](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

## <a name="enable-logging"></a>로깅 사용

[Azure Portal](#azure-portal) 또는 [PowerShell](#powershell)을 사용하여 진단 로깅을 활성화할 수 있습니다.

### <a name="azure-portal"></a>Azure Portal

1. [포털](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택한 다음, *네트워크 보안 그룹*을 입력합니다. 검색 결과에 **네트워크 보안 그룹**이 표시되면 선택합니다.
3. 로깅을 활성화하려는 NSG를 선택합니다.
4. **모니터링** 아래에서 **진단 로그**를 선택한 다음, 다음 그림에 표시된 것처럼 **진단 켜기**를 선택합니다.
 
    ![진단 켜기](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. **진단 설정** 아래에서 다음 정보를 입력하거나 선택한 다음, **저장**을 선택합니다.

    | 설정                                                                                     | 값                                                          |
    | ---------                                                                                   |---------                                                       |
    | Name                                                                                        | 선택한 이름입니다.  예: *myNsgDiagnostics*      |
    | **저장소 계정에 보관**, **이벤트 허브로의 스트림** 및 **Log Analytics에 보내기** | 여러 대상을 선택할 수 있습니다. 각각에 대해 자세히 알아보려면 [로그 대상](#log-destinations)을 참조하세요.                                                                                                                                           |
    | LOG                                                                                         | 둘 중 하나 또는 두 로그 범주를 선택합니다. 각 범주에 대해 기록된 데이터에 대해 자세히 알아보려면 [로그 범주](#log-categories)를 참조하세요.                                                                                                                                             |
6. 로그를 보고 분석합니다. 자세한 내용은 [로그 보기 및 분석](#view-and-analyze-logs)을 참조하세요.

### <a name="powershell"></a>PowerShell

[Azure Cloud Shell](https://shell.azure.com/powershell) 뒤에 오는 명령을 실행하거나 컴퓨터에서 PowerShell을 실행하여 실행할 수 있습니다. Azure Cloud Shell은 무료 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 컴퓨터에서 PowerShell을 실행하는 경우 *AzureRM* PowerShell 모듈, 버전 6.1.1 이상이 필요합니다. 컴퓨터에서 `Get-Module -ListAvailable AzureRM`을 실행하여 설치된 버전을 확인합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. PowerShell을 로컬로 실행 중인 경우 `Login-AzureRmAccount`를 실행하여 [필요한 권한](virtual-network-network-interface.md#permissions)을 가진 계정으로 Azure에 로그인해야 합니다.

진단 로깅을 활성화하려면 기존 NSG의 ID가 필요합니다. 기존 NSG가 없는 경우 [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)을 사용하여 만들 수 있습니다.

[Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)을 사용하여 진단 로깅을 활성화하려는 네트워크 보안 그룹을 검색합니다. 예를 들어 *myResourceGroup*이라는 리소스 그룹에 존재하는 *myNsg*라는 NSG를 검색하려면 다음 명령을 입력합니다.

```azurepowershell-interactive
$Nsg=Get-AzureRmNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

세 가지 대상 유형에 대한 진단 로그를 작성할 수 있습니다. 자세한 내용은 [로그 대상](#log-destinations)을 참조하세요. 이 문서에서 로그는 예를 들어 *Log Analytics* 대상으로 전송됩니다. [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace)를 사용하여 기존 Log Analytics 작업 영역을 검색합니다. 예를 들어 *LaWorkspaces*라는 리소스 그룹에서 *myLaWorkspace*라는 기존 작업 영역을 검색하려면 다음 명령을 입력합니다.

```azurepowershell-interactive
$Oms=Get-AzureRmOperationalInsightsWorkspace `
  -ResourceGroupName LaWorkspaces `
  -Name myLaWorkspace
```

기존 작업 영역이 없는 경우 [New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace)를 사용하여 만들 수 있습니다.

로그를 활성화할 수 있는 두 가지 범주의 로깅이 있습니다. 자세한 내용은 [로그 범주](#log-categories)를 참조하세요. [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting)을 사용하여 NSG에 대한 진단 로깅을 활성화합니다. 다음 예제에서는 이전에 검색한 NSG 및 작업 영역에 대한 ID를 사용하여 NSG에 대한 작업 영역으로 이벤트 및 카운터 범주 데이터를 기록합니다.

```azurepowershell-interactive
Set-AzureRmDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

둘 다가 아닌 하나의 범주에 대해 데이터를 기록하려는 경우 *NetworkSecurityGroupEvent* 또는 *NetworkSecurityGroupRuleCounter*가 뒤에 오는 이전 명령에 `-Categories` 옵션을 추가합니다. Log Analytics 작업 영역이 아닌 다른 [대상](#log-destinations)에 기록하려면 Azure [Storage 계정](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [이벤트 허브](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 대한 적절한 매개 변수를 사용합니다.

로그를 보고 분석합니다. 자세한 내용은 [로그 보기 및 분석](#view-and-analyze-logs)을 참조하세요.

## <a name="log-destinations"></a>로그 대상

진단 데이터는 다음 작업이 가능합니다.
- 감사 또는 수동 검사를 위해 [Azure Storage 계정에 기록합니다](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 리소스 진단 설정을 사용하여 보존 기간(일)을 지정할 수 있습니다.
- 타사 서비스 또는 사용자 지정 분석 솔루션(예: PowerBI)에서 수집하도록 [Event Hub로 스트리밍합니다](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure Log Analytics에 기록합니다](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics).

## <a name="log-categories"></a>로그 범주

JSON 형식의 데이터는 다음 로그 범주에 대해 기록됩니다.

### <a name="event"></a>행사

이벤트 로그는 MAC 주소에 따라 VM에 적용될 NSG 규칙에 대한 정보를 포함합니다. 다음 예제 데이터는 각 이벤트에 대해 기록됩니다.

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"[PORT RANGE]",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter"></a>규칙 카운터

규칙 카운터 로그는 리소스에 적용되는 각 규칙에 대한 정보를 포함합니다. 다음 예제 데이터는 규칙이 적용될 때마다 기록됩니다.

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> 통신에 대한 원본 IP 주소는 기록되지 않습니다. NSG에 대한 [NSG 흐름 로깅](../network-watcher/network-watcher-nsg-flow-logging-portal.md)을 활성화할 수 있지만 모든 규칙 카운터 정보뿐만 아니라 통신을 시작한 원본 IP 주소를 기록합니다. NSG 흐름 로그 데이터가 Azure Storage 계정에 기록됩니다. Azure Network Watcher의 [트래픽 분석](../network-watcher/traffic-analytics.md) 기능을 사용하여 데이터를 분석할 수 있습니다.

## <a name="view-and-analyze-logs"></a>로그 보기 및 분석

진단 로그 데이터를 보는 방법을 알아보려면 [Azure 진단 로그 개요](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요. 다음으로 진단 데이터를 보내는 경우:
- **Log Analytics**: 향상된 인사이트에 대해 [네트워크 보안 그룹 분석](../log-analytics/log-analytics-azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-log-analytics
) 솔루션을 사용할 수 있습니다. 솔루션은 가상 머신에서 네트워크 인터페이스의 MAC 주소당 트래픽을 허용하거나 거부하는 NSG 규칙에 대한 시각화를 제공합니다.
- **Azure Storage 계정**: PT1H.json 파일에 데이터가 기록됩니다. 다음을 찾을 수 있습니다.
    - 다음 경로에서 이벤트 로그: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
    - 다음 경로에서 규칙 카운터 로그: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>다음 단계

- 이전에 감사 또는 작업 로그로 알려진 [작업 로깅](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 대해 자세히 알아봅니다. 활동 로깅은 Azure 배포 모델 중 하나를 통해 만든 NSG에 대해 기본적으로 사용됩니다. 활동 로그의 NSG 내에서 완료된 작업을 확인하려면 다음과 같은 리소스 유형을 포함하는 항목을 찾습니다.
    - Microsoft.ClassicNetwork/networkSecurityGroups
    - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
    - Microsoft.Network/networkSecurityGroups
    - Microsoft.Network/networkSecurityGroups/securityRules
- 각 흐름에 대한 원본 IP 주소를 포함하도록 진단 정보를 기록하는 방법을 알아보려면 [NSG 흐름 로깅](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.