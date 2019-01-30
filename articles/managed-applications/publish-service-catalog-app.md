---
title: Azure 서비스 카탈로그 관리형 애플리케이션 만들기 및 게시 | Microsoft Docs
description: 조직의 구성원을 위한 Azure 관리형 애플리케이션을 만드는 방법이 나와 있습니다.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 7e9db85fb91dd0c9a33cc8205bdb30a648dfd38a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438748"
---
# <a name="create-and-publish-a-managed-application-definition"></a>관리되는 응용 프로그램 정의 만들기 및 게시

조직의 구성원을 위한 Azure [관리되는 애플리케이션](overview.md)을 만들고 게시할 수 있습니다. 예를 들어 IT 부서에서는 조직 표준을 충족하는 관리되는 애플리케이션을 게시할 수 있습니다. 이러한 관리되는 애플리케이션은 Azure Marketplace가 아닌 서비스 카탈로그를 통해 사용할 수 있습니다.

서비스 카탈로그에 대한 관리형 애플리케이션을 게시하려면 다음을 수행해야 합니다.

* 관리되는 애플리케이션과 함께 배포할 리소스를 정의하는 템플릿을 만듭니다.
* 관리 되는 애플리케이션을 배포할 때 포털에 대한 사용자 인터페이스 요소를 정의합니다.
* 필요한 템플릿 파일이 포함된 .zip 패키지를 만듭니다.
* 사용자의 구독에 속한 리소스 그룹에 액세스해야 하는 사용자, 그룹 또는 애플리케이션을 결정합니다.
* .zip 패키지를 나타내고 ID에 대한 액세스를 요청하는 관리형 애플리케이션 정의를 만듭니다.

이 문서에서는 관리되는 애플리케이션에는 하나의 저장소 계정만이 포함됩니다. 관리되는 애플리케이션을 게시하는 단계를 설명하기 위한 것입니다. 전체 예제는 [Azure 관리되는 애플리케이션의 샘플 프로젝트](sample-projects.md)를 참조하세요.

이 아티클의 PowerShell 예제에는 Azure PowerShell 6.2 이상이 필요합니다. 필요에 따라 [버전을 업데이트합니다](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="create-the-resource-template"></a>리소스 템플릿 만들기

모든 관리되는 애플리케이션 정의에는 **mainTemplate.json**이라는 파일이 포함됩니다. 여기에서 표시할 Azure 리소스를 정의합니다. 템플릿은 일반 Resource Manager 템플릿과 차이가 없습니다.

이름이 **mainTemplate.json**인 파일을 만듭니다. 이름은 대/소문자를 구분합니다.

파일에 다음 JSON을 추가합니다. 저장소 계정을 만들기 위한 매개 변수를 정의하고 저장소 계정의 속성을 지정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

mainTemplate.json 파일을 저장합니다.

## <a name="create-the-user-interface-definition"></a>사용자 인터페이스 정의 만들기

Azure Portal은 **createUiDefinition.json** 파일을 사용하여 관리되는 애플리케이션을 만드는 사용자를 위한 사용자 인터페이스를 생성합니다. 사용자가 각 매개 변수에 대한 입력을 제공하는 방법을 정의합니다. 드롭다운 목록, 텍스트 상자, 암호 상자 및 기타 입력 도구와 같은 옵션을 사용할 수 있습니다. 관리형 애플리케이션에 대한 UI 정의 파일을 만드는 방법은 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.

이름이 **createUiDefinition.json**인 파일을 만듭니다. 이름은 대/소문자를 구분합니다.

파일에 다음 JSON을 추가합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

createUiDefinition.json 파일을 저장합니다.

## <a name="package-the-files"></a>파일을 패키지로 만들기

이름이 app.zip인 .zip 파일에 두 파일을 추가합니다. 두 파일이 .zip 파일의 루트 수준에 있어야 합니다. 폴더에 배치하는 경우 관리되는 애플리케이션 정의를 만들 때 필요한 파일이 없음을 나타내는 오류가 나타납니다. 

패키지를 사용할 수 있는 액세스 가능한 위치에 패키지를 업로드합니다. 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>관리형 애플리케이션 정의 만들기

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Azure Active Directory 사용자 그룹 또는 애플리케이션 만들기

다음 단계는 고객을 대신하여 리소스를 관리하기 위한 사용자 그룹 또는 애플리케이션을 선택하는 것입니다. 이 사용자 그룹 또는 애플리케이션에는 할당된 역할에 따라 관리형 리소스 그룹에 대한 권한이 있습니다. 역할은 소유자 또는 참가자와 같은 기본 제공 RBAC(역할 기반 Access Control) 역할일 수 있습니다. 개별 사용자에게도 리소스를 관리할 수 있는 권한을 부여할 수 있지만, 일반적으로 사용자 그룹에 이 권한을 할당합니다. 새 Active Directory 사용자 그룹을 만들려면 [그룹을 만들고 Azure Active Directory에 구성원 추가](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

리소스 관리에 사용할 사용자 그룹의 개체 ID가 필요합니다. 

```powershell
$groupID=(Get-AzureRmADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>역할 정의 ID 가져오기

다음으로 사용자, 사용자 그룹 또는 애플리케이션에 대한 액세스 권한을 부여하려는 RBAC 기본 제공 역할에 대한 역할 정의 ID가 필요합니다. 일반적으로 소유자 또는 참가자 또는 읽기 권한자 역할을 사용합니다. 다음 명령은 소유자 역할에 대한 역할 정의 ID를 가져오는 방법을 보여 줍니다.

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>관리형 애플리케이션 정의 만들기

관리되는 애플리케이션 정의를 저장하기 위한 리소스 그룹이 아직 없는 경우 지금 새로 만듭니다.

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

이제 관리형 애플리케이션 정의 리소스를 만듭니다.

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

### <a name="make-sure-users-can-see-your-definition"></a>사용자가 정의를 볼 수 있는지 확인합니다.

사용자가 관리되는 애플리케이션 정의에 액세스할 수 있지만 조직의 다른 사용자도 액세스할 수 있는 것이 좋습니다. 정의에서 최소한 읽기 역할을 부여합니다. 구독 또는 리소스 그룹에서 이 수준의 액세스를 상속받을 수 있습니다. 정의에 대한 액세스 권한이 있고 사용자나 그룹을 추가하려면 [역할 기반 액세스 제어를 사용하여 Azure 구독 리소스에 대한 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 관리되는 응용 프로그램을 Azure Marketplace에 게시하려면 [Marketplace의 Azure 관리되는 응용 프로그램](publish-marketplace-app.md)을 참조하세요.
* 관리되는 애플리케이션 인스턴스를 배포하려면 [Azure Portal을 통해 서비스 카탈로그 앱 배포](deploy-service-catalog-quickstart.md)를 참조하세요.