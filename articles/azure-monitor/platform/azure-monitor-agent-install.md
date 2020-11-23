---
title: Azure Monitor 에이전트 설치
description: Azure virtual machines 및 Azure Arc 사용 서버에 Azure Monitor 에이전트 (AMA)를 설치 하는 옵션입니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 4c6252b31b4be05ea3c0bcf160a28bf335239b23
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324859"
---
# <a name="install-the-azure-monitor-agent-preview"></a>Azure Monitor 에이전트 설치 (미리 보기)
이 문서에서는 Azure virtual machines와 Azure Arc 사용 서버 모두에 [Azure Monitor 에이전트](azure-monitor-agent-overview.md) 를 설치 하는 데 사용할 수 있는 여러 옵션을 제공 하며, 에이전트가 수집 해야 하는 데이터를 정의 하는 [데이터 수집 규칙을 사용 하 여 연결](data-collection-rule-azure-monitor-agent.md) 을 만드는 옵션도 제공 합니다.

## <a name="prerequisites"></a>사전 요구 사항
Azure Monitor 에이전트를 설치 하기 전에 다음 필수 구성 요소가 필요 합니다.

- Azure virtual machines에서 [관리 되는 시스템 id](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) 를 사용 하도록 설정 해야 합니다. 이는 Azure Arc 사용 서버에는 필요 하지 않습니다. 에이전트를 [Azure Portal를 사용 하 여 데이터 수집 규칙을 만들고 할당 하](#install-with-azure-portal)는 프로세스의 일부로 설치 하는 경우 시스템 id가 자동으로 사용 됩니다.
- 가상 컴퓨터의 가상 네트워크에서 [AzureResourceManager service 태그](../../virtual-network/service-tags-overview.md) 를 사용 하도록 설정 해야 합니다.

## <a name="virtual-machine-extension-details"></a>가상 컴퓨터 확장 세부 정보
Azure Monitor 에이전트는 다음 표의 세부 정보를 사용 하 여 [AZURE VM 확장](../../virtual-machines/extensions/overview.md) 으로 구현 됩니다. 이 문서에서 설명 하는 것을 포함 하 여 가상 머신 확장을 설치 하는 방법 중 하나를 사용 하 여 설치할 수 있습니다.

| 속성 | Windows | Linux |
|:---|:---|:---|
| 게시자 | Microsoft. Azure 모니터  | Microsoft. Azure 모니터 |
| 유형      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |


## <a name="install-with-azure-portal"></a>Azure Portal을 사용하여 설치
Azure Portal를 사용 하 여 Azure Monitor 에이전트를 설치 하려면 프로세스에 따라 Azure Portal에서 [데이터 수집 규칙을 만듭니다](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) . 이를 통해 하나 이상의 Azure virtual machines 또는 Azure Arc 사용 서버에 데이터 수집 규칙을 연결할 수 있습니다. 에이전트는 아직 없는 이러한 가상 컴퓨터에 설치 됩니다.


## <a name="install-with-resource-manager-template"></a>리소스 관리자 템플릿으로 설치
리소스 관리자 템플릿을 사용 하 여 Azure virtual machines 및 Azure Arc 사용 서버에 Azure Monitor 에이전트를 설치 하 고 데이터 수집 규칙과의 연결을 만들 수 있습니다. 연결을 만들기 전에 데이터 수집 규칙을 만들어야 합니다.

에이전트를 설치 하 고 다음에서 연결을 만들기 위한 샘플 템플릿을 가져옵니다. 

- [Azure Monitor 에이전트 (Azure 및 Azure Arc)를 설치 하기 위한 템플릿](../samples/resource-manager-agent.md#azure-monitor-agent-preview) 
- [데이터 수집 규칙과의 연결을 만들기 위한 템플릿](../samples/resource-manager-data-collection-rules.md)

다음 명령과 같이 [리소스 관리자 템플릿에 대 한 배포 방법을](../../azure-resource-manager/templates/deploy-powershell.md) 사용 하 여 템플릿을 설치 합니다.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>PowerShell을 사용 하 여 설치
PowerShell 명령을 사용 하 여 가상 머신 확장을 추가 하는 azure virtual machines 및 Azure Arc 사용 서버에 Azure Monitor 에이전트를 설치할 수 있습니다. 

### <a name="azure-virtual-machines"></a>Azure 가상 머신
다음 PowerShell 명령을 사용 하 여 Azure virtual machines에 Azure Monitor 에이전트를 설치 합니다.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
---

### <a name="azure-arc-enabled-servers"></a>Azure Arc 지원 서버
다음 PowerShell 명령을 사용 하 여 Azure Monitor 에이전트 onAzure Arc 사용 서버를 설치 합니다.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>Azure CLI
가상 머신 확장을 추가 하는 Azure CLI 명령을 사용 하 여 azure virtual machines 및 Azure Arc 사용 서버에 Azure Monitor 에이전트를 설치할 수 있습니다. 

### <a name="azure-virtual-machines"></a>Azure 가상 머신
다음 CLI 명령을 사용 하 여 Azure virtual machines에 Azure Monitor 에이전트를 설치 합니다.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Azure Arc 지원 서버
다음 CLI 명령을 사용 하 여 Azure Monitor 에이전트 onAzure Arc 사용 서버를 설치 합니다.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>다음 단계

- 에이전트에서 데이터를 수집 하 고 Azure Monitor로 보내기 위한 [데이터 수집 규칙을 만듭니다](data-collection-rule-azure-monitor-agent.md) .
