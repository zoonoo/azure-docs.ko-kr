---
title: 샘플 - 가상 네트워크 엔드포인트가 없는 Key Vault 자격 증명 모음 감사
description: 이 샘플 정책은 Key Vault 자격 증명 모음을 감사하여 가상 네트워크 엔드포인트가 없는 인스턴스를 검색합니다.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: 8269e06d3e2fd92e0d20fbc3dc84d0a1b6761cda
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658439"
---
# <a name="key-vault-vaults-with-no-virtual-network-endpoints"></a>가상 네트워크 엔드포인트가 없는 Key Vault 자격 증명 모음

이 정책은 가상 네트워크 엔드포인트가 없는 Key Vault 자격 증명 모음을 감사합니다. 보안 요구 사항을 적용하려면 이 정책을 사용하세요. 자세한 내용은 [Key Vault의 가상 네트워크 서비스 엔드포인트](../../../key-vault/key-vault-overview-vnet-service-endpoints.md)를 참조하세요.

다음 방법을 사용하여 이 샘플 정책을 배포할 수 있습니다.

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>샘플 정책

### <a name="policy-definition"></a>정책 정의 

완전히 구성된 JSON 정책 정의로 REST API에서, 'Azure에 배포' 단추에서, 그리고 포털에서 수동으로 사용됩니다.

[!code-json[full](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.json "KeyVault vnet rules")]

> [!NOTE]
> 포털에서 수동으로 정책을 만드는 경우 위의 **properties.parameters** 및 **properties.policyRule** 부분을 사용합니다. 두 섹션을 중괄호 `{}`로 묶어서 유효한 JSON으로 만듭니다.

### <a name="policy-rules"></a>정책 규칙

정책 규칙을 정의하는 JSON으로, Azure CLI 및 Azure PowerShell에서 사용됩니다.

[!code-json[rule](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>정책 매개 변수

이 샘플 정책 정의는 매개 변수가 정의되지 않았습니다.

## <a name="azure-portal"></a>Azure portal

[![Azure에 배포](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)
[![Azure Gov에 배포](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

### <a name="deploy-with-azure-powershell"></a>Azure PowerShell을 사용하여 배포

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "audit-keyvault-vnet-rules" -DisplayName "Audit if Key Vault has no virtual network rules" -description "Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'audit-keyvault-vnet-rules-assignment' -DisplayName 'Audit Key Vault Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition
```

### <a name="remove-with-azure-powershell"></a>Azure PowerShell을 사용하여 제거

이전 할당 및 정의를 제거하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell 설명

배포 및 제거 스크립트는 다음 명령을 사용합니다. 다음 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | 새 Azure Policy 정의를 만듭니다. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | 단일 리소스 그룹을 가져옵니다. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | 새 Azure Policy 할당을 만듭니다. 이 예제에서는 정의를 제공하지만, 이니셔티브를 취할 수도 있습니다. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | 기존 Azure Policy 할당을 제거합니다. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | 기존 Azure Policy 정의를 제거합니다. |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'audit-keyvault-vnet-rules' --display-name 'Audit if Key Vault has no virtual network rules' --description 'Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' --mode Indexed)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'audit-keyvault-vnet-rules-assignment' --display-name 'Audit Key Vault Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r`)
```

### <a name="remove-with-azure-cli"></a>Azure CLI를 사용하여 제거

이전 할당 및 정의를 제거하려면 다음 명령을 실행합니다.

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Azure CLI 설명

| 명령 | 메모 |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | 새 Azure Policy 정의를 만듭니다. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | 단일 리소스 그룹을 가져옵니다. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | 새 Azure Policy 할당을 만듭니다. 이 예제에서는 정의를 제공하지만, 이니셔티브를 취할 수도 있습니다. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | 기존 Azure Policy 할당을 제거합니다. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | 기존 Azure Policy 정의를 제거합니다. |

## <a name="rest-api"></a>REST API

[ARMClient](https://github.com/projectkudu/ARMClient) 또는 PowerShell처럼 Resource Manager REST API와 상호 작용하는 데 사용할 수 있는 여러 도구가 있습니다.

### <a name="deploy-with-rest-api"></a>REST API를 사용하여 배포

- 정책 정의를 만들기(구독 범위) 요청 본문에 [정책 정의](#policy-definition) JSON 사용

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

- 정책 할당 만들기(리소스 그룹 범위)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

  요청 본문에 다음 JSON 예제 사용:

  ```json
  {
      "properties": {
          "displayName": "Audit Key Vault Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules"
      }
  }
  ```

### <a name="remove-with-rest-api"></a>REST API를 사용하여 제거

- 정책 할당 제거

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

- 정책 정의 제거

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

### <a name="rest-api-explanation"></a>REST API 설명

| 서비스 | 그룹 | 작업(Operation) | 메모 |
|---|---|---|---|
| 리소스 관리 | 정책 정의 | [만들기](/rest/api/resources/policydefinitions/createorupdate) | 구독에서 새 Azure Policy 정의를 만듭니다. 대안: [관리 그룹에서 만들기](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| 리소스 관리 | 정책 할당 | [만들기](/rest/api/resources/policyassignments/create) | 새 Azure Policy 할당을 만듭니다. 이 예제에서는 정의를 제공하지만, 이니셔티브를 취할 수도 있습니다. |
| 리소스 관리 | 정책 할당 | [삭제](/rest/api/resources/policyassignments/delete) | 기존 Azure Policy 할당을 제거합니다. |
| 리소스 관리 | 정책 정의 | [삭제](/rest/api/resources/policydefinitions/delete) | 기존 Azure Policy 정의를 제거합니다. 대안: [관리 그룹에서 삭제](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>다음 단계

- 추가 [Azure Policy 샘플](index.md) 검토
- [Azure Policy 정의 구조](../concepts/definition-structure.md) 검토