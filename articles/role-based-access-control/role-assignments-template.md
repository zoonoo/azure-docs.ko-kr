---
title: RBAC 및 Azure Resource Manager 템플릿을 사용하여 Azure 리소스에 대한 액세스 관리 | Microsoft Docs
description: RBAC(역할 기반 액세스 제어) 및 Azure Resource Manager 템플릿을 사용하여 Azure 리소스에 대한 사용자, 그룹 및 애플리케이션의 액세스를 관리하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e6511ff84c251577a5ff483f892387ab7d3d4d41
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360474"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>RBAC 및 Azure Resource Manager 템플릿을 사용하여 Azure 리소스에 대한 액세스 관리

[RBAC(역할 기반 액세스 제어)](overview.md)는 Azure 리소스에 대한 액세스를 관리하는 방법입니다. Azure PowerShell 또는 Azure CLI를 사용하는 것 외에도 RBAC 및 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md)을 사용하여 Azure 리소스에 대한 액세스를 관리할 수 있습니다. 템플릿은 리소스를 일관되고 반복적으로 배포해야 하는 경우 유용할 수 있습니다. 이 문서에서는 RBAC 및 템플릿을 사용하여 액세스를 관리할 수 있는 방법을 설명합니다.

## <a name="assign-role-to-resource-group-or-subscription"></a>리소스 그룹 또는 구독에 역할 할당

RBAC에서 액세스 권한을 부여하기 위해 역할 할당을 만듭니다. 다음 템플릿은 다음을 보여줍니다.
- 리소스 그룹 또는 구독 범위에서 사용자, 그룹 또는 응용 프로그램에 역할을 할당 하는 방법
- 매개 변수로 소유자, 기여자 및 읽기 권한자 역할 지정하는 방법

템플릿을 사용하려면 다음 입력을 지정해야 합니다.
- 역할을 할당하기 위한 사용자, 그룹 또는 애플리케이션의 고유 식별자
- 할당할 역할
- 역할 할당에 사용 되는 고유 식별자입니다. 또는 기본 식별자를 사용할 수 있습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

다음은 템플릿을 배포한 후 리소스 그룹의 사용자에 대 한 읽기 권한자 역할 할당의 예를 보여 줍니다.

![템플릿을 사용하여 역할 할당](./media/role-assignments-template/role-assignment-template.png)

역할 할당의 범위는 배포 수준에서 결정 됩니다. 이 문서에서는 리소스 그룹 및 구독 수준 배포 명령이 모두 표시 됩니다.

## <a name="assign-role-to-resource"></a>리소스에 역할 할당

리소스 수준에서 역할 할당을 만들어야 하는 경우 역할 할당의 형식이 다릅니다. 역할을 할당할 리소스의 리소스 공급자 네임 스페이스 및 리소스 형식을 제공 합니다. 또한 역할 할당 이름에 리소스 이름을 포함 합니다.

역할 할당의 유형 및 이름에는 다음 형식을 사용 합니다.

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

다음 템플릿은 저장소 계정을 배포 하 고 역할을 할당 합니다. 리소스 그룹 명령을 사용 하 여 배포 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "apiVersion": "2019-04-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[parameters('location')]",
      "sku": {
          "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleNameGuid'))]",
      "dependsOn": [
          "[variables('storageName')]"
      ],
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

다음은 템플릿을 배포한 후 사용자에 게 저장소 계정에 대 한 참가자 역할 할당의 예를 보여 줍니다.

![템플릿을 사용하여 역할 할당](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="deploy-template-using-azure-powershell"></a>Azure PowerShell을 사용하여 템플릿 배포

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Azure PowerShell를 사용 하 여 리소스 그룹이 나 구독에 이전 템플릿을 배포 하려면 다음 단계를 수행 합니다.

1. rbac-rg.json이라는 새 파일을 만들고 이전 템플릿을 복사합니다.

1. [Azure PowerShell](/powershell/azure/authenticate-azureps)에 로그인합니다.

1. 사용자, 그룹 또는 애플리케이션의 고유 식별자를 가져옵니다. 예를 들어 [Get-AzADUser](/powershell/module/az.resources/get-azaduser) 명령을 사용하여 Azure AD 사용자를 나열할 수 있습니다.

    ```azurepowershell
    $userid = (Get-AzADUser -DisplayName "{name}").id
    ```

1. 템플릿은 역할 할당을 식별 하는 데 사용 되는 GUID에 대 한 기본값을 생성 합니다. 특정 GUID를 지정 해야 하는 경우에는 roleNameGuid 매개 변수에 대해 해당 값을 전달 합니다. 식별자의 형식은 `11111111-1111-1111-1111-111111111111`입니다.

리소스 또는 리소스 그룹 수준에서 역할을 할당 하려면 다음 단계를 수행 합니다.

1. 예제 리소스 그룹 만듭니다.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 명령을 사용하여 배포를 시작합니다.

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    ```

    다음은 출력 예제입니다.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

구독 수준에서 역할을 할당 하려면 [AzDeployment](/powershell/module/az.resources/new-azdeployment) 명령을 사용 하 고 배포 위치를 지정 합니다.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
```

리소스 그룹에 대 한 배포 명령과 유사한 출력이 있습니다.

## <a name="deploy-template-using-the-azure-cli"></a>Azure CLI를 사용하여 템플릿 배포

Azure CLI를 사용 하 여 리소스 그룹이 나 구독에 이전 템플릿을 배포 하려면 다음 단계를 수행 합니다.

1. rbac-rg.json이라는 새 파일을 만들고 이전 템플릿을 복사합니다.

1. [Azure CLI](/cli/azure/authenticate-azure-cli)에 로그인합니다.

1. 사용자, 그룹 또는 애플리케이션의 고유 식별자를 가져옵니다. 예를 들어 [az ad user show](/cli/azure/ad/user#az-ad-user-show) 명령을 사용 하 여 Azure ad 사용자를 표시할 수 있습니다.

    ```azurecli
    userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
    ```

1. 템플릿은 역할 할당을 식별 하는 데 사용 되는 GUID에 대 한 기본값을 생성 합니다. 특정 GUID를 지정 해야 하는 경우에는 roleNameGuid 매개 변수에 대해 해당 값을 전달 합니다. 식별자의 형식은 `11111111-1111-1111-1111-111111111111`입니다.

리소스 또는 리소스 그룹 수준에서 역할을 할당 하려면 다음 단계를 수행 합니다.

1. 예제 리소스 그룹 만듭니다.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) 명령을 사용하여 배포를 시작합니다.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    ```

    다음은 출력 예제입니다.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```

구독 수준에서 역할을 할당 하려면 [az deployment create](/cli/azure/deployment#az-deployment-create) 명령을 사용 하 고 배포 위치를 지정 합니다.

```azurecli
az deployment create --location centralus --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
```

리소스 그룹에 대 한 배포 명령과 유사한 출력이 있습니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Azure Resource Manager 템플릿의 구조 및 구문 이해](../azure-resource-manager/resource-group-authoring-templates.md)
- [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?term=rbac)
