---
title: Azure PowerShell 또는 Resource Manager 템플릿을 사용 하 여 Vm (미리 보기)에 대 한 Azure Monitor를 사용 하도록 설정 | Microsoft Docs
description: 이 문서에서는 하나의 Vm에 대 한 Azure Monitor을 사용 하는 방법에 대해 설명 합니다. 또는 Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 더 많은 Azure 가상 머신 또는 가상 머신 확장을 설정 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ff284ea0adf6021ace84cd6a41f0a0e4e987a9c8
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144240"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Azure PowerShell 또는 Resource Manager 템플릿을 사용 하 여 Vm (미리 보기)에 대 한 Azure Monitor를 사용 하도록 설정

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

이 문서에서는 Azure virtual machines에 대 한 Vm (미리 보기)에 대 한 Azure Monitor를 사용 하도록 설정 하는 방법에 설명 하거나 Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 가상 머신 확장 집합. 이 프로세스의 끝은 성공적으로 시작 가상 머신의 모든 모니터링 및 모든 성능 또는 가용성 문제 발생 하는 경우에 대해 알아봅니다.

## <a name="set-up-a-log-analytics-workspace"></a>Log Analytics 작업 영역 설정 

Log Analytics 작업 영역에 없는 경우 새로 만들려면 해야 합니다. 제안 된 방법을 검토 합니다 [필수 구성 요소](vminsights-enable-overview.md#log-analytics) 구성 하는 단계를 진행 하기 전에 섹션입니다. 그런 다음 Azure Resource Manager 템플릿 메서드를 사용 하 여 Vm에 대 한 Azure Monitor의 배포를 완료할 수 있습니다.

### <a name="enable-performance-counters"></a>성능 카운터 사용하도록 설정

솔루션에서 참조되는 Log Analytics 작업 영역이 솔루션에 필요한 성능 카운터를 수집하도록 구성되지 않은 경우 사용하도록 설정해야 합니다. 두 가지 방법 중 하나로 수행할 수 있습니다.
* 수동으로([Log Analytics의 Windows 및 Linux 성능 데이터 원본](../../azure-monitor/platform/data-sources-performance-counters.md)에 설명되어 있음)
* 다운로드에서 사용할 수 있는 PowerShell 스크립트를 실행 하 여 [Azure PowerShell 갤러리](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>ServiceMap 및 InfrastructureInsights 솔루션 설치
이 방법은 Log Analytics 작업 영역에 솔루션 구성 요소를 사용하도록 구성을 지정하는 JSON 템플릿을 포함합니다.

템플릿을 사용 하 여 리소스를 배포 하는 방법을 모 르 참조:
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI를 사용 하려면 먼저를 설치 하 여 로컬로 CLI를 사용 해야 합니다. Azure CLI 버전 2.0.27 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 를 설치 하거나 업그레이드 Azure CLI 참조 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다.

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. 이 파일을 *installsolutionsforvminsights.json*으로 로컬 폴더에 저장합니다.

1. *WorkspaceName*, *ResourceGroupName* 및 *WorkspaceLocation*의 값을 캡처합니다. *WorkspaceName* 값은 Log Analytics 작업 영역의 이름입니다. *WorkspaceLocation* 값은 작업 영역이 정의된 지역입니다.

1. 이제 이 템플릿을 배포할 수 있습니다.
 
    * 템플릿이 포함된 폴더에서 다음 PowerShell 명령을 사용합니다.

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        구성 변경을 완료 하려면 몇 분 정도 걸릴 수 있습니다. 완료 되 면 다음과 유사한 결과 포함 하는 다음 메시지가 나타납니다.

        ```powershell
        provisioningState       : Succeeded
        ```

    * Azure CLI를 사용하여 다음 명령을 실행하려면:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        구성 변경을 완료 하려면 몇 분 정도 걸릴 수 있습니다. 완료 되 면 다음과 유사한 결과 포함 하는 메시지가 표시 됩니다.

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용 하 여 사용 하도록 설정
가상 머신과 가상 머신 확장 집합에 온 보 딩에 대 한 예제에서는 Azure Resource Manager 템플릿 작성 했습니다. 이러한 템플릿은 모니터링이 설정 되어 있는 새 리소스를 만들고 기존 리소스에서 모니터링을 사용 하 여 시나리오를 포함 합니다.

>[!NOTE]
>템플릿을 등록 상태가 될 리소스와 동일한 리소스 그룹에 배포 해야 합니다.

템플릿을 사용 하 여 리소스를 배포 하는 방법을 모 르 참조:
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI를 사용 하려면 먼저를 설치 하 여 로컬로 CLI를 사용 해야 합니다. Azure CLI 버전 2.0.27 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 를 설치 하거나 업그레이드 Azure CLI 참조 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다.

### <a name="download-templates"></a>템플릿 다운로드

Azure Resource Manager 템플릿 수 있는 보관 파일 (.zip)에 제공 됩니다 [다운로드](https://aka.ms/VmInsightsARMTemplates) GitHub 리포지토리에서에서. 파일의 내용을 템플릿 및 매개 변수 파일을 사용 하 여 각 배포 시나리오를 나타내는 폴더를 포함 합니다. 실행 하기 전에 매개 변수 파일을 수정 하 고 필요한 값을 지정 합니다. 특정 요구 사항을 지원 하도록 사용자 지정 하려면 필요 하지 않은 경우에 템플릿 파일을 수정 하지 마세요. 매개 변수 파일을 수정한 후에이 문서의 뒷부분에 설명 된 다음 메서드를 사용 하 여 배포할 수 있습니다. 

다운로드 파일에는 다양 한 시나리오에 대 한 다음 템플릿을 포함 됩니다.

- **ExistingVmOnboarding** 템플릿 가상 머신이 이미 있는 경우 Vm에 대 한 Azure Monitor를 사용 합니다.
- **NewVmOnboarding** 템플릿 가상 컴퓨터를 만들고 모니터링 하는 Vm에 대 한 Azure Monitor를 사용 하도록 설정 합니다.
- **ExistingVmssOnboarding** 템플릿 가상 머신 확장 집합이 이미 있는 경우 Vm에 대 한 Azure Monitor를 사용 합니다.
- **NewVmssOnboarding** 템플릿 가상 머신 확장 집합을 만들고 모니터링 하는 Vm에 대 한 Azure Monitor를 사용 하도록 설정 합니다.
- **ConfigureWorksapce** 템플릿은 솔루션와 Linux 및 Windows 운영 체제 성능 카운터 컬렉션을 사용 하 여 Vm에 대 한 Azure Monitor를 지원 하기 위해 Log Analytics 작업 영역을 구성 합니다.

>[!NOTE]
>가상 머신 확장 집합 이미 있던 업그레이드 정책 설정 되어 있으면 **수동**에 대 한 Vm에 대 한 Azure Monitor를 사용할 수 없습니다 실행 한 후 기본적으로 인스턴스를 **ExistingVmssOnboarding** Azure Resource Manager 템플릿입니다. 수동으로 인스턴스를 업그레이드 해야 합니다.

### <a name="deploy-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 배포

다음 단계를 사용 하면 Azure PowerShell을 사용 하 여 모니터링할 수 있습니다.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
구성 변경을 완료 하려면 몇 분 정도 걸릴 수 있습니다. 완료 되 면 다음과 유사한 결과 포함 하는 다음 메시지가 나타납니다.

```powershell
provisioningState       : Succeeded
```
### <a name="deploy-by-using-the-azure-cli"></a>Azure CLI를 사용 하 여 배포

다음 단계를 사용 하면 Azure CLI를 사용 하 여 모니터링할 수 있습니다.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

출력은 다음과 유사합니다.

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>PowerShell을 통해 사용하도록 설정

Azure Monitor Vm에 대 한 여러 Vm 또는 가상 머신 확장 집합을 사용 하려면 PowerShell 스크립트를 사용 하 여 [설치 VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0)합니다. Azure PowerShell 갤러리의 제품은입니다. 이 스크립트를 반복 합니다.

- 모든 가상 머신과 가상 머신 확장 집합에서 구독 합니다.
- 지정 된 범위가 지정 된 리소스 그룹 *ResourceGroup*합니다. 
- 지정 된 단일 VM 또는 가상 머신 확장 집합 *이름을*입니다.

각 VM 또는 가상 머신 확장 집합의 경우 스크립트에서 VM 확장을 이미 설치했는지 여부를 확인합니다. VM 확장이 설치 되지 않은 경우 스크립트를 다시 설치 하려고 시도 합니다. VM 확장이 설치 된 경우 스크립트는 Log Analytics 및 Dependency Agent VM 확장을 설치 합니다.

이 스크립트에서는 Azure PowerShell 모듈 Az 버전 1.0.0 이상. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

스크립트의 인수 세부 정보 및 사용법 예제 목록을 가져오려면 `Get-Help`를 실행합니다.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

다음 예제에서는 VM용 Azure Monitor를 사용하도록 설정하고 예상되는 출력을 이해하기 위해 폴더에서 PowerShell 명령을 사용하는 방법을 보여줍니다.

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>다음 단계

가상 컴퓨터에 대 한 모니터링이 활성화 했으므로이 정보는 Vm에 대 한 Azure Monitor를 사용 하 여 분석을 위해 사용할 수 있습니다.
 
- 상태 기능을 사용 하는 방법에 알아보려면 참조 [Vm 상태에 대 한 Azure Monitor 뷰](vminsights-health.md)합니다. 
- 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요. 
- 병목 현상 및 VM의 성능으로 전체적인 사용률을 확인 하려면 [Azure VM 성능 보기](vminsights-performance.md)합니다. 
- 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.