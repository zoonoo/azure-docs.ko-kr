---
title: 명령줄 도구를 사용 하 여 시작 하 고 Azure DevTest Labs Vm 중지 | Microsoft Docs
description: Azure DevTest Labs에서 가상 머신을 시작 및 중지 하려면 명령줄 도구를 사용 하는 방법에 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: a8132735d1af08055e9341608dcac0564ed4b927
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236672"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>명령줄 도구를 사용 하 여 Azure DevTest Labs 가상 머신을 시작 및 중지 하려면
이 문서에서는 Azure PowerShell 또는 Azure CLI를 사용 하 여 시작 하거나 Azure DevTest Labs의 랩에서 가상 머신을 중지 하는 방법을 보여 줍니다. 이러한 작업을 자동화 하는 PowerShell/CLI 스크립트를 만들 수 있습니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요
Azure DevTest Labs는 빠르고 쉬우며 간결한 개발/테스트 환경을 만들 수 있습니다. 수 있도록 비용 관리를 신속 하 게 Vm을 프로 비전 하 고 최소화 하려면 낭비 합니다.  Azure portal에서 자동으로 시작 하 고 특정 시간에 중지 랩에서 Vm을 구성할 수 있는 기본 제공 기능이 있습니다. 

그러나 일부 시나리오에서는 시작 하 고 PowerShell/CLI 스크립트에서 Vm의 중지를 자동화 수 있습니다. 시작 하 고 언제 든 대신 특정 시간에 개별 컴퓨터를 중지를 사용 하 여 유연성이 제공 합니다. 다음은 일부의 상황에서 어떤 실행 스크립트를 사용 하 여 이러한 작업 유용할 것입니다.

- 3 계층 응용 프로그램을 테스트 환경의 일부로 사용 하는 경우 계층은 시퀀스에서 시작 해야 합니다. 
- 비용을 절약 하는 사용자 지정 조건을 충족 되 면 VM을 해제 합니다. 
- 흐름의 시작 부분에서 시작, 빌드 컴퓨터 Vm 사용, 컴퓨터 또는 인프라를 테스트 프로세스가 완료 되 면 Vm을 중지 하도록 CI/CD 워크플로에서 작업으로 사용 합니다. 이 예제는 Azure DevTest Labs를 사용 하 여 사용자 지정 이미지 팩터리 것입니다.  

## <a name="azure-powershell"></a>Azure PowerShell
다음 PowerShell 스크립트는 랩에서 VM을 시작합니다. [호출 AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) 이 스크립트에 대 한 기본 초점 이기도 합니다. 합니다 **ResourceId** 매개 변수는 랩의 VM에 대 한 정규화 된 리소스 ID입니다. **동작** 매개 변수는 위치를 **시작** 또는 **중지** 옵션은 필요에 따라 설정 됩니다.

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
if ($(Get-Module -Name AzureRM).Version.Major -eq 6) {
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -Name $devTestLabName
} else {
    $devTestLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $devTestLabName
}

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
합니다 [Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) DevTest Labs Vm의 중지 하 고 시작을 자동화 하는 다른 방법이 있습니다. Azure CLI 수 [설치](/cli/azure/install-azure-cli?view=azure-cli-latest) 다른 운영 체제에서. 다음 스크립트를 시작 하 고 랩에 VM을 중지 하는 것에 대 한 명령을 제공 합니다. 

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
Azure portal을 사용 하 여 이러한 작업을 수행 하려면 다음 문서를 참조 하세요. [VM을 다시 시작](devtest-lab-restart-vm.md)합니다.
