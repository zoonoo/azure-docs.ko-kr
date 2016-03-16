<properties
   pageTitle="역할 할당에 대한 리소스 관리자 템플릿 | Microsoft Azure"
   description="템플릿을 통해 역할 할당을 배포하기 위한 리소스 관리자 스키마를 보여 줍니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/04/2016"
   ms.author="tomfitz"/>

# 역할 할당 템플릿 스키마

지정된 범위의 역할에 사용자, 그룹 또는 서비스 주체를 할당합니다.

## 스키마 형식

역할 할당을 만들려면 템플릿의 리소스 섹션에 다음 스키마를 추가합니다.
    
    {
        "type": "Microsoft.Authorization/roleAssignments",
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

## 값

다음 표에서는 스키마에 설정해야 하는 값에 대해 설명합니다.

| 이름 | 형식 | 필수 | 허용된 값 | 설명 |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | 예 | **Microsoft.Authorization/roleAssignments** | 만들려는 리소스 종류입니다. |
| apiVersion | enum | 예 | **2015-07-01** | 리소스를 만들 때 사용하는 API 버전입니다. |  
| name | string | 예 | GUID | 새 역할 할당에 대한 식별자입니다. |
| dependsOn | array | 아니요 | 리소스 이름 또는 리소스 고유 식별자의 쉼표로 구분된 목록입니다. | 이 역할 할당이 종속된 리소스의 컬렉션입니다. 리소스 범위의 역할을 할당하는 경우 해당 리소스가 동일한 템플릿에서 배포되면 해당 리소스 이름을 이 요소에 포함하여 리소스가 먼저 배포되도록 합니다. | 
| properties | object | 예 | (아래 참조) | 역할 정의, 서비스 주체 및 범위를 식별하는 개체입니다. |  

### properties 개체

| 이름 | 형식 | 필수 | 허용된 값 | 설명 |
| ------- | ---- | ---------------- | -------- | ----------- |
| roleDefinitionId | string | 예 | **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** | 역할 할당에서 사용할 기존 역할 정의의 식별자입니다. |
| principalId | string | 예 | GUID | 기존 서비스 주체의 식별자입니다. 이 식별자는 디렉터리 내의 ID에 매핑되며 사용자, 서비스 주체 또는 보안 그룹을 가리킬 수 있습니다. |
| scope | string | 예 | 리소스 그룹의 경우:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}**<br /><br />리소스의 경우:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** | 이 역할 할당이 적용되는 범위입니다. |


## 역할 할당 리소스를 사용하는 방법

배포 중에 사용자, 그룹 또는 서비스 주체를 역할에 추가해야 하는 경우 역할 할당을 템플릿에 추가합니다. 역할 할당은 더 높은 수준의 범위에서 상속되므로 구독 수준에서 서비스 주체를 역할에 이미 추가한 경우 리소스 그룹이나 리소스에 대해 이를 다시 할당할 필요가 없습니다.

다음을 사용하여 **name**에 대한 새 식별자를 생성할 수 있습니다.

    PS C:\> $name = [System.Guid]::NewGuid().toString()

다음을 사용하여 역할 정의에 대한 전역 고유 식별자를 검색할 수 있습니다.

    PS C:\> $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

다음 명령 중 하나를 사용하여 서비스 주체에 대한 식별자를 검색할 수 있습니다.

**Auditors**라는 그룹의 경우:

    PS C:\> $principal = (Get-AzureRmADGroup -SearchString Auditors).id

**exampleperson**이라는 사용자의 경우:

    PS C:\> $principal = (Get-AzureRmADUser -SearchString exampleperson).id

**exampleapp**이라는 서비스 주체의 경우:

    PS C:\> $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 
 

## 예

다음 예제에서는 리소스 그룹에 대한 역할에 그룹을 할당합니다.

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

## 빠른 시작 템플릿

다음 템플릿은 역할 할당 리소스를 사용하는 방법을 보여 줍니다.

- [리소스 그룹에 기본 제공 역할 할당](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-resourcegroup)
- [기존 VM에 기본 제공 역할 할당](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-virtualmachine)
- [여러 기존 VM에 기본 제공 역할 할당](https://github.com/Azure/azure-quickstart-templates/tree/master/201-rbac-builtinrole-multipleVMs)

## 다음 단계

- 템플릿 구조에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
- 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure Active Directory 역할 기반 액세스 제어](active-directory/role-based-access-control-configure.md)를 참조하세요.

<!---HONumber=AcomDC_0107_2016-->