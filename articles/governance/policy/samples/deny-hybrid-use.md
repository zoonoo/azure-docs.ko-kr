---
title: 샘플 - 하이브리드 사용 혜택 거부
description: 이 샘플 정책 정의에서는 AHUB(Azure Hybrid Use Benefit) 사용이 금지됩니다.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 88fd25b8b2d19cbe5dcb9b3978e16bb832e8568e
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803037"
---
# <a name="sample---deny-hybrid-use-benefit"></a>샘플 - 하이브리드 사용 혜택 거부

AHUB(Azure Hybrid Use Benefit) 사용을 금지합니다. 온-프레미스 라이선스 사용을 허용하기 원하지 않을 때 사용합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>샘플 템플릿

[!code-json[main](../../../../policy-templates/samples/compute/deny-hybrid-use-benefit/azurepolicy.json "Deny hybrid use benefit")]

[PowerShell](#deploy-with-powershell) 또는 [Azure CLI](#deploy-with-azure-cli)와 함께 [Azure Portal](#deploy-with-the-portal)을 사용하여 이 템플릿을 배포할 수 있습니다.

## <a name="deploy-with-the-portal"></a>포털을 사용하여 배포

[![Azure에 Policy 샘플 배포](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fdeny-hybrid-use-benefit%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>PowerShell을 사용하여 배포 

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "deny-hybrid-use-benefit" -DisplayName "Deny hybrid use benefit" -description "This policy will deny usage of hybrid use benefit." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/deny-hybrid-use-benefit/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/deny-hybrid-use-benefit/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -PolicyDefinition $definition
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
az policy definition create --name 'deny-hybrid-use-benefit' --display-name 'Deny hybrid use benefit' --description 'This policy will deny usage of hybrid use benefit.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/deny-hybrid-use-benefit/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/deny-hybrid-use-benefit/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "deny-hybrid-use-benefit"
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI 배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](index.md)에서 더 많은 샘플 검토