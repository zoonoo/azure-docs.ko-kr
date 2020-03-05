---
title: 템플릿에서 아티팩트 보호
description: Azure Resource Manager 템플릿에서 사용된 아티팩트를 보호하는 방법에 대해 알아봅니다.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7069ff363cf274ba855efc9b598d8d01e64e18d1
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250120"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>자습서: Azure Resource Manager 템플릿 배포에서 아티팩트 보호

SAS(공유 액세스 서명)가 포함된 Azure Storage 계정을 사용하여 Azure Resource Manager 템플릿에서 사용된 아티팩트를 보호하는 방법에 대해 알아봅니다. 배포 아티팩트는 배포를 완료하는 데 필요한 기본 템플릿 파일 이외의 모든 파일입니다. 예를 들어 [자습서: Azure Resource Manager 템플릿을 사용하여 SQL BACPAC 파일 가져오기](./template-tutorial-deploy-sql-extensions-bacpac.md)에서 기본 템플릿은 Azure SQL Database 인스턴스를 만듭니다. 또한 BACPAC 파일을 호출하여 테이블을 만들고 데이터를 삽입합니다. BACPAC 파일은 아티팩트이며 Azure Storage 계정에 저장됩니다. 스토리지 계정 키를 사용하여 아티팩트에 액세스했습니다. 

이 자습서에서는 SAS를 사용하여 고유한 Azure Storage 계정에서 BACPAC 파일에 대한 제한된 액세스 권한을 부여합니다. SAS에 대한 자세한 내용은 [SAS(공유 액세스 서명) 사용](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

연결된 템플릿을 보호하는 방법을 알아보려면 [자습서: 연결된 Azure Resource Manager 템플릿 만들기](./template-tutorial-create-linked-templates.md)를 참조하세요.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * BACPAC 파일을 준비합니다.
> * 기존 템플릿을 엽니다.
> * 템플릿을 편집합니다.
> * 템플릿을 배포합니다.
> * 배포를 확인합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음이 필요합니다.

* Resource Manager 도구 확장이 포함된 Visual Studio Code. [Visual Studio Code를 사용하여 Azure Resource Manager 템플릿 만들기](./use-vs-code-to-create-template.md)를 참조하세요.
* [자습서: Azure Resource Manager 템플릿을 사용하여 SQL BACPAC 파일 가져오기](./template-tutorial-deploy-sql-extensions-bacpac.md)를 검토합니다. 이 자습서에서 사용된 파일은 해당 자습서에서 개발된 것입니다. 완료된 템플릿의 다운로드 링크는 이 문서에서 제공됩니다.
* 보안을 강화하려면 SQL Server 관리자 계정에 대해 생성된 암호를 사용하세요. 암호를 생성하는 데 사용할 수 있는 샘플은 다음과 같습니다.

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault는 암호화 키 및 기타 비밀을 보호하기 위한 것입니다. 자세한 내용은 [자습서: Resource Manager 템플릿 배포에 Azure Key Vault 통합](./template-tutorial-use-key-vault.md)을 참조하세요. 또한 3개월 마다 암호를 업데이트하는 것도 좋습니다.

## <a name="prepare-a-bacpac-file"></a>BACPAC 파일 준비

이 섹션에서는 Resource Manager 템플릿을 배포하는 경우 파일에 안전하게 액세스할 수 있도록 BACPAC 파일을 준비합니다. 이 섹션에는 다섯 개의 프로시저가 있습니다.

* BACPAC 파일 다운로드
* Azure Storage 계정 만들기
* 스토리지 계정 Blob 컨테이너를 만듭니다.
* 컨테이너에 BACPAC 파일 업로드
* BACPAC 파일의 SAS 토큰 검색

1. **사용해 보기**를 선택하여 클라우드 셸을 엽니다. 그런 다음, 다음 PowerShell 스크립트를 셸 창에 붙여 넣습니다.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

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

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. BACPAC 파일 URL 및 SAS 토큰을 기록합니다. 템플릿을 배포할 때 필요합니다.

## <a name="open-an-existing-template"></a>기존 템플릿 열기

이 세션에서는 [자습서: Azure Resource Manager 템플릿을 사용하여 SQL BACPAC 파일 가져오기](./template-tutorial-deploy-sql-extensions-bacpac.md)에서 만든 템플릿을 수정하여 SAS 토큰으로 BACPAC 파일을 호출합니다. SQL 확장 자습서에서 개발된 템플릿은 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json)에서 공유됩니다.

1. Visual Studio Code에서 **파일** > **파일 열기**를 차례로 선택합니다.
1. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

1. **열기**를 선택하여 파일을 엽니다.

    템플릿에는 다음과 같은 4개의 리소스가 정의되어 있습니다.

   * `Microsoft.Sql/servers`입니다. [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers)를 참조하세요.
   * `Microsoft.SQL/servers/firewallRules`입니다. [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules)를 참조하세요.
   * `Microsoft.SQL/servers/databases`입니다. [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)를 참조하세요.
   * `Microsoft.SQL/server/databases/extensions`입니다. [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)를 참조하세요.

        템플릿을 사용자 지정하기 전에 템플릿의 몇 가지 기본적인 내용을 이해하면 유용합니다.
1. **파일** > **다른 이름으로 저장**을 선택하여 파일 복사본을 로컬 컴퓨터에 *azuredeploy.json*이라는 이름으로 저장합니다.

## <a name="edit-the-template"></a>템플릿 편집

1. storageAccountKey 매개 변수 정의를 다음 매개 변수 정의로 바꿉니다. 

    ```json
        "_artifactsLocationSasToken": {
          "type": "securestring",
          "metadata": {
            "description": "Specifies the SAS token required to access the artifact location."
          }
        },
    ```

    ![Resource Manager 자습서 보호 아티팩트 매개 변수](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

1. SQL 확장 리소스의 다음 세 가지 요소 값을 업데이트합니다.

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

완성된 템플릿은 다음과 같습니다.

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>템플릿 배포

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

배포 절차는 [템플릿 배포](./template-tutorial-create-multiple-instances.md#deploy-the-template) 섹션을 참조하세요. 다음 PowerShell 배포 스크립트를 대신 사용합니다.

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

생성된 암호를 사용합니다. [필수 조건](#prerequisites)을 참조하세요.
_artifactsLocation, _artifactsLocationSasToken 및 bacpacFileName 값은 [BACPAC 파일 준비](#prepare-a-bacpac-file)를 참조하세요.

## <a name="verify-the-deployment"></a>배포 확인

포털에서 새로 배포된 리소스 그룹의 SQL 데이터베이스를 선택합니다. **쿼리 편집기(미리 보기)** 를 선택한 다음, 관리자 자격 증명을 입력합니다. 두 테이블을 데이터베이스로 가져온 것을 볼 수 있습니다.

![쿼리 편집기(미리 보기)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal에서 왼쪽 메뉴에 있는 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다. 리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 SAS 토큰을 사용하여 SQL 서버 및 SQL 데이터베이스를 배포하고 BACPAC 파일을 가져왔습니다. Azure 파이프라인을 만들어 Resource Manager 템플릿을 지속적으로 개발하고 배포하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Pipelines를 사용한 연속 통합](./template-tutorial-use-azure-pipelines.md)
