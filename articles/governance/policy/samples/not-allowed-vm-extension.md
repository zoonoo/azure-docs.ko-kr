---
title: 샘플 - 허용되지 않는 VM 확장
description: 이 샘플 정책 정의에서는 가상 머신에서 지정된 확장의 사용이 금지됩니다.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 29ca02023eb06463962dd207ca74bc705069a3aa
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256688"
---
# <a name="sample---not-allowed-vm-extensions"></a>샘플 - 허용되지 않는 VM 확장

지정된 확장의 사용을 금지합니다. 금지된 확장 형식을 포함하는 배열을 지정합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>샘플 템플릿

[!code-json[main](../../../../policy-templates/samples/compute/not-allowed-vmextension/azurepolicy.json "Not allowed VM Extensions")]

[PowerShell](#deploy-with-powershell) 또는 [Azure CLI](#deploy-with-azure-cli)와 함께 [Azure Portal](#deploy-with-the-portal)을 사용하여 이 템플릿을 배포할 수 있습니다.

## <a name="deploy-with-the-portal"></a>포털을 사용하여 배포

[![DAzure에 Policy 샘플 배포](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fnot-allowed-vmextension%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>PowerShell을 사용하여 배포 

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "not-allowed-vmextension" -DisplayName "Not allowed VM Extensions" -description "This policy governs which VM extensions that are explicitly denied." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/not-allowed-vmextension/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/not-allowed-vmextension/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -notAllowedExtensions <Denied extension> -PolicyDefinition $definition
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
az policy definition create --name 'not-allowed-vmextension' --display-name 'Not allowed VM Extensions' --description 'This policy governs which VM extensions that are explicitly denied.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/not-allowed-vmextension/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/not-allowed-vmextension/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "not-allowed-vmextension"
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI 배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](index.md)에서 더 많은 샘플 검토