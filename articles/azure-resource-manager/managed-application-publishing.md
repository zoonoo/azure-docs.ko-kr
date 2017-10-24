---
title: "Azure 서비스 카탈로그 관리되는 응용 프로그램 만들기 및 게시 | Microsoft Docs"
description: "조직의 구성원을 위한 Azure 관리되는 응용 프로그램을 만드는 방법이 나와 있습니다."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/09/2017
ms.author: gauravbh
ms.openlocfilehash: 3ff2108d08bacc4bc5f79a768b9c131aa7e6fceb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>내부 사용을 위한 관리되는 응용 프로그램 게시

조직의 구성원을 위한 Azure [관리되는 응용 프로그램](managed-application-overview.md)을 만들고 게시할 수 있습니다. 예를 들어 조직 표준을 준수하도록 하는 IT 부서에서 관리되는 응용 프로그램을 게시할 수 있습니다. 이러한 관리되는 응용 프로그램은 Azure Marketplace가 아닌 서비스 카탈로그를 통해 사용할 수 있습니다.

서비스 카탈로그에 대한 관리되는 응용 프로그램을 게시하려면 다음을 수행해야 합니다.

* 필요한 두 템플릿 파일이 포함된 .zip 패키지를 만듭니다.
* 사용자의 구독에 속한 리소스 그룹에 액세스해야 하는 사용자, 그룹 또는 응용 프로그램을 결정합니다.
* .zip 패키지를 나타내고 ID에 대한 액세스를 요청하는 관리되는 응용 프로그램 정의를 만듭니다.

## <a name="create-a-managed-application-package"></a>관리되는 응용 프로그램 패키지 만들기

첫 번째 단계는 필요한 두 템플릿 파일을 만드는 것입니다. 두 파일 모두를 하나의 .zip 파일로 패키지하고, 저장소 계정과 같이 액세스할 수 있는 위치에 업로드합니다. 관리되는 응용 프로그램 정의를 만들 때 이 .zip 파일에 대한 링크를 전달합니다.

* **mainTemplate.json**: 관리되는 응용 프로그램의 일부로 프로비전되는 Azure 리소스를 정의합니다. 템플릿은 일반 Resource Manager 템플릿과 차이가 없습니다. 예를 들어 관리되는 응용 프로그램을 통해 저장소 계정을 만들려면 mainTemplate.json에 다음이 포함됩니다.

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]",
            "apiVersion": "2016-01-01",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {}
  }
  ```

* **createUiDefinition.json**: Azure Portal에서 이 파일을 사용하여 관리되는 응용 프로그램을 만드는 사용자를 위한 사용자 인터페이스를 생성합니다. 사용자가 각 매개 변수에 대한 입력을 제공하는 방법을 정의합니다. 드롭다운 목록, 텍스트 상자, 암호 상자 및 기타 입력 도구와 같은 옵션을 사용할 수 있습니다. 관리되는 응용 프로그램에 대한 UI 정의 파일을 만드는 방법은 [CreateUiDefinition 시작](managed-application-createuidefinition-overview.md)을 참조하세요.

  다음 예제에서는 사용자가 텍스트 상자를 통해 저장소 계정 이름 접두사를 지정할 수 있게 하는 createUiDefinition.json 파일을 보여 줍니다.

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {
                "name": "storageAccounts",
                "type": "Microsoft.Common.TextBox",
                "label": "Storage account name prefix",
                "defaultValue": "storage",
                "toolTip": "Provide a value that is used for the prefix of your storage account. Limit to 11 characters.",
                "constraints": {
                    "required": true,
                    "regex": "^[a-z0-9A-Z]{1,11}$",
                    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-11 characters long."
                },
                "visible": true
            }
        ],
        "steps": [],
        "outputs": {
            "storageAccountNamePrefix": "[basics('storageAccounts')]"
        }
    }
  }
  ```

필요한 모든 파일이 준비되면 .zip 파일로 패키지합니다. 두 파일이 .zip 파일의 루트 수준에 있어야 합니다. 폴더에 배치하는 경우 관리되는 응용 프로그램 정의를 만들 때 필요한 파일이 없음을 나타내는 오류가 나타납니다. 패키지를 사용할 수 있는 액세스 가능한 위치에 패키지를 업로드합니다. 이 문서의 나머지 부분에서는 .zip 파일을 공개적으로 액세스할 수 있는 저장소 blob 컨테이너에 있다고 가정합니다.

Azure CLI 또는 포털 중 하나를 사용하여 서비스 카탈로그에 대한 관리되는 응용 프로그램을 만들 수 있습니다. 두 방법은 모두 이 문서에 나와 있습니다.

## <a name="create-managed-application-with-azure-cli"></a>Azure CLI를 사용하여 관리되는 응용 프로그램 만들기

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Azure Active Directory 사용자 그룹 또는 응용 프로그램 만들기

다음 단계는 고객을 대신하여 리소스를 관리하기 위한 사용자 그룹 또는 응용 프로그램을 선택하는 것입니다. 이 사용자 그룹 또는 응용 프로그램에는 할당된 역할에 따라 관리되는 리소스 그룹에 대한 권한이 있습니다. 역할은 소유자 또는 참가자와 같은 기본 제공 RBAC(역할 기반 액세스 제어) 역할일 수 있습니다. 개별 사용자에게도 리소스를 관리할 수 있는 권한을 부여할 수 있지만, 일반적으로 사용자 그룹에 이 권한을 할당합니다. 새 Active Directory 사용자 그룹을 만들려면 [그룹을 만들고 Azure Active Directory에 구성원 추가](../active-directory/active-directory-groups-create-azure-portal.md)를 참조하세요.

리소스 관리에 사용할 사용자 그룹의 개체 ID가 필요합니다. 다음 예제에서는 그룹의 표시 이름에서 개체 ID를 가져오는 방법을 보여 줍니다.

```azurecli-interactive
az ad group show --group exampleGroupName
```

예제 명령은 다음 출력을 반환합니다.

```azurecli
{
    "displayName": "exampleGroupName",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```

개체 ID만 검색하려면 다음을 사용합니다.

```azurecli-interactive
groupid=$(az ad group show --group exampleGroupName --query objectId --output tsv)
```

### <a name="get-the-role-definition-id"></a>역할 정의 ID 가져오기

다음으로 사용자, 사용자 그룹 또는 응용 프로그램에 대한 액세스 권한을 부여하려는 RBAC 기본 제공 역할에 대한 역할 정의 ID가 필요합니다. 일반적으로 소유자 또는 참가자 또는 읽기 권한자 역할을 사용합니다. 다음 명령은 소유자 역할에 대한 역할 정의 ID를 가져오는 방법을 보여 줍니다.

```azurecli-interactive
az role definition list --name owner
```

해당 명령은 다음 출력을 반환합니다.

```azurecli
{
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
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

위 예제에서 보여 주는 "name" 속성의 값이 필요합니다. 다음을 통해 해당 속성만 검색할 수 있습니다.

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

### <a name="create-the-managed-application-definition"></a>관리되는 응용 프로그램 정의 만들기

관리되는 응용 프로그램 정의를 저장하기 위한 리소스 그룹이 아직 없는 경우 지금 새로 만듭니다.

```azurecli-interactive
az group create --name managedApplicationGroup --location westcentralus
```

이제 관리되는 응용 프로그램 정의 리소스를 만듭니다.

```azurecli-interactive
az managedapp definition create \
  --name storageApp \
  --location "westcentralus" \
  --resource-group managedApplicationGroup \
  --lock-level ReadOnly \
  --display-name myteststorageapp \
  --description storageapp \
  --authorizations "$groupid:$roleid" \
  --package-file-uri <uri-path-to-zip-file>
```

앞의 예제에서 사용한 매개 변수는 다음과 같습니다.

* **resource-group**: 관리되는 응용 프로그램 정의를 만든 리소스 그룹의 이름입니다.
* **lock-level**: 관리되는 리소스 그룹에 배치하는 잠금 유형입니다. 고객이 이 리소스 그룹에서 바람직하지 않은 작업을 수행할 수 없게 합니다. 현재 지원되는 유일한 잠금 수준은 ReadOnly입니다. ReadOnly를 지정하는 경우 고객은 관리되는 리소스 그룹에 있는 리소스만 읽을 수 있습니다.
* **authorizations**: 관리되는 리소스 그룹에 권한을 부여하는 데 사용되는 보안 주체 ID 및 역할 정의 ID를 설명합니다. `<principalId>:<roleDefinitionId>` 형식으로 지정됩니다. 이 속성에는 여러 값을 지정할 수도 있습니다. 여러 값이 필요하면 `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` 형식으로 지정해야 합니다. 여러 값은 공백으로 구분됩니다.
* **package-file-uri**: Azure Storage Blob일 수 있는 템플릿 파일이 포함된 관리되는 응용 프로그램 패키지의 위치입니다.

## <a name="create-managed-application-with-portal"></a>포털을 사용하여 관리되는 응용 프로그램 만들기

1. 필요에 따라 리소스를 관리할 Azure Active Directory 사용자 그룹을 만듭니다. 자세한 내용은 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../active-directory/active-directory-groups-create-azure-portal.md)를 참조하세요.
1. 왼쪽 위 모서리에서 **+ 새로 만들기**를 선택합니다.

   ![새 서비스](./media/managed-application-publishing/new.png)

1. **서비스 카탈로그**를 검색합니다.

1. 결과에서 **서비스 카탈로그 관리되는 응용 프로그램 정의**를 찾을 때까지 스크롤합니다. 이를 선택합니다.

   ![관리되는 응용 프로그램 정의 검색](./media/managed-application-publishing/select-managed-apps-definition.png)

1. **만들기**를 선택하여 관리 응용 프로그램 정의 만들기 프로세스를 시작합니다.

   ![관리되는 응용 프로그램 정의 만들기](./media/managed-application-publishing/create-definition.png)

1. 이름, 표시 이름, 설명, 위치, 구독 및 리소스 그룹에 대한 값을 제공합니다. 패키지 파일 URI의 경우 만든 zip 파일의 경로를 제공합니다.

   ![값 제공](./media/managed-application-publishing/fill-application-values.png)

1. [인증 및 잠금 수준] 섹션에 도달하면 **권한 부여 추가**를 선택합니다.

   ![권한 부여 추가](./media/managed-application-publishing/add-authorization.png)

1. 리소스를 관리할 Azure Active Directory 그룹을 선택하고 **확인**을 선택합니다.

   ![권한 부여 그룹 추가](./media/managed-application-publishing/add-auth-group.png)

1. 모든 값을 입력했으면 **만들기**를 선택합니다.

   ![관리되는 응용 프로그램 만들기](./media/managed-application-publishing/create-app.png)

## <a name="next-steps"></a>다음 단계

* 관리되는 응용 프로그램에 대한 소개는 [관리되는 응용 프로그램 개요](managed-application-overview.md)를 참조하세요.
* 파일의 예제는 [관리되는 응용 프로그램 샘플](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)을 참조하세요.
* 서비스 카탈로그 관리되는 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [서비스 카탈로그 관리되는 응용 프로그램 사용](managed-application-consumption.md)을 참조하세요.
* Azure Marketplace에 관리되는 응용 프로그램을 게시하는 방법에 대한 자세한 내용은 [Marketplace의 Azure 관리되는 응용 프로그램](managed-application-author-marketplace.md)을 참조하세요.
* Marketplace의 관리되는 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [Marketplace의 Azure 관리되는 응용 프로그램 사용](managed-application-consume-marketplace.md)을 참조하세요.
* 관리되는 응용 프로그램에 대한 UI 정의 파일을 만드는 방법은 [CreateUiDefinition 시작](managed-application-createuidefinition-overview.md)을 참조하세요.
