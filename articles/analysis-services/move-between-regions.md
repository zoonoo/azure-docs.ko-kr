---
title: 다른 지역으로 Azure Analysis Services 이동 | Microsoft Docs
description: Azure Analysis Services 리소스를 다른 지역으로 이동 하는 방법을 설명 합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 06/09/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 1f7ecf960ae94fae4d829e73daf051b9062e478d
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018197"
---
# <a name="move-analysis-services-to-a-different-region"></a>다른 지역으로 Analysis Services 이동

이 문서에서는 Analysis Services 서버 리소스를 다른 Azure 지역으로 이동 하는 방법을 설명 합니다. 예를 들어 사용자에 게 더 가까운 Azure 지역을 활용 하거나, 특정 지역 에서만 지원 되는 서비스 계획을 사용 하거나, 내부 정책 및 거 버 넌 스 요구 사항을 충족 하기 위해 여러 가지 이유로 서버를 다른 지역으로 이동할 수 있습니다. 

이 연결 된 연결 문서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> 
> * 원본 서버 모델 데이터베이스를 [Blob 저장소](../storage/blobs/storage-blobs-introduction.md)에 백업 합니다.
> * 원본 서버 [리소스 템플릿을](../azure-resource-manager/templates/overview.md)내보냅니다.
> * 저장소 [공유 액세스 서명 (SAS)](../storage/common/storage-sas-overview.md)을 가져옵니다.
> * 리소스 템플릿을 수정 합니다.
> * 템플릿을 배포 하 여 새 대상 서버를 만듭니다.
> * Model 데이터베이스를 새 대상 서버로 복원 합니다.
> * 새 대상 서버 및 데이터베이스를 확인 합니다.
> * 원본 서버를 삭제 합니다.

이 문서에서는 리소스 템플릿을 사용 하 여 **기본 구성이** 포함 된 단일 Analysis Services 서버를 동일한 구독의 다른 지역 *및* 리소스 그룹으로 마이그레이션하는 방법에 대해 설명 합니다. 템플릿을 사용 하면 대상 서버가 영역 및 리소스 그룹을 제외 하 고 원본 서버로 동일한 속성을 사용 하 여 구성 되도록 구성 된 서버 속성이 유지 됩니다. 이 문서에서는 데이터 원본, 저장소 및 게이트웨이 리소스와 같은 리소스 그룹에 포함 될 수 있는 연결 된 리소스를 이동 하는 방법에 대해서는 설명 하지 않습니다. 

서버를 다른 지역으로 이동 하기 전에 자세한 계획을 만드는 것이 좋습니다. 이동 해야 할 수도 있는 게이트웨이 및 저장소와 같은 추가 리소스를 고려 합니다. 모든 계획을 사용 하는 경우 프로덕션 서버를 이동 하기 전에 테스트 서버를 사용 하 여 하나 이상의 평가판 이동 작업을 완료 하는 것이 중요 합니다.

> [!IMPORTANT]
> 클라이언트 응용 프로그램 및 연결 문자열은 서버가 있는 지역을 포함 하는 Uri 인 전체 서버 이름을 사용 하 여 Analysis Services에 연결 합니다. `asazure://westcentralus.asazure.windows.net/advworks01`)을 입력합니다. 서버를 다른 지역으로 이동 하는 경우에는 서버 이름 Uri에서 다른 지역을 포함 하는 다른 지역에 새 서버 리소스를 효과적으로 만드는 것입니다. 스크립트에서 사용 되는 클라이언트 응용 프로그램 및 연결 문자열은 새 서버 이름 Uri를 사용 하 여 새 서버에 연결 해야 합니다. [서버 이름 별칭](analysis-services-server-alias.md) 을 사용 하면 서버 이름 Uri를 변경 해야 하는 위치 수를 줄일 수 있지만 지역 이동 전에는 구현 해야 합니다.

> [!IMPORTANT]
> Azure 지역에서 서로 다른 IP 주소 범위를 사용 합니다. 서버 및/또는 저장소 계정이 있는 지역에 대해 방화벽 예외가 구성 된 경우 다른 IP 주소 범위를 구성 해야 할 수 있습니다. 자세히 알아보려면 [Analysis Services 네트워크 연결에 대 한 질문과 대답](analysis-services-network-faq.md)을 참조 하세요.

> [!NOTE]
> 이 문서에서는 원본 서버 지역의 저장소 컨테이너에서 대상 서버로 데이터베이스 백업을 복원 하는 방법을 설명 합니다. 경우에 따라 다른 지역에서 백업을 복원 하면 성능이 저하 될 수 있습니다. 특히 데이터베이스 규모가 클 경우에는 성능이 저하 될 수 있습니다. 데이터베이스 복원 중 최상의 성능을 위해 대상 서버 지역에 새 저장소 컨테이너를 마이그레이션하거나 만듭니다. 대상 서버에 데이터베이스를 복원 하기 전에 원본 지역 저장소 컨테이너에서 대상 지역 저장소 컨테이너로 .abf 백업 파일을 복사 합니다. 이 문서의 범위를 벗어난 경우, 특히 매우 큰 데이터베이스의 경우 원본 서버에서 데이터베이스를 스크립팅 하 고 대상 서버에서 데이터베이스를 다시 만들고 처리 하 여 데이터베이스 데이터를 로드 하는 것이 백업/복원을 사용 하는 것 보다 비용 효율적일 수 있습니다.

> [!NOTE]
> 온-프레미스 데이터 게이트웨이를 사용 하 여 데이터 원본에 연결 하는 경우에도 게이트웨이 리소스를 대상 서버 지역으로 이동 해야 합니다. 자세히 알아보려면 [온-프레미스 데이터 게이트웨이 설치 및 구성](analysis-services-gateway-install.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

- **Azure storage 계정**: .abf 백업 파일을 저장 하는 데 필요 합니다.
- **SSMS (SQL Server Management Studio)**: model 데이터베이스를 백업 및 복원 하는 데 필요 합니다.
- **Azure PowerShell**. PowerShell을 사용 하 여이 작업을 완료 하도록 선택 하는 경우에만 필요 합니다.

## <a name="prepare"></a>준비

### <a name="backup-model-databases"></a>Model 데이터베이스 백업

원본 서버에 대해 **저장소 설정이** 아직 구성 되지 않은 경우 [저장소 설정 구성](analysis-services-backup.md#configure-storage-settings)의 단계를 따르세요.

저장소 설정이 구성 된 경우 [backup](analysis-services-backup.md#backup) 의 단계에 따라 저장소 컨테이너에 .abf backup 모델을 만듭니다. 나중에 .abf 백업을 새 대상 서버에 복원 합니다.


### <a name="export-template"></a>템플릿 내보내기

템플릿에는 원본 서버의 구성 속성이 포함 되어 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 템플릿을 내보내려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **모든 리소스**를 선택한 다음 Analysis Services 서버를 선택 합니다.

3. > **설정**  >  **템플릿 내보내기**를 선택 합니다.

4. **템플릿 내보내기** 블레이드에서 **다운로드** 를 선택 합니다.

5. 포털에서 다운로드 한 .zip 파일을 찾은 다음 폴더에 해당 파일의 압축을 풉니다.

   Zip 파일에는 새 서버를 배포 하는 데 필요한 템플릿 및 매개 변수를 구성 하는. n a w 파일이 포함 되어 있습니다.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 템플릿을 내보내려면:

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Id가 둘 이상의 구독과 연결 된 경우 활성 구독을 이동 하려는 서버 리소스의 구독으로 설정 합니다.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. 원본 서버의 템플릿을 내보냅니다. 이러한 명령은 ResourceGroupName as filename이 있는 json 템플릿을 현재 디렉터리에 저장 합니다.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>저장소 공유 액세스 서명 (SAS) 가져오기

템플릿에서 대상 서버를 배포 하는 경우 데이터베이스 백업이 포함 된 저장소 컨테이너를 지정 하려면 사용자 위임 SAS 토큰 (Uri)이 필요 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

포털을 사용 하 여 공유 액세스 서명을 가져오려면:

1. 포털에서 서버 데이터베이스를 백업 하는 데 사용 되는 저장소 계정을 선택 합니다.

2. **Storage 탐색기**를 선택 하 고 **BLOB 컨테이너**를 확장 합니다. 

3. 저장소 컨테이너를 마우스 오른쪽 단추로 클릭 하 고 **공유 액세스 서명 가져오기**를 선택 합니다.

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="SAS 가져오기":::

4. **공유 액세스 서명**에서 **만들기**를 선택 합니다. 기본적으로 SAS는 24 시간 후에 만료 됩니다.

5. **URI**를 복사 하 고 저장 합니다. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 공유 액세스 서명을 얻으려면 PowerShell을 사용 하 여 [컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob)의 단계를 따르세요.

---

### <a name="modify-the-template"></a>템플릿 수정

텍스트 편집기를 사용 하 여 내보낸 파일의 template.js를 수정 하 고 영역 및 blob 컨테이너 속성을 변경 합니다. 

템플릿을 수정 하려면:

1. 텍스트 편집기의 **위치** 속성에서 새 대상 지역을 지정 합니다. **BackupBlobContainerUri** 속성에서 SAS 키를 사용 하 여 저장소 컨테이너 Uri를 붙여넣습니다. 

    다음 예제에서는 서버 advworks1에 대 한 대상 지역을로 설정 하 `South Central US` 고 공유 액세스 서명으로 저장소 컨테이너 Uri를 지정 합니다. 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. 템플릿을 저장하는 경우

#### <a name="regions"></a>영역

Azure 지역을 얻으려면 [azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요. PowerShell을 사용 하 여 지역을 가져오려면 [AzLocation](/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) 명령을 실행 합니다.

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>이동

다른 지역에 새 서버 리소스를 배포 하려면 이전 섹션에서 내보내고 수정한 파일 ** 에template.js** 를 사용 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 포털에서 **리소스 만들기**를 선택 합니다.

2. **Marketplace 검색**에서 **템플릿 배포**를 입력 하 고 **enter**키를 누릅니다.

3. **템플릿 배포**를 선택 합니다.

4. **만들기**를 선택합니다.

5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.

6. **파일 로드**를 선택 하 고 지침에 따라 내보내고 수정한 파일 **에template.js** 를 로드 합니다.

7. 새 대상 서버에 대 한 올바른 속성이 템플릿 편집기에 표시 되는지 확인 합니다.

8. **저장**을 선택합니다.

9. 속성 값을 입력 하거나 선택 합니다.

    - **구독**: Azure 구독을 선택합니다.
    
    - **리소스 그룹**: **새로 만들기**를 선택 하 고 리소스 그룹 이름을 입력 합니다. 이미 같은 이름의 Analysis Services 서버가 포함 되어 있지 않은 경우 기존 리소스 그룹을 선택할 수 있습니다.
    
    - **위치**: 템플릿에서 지정한 동일한 지역을 선택 합니다.

10. **검토 및 만들기를**선택 합니다.

11. 사용 약관을 검토 한 다음 **만들기**를 선택 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 명령을 사용 하 여 템플릿을 배포 합니다.

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>대상 서버 Uri 가져오기

SSMS에서 새 대상 서버에 연결 하 여 model 데이터베이스를 복원 하려면 새 대상 서버 Uri를 가져와야 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

포털에서 서버 Uri를 가져오려면:

1. 포털에서 새 대상 서버 리소스로 이동 합니다.

2. **개요** 페이지에서 **서버 이름** Uri를 복사 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 서버 Uri를 가져오려면 다음 명령을 사용 합니다.

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
출력에서 **Serverfullname** 을 복사 합니다.

---

### <a name="restore-model-database"></a>Model 데이터베이스 복원

[복원](analysis-services-backup.md#restore) 에 설명 된 단계에 따라 model database. .abf backup을 새 대상 서버로 복원 합니다.

선택 사항: model 데이터베이스를 복원한 후 모델 및 테이블을 처리 하 여 데이터 원본에서 데이터를 새로 고칩니다. SSMS를 사용 하 여 모델 및 테이블을 처리 하려면 다음을 수행 합니다.

1. SSMS에서 model 데이터베이스를 마우스 오른쪽 단추로 클릭 하 > **데이터베이스를 처리**합니다.

2. 테이블 **을 확장 하**고 테이블을 마우스 오른쪽 단추로 클릭 합니다. **테이블 처리**에서 모든 테이블을 선택 하 고 **확인**을 선택 합니다.

## <a name="verify"></a>확인

1. 포털에서 새 대상 서버로 이동 합니다.

2. 개요 페이지의 **Analysis Services 서버에 있는 모델**에서 복원 된 모델이 나타나는지 확인 합니다.

3. Power BI 또는 Excel과 같은 클라이언트 응용 프로그램을 사용 하 여 새 서버의 모델에 연결 합니다. 테이블, 측정값, 계층 등의 모델 개체가 표시 되는지 확인 합니다. 

4. 모든 자동화 스크립트를 실행 합니다. 성공적으로 실행 되었는지 확인 합니다.

선택 사항: [ALM 도구 키트](http://alm-toolkit.com/) 는 Power BI 데이터 집합 *및* Analysis Services 테이블 형식 모델 데이터베이스를 비교 하 고 관리 하기 위한 *오픈 소스* 도구입니다. 도구 키트를 사용 하 여 원본 및 대상 서버 데이터베이스에 모두 연결 하 고 비교 합니다. 데이터베이스 마이그레이션이 성공적으로 수행 되 면 모델 개체의 정의가 동일 하 게 됩니다. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="SAS 가져오기":::

## <a name="clean-up-resources"></a>리소스 정리

클라이언트 응용 프로그램에서 새 서버에 연결할 수 있는지 확인 하 고 자동화 스크립트가 제대로 실행 되 고 있으면 원본 서버를 삭제 합니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

포털에서 원본 서버를 삭제 하려면 다음을 수행 합니다.

원본 서버의 **개요** 페이지에서 **삭제**를 선택 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 원본 서버를 삭제 하려면 Remove-AzAnalysisServicesServer 명령을 사용 합니다.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> 영역 이동을 완료 한 후에는 새 대상 서버에서 원본 서버 지역의 저장소 컨테이너가 아닌 동일한 백업 지역에 저장소 컨테이너를 사용 하는 것이 좋습니다.