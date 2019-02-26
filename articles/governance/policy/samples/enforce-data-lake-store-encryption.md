---
title: 샘플 - Data Lake Store에 대한 암호화 필요
description: 이 샘플 정책 정의에서는 Data Lake Store 암호화를 사용하도록 설정해야 합니다.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: e4de4c78bec9f4ffa6c4e2711a2d4260279ca559
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56340606"
---
# <a name="sample---require-data-lake-store-encryption"></a>샘플 - Data Lake Store 암호화 필요

이 기본 제공 정책은 암호화가 설정되지 않은 모든 Data Lake Store 계정을 거부합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>샘플 템플릿

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DataLakeStore/accounts"
      },
      {
        "field": "Microsoft.DataLakeStore/accounts/encryptionState",
        "equals": "Disabled"
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

[PowerShell](#deploy-with-powershell) 또는 [Azure CLI](#deploy-with-azure-cli)와 함께 [Azure Portal](#deploy-with-the-portal)을 사용하여 이 템플릿을 배포할 수 있습니다. 기본 제공 정책을 가져오려면 ID `a7ff3161-0087-490a-9ad9-ad6217f4f43a`를 사용합니다.

## <a name="deploy-with-the-portal"></a>포털을 사용하여 배포

정책에 할당할 경우 사용할 수 있는 기본 제공 정의에서 **DataLakeStore 계정에 암호화 적용**을 선택합니다.

## <a name="deploy-with-powershell"></a>PowerShell을 사용하여 배포 

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/a7ff3161-0087-490a-9ad9-ad6217f4f43a

New-AzPolicyAssignment -name "Data Lake Store encryption" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>PowerShell 배포 정리

정책 할당을 제거하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "Data Lake Store encryption" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "Data Lake Store encryption" --policy a7ff3161-0087-490a-9ad9-ad6217f4f43a
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI 배포 정리

정책 할당을 제거하려면 다음 명령을 실행합니다.

```azurecli-interactive
az policy assignment delete --name "Data Lake Store encryption" --resource-group myResourceGroup
```

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](index.md)에서 더 많은 샘플 검토