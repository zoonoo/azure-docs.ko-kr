---
title: 연결된 Azure Resource Manager 템플릿 만들기 | Microsoft Docs
description: 가상 머신을 만들기 위해 연결된 Azure Resource Manager 템플릿을 만드는 방법 알아보기
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/18/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: de2e848bd587f3b9bf2efe3fa8df3710e24243e4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241393"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>자습서: 연결된 Azure Resource Manager 템플릿 만들기

연결된 Azure Resource Manager 템플릿을 만드는 방법을 알아봅니다. 연결된 템플릿을 사용하여 하나의 템플릿이 다른 템플릿을 호출하도록 할 수 있습니다. 템플릿 모듈화에 적합합니다. 이 자습서에서는 가상 머신, 가상 네트워크 및 스토리지 계정을 포함한 다른 종속 리소스를 만드는 [자습서: 종속 리소스가 있는 Azure Resource Manager 템플릿 만들기](./resource-manager-tutorial-create-templates-with-dependent-resources.md)에서 사용하는 것과 동일한 템플릿을 사용합니다. 스토리지 계정 리소스 만들기를 연결된 템플릿으로 구분합니다.

연결된 템플릿을 호출하는 것은 함수를 호출하는 것과 같습니다.  매개 변수 값을 연결된 템플릿에 전달하는 방법과 연결된 템플릿에서 "반환 값"을 가져오는 방법도 알아봅니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 빠른 시작 템플릿 열기
> * 연결된 템플릿 만들기
> * 연결된 템플릿 업로드
> * 연결된 템플릿에 연결
> * 종속성 구성
> * 템플릿 배포
> * 추가 사례

자세한 내용은 [Azure 리소스를 배포할 때 연결된 템플릿 및 중첩된 템플릿 사용](./resource-group-linked-templates.md)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* [Visual Studio Code](https://code.visualstudio.com/) 및 [Resource Manager 도구 확장](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)
* 보안을 강화하려면 가상 머신 관리자 계정에 생성된 암호를 사용합니다. 암호를 생성하는 방법에 대한 샘플은 다음과 같습니다.

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault는 암호화 키 및 기타 비밀을 보호하기 위한 것입니다. 자세한 내용은 [자습서: Resource Manager 템플릿 배포에 Azure Key Vault 통합](./resource-manager-tutorial-use-key-vault.md)을 참조하세요. 또한 3개월 마다 암호를 업데이트하는 것도 좋습니다.

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

Azure 퀵 스타트 템플릿은 Resource Manager 템플릿용 저장소입니다. 템플릿을 처음부터 새로 만드는 대신 샘플 템플릿을 찾아서 사용자 지정할 수 있습니다. 이 자습서에 사용되는 템플릿의 이름은 [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)입니다. 이 템플릿은 [자습서: 종속 리소스가 있는 Azure Resource Manager 템플릿 만들기](./resource-manager-tutorial-create-templates-with-dependent-resources.md)에서 사용한 대로 Cloud Shell 배포 방법을 사용합니다. 다음으로 사용될 동일한 두 개의 템플릿 복사본을 저장합니다.

* **기본 템플릿**: 저장소 계정을 제외하고 모든 리소스를 만듭니다.
* **연결된 템플릿**: 저장소 계정을 만듭니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. **열기**를 선택하여 파일을 엽니다.
4. 템플릿에 5개 리소스가 정의되어 있습니다.

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     템플릿을 사용자 지정하기 전에 템플릿 스키마에 대한 몇 가지 기본 사항을 이해하는 것이 좋습니다.
5. **파일**>**다른 이름으로 저장**을 선택하여 파일 복사본을 로컬 컴퓨터에 **azuredeploy.json**이라는 이름으로 저장합니다.
6. **파일**>**이름으로 저장**을 선택하여 **linkedTemplate.json**이라는 이름으로 다른 파일의 복사본을 만듭니다.

## <a name="create-the-linked-template"></a>연결된 템플릿 만들기

연결된 템플릿은 저장소 계정을 만듭니다. 연결된 템플릿을 독립 실행형 템플릿으로 사용하여 스토리지 계정을 만들 수 있습니다. 이 자습서에서 연결된 템플릿은 두 개의 매개 변수를 사용하고 값을 기본 템플릿으로 다시 전달합니다. 이 "반환" 값은 `outputs` 요소에서 정의됩니다.

1. 파일이 열려 있지 않은 경우 Visual Studio Code에서 **linkedTemplate.json**을 엽니다.
2. 다음과 같이 변경합니다.

    * **location** 이외의 모든 매개 변수를 제거합니다.
    * **storageAccountName**이라는 매개 변수를 추가합니다.
        ```json
        "storageAccountName":{
          "type": "string",
          "metadata": {
              "description": "Azure Storage account name."
          }
        },
        ```
        스토리지 계정 이름과 위치는 기본 템플릿에서 연결된 템플릿으로 매개 변수로 전달됩니다.

    * **variables** 요소 및 모든 변수 정의를 제거합니다.
    * 스토리지 계정 이외의 모든 리소스를 제거합니다. 총 4개의 리소스를 제거합니다.
    * 스토리지 계정 리소스의 **name** 요소 값을 다음으로 업데이트합니다.

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * **출력** 요소를 업데이트하면 다음과 같이 표시됩니다.

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```
       **storageUri**는 기본 템플릿에서 가상 머신 리소스 정의에 필요합니다.  출력 값으로 기본 템플릿에 값을 다시 전달합니다.

        완료되면 템플릿은 다음과 같습니다.

        ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "storageAccountName": {
              "type": "string",
              "metadata": {
                "description": "Azure Storage account name."
              }
            },
            "location": {
              "type": "string",
              "defaultValue": "[resourceGroup().location]",
              "metadata": {
                "description": "Location for all resources."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "Storage",
              "properties": {}
            }
          ],
          "outputs": {
            "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
          }
        }
        ```
3. 변경 내용을 저장합니다.

## <a name="upload-the-linked-template"></a>연결된 템플릿 업로드

기본 템플릿과 연결된 템플릿은 배포를 실행하는 위치에서 액세스할 수 있어야 합니다. 이 자습서에서는 [자습서: 종속 리소스가 있는 Azure Resource Manager 템플릿 만들기](./resource-manager-tutorial-create-templates-with-dependent-resources.md)에서 사용한 대로 Cloud Shell 배포 방법을 사용합니다. 기본 템플릿(azuredeploy.json)이 셸에 업로드됩니다. 연결된 템플릿(linkedTemplate.json)은 어딘가에서 안전하게 공유되어야 합니다. 다음 PowerShell 스크립트는 Azure Storage 계정을 만들고, 템플릿을 Storage 계정에 업로드한 다음, 템플릿 파일에 대해 제한된 액세스 권한을 부여하는 SAS 토큰을 생성합니다. 자습서를 간단히 하기 위해 스크립트는 공유 위치에서 완성된 연결된 템플릿을 다운로드합니다. 만든 연결된 템플릿을 사용하려면 [Cloud Shell](https://shell.azure.com)을 사용하여 연결된 템플릿을 업로드한 다음, 사용자 고유의 연결된 템플릿을 사용하도록 스크립트를 수정합니다.

> [!NOTE]
> 스크립트는 SAS 토큰을 8시간 이내에 사용하도록 제한합니다. 이 자습서를 완료하는 데 더 많은 시간이 필요한 경우 만료 시간을 늘립니다.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context

# Upload the linked template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

echo "You need the following values later in the tutorial:"
echo "Resource Group Name: $resourceGroupName"
echo "Linked template URI with SAS token: $templateURI"
```

1. **사용해 보세요** 녹색 단추를 선택하여 Azure Cloud Shell 창을 엽니다.
2. **복사**를 선택하여 PowerShell 스크립트를 복사합니다.
3. 셸 창(군청색 부분) 내의 아무 곳을 마우스 오른쪽 단추로 클릭한 다음, **붙여넣기**를 선택합니다.
4. 셸 창의 끝에 있는 두 개의 값(리소스 그룹 이름 및 연결된 템플릿 URI)을 적어 둡니다. 이러한 값은 자습서의 뒷부분에서 필요합니다.
5. **포커스 모드 종료**를 선택하여 셸 창을 닫습니다.

실제로 기본 템플릿을 배포할 때 SAS 토큰을 생성하고 SAS 토큰에 더 짧은 시간 범위의 만료 시간을 지정하여 보안을 강화합니다. 자세한 내용은 [배포하는 동안 SAS 토큰 제공](./resource-manager-powershell-sas-token.md#provide-sas-token-during-deployment)을 참조하세요.

## <a name="call-the-linked-template"></a>연결된 템플릿 호출

기본 템플릿은 azuredeploy.json이라고 합니다.

1. 열려 있지 않은 경우 Visual Studio Code에서 **azuredeploy.json**을 엽니다.
2. 템플릿에서 스토리지 계정 리소스 정의를 삭제합니다.

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    ```
3. 저장소 계정 정의가 있던 위치로 다음 json 코드 조각을 추가합니다.

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    다음 세부 사항에 주의하세요.

    * 기본 템플릿의 `Microsoft.Resources/deployments` 리소스는 다른 템플릿에 연결하는 데 사용됩니다.
    * `deployments` 리소스는 `linkedTemplate`이라는 이름을 갖습니다. 이 이름은 [종속성 구성](#configure-dependency)에 사용됩니다.
    * 연결된 템플릿을 호출할 때 [증분](./deployment-modes.md) 배포 모드만 사용할 수 있습니다.
    * `templateLink/uri`는 연결된 템플릿 URI를 포함합니다. 연결된 템플릿(SAS 토큰이 있는 템플릿)을 업로드할 때 얻을 수 있는 URI로 값을 업데이트합니다.
    * `parameters`를 사용하여 기본 템플릿의 값을 연결된 템플릿에 전달합니다.
4. 연결된 템플릿(SAS 토큰이 있는 값)을 업로드할 때 얻은 값으로 `uri` 요소의 값을 업데이트했는지 확인합니다. 실제로 URI를 매개 변수에 제공하려고 합니다.
5. 수정된 템플릿을 저장합니다.

## <a name="configure-dependency"></a>종속성 구성

[자습서: 종속 리소스가 있는 Azure Resource Manager 템플릿 만들기](./resource-manager-tutorial-create-templates-with-dependent-resources.md)를 다시 생각해 보면 가상 머신 리소스는 스토리지 계정에 따라 달라집니다.

![Azure Resource Manager 템플릿 종속성 다이어그램](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

저장소 계정은 이제 연결된 템플릿에서 정의되므로 `Microsoft.Compute/virtualMachines` 리소스의 다음 두 요소를 업데이트해야 합니다.

* `dependOn` 요소를 다시 구성합니다. 저장소 계정 정의가 연결된 템플릿으로 이동합니다.
* `properties/diagnosticsProfile/bootDiagnostics/storageUri` 요소를 다시 구성합니다. [연결된 템플릿 만들기](#create-the-linked-template)에서 출력 값을 추가했습니다.

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    기본 템플릿에서 이 값은 필수입니다.

1. 열려 있지 않은 경우 Visual Studio Code에서 azuredeploy.json을 엽니다.
2. 가상 머신 리소스 정의를 확장하고, 다음 스크린샷에 표시된 대로 **dependsOn**을 업데이트합니다.

    ![종속성을 구성하는 Azure Resource Manager 연결된 템플릿](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate*은 배포 리소스의 이름입니다.
3. 이전 스크린샷과 같이 **properties/diagnosticsProfile/bootDiagnostics/storageUri**를 업데이트합니다.
4. 수정된 템플릿을 저장합니다.

## <a name="deploy-the-template"></a>템플릿 배포

배포 절차는 [템플릿 배포](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) 섹션을 참조하세요. 연결된 템플릿을 저장하는 스토리지 계정과 동일한 리소스 그룹 이름을 사용합니다. 이렇게 하면 다음 섹션에서 리소스를 더 쉽게 정리할 수 있습니다. 보안을 강화하려면 가상 머신 관리자 계정에 생성된 암호를 사용합니다. [필수 조건](#prerequisites)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="additional-practice"></a>추가 사례

프로젝트를 향상시키려면 완성된 프로젝트를 다음과 같이 추가로 변경합니다.

1. 매개 변수를 통해 연결된 템플릿 URI 값을 가져오도록 기본 템플릿(azuredeploy.json)을 수정합니다.
2. 연결된 템플릿을 업로드할 때 SAS 토큰을 생성하는 대신, 기본 템플릿을 배포할 때 토큰을 생성합니다. 자세한 내용은 [배포하는 동안 SAS 토큰 제공](./resource-manager-powershell-sas-token.md#provide-sas-token-during-deployment)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 템플릿을 기본 템플릿과 연결된 템플릿으로 모듈화했습니다. 가상 머신 확장을 사용하여 배포 후 작업을 수행하는 방법은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [가상 머신 확장 배포](./resource-manager-tutorial-deploy-vm-extensions.md)
