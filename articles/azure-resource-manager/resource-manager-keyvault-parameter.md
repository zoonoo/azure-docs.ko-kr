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
ms.date: 12/09/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 23fb716997145152bd09d177b75973ad0b4ca9f3
ms.openlocfilehash: 171cfe2a8750025914545701fa3423c7b9baa5f2


---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달

배포 중에 보안 값(예: 암호)을 매개 변수로 전달해야 할 경우 [Azure Key Vault](../key-vault/key-vault-whatis.md)에서 값을 검색할 수 있습니다. 매개 변수 파일에서 Key Vault 및 암호를 참조하여 이 값을 검색합니다. 해당 Key Vault ID만 참조하므로 이 값은 절대 노출되지 않습니다. 리소스를 배포할 때마다 수동으로 비밀 값을 입력하지 않아도 됩니다. Key Vault는 배포하는 리소스 그룹과는 다른 구독에 있을 수 있습니다. Key Vault를 참조할 때는 구독 ID를 포함합니다.

이 항목에서는 Key Vault 및 암호를 만들고, Resource Manager 템플릿의 암호에 대해 액세스 권한을 구성하고, 암호를 매개 변수로 전달하는 방법을 보여 줍니다. Key Vault 및 암호가 이미 있으나 템플릿 및 사용자 액세스 권한을 확인해야 할 경우 [암호에 대한 액세스를 사용하도록 설정](#enable-access-to-the-secret) 섹션으로 이동합니다. Key Vault 및 암호가 이미 있으며 템플릿 및 사용자 액세스 권한에 대해 구성된 경우 [정적 ID로 암호 참조](#reference-a-secret-with-static-id) 섹션으로 이동합니다. 

## <a name="deploy-a-key-vault-and-secret"></a>키 자격 증명 모음 및 암호 배포

Resource Manager 템플릿을 통해 Key Vault 및 암호를 배포할 수 있습니다. 예를 들어 [Key Vault 템플릿](resource-manager-template-keyvault.md) 및 [Key Vault 암호 템플릿](resource-manager-template-keyvault-secret.md)을 참조하세요. 키 자격 증명 모음을 만들 때 **enabledForTemplateDeployment** 속성을 **true**로 설정하여 다른 Resource Manager 템플릿에서 참조할 수 있습니다. 

또는 Azure Portal을 통해 Key Vault 및 암호를 만들 수 있습니다. 

1. **새로 만들기** -> **보안+ID** -> **Key Vault**를 선택합니다.

   ![새 Key Vault 만들기](./media/resource-manager-keyvault-parameter/new-key-vault.png)

2. Key Vault에 대한 값을 제공합니다. 지금은 **액세스 정책** 및 **고급 액세스 권한 정책** 설정을 무시해도 됩니다. 이러한 설정은 이 섹션에 설명되어 있습니다. **만들기**를 선택합니다.

   ![Key Vault 설정](./media/resource-manager-keyvault-parameter/create-key-vault.png)

3. 이제 Key Vault가 있습니다. 해당 Key Vault를 선택합니다.

4. Key Vault 블레이드에서 **암호**를 선택합니다.

   ![암호 선택](./media/resource-manager-keyvault-parameter/select-secret.png)

5. **추가**를 선택합니다.

   ![추가 선택](./media/resource-manager-keyvault-parameter/add-secret.png)

6. 업로드 옵션으로 **수동**을 선택합니다. 암호의 이름 및 값을 제공합니다. **만들기**를 선택합니다.

   ![암호 제공](./media/resource-manager-keyvault-parameter/provide-secret.png)

Key Vault 및 암호를 만들었습니다.

## <a name="enable-access-to-the-secret"></a>암호에 대한 액세스를 사용하도록 설정

새 Key Vault를 사용하든, 기존 Key Vault를 사용하든, 템플릿을 배포하는 사용자가 암호에 액세스할 수 있어야 합니다. 암호를 참조하는 템플릿을 배포하는 사용자에게는 Key Vault에 대한 `Microsoft.KeyVault/vaults/deploy/action` 권한이 있어야 합니다. [소유자](../active-directory/role-based-access-built-in-roles.md#owner) 및 [참여자](../active-directory/role-based-access-built-in-roles.md#contributor) 역할 모두 이 액세스 권한을 부여합니다. 또한 이 사용 권한을 부여 하는 [사용자 지정 역할](../active-directory/role-based-access-control-custom-roles.md)을 만들고 해당 역할에 사용자를 추가할 수도 있습니다. 또한 Resource Manager에 배포 동안 Key Vault에 액세스하는 기능을 부여해야 합니다.

포털을 통해 이러한 단계를 확인하거나 수행할 수 있습니다.

1. **액세스 제어(IAM)**를 선택합니다.

   ![액세스 제어 선택](./media/resource-manager-keyvault-parameter/select-access-control.png)

2. 템플릿 배포에 사용하려는 계정이 아직 소유자 또는 참가자가 아닌 경우(또는 `Microsoft.KeyVault/vaults/deploy/action` 권한을 부여하는 사용자 지정 역할에 추가됨) **추가**를 선택합니다.

   ![사용자 추가](./media/resource-manager-keyvault-parameter/add-user.png)

3. 참가자 또는 소유자 역할을 선택하고 해당 역할에 할당할 ID를 검색합니다. **확인**을 선택하여 역할에 대한 ID 추가를 완료합니다.

   ![사용자 추가](./media/resource-manager-keyvault-parameter/search-user.png)

4. 배포 동안 템플릿에서의 액세스를 허용하려면 **고급 액세스 권한 제어**를 선택합니다. **템플릿 배포를 위해 Azure Resource Manager에 대한 액세스 사용** 옵션을 선택합니다.

   ![템플릿 액세스 사용](./media/resource-manager-keyvault-parameter/select-template-access.png)

## <a name="reference-a-secret-with-static-id"></a>정적 ID로 암호 참조

템플릿에 값을 전달하는 **매개 변수 파일(템플릿 아님)** 내에서 암호를 참조합니다. 키 자격 증명 모음의 리소스 식별자와 암호 이름을 전달하여 암호를 참조합니다. 다음 예에서는 키 자격 증명 모음 비밀이 이미 있어야 하고, 리소스 ID에 정적 값을 제공합니다.

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

템플릿에서 암호를 허용하는 매개 변수는 **securestring**이어야 합니다. 다음 예제에서는 관리자 암호가 필요한 SQL 서버를 배포하는 템플릿의 관련 섹션을 보여 줍니다.

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




<!--HONumber=Dec16_HO2-->


