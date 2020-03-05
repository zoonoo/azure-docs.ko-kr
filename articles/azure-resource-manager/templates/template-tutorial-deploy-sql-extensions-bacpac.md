---
title: 템플릿을 사용하여 SQL BACPAC 파일 가져오기
description: Azure SQL Database 확장을 사용하여 Azure Resource Manager 템플릿을 통해 SQL BACPAC 파일을 가져오는 방법에 대해 알아봅니다.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 27ac4b67aa19aa59abe80ccf9409acf7b587a22b
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250104"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>자습서: Azure Resource Manager 템플릿을 사용하여 SQL BACPAC 파일 가져오기

Azure SQL Database 확장을 사용하여 Azure Resource Manager 템플릿을 통해 BACPAC 파일을 가져오는 방법에 대해 알아봅니다. 배포 아티팩트는 배포를 완료하는 데 필요한 기본 템플릿 파일 이외의 모든 파일입니다. BACPAC 파일은 아티팩트입니다. 

이 자습서에서는 Azure SQL Server, SQL Database를 배포하는 템플릿을 만들고 BACPAC 파일을 가져옵니다. Azure Resource Manager 템플릿을 사용하여 Azure 가상 머신 확장을 배포하는 방법에 대한 자세한 내용은 다음을 참조하세요. [자습서: Azure Resource Manager 템플릿을 사용하여 가상 머신 확장 배포](./template-tutorial-deploy-vm-extensions.md)

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * BACPAC 파일을 준비합니다.
> * 빠른 시작 템플릿을 엽니다.
> * 템플릿을 편집합니다.
> * 템플릿을 배포합니다.
> * 배포를 확인합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음이 필요합니다.

* Resource Manager Tools 확장이 있는 Visual Studio Code. [Visual Studio Code를 사용하여 Azure Resource Manager 템플릿 만들기](./use-vs-code-to-create-template.md)를 참조하세요.
* 보안을 강화하려면 Azure SQL Server 관리자 계정에 대해 생성된 암호를 사용하세요. 암호를 생성하는 데 사용할 수 있는 샘플은 다음과 같습니다.

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault는 암호화 키 및 기타 비밀을 보호하기 위한 것입니다. 자세한 내용은 [자습서: Resource Manager 템플릿 배포에 Azure Key Vault 통합](./template-tutorial-use-key-vault.md)을 참조하세요. 또한 3개월 마다 암호를 업데이트하는 것도 좋습니다.

## <a name="prepare-a-bacpac-file"></a>BACPAC 파일 준비

BACPAC 파일은 [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac)에서 공유됩니다. 사용자 고유의 파일을 만들려면 [Azure SQL 데이터베이스를 BACPAC 파일로 내보내기](../../sql-database/sql-database-export.md)를 참조하세요. 사용자 고유의 위치에 파일을 게시하기로 선택하는 경우 자습서의 뒷부분에서 템플릿을 업데이트해야 합니다.

Resource Manager 템플릿을 사용하여 BACPAC 파일을 가져오려면 먼저 Azure Storage 계정에 BACPAC 파일을 저장해야 합니다. 다음 PowerShell 스크립트는 다음 단계를 수행하여 BACPAC 파일을 준비합니다.

* BACPAC 파일 다운로드
* Azure Storage 계정 만들기
* 스토리지 계정 Blob 컨테이너를 만듭니다.
* 컨테이너에 BACPAC 파일 업로드
* 스토리지 계정 키와 Blob URL을 표시합니다.

1. **사용해보기**를 선택하여 클라우드 셸을 엽니다. 그런 다음, 다음 PowerShell 스크립트를 셸 창에 붙여넣습니다.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. 스토리지 계정 키와 BACPAC 파일 URL을 기록합니다. 템플릿을 배포할 때 필요합니다.

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

이 자습서에서 사용된 템플릿은 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json)에 저장됩니다.

1. Visual Studio Code에서 **파일** > **파일 열기**를 차례로 선택합니다.
1. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. **열기**를 선택하여 파일을 엽니다.

    템플릿에는 다음과 같은 2개의 리소스가 정의되어 있습니다.

   * `Microsoft.Sql/servers`입니다. [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/servers)를 참조하세요.
   * `Microsoft.SQL.servers/databases`입니다. [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)를 참조하세요.

        템플릿을 사용자 지정하기 전에 템플릿의 몇 가지 기본적인 내용을 이해하면 유용합니다.
1. **파일** > **다른 이름으로 저장**을 선택하여 파일 복사본을 로컬 컴퓨터에 *azuredeploy.json*이라는 이름으로 저장합니다.

## <a name="edit-the-template"></a>템플릿 편집

1. **매개 변수** 섹션의 끝에 두 개의 매개 변수를 더 추가하여 스토리지 계정 키와 BACPAC URL을 설정합니다.

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    **adminPassword** 뒤에 쉼표를 추가합니다. Visual Studio Code에서 JSON 파일의 형식을 지정하려면 Shift + Alt + F를 선택합니다.

    이러한 두 값을 가져오려면 [BACPAC 파일 준비](#prepare-a-bacpac-file)를 참조하세요.

1. 두 개의 추가 리소스를 템플릿에 추가합니다.

    * SQL Database 확장이 BACPAC 파일을 가져올 수 있도록 허용하려면 Azure 서비스의 트래픽을 허용해야 합니다. SQL 서버 정의 아래에서 다음 방화벽 규칙 정의를 추가합니다.

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        템플릿은 다음과 같습니다.

        ![방화벽 규칙 정의를 사용하는 템플릿](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * 다음 JSON을 사용하여 데이터베이스 정의에 SQL Database 확장 리소스를 추가합니다.

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        템플릿은 다음과 같습니다.

        ![SQL Database 확장이 있는 템플릿](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        리소스 정의를 이해하려면 [SQL Database 확장 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions)를 확인하세요. 다음은 중요한 요소입니다.

        * **dependsOn**: SQL 데이터베이스가 만들어지면 확장 리소스를 만들어야 합니다.
        * **storageKeyType**: 사용할 스토리지 키 형식을 지정합니다. 값은 `StorageAccessKey` 또는 `SharedAccessKey`일 수 있습니다. 이 자습서에서는 `StorageAccessKey`를 사용합니다.
        * **storageKey**: BACPAC 파일이 저장된 스토리지 계정의 키를 지정합니다. 스토리지 키 형식이 `SharedAccessKey`이면 앞에 “?”가 있어야 합니다.
        * **storageUri**: 스토리지 계정에 저장된 BACPAC 파일의 URL을 지정합니다.
        * **administratorLoginPassword**: SQL 관리자의 암호입니다. 생성된 암호를 사용합니다. [필수 조건](#prerequisites)을 참조하세요.

완성된 템플릿은 다음과 같습니다.

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>템플릿 배포

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

배포 절차는 [템플릿 배포](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) 섹션을 참조하세요. 다음 PowerShell 배포 스크립트를 대신 사용합니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

모든 리소스가 동일한 리소스 그룹 내에 저장되도록 BACPAC 파일을 준비할 때 사용한 것과 동일한 프로젝트 이름을 사용하는 것이 좋습니다. 이러한 방식으로 리소스 정리와 같은 리소스 작업을 더 쉽게 관리할 수 있습니다. 동일한 프로젝트 이름을 사용하는 경우 스크립트에서 `New-AzResourceGroup` 명령을 제거하거나, 기존 리소스 그룹을 업데이트할 것인지 묻는 메시지가 표시될 때 예(y) 또는 아니오(n)로 대답할 수 있습니다.

생성된 암호를 사용합니다. [필수 조건](#prerequisites)을 참조하세요.

## <a name="verify-the-deployment"></a>배포 확인

클라이언트 컴퓨터에서 SQL 서버에 액세스하려면 방화벽 규칙을 추가해야 합니다. 자세한 내용은 [IP 방화벽 규칙 만들기 및 관리](../../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules)를 참조하세요.

Azure Portal에서 새로 배포된 리소스 그룹의 SQL 데이터베이스를 선택합니다. **쿼리 편집기(미리 보기)** 를 선택한 다음, 관리자 자격 증명을 입력합니다. 두 테이블을 데이터베이스로 가져온 것을 볼 수 있습니다.

![쿼리 편집기(미리 보기)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal에서 왼쪽 메뉴에 있는 **리소스 그룹**을 선택합니다.
1. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
1. 해당 리소스 그룹 이름을 선택합니다. 리소스 그룹에 총 6개의 리소스가 표시됩니다.
1. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 SQL Server 및 SQL Database를 배포하고 BACPAC 파일을 가져왔습니다. BACPAC 파일은 Azure Storage 계정에 저장됩니다. URL이 있는 사용자는 누구나 파일에 액세스할 수 있습니다. BACPAC 파일(아티팩트)을 보호하는 방법에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [아티팩트 보호](./template-tutorial-secure-artifacts.md)
