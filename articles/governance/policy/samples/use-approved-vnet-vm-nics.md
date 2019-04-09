---
title: 샘플 - VM 네트워크 인터페이스에 대해 승인된 vNet 사용
description: 이 샘플 정책 정의에서는 네트워크 인터페이스가 승인된 가상 네트워크를 사용해야 합니다.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 14bb7c3cc242c040d1cd00ddbb926dca1ce43843
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801660"
---
# <a name="sample---use-approved-vnet-for-vm-network-interfaces"></a>샘플 - VM 네트워크 인터페이스에 대해 승인된 vNet 사용

이 정책에서는 네트워크 인터페이스가 승인된 가상 네트워크 사용해야 합니다. 승인된 가상 네트워크 ID를 지정합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>샘플 템플릿

[!code-json[main](../../../../policy-templates/samples/Network/vm-creation-in-approved-vnet/azurepolicy.json "Use approved vNet for VM network interfaces")]

[PowerShell](#deploy-with-powershell) 또는 [Azure CLI](#deploy-with-azure-cli)와 함께 [Azure Portal](#deploy-with-the-portal)을 사용하여 이 템플릿을 배포할 수 있습니다.

## <a name="deploy-with-the-portal"></a>포털을 사용하여 배포

[![Azure에 Policy 샘플 배포](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fvm-creation-in-approved-vnet%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>PowerShell을 사용하여 배포 

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "vm-creation-in-approved-vnet" -DisplayName "Use approved vNet for VM network interfaces" -description "This policy enforces VM network interfaces to use vNet." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/vm-creation-in-approved-vnet/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/vm-creation-in-approved-vnet/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -vNetId <vNet Id> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>PowerShell 배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'vm-creation-in-approved-vnet' --display-name 'Use approved vNet for VM network interfaces' --description 'This policy enforces VM network interfaces to use vNet.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/vm-creation-in-approved-vnet/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/vm-creation-in-approved-vnet/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "vm-creation-in-approved-vnet"
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI 배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](index.md)에서 더 많은 샘플 검토