---
title: Azure Resource Manager 템플릿 배포에서 아티팩트 보호 | Microsoft Docs
description: Azure Resource Manager 템플릿에서 사용된 아티팩트를 보호하는 방법에 대해 알아봅니다.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/25/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fd68edcc727ab08ed9d3ba765bbe795e88de5fc9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60391311"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>자습서: Azure Resource Manager 템플릿 배포에서 아티팩트 보호

SAS(공유 액세스 서명)가 포함된 Azure Storage 계정을 사용하여 Azure Resource Manager 템플릿에서 사용된 아티팩트를 보호하는 방법에 대해 알아봅니다. 배포 아티팩트는 배포를 완료하는 데 필요한 기본 템플릿 파일 이외의 모든 파일입니다. 예를 들어 [자습서: Azure Resource Manager 템플릿을 사용하여 SQL BACPAC 파일 가져오기](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)에서 기본 템플릿은 Azure SQL Database를 만듭니다. 또한 BACPAC 파일을 호출하여 테이블을 만들고 데이터를 삽입합니다. BACPAC 파일은 아티팩트입니다. 아티팩트는 공용 액세스를 사용하여 Azure Storage 계정에 저장됩니다. 이 자습서에서는 SAS를 사용하여 고유한 Azure Storage 계정에서 BACPAC 파일에 대한 제한된 액세스 권한을 부여합니다. SAS에 대한 자세한 내용은 [SAS(공유 액세스 서명) 사용](../storage/common/storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

연결된 템플릿을 보호하는 방법을 알아보려면 [자습서: 연결된 Azure Resource Manager 템플릿 만들기](./resource-manager-tutorial-create-linked-templates.md)를 참조하세요.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * BACPAC 파일 준비
> * 기존 템플릿 열기
> * 템플릿 편집
> * 템플릿 배포
> * 배포 확인

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* Resource Manager Tools 확장이 있는 [Visual Studio Code](https://code.visualstudio.com/)  [확장 설치](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)를 참조하세요.
* [자습서: Azure Resource Manager 템플릿을 사용하여 SQL BACPAC 파일 가져오기](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)를 검토합니다. 이 자습서에서 사용된 파일은 해당 자습서에서 개발된 것입니다. 완료된 템플릿의 다운로드 링크는 이 문서에서 제공됩니다.
* 보안을 강화하려면 SQL Server 관리자 계정에 대해 생성된 암호를 사용하세요. 암호를 생성하는 방법에 대한 샘플은 다음과 같습니다.

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault는 암호화 키 및 기타 비밀을 보호하기 위한 것입니다. 자세한 내용은 [자습서: Resource Manager 템플릿 배포에 Azure Key Vault 통합](./resource-manager-tutorial-use-key-vault.md)을 참조하세요. 또한 3개월 마다 암호를 업데이트하는 것도 좋습니다.

## <a name="prepare-a-bacpac-file"></a>BACPAC 파일 준비

이 섹션에서는 Resource Manager 템플릿을 배포하는 경우 파일에 안전하게 액세스할 수 있도록 BACPAC 파일을 준비합니다. 이 섹션에는 다섯 개의 프로시저가 있습니다.

* BACPAC 파일 다운로드
* Azure Storage 계정 만들기
* Storage 계정 Blob 컨테이너 만들기
* 컨테이너에 BACPAC 파일 업로드
* BACPAC 파일의 SAS 토큰 검색

PowerShell 스크립트를 사용하여 이러한 단계를 자동화하려면 [연결된 템플릿 업로드](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template)의 스크립트를 참조하세요.

### <a name="download-the-bacpac-file"></a>BACPAC 파일 다운로드

[BACPAC 파일](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac)을 다운로드하고 **SQLDatabaseExtension.bacpac**라는 동일한 이름으로 로컬 컴퓨터에 파일을 저장합니다.

### <a name="create-a-storage-account"></a>저장소 계정 만들기

1. 다음 이미지를 선택하여 Azure Portal에서 Resource Manager 템플릿을 엽니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. 다음과 같은 속성을 입력합니다.

    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: **새로 만들기**를 선택하고 이름을 지정합니다. 리소스 그룹은 관리용인 Azure 리소스에 대한 컨테이너입니다. 이 자습서에서는 스토리지 계정 및 Azure SQL Database에 동일한 리소스 그룹을 사용할 수 있습니다. 이 리소스 그룹 이름을 메모해두세요. 자습서의 뒷부분에 나오는 Azure SQL Database를 만들 때 필요합니다.
    * **위치**: 지역을 선택합니다. 예: **미국 중부** 
    * **스토리지 계정 형식**: **Standard_LRS**라는 기본값을 사용합니다.
    * **위치**: **[resourceGroup().location]** 이라는 기본값을 사용합니다. 즉, 스토리지 계정에 리소스 그룹 위치를 사용합니다.
    * **위에 명시된 사용 약관에 동의함**: (선택됨)
3. **구매**를 선택합니다.
4. 배포 상태를 확인하기 위해 포털의 오른쪽 위 모서리에서 알림 아이콘(벨 아이콘)을 선택합니다.

    ![Resource Manager 자습서 포털 알림 창](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. 스토리지 계정이 성공적으로 배포되면 알림 창에서 **리소스 그룹으로 이동**을 선택하여 리소스 그룹을 엽니다.

### <a name="create-a-blob-container"></a>Blob 컨테이너 만들기

파일을 업로드하려면 먼저 Blob 컨테이너가 필요합니다. 

1. 저장소 계정을 선택하여 엽니다. 리소스 그룹에 나열된 스토리지 계정이 표시됩니다. 스토리지 계정 이름은 다음 스크린샷에 표시된 것과 다릅니다.

    ![Resource Manager 자습서 스토리지 계정](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. **Blob** 타일을 선택합니다.

    ![Resource Manager 자습서 Blob](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. 위쪽에서 **+ 컨테이너**를 선택하여 새 컨테이너를 만듭니다.
4. 다음 값을 입력합니다.

    * **이름**: **sqlbacpac**를 입력합니다. 
    * **공용 액세스 수준**: **개인(익명 액세스 없음)** 이라는 기본값을 사용합니다.
5. **확인**을 선택합니다.
6. **sqlbacpac**를 선택하여 새로 만든 컨테이너를 엽니다.

### <a name="upload-the-bacpac-file-to-the-container"></a>컨테이너에 BACPAC 파일 업로드

1. **업로드**를 선택합니다.
2. 다음 값을 입력합니다.

    * **파일**: 지침에 따라 이전에 다운로드한 BACPAC 파일을 선택합니다. 기본 이름은 **SQLDatabaseExtension.bacpac**입니다.
    * **인증 형식**: **SAS**를 선택합니다.  *SAS*가 기본값입니다.
3. **업로드**를 선택합니다.  파일이 성공적으로 업로드되면 파일 이름이 컨테이너에 나열됩니다.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />SAS 토큰 만들기

1. 컨테이너에서 **SQLDatabaseExtension.bacpac**를 마우스 오른쪽 단추로 클릭한 다음, **SAS 생성**을 선택합니다.
2. 다음 값을 입력합니다.

    * **사용 권한**: **읽기**라는 기본값을 사용합니다.
    * **시작 및 만료 날짜/시간**: 기본값을 사용하면 8시간 동안 SAS 토큰을 사용할 수 있습니다. 이 자습서를 완료하는 데 시간이 더 필요한 경우 **만료**를 업데이트합니다.
    * **허용된 IP 주소**: 이 필드는 공백으로 둡니다.
    * **허용된 프로토콜**: 기본값을 사용합니다. **HTTPS**입니다.
    * **서명 키**: 기본값을 사용합니다. **키 1**입니다.
3. **URL 및 Blob SAS 토큰 생성**을 선택합니다.
4. **Blob SAS URL**의 복사본을 만듭니다. URL의 중간에서 파일 이름은 **SQLDatabaseExtension.bacpac**입니다.  파일 이름은 URL을 세 부분으로 나눕니다.

   - **아티팩트 위치**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/입니다. 위치가 "/"로 끝나야 합니다.
   - **BACPAC 파일 이름**: SQLDatabaseExtension.bacpac입니다.
   - **아티팩트 위치 SAS 토큰**: 토큰이 "?"로 시작해야 합니다.

     [템플릿 배포](#deploy-the-template)에서 이러한 세 가지 값이 필요합니다.

## <a name="open-an-existing-template"></a>기존 템플릿 열기

이 세션에서는 [자습서: Azure Resource Manager 템플릿을 사용하여 SQL BACPAC 파일 가져오기](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)에서 만든 템플릿을 수정하여 SAS 토큰으로 BACPAC 파일을 호출합니다.  SQL 확장 자습서에서 개발된 템플릿은 [https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json)에서 공유됩니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json
    ```
3. **열기**를 선택하여 파일을 엽니다.

    템플릿에 5개 리소스가 정의되어 있습니다.

   * `Microsoft.Sql/servers`. [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers)를 참조하세요.
   * `Microsoft.SQL/servers/securityAlertPolicies`. [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies)를 참조하세요.
   * `Microsoft.SQL/servers/filewallRules`. [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules)를 참조하세요.
   * `Microsoft.SQL/servers/databases`.  [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)를 참조하세요.
   * `Microsoft.SQL/server/databases/extensions`.  [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)를 참조하세요.

     템플릿을 사용자 지정하기 전에 템플릿의 몇 가지 기본적인 내용을 이해하면 유용합니다.
4. **파일**>**다른 이름으로 저장**을 선택하여 파일 복사본을 로컬 컴퓨터에 **azuredeploy.json**이라는 이름으로 저장합니다.

## <a name="edit-the-template"></a>템플릿 편집

다음 추가 매개 변수를 추가합니다.

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Resource Manager 자습서 보호 아티팩트 매개 변수](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

다음 두 가지 요소 값을 업데이트합니다.

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>템플릿 배포

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

배포 절차는 [템플릿 배포](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) 섹션을 참조하세요. 다음 PowerShell 배포 스크립트를 대신 사용합니다.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

생성된 암호를 사용합니다. [필수 조건](#prerequisites)을 참조하세요.
_artifactsLocation, _artifactsLocationSasToken 및 bacpacFileName이라는 값은 [SAS 토큰 생성](#generate-a-sas-token)을 참조하세요.

## <a name="verify-the-deployment"></a>배포 확인

포털에서 새로 배포된 리소스 그룹의 SQL 데이터베이스를 선택합니다. **쿼리 편집기(미리 보기)** 를 선택한 다음, 관리자 자격 증명을 입력합니다. 두 테이블을 데이터베이스로 가져온 것을 볼 수 있습니다.

![Azure Resource Manager가 sql 확장 BACPAC 배포](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 SAS 토큰을 사용하여 SQL Server, SQL Database를 배포하고 BACPAC 파일을 가져왔습니다. 여러 지역에 Azure 리소스를 배포하는 방법 및 안전한 배포 사례를 사용하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Deployment Manager 사용](./resource-manager-tutorial-deploy-vm-extensions.md)
