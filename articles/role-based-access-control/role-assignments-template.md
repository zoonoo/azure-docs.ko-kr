---
title: RBAC 및 Azure Resource Manager 템플릿을 사용하여 액세스 관리 | Microsoft Docs
description: RBAC(역할 기반 액세스 제어) 및 Azure Resource Manager 템플릿을 사용하여 사용자, 그룹 및 애플리케이션의 액세스 권한을 관리하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5e080614d4f0001a0bf1b44dd402f37db2463e03
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206696"
---
# <a name="manage-access-using-rbac-and-azure-resource-manager-templates"></a>RBAC 및 Azure Resource Manager 템플릿을 사용하여 액세스 관리

[RBAC(역할 기반 액세스 제어)](overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. Azure PowerShell 또는 Azure CLI를 사용하는 것 외에도 RBAC 및 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md)을 사용하여 Azure 리소스에 대한 액세스를 관리할 수 있습니다. 템플릿은 리소스를 일관되고 반복적으로 배포해야 하는 경우 유용할 수 있습니다. 이 문서에서는 RBAC 및 템플릿을 사용하여 액세스를 관리할 수 있는 방법을 설명합니다.

## <a name="example-template-to-create-a-role-assignment"></a>역할 할당을 만드는 예제 템플릿

RBAC에서 액세스 권한을 부여하기 위해 역할 할당을 만듭니다. 다음 템플릿은 다음을 보여줍니다.
- 리소스 그룹 범위에서 사용자, 그룹 또는 애플리케이션에 역할을 할당하는 방법
- 매개 변수로 소유자, 기여자 및 읽기 권한자 역할 지정하는 방법

템플릿을 사용하려면 다음 입력을 지정해야 합니다.
- 리소스 그룹의 이름
- 역할을 할당하기 위한 사용자, 그룹 또는 애플리케이션의 고유 식별자
- 할당할 역할
- 역할 할당에 사용할 고유 식별자

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
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "scope": "[resourceGroup().id]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2017-05-01",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('scope')]"
      }
    }
  ]
}
```

다음에서는 템플릿을 배포한 후 사용자에게 읽기 권한자 역할을 할당하는 예를 보여줍니다.

![템플릿을 사용하여 역할 할당](./media/role-assignments-template/role-assignment-template.png)

## <a name="deploy-template-using-azure-powershell"></a>Azure PowerShell을 사용하여 템플릿 배포

Azure PowerShell을 사용하여 이전 템플릿을 배포하려면 다음 단계를 수행합니다.

1. rbac-rg.json이라는 새 파일을 만들고 이전 템플릿을 복사합니다.

1. [Azure PowerShell](/powershell/azure/authenticate-azureps)에 로그인합니다.

1. 사용자, 그룹 또는 애플리케이션의 고유 식별자를 가져옵니다. 예를 들어 [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) 명령을 사용하여 Azure AD 사용자를 나열할 수 있습니다.

    ```azurepowershell
    Get-AzureRmADUser
    ```

1. GUID 도구를 사용하여 역할 할당에 사용할 고유 식별자를 생성합니다. 식별자의 형식은 `11111111-1111-1111-1111-111111111111`입니다.

1. 예제 리소스 그룹 만듭니다.

    ```azurepowershell
    New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) 명령을 사용하여 배포를 시작합니다.

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    ```

    필수 매개 변수를 지정하라는 메시지가 표시됩니다. 다음은 출력 예제입니다.

    ```Output
    PS /home/user> New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    
    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    principalId: 22222222-2222-2222-2222-222222222222
    builtInRoleType: Reader
    roleNameGuid: 11111111-1111-1111-1111-111111111111
    
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

## <a name="deploy-template-using-the-azure-cli"></a>Azure CLI를 사용하여 템플릿 배포

Azure CLI를 사용하여 이전 템플릿을 배포하려면 다음 단계를 수행합니다.

1. rbac-rg.json이라는 새 파일을 만들고 이전 템플릿을 복사합니다.

1. [Azure CLI](/cli/azure/authenticate-azure-cli)에 로그인합니다.

1. 사용자, 그룹 또는 애플리케이션의 고유 식별자를 가져옵니다. 예를 들어 [az ad user list](/cli/azure/ad/user#az-ad-user-list) 명령을 사용하여 Azure AD 사용자를 나열할 수 있습니다.

    ```azurecli
    az ad user list
    ```

1. GUID 도구를 사용하여 역할 할당에 사용할 고유 식별자를 생성합니다. 식별자의 형식은 `11111111-1111-1111-1111-111111111111`입니다.

1. 예제 리소스 그룹 만듭니다.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) 명령을 사용하여 배포를 시작합니다.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    ```

    필수 매개 변수를 지정하라는 메시지가 표시됩니다. 다음은 출력 예제입니다.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    Please provide string value for 'principalId' (? for help): 22222222-2222-2222-2222-222222222222
    Please provide string value for 'builtInRoleType' (? for help):
     [1] Owner
     [2] Contributor
     [3] Reader
    Please enter a choice [1]: 3
    Please provide string value for 'roleNameGuid' (? for help): 11111111-1111-1111-1111-111111111111
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
    
## <a name="next-steps"></a>다음 단계

- [첫 번째 Azure Resource Manager 템플릿을 만들고 배포](../azure-resource-manager/resource-manager-create-first-template.md)
- [Azure Resource Manager 템플릿의 구조 및 구문 이해](../azure-resource-manager/resource-group-authoring-templates.md)
- [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?term=rbac)
