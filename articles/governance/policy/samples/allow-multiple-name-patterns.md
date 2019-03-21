---
title: 샘플 - 여러 이름 패턴
description: 이 샘플 정책 정의에서는 리소스가 제공된 이름 패턴 중 하나와 일치해야 합니다.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: b2acbc705d69c0b874ae479f1ec49274c9853dea
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551146"
---
# <a name="sample---allow-multiple-name-patterns"></a>샘플 - 여러 이름 패턴 허용

리소스에 여러 이름 패턴 중 하나를 사용할 수 있습니다. 정책 규칙에 허용 이름 패턴을 지정합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>샘플 템플릿

[!code-json[main](../../../../policy-templates/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.json "allow multiple name patterns")]

[PowerShell](#deploy-with-powershell) 또는 [Azure CLI](#deploy-with-azure-cli)와 함께 [Azure Portal](#deploy-with-the-portal)을 사용하여 이 템플릿을 배포할 수 있습니다.

## <a name="deploy-with-the-portal"></a>포털을 사용하여 배포

[![Azure에 배포](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FTextPatterns%2Fallow-multiple-name-patterns%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>PowerShell을 사용하여 배포 

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "allow-multiple-name-patterns" -DisplayName "Allow one of many name patterns to be used for resources." -description "Allow one of many name patterns to be used for resources." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope> -PolicyDefinition $definition
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
az policy definition create --name 'allow-multiple-name-patterns' --display-name 'Allow one of many name patterns to be used for resources.' --description 'Allow one of many name patterns to be used for resources.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/allow-multiple-name-patterns/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "allow-multiple-name-patterns" 
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI 배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](index.md)에서 더 많은 샘플 검토