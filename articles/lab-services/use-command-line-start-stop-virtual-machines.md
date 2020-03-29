---
title: 명령줄 도구를 사용하여 VM Azure DevTest 랩시작 및 중지
description: 명령줄 도구를 사용하여 Azure DevTest Labs에서 가상 컴퓨터를 시작하고 중지하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169242"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>명령줄 도구를 사용하여 Azure DevTest Labs 가상 컴퓨터를 시작하고 중지합니다.
이 문서에서는 Azure PowerShell 또는 Azure CLI를 사용하여 Azure DevTest Labs의 랩에서 가상 컴퓨터를 시작하거나 중지하는 방법을 보여 줍니다. PowerShell/CLI 스크립트를 만들어 이러한 작업을 자동화할 수 있습니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요
Azure DevTest Labs는 빠르고 간편하며 간결한 개발/테스트 환경을 만드는 방법입니다. 이를 통해 비용을 관리하고, VM을 신속하게 프로비저닝하고, 낭비를 최소화할 수 있습니다.  Azure 포털에는 랩의 VM을 특정 시간에 자동으로 시작하고 중지하도록 구성할 수 있는 기본 제공 기능이 있습니다. 

그러나 일부 시나리오에서는 PowerShell/CLI 스크립트에서 VM의 시작 및 중지를 자동화할 수 있습니다. 특정 시간이 아닌 언제든지 개별 기계를 시작하고 중지할 때 유연성을 제공합니다. 다음은 스크립트를 사용하여 이러한 작업을 실행하는 데 도움이 되는 몇 가지 상황입니다.

- 3계층 응용 프로그램을 테스트 환경의 일부로 사용하는 경우 계층을 순서대로 시작해야 합니다. 
- 비용을 절감하기 위해 사용자 지정 기준이 충족되면 VM을 끕니다. 
- CI/CD 워크플로 내에서 작업으로 사용하여 흐름의 시작 단계에서 시작하고 VM을 빌드 컴퓨터, 테스트 컴퓨터 또는 인프라로 사용한 다음 프로세스가 완료되면 VM을 중지합니다. 예를 들어 Azure DevTest 랩이 있는 사용자 지정 이미지 팩터리입니다.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> 다음 스크립트는 Azure PowerShell Az 모듈을 사용합니다. 

다음 PowerShell 스크립트는 랩에서 VM을 시작합니다. [호출-AzResourceAction이](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) 스크립트에 대 한 주요 초점입니다. **ResourceId** 매개 변수는 랩의 VM에 대해 정규화된 리소스 ID입니다. **작업** 매개 변수는 필요한 항목에 따라 **시작** 또는 **중지** 옵션이 설정된 위치입니다.

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
[Azure CLI는](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) DevTest Labs VM의 시작 및 중지를 자동화하는 또 다른 방법입니다. Azure CLI는 다른 운영 체제에 [설치할](/cli/azure/install-azure-cli?view=azure-cli-latest) 수 있습니다. 다음 스크립트는 랩에서 VM을 시작하고 중지하는 명령을 제공합니다. 

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
이러한 작업을 수행 하려면 Azure 포털을 사용 하 여 다음 문서를 참조: [VM 을 다시 시작](devtest-lab-restart-vm.md)합니다.
