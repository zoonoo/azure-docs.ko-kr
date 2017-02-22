---
title: "역할 할당에 대한 Resource Manager 템플릿 | Microsoft Docs"
description: "템플릿을 통해 역할 할당을 배포하기 위한 리소스 관리자 스키마를 보여 줍니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: d9541169-00d1-4497-b57f-68896d51849b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 69767db0399d02b82495da475cb8c5bf25710c87


---
# <a name="role-assignments-template-schema"></a>역할 할당 템플릿 스키마
지정된 범위의 역할에 사용자, 그룹 또는 서비스 주체를 할당합니다.

## <a name="resource-format"></a>리소스 형식
역할 할당을 만들려면 템플릿의 리소스 섹션에 다음 스키마를 추가합니다.

    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>값
다음 표에서는 스키마에 설정해야 하는 값에 대해 설명합니다.

| 이름 | 필수 | 설명 |
| --- | --- | --- |
| type |예 |만들려는 리소스 종류입니다.<br /><br /> 리소스 그룹:<br />**Microsoft.Authorization/roleAssignments**<br /><br />리소스:<br />**{provider-namespace}/{resource-type}/providers/roleAssignments** |
| apiVersion |예 |리소스를 만들 때 사용하는 API 버전입니다.<br /><br /> **2015-07-01** 사용. |
| name |예 |새 역할 할당에 대한 전역 고유 식별자입니다. |
| dependsOn |아니요 |리소스 이름 또는 리소스 고유 식별자의 쉼표로 구분된 배열입니다.<br /><br />이 역할 할당이 종속된 리소스의 컬렉션입니다. 리소스 범위의 역할을 할당하는 경우 해당 리소스가 동일한 템플릿에서 배포되면 해당 리소스 이름을 이 요소에 포함하여 리소스가 먼저 배포되도록 합니다. |
| properties |예 |역할 정의, 서비스 주체 및 범위를 식별하는 속성 개체입니다. |

### <a name="properties-object"></a>properties 개체
| 이름 | 필수 | 설명 |
| --- | --- | --- |
| roleDefinitionId |예 |역할 할당에서 사용할 기존 역할 정의의 식별자입니다.<br /><br /> 이때 다음 형식을 사용합니다.<br /> **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId |예 |기존 서비스 주체의 전역 고유 식별자입니다. 이 값은 디렉터리 내의 ID에 매핑되며 사용자, 서비스 주체 또는 보안 그룹을 가리킬 수 있습니다. |
| scope |아니요 |이 역할 할당이 적용되는 범위입니다.<br /><br />리소스 그룹의 경우 다음을 사용합니다.<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}**  <br /><br />리소스의 경우 다음을 사용합니다.<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |

## <a name="how-to-use-the-role-assignment-resource"></a>역할 할당 리소스를 사용하는 방법
배포 중에 사용자, 그룹 또는 서비스 주체를 역할에 추가해야 하는 경우 역할 할당을 템플릿에 추가합니다. 역할 할당은 더 높은 수준의 범위에서 상속되므로 구독 수준에서 서비스 주체를 역할에 이미 추가한 경우 리소스 그룹이나 리소스에 대해 이를 다시 할당할 필요가 없습니다.

역할 할당을 작업할 때 제공해야 하는 많은 ID 값이 있습니다. PowerShell 또는 Azure CLI를 통해 값을 검색할 수 있습니다.

### <a name="powershell"></a>PowerShell
역할 할당 이름에는 전역 고유 식별자가 필요합니다. 다음을 사용하여 **name** 에 대한 새 식별자를 생성할 수 있습니다.

    $name = [System.Guid]::NewGuid().toString()

다음을 사용하여 역할 정의에 대한 식별자를 검색할 수 있습니다.

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

다음 명령 중 하나를 사용하여 서비스 주체에 대한 식별자를 검색할 수 있습니다.

**Auditors**라는 그룹의 경우:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

**exampleperson**이라는 사용자의 경우:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

**exampleapp**이라는 서비스 주체의 경우:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>Azure CLI
다음을 사용하여 역할 정의에 대한 식별자를 검색할 수 있습니다.

    azure role show Reader --json | jq .[].Id -r

다음 명령 중 하나를 사용하여 서비스 주체에 대한 식별자를 검색할 수 있습니다.

**Auditors**라는 그룹의 경우:

    azure ad group show --search Auditors --json | jq .[].objectId -r

**exampleperson**이라는 사용자의 경우:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

**exampleapp**이라는 서비스 주체의 경우:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>예
다음 템플릿은 역할에 대한 식별자와 사용자, 그룹 또는 서비스 주체에 대한 식별자를 수신합니다. 리소스 그룹 수준에서 역할을 할당합니다.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



다음 템플릿은 저장소 계정을 만들고 읽기 역할을 저장소 계정에 할당합니다. 배포를 간소화하기 위해 두 그룹 및 읽기 역할에 대한 식별자가 템플릿에 포함됩니다. 이러한 값은 스크립트를 통해 배포 시 검색할 수 있으며 매개 변수로 전달됩니다.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>빠른 시작 템플릿
다음 템플릿은 역할 할당 리소스를 사용하는 방법을 보여 줍니다.

* [리소스 그룹에 기본 제공 역할 할당](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
* [기존 VM에 기본 제공 역할 할당](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
* [여러 기존 VM에 기본 제공 역할 할당](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>다음 단계
* 템플릿 구조에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
* 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure Active Directory 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)를 참조하세요.




<!--HONumber=Nov16_HO3-->


