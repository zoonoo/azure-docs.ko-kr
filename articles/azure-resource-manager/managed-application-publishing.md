---
title: "Azure Managed Application 만들기 및 게시 | Microsoft Docs"
description: "ISV 또는 파트너에서 Azure Managed Application을 만드는 방법을 보여 줍니다."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/08/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 56dd68e328abd6c1dacdf7a8e051ca6b3cd07083
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---
# <a name="create-and-publish-an-azure-managed-application"></a>Azure Managed Application 만들기 및 게시 

[Managed Application 개요](managed-application-overview.md) 문서에서 설명한 대로 종단 간 환경에는 두 가지 시나리오가 있습니다. 하나는 관리되는 응용 프로그램을 만들어 고객이 사용할 수 있게 하려는 게시자 또는 ISV입니다. 다른 하나는 관리되는 응용 프로그램의 고객 또는 소비자입니다. 이 문서에서는 첫 번째 시나리오를 중점적으로 다루며, ISV에서 관리되는 응용 프로그램을 만들고 게시하는 방법에 대해 설명합니다. 

관리되는 응용 프로그램을 만들려면 다음을 만들어야 합니다.

* 템플릿 파일이 포함된 패키지
* 고객의 구독에 속한 리소스 그룹에 액세스할 수 있는 사용자, 그룹 또는 응용 프로그램
* 어플라이언스 정의

파일의 예제는 [Managed Application 샘플](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)을 참조하세요.

## <a name="create-managed-application-package"></a>관리되는 응용 프로그램 패키지 만들기

첫 번째 단계는 기본 템플릿 파일이 포함된 관리되는 응용 프로그램 패키지를 만드는 것입니다. 게시자 또는 ISV에서 세 개의 파일을 만듭니다. 

* 첫 번째 파일은 **applianceMainTemplate.json**입니다. 이 템플릿 파일은 관리되는 응용 프로그램의 일부로 프로비전되는 실제 리소스를 정의합니다. 예를 들어 관리되는 응용 프로그램을 사용하여 저장소 계정을 만들기 위해 applianceMainTemplate.json에서 다음을 포함합니다. 

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
            "storageAccountName": {
                "type": "String"
          }
      },
      "resources": [{
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('storageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "westus",
          "sku": {
              "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {        
          }
      }],
      "outputs": {        
      }
  }
  ```

* 게시자가 만들어야 하는 두 번째 파일은 **mainTemplate.json**입니다. 이 템플릿 파일에는 어플라이언스 리소스(Microsoft.Solutions/appliances)만 포함됩니다. 또한 applianceMainTemplate.json의 리소스에 필요한 모든 매개 변수도 포함됩니다. 

  관리되는 응용 프로그램을 만드는 중에 입력으로 필요한 두 가지 중요한 속성이 있습니다. **managedResourceGroupId** 속성은 applianceMainTemplate.json에 정의된 리소스를 만드는 리소스 그룹의 ID입니다. ID의 형식은 다음과 같습니다. `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`

  **applianceDefinitionId** 속성은 관리되는 응용 프로그램 정의 리소스의 ID입니다. ID의 형식은 다음과 같습니다. `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Solutions/applianceDefinitions/{applianceDefinitionName}`

  이러한 두 값에 대한 매개 변수를 제공하면 소비자가 관리되는 응용 프로그램을 만들 때 지정할 수 있습니다. 아래 예제에서 이러한 속성에 해당하는 두 매개 변수는 managedByResourceGroup 및 applianceDefinitonId입니다.

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          },
          "applianceDefinitionId": {
              "type": "String"
          },
          "managedByResourceGroup": {
              "type": "String"
          },
          "applianceName": {
              "type": "String"
          },
      },
      "variables": {            
      },
      "resources": [{
          "type": "Microsoft.Solutions/appliances",
          "name": "[parameters('applianceName')]",
          "apiVersion": "2016-09-01-preview",
          "location": "[resourceGroup().location]",
          "kind": "ServiceCatalog",
          "properties": {
              "ManagedResourceGroupId": "[parameters('managedByResourceGroup')]",
              "applianceDefinitionId": "[parameters('applianceDefinitionId')]",
              "Parameters": {
                  "storageAccountName": {
                      "value": "[parameters('storageAccountName')]"
                  }                
              }
          }
      }]
  }
  ```

* 패키지에 필요한 세 번째 파일은 **createUiDefinition.json**입니다. Azure Portal에서 이 파일을 사용하여 관리되는 응용 프로그램을 만드는 사용자를 위한 사용자 인터페이스를 생성합니다. 관리되는 응용 프로그램에 대한 매개 변수를 정의하고, 소비자가 각 매개 변수에 대한 입력을 얻을 수 있는 방법을 정의합니다. 드롭다운 선택기, 텍스트 상자, 암호 상자 및 기타 입력 도구와 같은 옵션을 사용할 수 있습니다. 관리되는 응용 프로그램에 대한 UI 정의 파일을 만드는 방법은 [CreateUiDefinition 시작](managed-application-createuidefinition-overview.md)을 참조하세요.

필요한 파일이 모두 준비되면 패키지를 사용할 수 있는 위치에 해당 패키지를 업로드합니다.


## <a name="create-azure-ad-user-group-or-application"></a>Azure AD 사용자 그룹 또는 응용 프로그램 만들기
다음으로 고객을 대신하여 리소스를 관리하는 데 사용하려는 사용자 그룹 또는 응용 프로그램을 만듭니다. 이 사용자 그룹 또는 응용 프로그램에는 역할에서 설명한 대로 관리되는 리소스 그룹에 대한 권한이 있습니다. 역할은 **소유자** 또는 **참가자**와 같은 기본 제공 RBAC 역할일 수 있습니다. 개별 사용자에게도 리소스를 관리할 수 있는 권한을 부여할 수 있지만, 일반적으로 사용자 그룹을 사용하기 위해 이 권한을 할당합니다. 새 활성 디렉터리 사용자 그룹을 작성하려면 다음을 사용합니다.

```azurecli
az ad group create –display-name "name" –mail-nickname "nickname"
```

기존 그룹을 사용할 수도 있습니다. 새로 만든 그룹이나 기존 사용자 그룹의 개체 ID가 필요합니다. 다음 예제에서는 그룹을 만드는 데 사용된 표시 이름에서 개체 ID를 가져오는 방법을 보여 줍니다.

```azurecli
az ad group show –group "groupName"
```

예제:

```azurecli
az ad group show --group ravAppliancetestADgroup
```

반환되는 출력은 다음과 같습니다.

```json 
{
    "displayName": "ravAppliancetestADgroup",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```
    
위 예제에서 보여 주는 objectId 값이 필요합니다. 

## <a name="get-the-role-definition-id"></a>역할 정의 ID 가져오기

다음으로 사용자, 사용자 그룹 또는 응용 프로그램에 대한 액세스 권한을 부여하려는 RBAC 기본 제공 역할에 대한 역할 정의 ID가 필요합니다. 일반적으로 "소유자" 또는 "참가자" 또는 "읽기 권한자" 역할을 사용하려고 합니다. 다음 명령은 "소유자" 역할에 대한 역할 정의 ID를 가져오는 방법을 보여 줍니다.

```azurecli
az role definition list --name owner
```

반환되는 출력은 다음과 같습니다.

```json
{
    "id": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "properties": {
      "assignableScopes": [
    "/"
      ],
      "description": "Lets you manage everything, including access to resources.",
      "permissions": [
    {
      "actions": [
    "*"
      ],
      "notActions": []
    }
      ],
      "roleName": "Owner",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
}
```

위 예제에서 보여 주는 "name" 속성의 값이 필요합니다.


## <a name="create-the-managed-application-definition"></a>Managed Application 정의 만들기

마지막 단계는 관리되는 응용 프로그램 정의 리소스를 만드는 것입니다. 어플라이언스 패키지와 권한 부여를 만든 후에 다음 명령을 사용하여 어플라이언스 정의를 만듭니다. 

```azurecli
az managedapp definition create -n ravtestAppDef4 -l "westcentralus" 
    --resource-group ravApplianceDefRG3 --lock-level ReadOnly 
    --display-name ravtestappdef --description ravtestdescription  
    --authorizations "9aabd3ad-3716-4242-9d8e-a85df479d5d9:8e3af657-a8ff-443c-a75c-2fe8c4bcb635" 
    --package-file-uri "{path to package}" --debug 
```

앞의 예제에서 사용한 매개 변수는 다음과 같습니다.

- resource-group - 어플라이언스 정의를 만든 리소스 그룹의 이름입니다.
- lock-level - 관리되는 리소스 그룹에 배치하는 잠금 유형입니다. 고객이 이 리소스 그룹에서 바람직하지 않은 작업을 수행할 수 없게 합니다. 현재 지원되는 유일한 잠금 수준은 **ReadOnly**입니다. ReadOnly를 지정하는 경우 고객은 관리되는 리소스 그룹에 있는 리소스만 읽을 수 있습니다.
- authorization - 관리되는 리소스 그룹에 권한을 부여하는 데 사용되는 보안 주체 ID 및 역할 정의 ID를 설명합니다. `<principalId>:<roleDefinitionId>` 형식으로 지정됩니다. 이 속성에는 여러 값을 지정할 수도 있습니다. 여러 값이 필요하면 `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` 형식으로 지정해야 합니다. 여러 값은 공백으로 구분됩니다.
- package-file-uri - Azure Storage Blob일 수 있는 템플릿 파일이 포함된 어플라이언스 패키지의 위치입니다. 


## <a name="next-steps"></a>다음 단계

* 관리되는 응용 프로그램에 대한 소개는 [Azure Managed Application 개요](managed-application-overview.md)를 참조하세요.
* 파일의 예제는 [Managed Application 샘플](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)을 참조하세요.
* 소비자 환경을 이해하려면 [Azure Managed Application 사용](managed-application-consumption.md)을 참조하세요.
* 관리되는 응용 프로그램에 대한 UI 정의 파일을 만드는 방법은 [CreateUiDefinition 시작](managed-application-createuidefinition-overview.md)을 참조하세요.
