---
title: "Resource Manager 템플릿에서 Key Vault 비밀 | Microsoft Docs"
description: "배포하는 동안 키 자격 증명 모음의 암호를 매개 변수로 전달하는 방법을 보여 줍니다."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c582c144-4760-49d3-b793-a3e1e89128e2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: fa688748be96aa614b46a218e19a0e771b908baf


---
# <a name="pass-secure-values-during-deployment"></a>배포 중 보안 값 전달

배포하는 동안 보안 값(예: 암호)을 매개 변수로 전달해야 하는 경우 이 값을 [Azure Key Vault](../key-vault/key-vault-whatis.md)에 암호로 저장하고 매개 변수 파일에서 비밀을 참조할 수 있습니다. 해당 Key Vault ID만 참조하므로 이 값은 절대 노출되지 않습니다. 리소스를 배포할 때마다 수동으로 비밀 값을 입력하지 않아도 됩니다.

## <a name="deploy-a-key-vault-and-secret"></a>키 자격 증명 모음 및 암호 배포

키 자격 증명 모음 및 암호 배포에 대한 자세한 내용은 [키 자격 증명 모음 스키마](resource-manager-template-keyvault.md) 및 [키 자격 증명 모음 비밀 스키마](resource-manager-template-keyvault-secret.md)를 참조하세요. 키 자격 증명 모음을 만들 때 **enabledForTemplateDeployment** 속성을 **true**로 설정하여 다른 Resource Manager 템플릿에서 참조할 수 있습니다. 

## <a name="reference-a-secret-with-static-id"></a>정적 ID로 암호 참조

템플릿에 값을 전달하는 매개 변수 파일 내에서 비밀을 참조합니다. 키 자격 증명 모음의 리소스 식별자와 암호 이름을 전달하여 암호를 참조합니다. 다음 예에서는 키 자격 증명 모음 비밀이 이미 있어야 하고, 리소스 ID에 정적 값을 제공합니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "sqlsvrAdminLoginPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          },
          "secretName": "adminPassword"
        }
      },
      "sqlsvrAdminLogin": {
        "value": "exampleadmin"
      }
    }
}
```

암호를 허용하는 매개 변수는 **securestring**이어야 합니다. 다음 예제에서는 관리자 암호가 필요한 SQL 서버를 배포하는 템플릿의 관련 섹션을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlsvrAdminLogin": {
            "type": "string",
            "minLength": 4
        },
        "sqlsvrAdminLoginPassword": {
            "type": "securestring"
        },
        ...
    },
    "resources": [
        {
          "name": "[variables('sqlsvrName')]",
          "type": "Microsoft.Sql/servers",
          "location": "[resourceGroup().location]",
          "apiVersion": "2014-04-01-preview",
          "properties": {
              "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
              "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
          },
          ...
        }
    ],
    "variables": {
        "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
    },
    "outputs": { }
}
```

비밀을 참조하는 템플릿을 배포하는 사용자에게는 키 자격 증명 모음에 대한 **Microsoft.KeyVault/vaults/deploy/action** 권한이 있어야 합니다. [소유자](../active-directory/role-based-access-built-in-roles.md#owner) 및 [참여자](../active-directory/role-based-access-built-in-roles.md#contributor) 역할 모두 이 액세스 권한을 부여합니다. 또한 이 사용 권한을 부여 하는 [사용자 지정 역할](../active-directory/role-based-access-control-custom-roles.md)을 만들고 해당 역할에 사용자를 추가할 수도 있습니다.

## <a name="reference-a-secret-with-dynamic-id"></a>동적 ID로 암호 참조

이전 섹션에서는 키 자격 증명 모음 암호에 대해 정적 리소스 ID를 전달하는 방법을 살펴보았습니다. 하지만, 일부 시나리오에서는, 현재 배포를 기반으로 달라지는 키 자격 증명 모음 암호를 참조해야 합니다. 이런 경우, 매개 변수 파일에 리소스 ID를 하드 코딩할 수 없습니다. 아쉽게도, 매개 변수 파일에 템플릿 식이 허용되지 않기 때문에 매개 변수 파일에 리소스 ID를 동적으로 생성할 수 없습니다.

키 자격 증명 모음 암호에 대한 리소스 ID를 동적으로 생성하려면, 암호가 필요한 리소스를 중첩된 템플릿으로 이동해야 합니다. 마스터 템플릿에서, 중첩된 템플릿을 추가하고 동적으로 생성된 리소스 ID를 포함하는 매개 변수에 전달합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }],
    "outputs": {}
}
```

## <a name="next-steps"></a>다음 단계
* 키 자격 증명 모음에 대한 일반 정보는 [Azure Key Vault 시작](../key-vault/key-vault-get-started.md)을 참조하세요.
* 가상 컴퓨터에서 키 자격 증명 모음을 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager에 대한 보안 고려 사항](best-practices-resource-manager-security.md)을 참조하세요.
* 키 암호를 참조하는 전체 예제는 [키 자격 증명 모음 예제](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)를 참조하세요.




<!--HONumber=Nov16_HO3-->


