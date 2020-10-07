---
title: Azure Resource Manager 템플릿을 사용하여 Azure Attestation 인증서 만들기
description: Azure Resource Manager 템플릿을 사용하여 Azure Attestation 인증서를 만드는 방법을 알아봅니다.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mbaldwin
ms.date: 08/31/2020
ms.openlocfilehash: a73294e41f27a15d70ab95b3aa7b100cab5320b4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89237118"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure Attestation 공급자 만들기

[Microsoft Azure Attestation](overview.md)은 TEE(신뢰할 수 있는 실행 환경)를 증명하기 위한 솔루션입니다. 이 빠른 시작에서는 Microsoft Azure Attestation 정책을 만들기 위해 ARM 템플릿(Azure Resource Manager 템플릿)을 배포하는 프로세스에 중점을 둡니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)


## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-attestation-provider-create)에서 나온 것입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "name": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "tags": {
      "type": "Object"
    },
    "policySigningCertificates": {
      "type": "string"
    }
  },
  "variables": {
    "PolicySigningCertificates": {
      "PolicySigningCertificates": {
        "keys": [
          {
            "kty": "RSA",
            "use": "sig",
            "x5c": [ "[parameters('policySigningCertificates')]" ]
          }
        ]
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('name')]",
      "location": "[parameters('location')]",
      "type": "Microsoft.Attestation/attestationProviders",
      "tags": "[parameters('tags')]",
      "Properties": "[if(empty(parameters('policySigningCertificates')), json('{}'), variables('PolicySigningCertificates'))]"
    }
  ]
}
```

템플릿에 정의된 Azure 리소스:
- Microsoft.Attestation/attestationProviders

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하여 Azure에 로그인하고 템플릿을 엽니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

2. 다음 값을 선택하거나 입력합니다.

    지정되지 않은 경우 기본 값을 사용하여 증명 공급자를 만듭니다.

    * **name**: Azure Attestation 공급자의 이름을 선택합니다.
    * **location**: 위치를 선택합니다. 예: **미국 중부**
    * **태그**: 위치를 선택합니다. 예: **미국 중부**

3. **구매**를 선택합니다. 증명 리소스가 성공적으로 배포되면 알림을 받게 됩니다.

Azure Portal은 템플릿을 배포하는데 사용됩니다. Azure Portal 외에도 Azure PowerShell, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 증명 리소스를 확인할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure Attestation은 이 빠른 시작을 기반으로 빌드됩니다. 이후의 빠른 시작 및 자습서를 계속 진행하려는 경우 이러한 리소스를 유지하는 것이 좋습니다.

더 이상 필요하지 않는 경우 리소스 그룹을 삭제하여 증명 리소스를 삭제합니다. Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 삭제하려면 다음을 수행합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿을 사용하여 증명 리소스를 만들고 배포의 유효성을 검사했습니다. Azure Attestation에 대해 자세히 알아보려면 [Azure Attestation 개요](overview.md)를 참조하세요.
