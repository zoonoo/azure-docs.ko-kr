---
title: 사용자 지정 작업 및 리소스가 있는 Azure Managed Application 만들기
description: 이 자습서에서는 Azure 사용자 지정 공급자가 있는 Azure Managed Application을 만드는 방법을 설명합니다.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: 3dd0887114156956b55f554d0265e3ca2b9b10ab
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336108"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>자습서: 사용자 지정 작업 및 리소스가 있는 관리형 애플리케이션 만들기

이 자습서에서는 사용자 지정 작업 및 리소스를 사용하여 사용자 고유의 관리형 애플리케이션을 직접 만듭니다. 관리형 애플리케이션에는 `Overview` 페이지의 사용자 지정 작업, `Table of Content`에서 개별 메뉴 항목으로 표시되는 사용자 지정 리소스 종류 및 사용자 지정 리소스 페이지의 사용자 지정 컨텍스트 작업이 포함됩니다.

이 자습서의 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 관리형 애플리케이션 인스턴스를 만들기 위한 사용자 인터페이스 정의 파일 작성
> * Azure 사용자 지정 공급자, Azure Storage 계정 및 Azure Function이 포함된 배포 템플릿 작성
> * 사용자 지정 작업 및 리소스가 포함된 보기 정의 아티팩트 작성
> * 관리형 애플리케이션 정의 배포
> * 관리형 애플리케이션 인스턴스 배포
> * 사용자 지정 작업 수행 및 사용자 지정 리소스 만들기

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 사항을 알고 있어야 합니다.

* [관리형 애플리케이션 정의를 만들고 게시](publish-service-catalog-app.md)하는 방법
* [Azure Portal을 통해 서비스 카탈로그 앱을 배포](deploy-service-catalog-quickstart.md)하는 방법
* [관리형 애플리케이션에 대한 Azure Portal 사용자 인터페이스를 만드는](create-uidefinition-overview.md) 방법
* [보기 정의 아티팩트](concepts-view-definition.md) 기능
* [Azure 사용자 지정 공급자](custom-providers-overview.md) 기능

## <a name="user-interface-definition"></a>사용자 인터페이스 정의

이 자습서에서는 관리형 애플리케이션을 만들고, 사용자 지정 공급자 인스턴스, 스토리지 계정 및 함수가 해당 관리형 리소스 그룹에 포함됩니다. 이 예제에서 사용되는 Azure Function은 작업 및 리소스에 대한 사용자 지정 공급자 작업을 처리하는 API를 구현합니다. Azure Storage 계정은 사용자 지정 공급자 리소스에 대한 기본 스토리지로 사용됩니다.

관리형 애플리케이션 인스턴스를 만들기 위한 사용자 인터페이스 정의에는 `funcname` 및 `storagename` 입력 요소가 포함됩니다. 스토리지 계정 이름 및 함수 이름은 전역적으로 고유해야 합니다. 기본적으로 함수 파일은 [함수 패키지 샘플](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip)에서 배포되지만 *createUIDefinition.json*의 패키지 링크에 대한 입력 요소를 추가하여 변경할 수 있습니다.

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

그리고 *createUIDefinition.json*의 출력은 다음과 같습니다.

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

전체 *createUIDefinition.json* 샘플은 [참고 자료: 사용자 인터페이스 요소 아티팩트](reference-createuidefinition-artifact.md)에서 찾을 수 있습니다.

## <a name="template-with-custom-provider"></a>사용자 지정 공급자가 포함된 템플릿

사용자 지정 공급자가 있는 관리형 애플리케이션 인스턴스를 만들려면 **mainTemplate.json**에서 **public**이라는 이름의 사용자 지정 공급자 리소스를 정의하고 **Microsoft.CustomProviders/resourceProviders**를 입력해야 합니다. 해당 리소스에서 서비스에 대한 리소스 종류 및 작업을 정의합니다. Azure Function 및 Azure Storage 계정 인스턴스를 배포하려면 각각 `Microsoft.Web/sites` 및 `Microsoft.Storage/storageAccounts` 종류의 리소스를 정의합니다.

이 자습서에서는 하나의 `users` 리소스 종류, `ping` 사용자 지정 작업 및 `users` 사용자 지정 리소스의 컨텍스트에서 수행될 `users/contextAction` 사용자 지정 작업을 만듭니다. 각 리소스 종류 및 작업에 대해 [createUIDefinition.json](#user-interface-definition)에 제공된 이름으로 함수를 가리키는 엔드포인트를 제공합니다. 리소스 종류에 대해 **routingType**을 `Proxy,Cache`로 지정하고, 작업에 대해 `Proxy`로 지정합니다.

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

전체 *mainTemplate.json* 샘플은 [참고 자료: 배포 템플릿 아티팩트](reference-main-template-artifact.md)에서 찾을 수 있습니다.

## <a name="view-definition-artifact"></a>보기 정의 아티팩트

관리형 애플리케이션에서 사용자 지정 작업 및 사용자 지정 리소스가 포함된 사용자 인터페이스를 정의하려면 **viewDefinition.json** 아티팩트를 작성해야 합니다. 보기 정의 아티팩트에 대한 자세한 내용은 [Azure Managed Applications의 보기 정의 아티팩트](concepts-view-definition.md)를 참조하세요.

이 자습서에서 정의하는 항목은 다음과 같습니다.
* 기본 텍스트 입력을 사용하는 `TestAction` 사용자 지정 작업을 나타내는 도구 모음 단추가 있는 *Overview*(개요) 페이지
* `users` 사용자 지정 리소스 종류를 나타내는 *Users*(사용자) 페이지
* `users` 형식의 사용자 지정 리소스 컨텍스트에서 수행될 *Users* 페이지의 `users/contextAction` 사용자 지정 리소스 작업

다음 예제에서는 "Overview" 페이지에 대한 보기 구성을 보여 줍니다.

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

다음 예제에는 사용자 지정 리소스 작업이 포함된 "Users" 리소스 페이지 구성이 포함되어 있습니다.

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

전체 *viewDefinition.json* 샘플은 [참고 자료: 보기 정의 아티팩트](reference-view-definition-artifact.md)에서 찾을 수 있습니다.

## <a name="managed-application-definition"></a>관리형 애플리케이션 정의

다음 관리형 애플리케이션 아티팩트를 zip 보관 파일에 패키지하고 스토리지에 업로드합니다.

* createUiDefinition.json
* mainTemplate.json
* viewDefinition.json

모든 파일은 루트 수준에 있어야 합니다. 아티팩트가 포함된 패키지는 모든 스토리지(예: GitHub Blob 또는 Azure Storage 계정 Blob)에 저장할 수 있습니다. 다음은 애플리케이션 패키지를 스토리지 계정에 업로드하는 스크립트입니다. 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

아래의 Azure CLI 스크립트를 실행하거나 Azure Portal의 단계에 따라 서비스 카탈로그 관리형 애플리케이션 정의를 배포합니다.

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

# <a name="azure-clitabazurecli-interactive"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

1. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에서 **관리형 애플리케이션 센터**를 입력하고 선택합니다.
2. **관리형 애플리케이션 센터**에서 **서비스 카탈로그 애플리케이션 정의**를 선택하고, **추가**를 클릭합니다. 
    
    ![서비스 카탈로그 추가](./media/managed-application-with-custom-providers/service-catalog-managed-application.png)

3. 서비스 카탈로그 정의를 만드는 데 필요한 값을 제공합니다.

    * 서비스 카탈로그 정의에 대한 고유 **이름**, **표시 이름** 및 *설명*(선택 사항)을 제공합니다.
    * 애플리케이션 정의가 만들어지는 **구독**, **리소스 그룹** 및 **위치**를 선택합니다. zip 패키지에 사용되는 것과 동일한 리소스 그룹을 사용하거나 새 리소스 그룹을 만들 수 있습니다.
    * **패키지 파일 URI**에 대해 이전 단계에서 만든 zip 파일의 경로를 제공합니다.

    ![값 제공](./media/managed-application-with-custom-providers/add-service-catalog-managed-application.png)

4. [인증 및 잠금 수준] 섹션에 도달하면 **권한 부여 추가**를 선택합니다.

    ![권한 부여 추가](./media/managed-application-with-custom-providers/add-authorization.png)

5. 리소스를 관리할 Azure Active Directory 그룹을 선택하고 **확인**을 선택합니다.

   ![권한 부여 그룹 추가](./media/managed-application-with-custom-providers/add-auth-group.png)

6. 모든 값을 입력했으면 **만들기**를 선택합니다.

   ![관리되는 애플리케이션 정의 만들기](./media/managed-application-with-custom-providers/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>관리형 애플리케이션 인스턴스

관리형 애플리케이션 정의가 배포되면 다음 스크립트를 실행하거나 Azure Portal의 단계에 따라 사용자 지정 공급자가 포함된 관리형 애플리케이션 인스턴스를 배포합니다.

# <a name="azure-clitabazurecli-interactive"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

1. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에서 **관리형 애플리케이션 센터**를 입력하고 선택합니다.
2. **관리형 애플리케이션 센터**에서 **서비스 카탈로그 애플리케이션**을 선택하고, **추가**를 클릭합니다. 

    ![관리형 애플리케이션 추가](./media/managed-application-with-custom-providers/add-managed-application.png)

3. **서비스 카탈로그 애플리케이션** 페이지의 검색 상자에서 서비스 카탈로그 정의 표시 이름을 입력합니다. 이전 단계에서 만든 정의를 선택하고, **만들기**를 클릭합니다.

    ![서비스 카탈로그 선택](./media/managed-application-with-custom-providers/select-service-catalog-definition.png)

4. 서비스 카탈로그 정의에서 관리형 애플리케이션 인스턴스를 만드는 데 필요한 값을 제공합니다.

    * 애플리케이션 인스턴스가 만들어지는 **구독**, **리소스 그룹** 및 **위치**를 선택합니다.
    * 고유한 Azure Function 이름과 Azure Storage 계정 이름을 제공합니다.

    ![애플리케이션 설정](./media/managed-application-with-custom-providers/application-settings.png)

5. 유효성 검사가 통과되면 **확인**을 클릭하여 관리형 애플리케이션의 인스턴스를 배포합니다. 
    
    ![관리형 애플리케이션 배포](./media/managed-application-with-custom-providers/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>사용자 지정 작업 및 리소스

서비스 카탈로그 애플리케이션 인스턴스가 배포되면 두 개의 새 리소스 그룹이 있습니다. 첫 번째 리소스 그룹인 `applicationGroup`에는 관리형 애플리케이션의 인스턴스가 포함되어 있고, 두 번째 리소스 그룹인 `managedResourceGroup`에는 **사용자 지정 공급자**를 포함하여 관리형 애플리케이션용 리소스가 포함되어 있습니다.

![애플리케이션 리소스 그룹](./media/managed-application-with-custom-providers/application-resource-groups.png)

관리형 애플리케이션 인스턴스로 이동하여 "Overview"(개요) 페이지에서 **사용자 지정 작업**을 수행하고, "Users"(사용자) 페이지에서 **users** 사용자 지정 리소스를 만들고, 사용자 지정 리소스에서 **사용자 지정 컨텍스트 작업**을 실행할 수 있습니다.

* "Overview" 페이지로 이동하여 "Ping Action"(Ping 작업) 단추를 클릭합니다.

![사용자 지정 작업 수행](./media/managed-application-with-custom-providers/perform-custom-action.png)

* "Users" 페이지로 이동하여 "Add"(추가) 단추를 클릭합니다. 리소스를 만드는 데 필요한 입력을 제공하고 양식을 제출합니다.

![사용자 지정 리소스 만들기](./media/managed-application-with-custom-providers/create-custom-resource.png)

* "Users" 페이지로 이동하여 "users" 리소스를 선택하고 "Custom Context Action"(사용자 지정 컨텍스트 작업)을 클릭합니다.

![사용자 지정 리소스 만들기](./media/managed-application-with-custom-providers/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>도움말 찾기

Azure Managed Applications에 대한 질문이 있는 경우 [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-managedapps)에서 문의하세요. 이미 비슷한 질문을 받고 답변했을 수 있으므로 게시하기 전에 먼저 확인하세요. 빠른 응답을 얻으려면 `azure-managedapps` 태그를 추가하세요.

## <a name="next-steps"></a>다음 단계

관리되는 애플리케이션을 Azure Marketplace에 게시하려면 [Marketplace의 Azure 관리되는 애플리케이션](publish-marketplace-app.md)을 참조하세요.
