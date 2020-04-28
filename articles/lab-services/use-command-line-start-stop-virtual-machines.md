---
title: 명령줄 도구를 사용 하 여 Vm을 시작 및 중지 Azure DevTest Labs
description: 명령줄 도구를 사용 하 여 Azure DevTest Labs에서 가상 머신을 시작 하 고 중지 하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: fd643559a09d5c75aad9be5f35c653994c8488cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169242"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>명령줄 도구를 사용 하 여 가상 컴퓨터 Azure DevTest Labs 시작 및 중지
이 문서에서는 Azure PowerShell 또는 Azure CLI를 사용 하 여 Azure DevTest Labs에서 랩에서 가상 머신을 시작 하거나 중지 하는 방법을 보여 줍니다. PowerShell/CLI 스크립트를 만들어 이러한 작업을 자동화할 수 있습니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요
Azure DevTest Labs는 빠르고 쉬우며 간결한 개발/테스트 환경을 만드는 방법입니다. 이를 통해 비용을 관리 하 고 신속 하 게 Vm을 프로 비전 하 고 낭비를 최소화할 수 있습니다.  특정 시간에 자동으로 시작 및 중지 하도록 랩에서 Vm을 구성할 수 있는 Azure Portal 기본 제공 기능이 있습니다. 

그러나 일부 시나리오에서는 PowerShell/CLI 스크립트에서 Vm의 시작 및 중지를 자동화 하는 것이 좋습니다. 특정 시간을 사용 하지 않고 언제 든 지 개별 컴퓨터를 시작 하 고 중지할 수 있는 유연성을 제공 합니다. 스크립트를 사용 하 여 이러한 작업을 실행 하는 데 도움이 되는 몇 가지 상황은 다음과 같습니다.

- 3 계층 응용 프로그램을 테스트 환경의 일부로 사용 하는 경우 계층을 순서 대로 시작 해야 합니다. 
- 비용을 절약 하기 위해 사용자 지정 조건을 충족 하는 경우 VM을 끕니다. 
- 이를 CI/CD 워크플로 내에서 작업으로 사용 하 여 흐름의 시작 부분에서 시작 하 고, Vm을 빌드 컴퓨터, 테스트 컴퓨터 또는 인프라로 사용한 다음, 프로세스가 완료 되 면 Vm을 중지 합니다. 이에 대 한 예는 Azure DevTest Labs 있는 사용자 지정 이미지 팩터리입니다.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> 다음 스크립트는 Azure PowerShell Az module을 사용 합니다. 

다음 PowerShell 스크립트는 랩에서 VM을 시작 합니다. 이 스크립트에 대 한 기본 포커스는 [AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) 입니다. **ResourceId** 매개 변수는 랩의 VM에 대 한 정규화 된 리소스 ID입니다. **Action** 매개 변수는 필요한 항목에 따라 **시작** 또는 **중지** 옵션이 설정 되는 위치입니다.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
[Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) 는 DevTest Labs vm의 시작 및 중지를 자동화 하는 또 다른 방법입니다. Azure CLI는 다른 운영 체제에 [설치할](/cli/azure/install-azure-cli?view=azure-cli-latest) 수 있습니다. 다음 스크립트는 랩에서 VM을 시작 및 중지 하기 위한 명령을 제공 합니다. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>다음 단계
이러한 작업을 수행 하려면 Azure Portal를 사용 하는 다음 문서를 참조 하세요. [VM을 다시 시작](devtest-lab-restart-vm.md)합니다.
