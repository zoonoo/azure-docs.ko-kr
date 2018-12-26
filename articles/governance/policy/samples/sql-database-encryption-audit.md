---
title: 샘플 ‑ SQL Database에 대한 투명한 데이터 암호화 감사
description: 이 샘플 정책은 SQL Database가 투명한 데이터 암호화를 사용하지 않는지를 감사합니다.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 10/29/2018
ms.author: dacoulte
ms.openlocfilehash: 36eb62dfa7ae45b70f5907241c66f2286f9ce760
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310626"
---
# <a name="audit-sql-database-encryption"></a>SQL Database 암호화 감사

이 기본 제공 정책은 SQL Database가 투명한 데이터 암호화를 사용하지 않는지를 감사합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>샘플 템플릿

[!code-json[main](../../../../policy-templates/samples/SQL/audit-sql-db-tde-status/azurepolicy.json "Audit TDE for SQL Database")]

[PowerShell](#deploy-with-powershell) 또는 [Azure CLI](#deploy-with-azure-cli)와 함께 [Azure Portal](#deploy-with-the-portal)을 사용하여 이 템플릿을 배포할 수 있습니다. 기본 제공 정책을 가져오려면 ID `17k78e20-9358-41c9-923c-fb736d382a12`를 사용합니다.

## <a name="deploy-with-the-portal"></a>포털을 사용하여 배포

정책을 할당할 경우 사용할 수 있는 기본 제공 정의에서 **투명한 데이터 암호화 상태 감사**를 선택합니다.

## <a name="deploy-with-powershell"></a>PowerShell을 사용하여 배포 

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/17k78e20-9358-41c9-923c-fb736d382a12

New-AzureRmPolicyAssignment -name "SQL TDE Audit" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>PowerShell 배포 정리

정책 할당을 제거하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name "SQL TDE Audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL TDE Audit" --policy 17k78e20-9358-41c9-923c-fb736d382a12
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI 배포 정리

정책 할당을 제거하려면 다음 명령을 실행합니다.

```azurecli-interactive
az policy assignment delete --name "SQL TDE Audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](index.md)에서 더 많은 샘플 검토