---
title: 다른 지역으로 Azure Analysis Services 이동 | Microsoft Docs
description: Azure Analysis Services 리소스를 다른 지역으로 이동하는 방법을 설명합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: 9fb994968835d6fb609c079f008f87d4b37ef85b
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113004883"
---
# <a name="move-analysis-services-to-a-different-region"></a>다른 지역으로 Analysis Services 이동 | Microsoft Docs

이 문서에서는 Analysis Services 서버 리소스를 다른 Azure 지역으로 이동하는 방법을 설명합니다. 예를 들어 사용자와 가까운 Azure 지역을 활용하거나, 특정 지역에서만 지원하는 서비스 계획을 사용하거나, 내부 정책 및 거버넌스 요구 사항을 충족하기 위한 등의 여러 가지 이유로 서버를 다른 지역으로 이동할 수 있습니다. 

이 문서 및 연결 문서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> 
> * 원본 서버 model 데이터베이스를 [Blob Storage](../storage/blobs/storage-blobs-introduction.md)에 백업합니다.
> * 원본 서버 [리소스 템플릿](../azure-resource-manager/templates/overview.md)을 내보냅니다.
> * [SAS(공유 액세스 서명)](../storage/common/storage-sas-overview.md) 스토리지 얻기
> * 리소스 템플릿을 수정합니다.
> * 템플릿을 배포하여 새 대상 서버를 만듭니다.
> * model 데이터베이스를 새 대상 서버로 복원합니다.
> * 새 대상 서버 및 데이터베이스를 확인합니다.
> * 원본 서버를 삭제합니다.

이 문서에서는 리소스 템플릿을 사용하여 **기본 구성** 이 포함된 단일 Analysis Services 서버를 동일한 구독의 다른 지역 *및* 리소스 그룹으로 마이그레이션하는 방법에 대해 설명합니다. 템플릿을 사용하면 구성된 서버 속성이 유지되므로 대상 서버가 지역 및 리소스 그룹을 제외하고 원본 서버와 동일한 속성으로 구성됩니다. 이 문서에서는 데이터 원본, 스토리지 및 게이트웨이 리소스와 같은 동일한 리소스 그룹의 일부일 수 있는 관련 리소스 이동에 대해 설명하지 않습니다. 

서버를 다른 지역으로 이동하기 전에 자세한 계획을 만드는 것이 좋습니다. 이동해야 할 수도 있는 게이트웨이 및 스토리지와 같은 추가 리소스를 고려합니다. 어떤 계획을 사용하든 프로덕션 서버를 이동하기 전에 테스트 서버를 사용하여 하나 이상의 이동 작업 시도를 완료하는 것이 중요합니다.

> [!IMPORTANT]
> 클라이언트 애플리케이션 및 연결 문자열은 서버가 있는 지역을 포함하는 URI인 전체 서버 이름을 사용하여 Analysis Services에 연결합니다. 예: `asazure://westcentralus.asazure.windows.net/advworks01`. 서버를 다른 지역으로 이동할 때 다른 지역에 새 서버 리소스를 효과적으로 만들며, 이는 서버 이름 URI에 다른 지역이 있습니다. 스크립트에서 사용되는 클라이언트 애플리케이션 및 연결 문자열은 새 서버 이름 URI를 사용하여 새 서버에 연결해야 합니다. [서버 이름 별칭](analysis-services-server-alias.md)을 사용하면 서버 이름 URI를 변경해야 하는 횟수를 줄일 수 있지만 지역 이동 전에 구현해야 합니다.

> [!IMPORTANT]
> Azure 지역은 서로 다른 IP 주소 범위를 사용합니다. 서버 및/또는 스토리지 계정이 있는 지역에 대해 방화벽 예외가 구성된 경우 다른 IP 주소 범위를 구성해야 할 수 있습니다. 자세히 알아보려면 [Analysis Services 네트워크 연결에 대한 FAQ(질문과 대답)](analysis-services-network-faq.yml)를 참조하세요.

> [!NOTE]
> 이 문서에서는 원본 서버 지역의 스토리지 컨테이너에서 대상 서버로 데이터베이스 백업을 복원하는 방법을 설명합니다. 경우에 따라, 다른 지역에서 백업을 복원하면 성능이 저하될 수 있습니다(특히, 데이터베이스 규모가 큰 경우). 데이터베이스 복원 중 최상의 성능을 위해 대상 서버 지역에 마이그레이션하거나 새 스토리지 컨테이너를 만듭니다. 대상 서버에 데이터베이스를 복원하기 전에 원본 지역 스토리지 컨테이너에서 대상 지역 스토리지 컨테이너로 .abf 백업 파일을 복사합니다. 이 문서의 범위를 벗어나지만, 원본 서버에서 데이터베이스를 스크립팅하고, 다시 만들고, 대상 서버에서 데이터베이스에서 처리하여 데이터베이스 데이터를 로드하는 것이 백업/복원을 사용하는 것보다 비용 효율적인 경우도 있습니다(특히, 매우 큰 데이터베이스를 사용하는 경우).

> [!NOTE]
> 온-프레미스 데이터 게이트웨이를 사용하여 데이터 원본에 연결하는 경우에도 게이트웨이 리소스를 대상 서버 지역으로 이동해야 합니다. 자세히 알아보려면 [온-프레미스 데이터 게이트웨이 설치 및 구성](analysis-services-gateway-install.md)을 참조하세요.

## <a name="prerequisites"></a>필수 요건

- **Azure 스토리지 계정**: .abf 백업 파일을 저장하는 데 필요합니다.
- **SSMS(SQL Server Management Studio)** : model 데이터베이스를 백업 및 복원하는 데 필요합니다.
- **Azure PowerShell**. PowerShell을 사용하여 이 작업을 완료하도록 선택하는 경우에만 필요합니다.

## <a name="prepare"></a>준비

### <a name="backup-model-databases"></a>model 데이터베이스 백업

**스토리지 설정** 이 아직 원본 서버에 대해 구성되지 않은 경우 [스토리지 설정 구성](analysis-services-backup.md#configure-storage-settings)의 단계를 따릅니다.

스토리지 설정이 구성된 경우 [백업](analysis-services-backup.md#backup)의 단계에 따라 스토리지 컨테이너에 model 데이터베이스 .abf 백업을 만듭니다. 나중에 .abf 백업을 새 대상 서버에 복원합니다.


### <a name="export-template"></a>템플릿 내보내기

템플릿에는 원본 서버의 구성 속성이 포함되어 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 템플릿을 내보내려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **모든 리소스** 를 선택한 다음 Analysis Services 서버를 선택합니다.

3. **설정** > **템플릿 내보내기** 를 선택합니다.

4. **템플릿 내보내기** 블레이드에서 **다운로드** 를 선택합니다.

5. 포털에서 다운로드한 .zip 파일을 찾은 다음, 폴더에 압축을 풉니다.

   zip 파일에는 새 서버를 배포하는 데 필요한 템플릿 및 매개 변수를 구성하는 .json 파일이 포함되어 있습니다.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 템플릿을 내보내려면:

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. ID가 둘 이상의 구독과 연결된 경우 활성 구독을 이동하려는 서버 리소스에 대한 구독으로 설정합니다.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. 원본 서버의 템플릿을 내보냅니다. 이러한 명령은 파일 이름이 ResourceGroupName인 json 템플릿을 현재 디렉터리에 저장합니다.

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

### <a name="get-storage-shared-access-signature-sas"></a>스토리지 SAS(공유 액세스 서명) 가져오기

템플릿에서 대상 서버를 배포하는 경우 데이터베이스 백업이 포함된 스토리지 컨테이너를 지정하려면 사용자 위임 SAS 토큰이 URI로서 필요합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

포털을 사용하여 공유 액세스 서명을 가져오려면:

1. 포털에서 서버 데이터베이스를 백업하는 데 사용되는 스토리지 계정을 선택합니다.

2. **Storage Explorer** 를 선택하고 **Blob 컨테이너** 를 확장합니다. 

3. 마우스 오른쪽 단추로 스토리지 컨테이너를 클릭한 다음, **공유 액세스 서명 가져오기** 를 선택합니다.

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="SAS 가져오기":::

4. **SAS(공유 액세스 서명)** 에서 **만들기** 를 선택합니다. 기본적으로 SAS는 24시간 후에 만료됩니다.

5. **URI** 를 복사하고 저장합니다. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 공유 액세스 서명을 얻으려면 [PowerShell을 사용하여 컨테이너 또는 Blob에 대한 사용자 위임 SAS 만들기](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob)의 단계를 따르세요.

---

### <a name="modify-the-template"></a>템플릿 수정

텍스트 편집기를 사용하여 내보낸 template.json 파일을 수정하고 영역 및 Blob 컨테이너 속성을 변경합니다. 

템플릿을 수정하려면:

1. 텍스트 편집기의 **location** 속성에서 새 대상 지역을 지정합니다. **BackupBlobContainerUri** 속성에서 SAS 키를 사용하여 스토리지 컨테이너 URI를 붙여넣습니다. 

    다음 예에서는 서버 advworks1에 대한 대상 지역을 `South Central US`로 설정하고 공유 액세스 서명으로 스토리지 컨테이너 URI를 지정합니다. 

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

Azure 지역을 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요. PowerShell을 사용하여 지역을 가져오려면 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 명령을 실행합니다.

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>이동

다른 지역에 새 서버 리소스를 배포하려면 이전 섹션에서 내보내고 수정한 파일 **template.json** 을 사용합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 포털에서 **리소스 만들기** 를 선택합니다.

2. **Marketplace 검색** 에서 **템플릿 배포** 를 입력하고 **ENTER** 를 누릅니다.

3. **템플릿 배포** 를 선택합니다.

4. **만들기** 를 선택합니다.

5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.

6. **파일 로드** 를 선택한 다음, 지침에 따라 마지막 섹션에서 다운로드한 **template.json** 파일을 로드합니다.

7. 새 대상 서버에 대한 올바른 속성이 템플릿 편집기에 표시되는지 확인합니다.

8. **저장** 을 선택합니다.

9. 다음 속성 값을 입력하거나 선택합니다.

    - **구독**: Azure 구독을 선택합니다.
    
    - **리소스 그룹**: **새로 만들기** 를 선택한 다음, 리소스 그룹 이름을 지정합니다. 아직 같은 이름의 Analysis Services 서버가 포함되어 있지 않은 경우 기존 리소스 그룹을 선택할 수 있습니다.
    
    - **위치**: 템플릿에서 지정한 동일한 지역을 선택합니다.

10. **검토 및 만들기** 를 선택합니다.

11. 용어 및 기본 사항을 검토한 다음 **만들기** 를 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 명령을 사용하여 템플릿을 배포합니다.

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>대상 서버 URI 가져오기

SSMS에서 새 대상 서버에 연결하여 model 데이터베이스를 복원하려면 새 대상 서버 URI를 가져와야 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

포털에서 서버 URI를 가져오려면:

1. 포털에서 새 대상 서버 리소스로 이동합니다.

2. **개요** 페이지에서 **서버 이름** URI를 복사합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 서버 URI를 가져오려면 다음 명령을 사용합니다.

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
출력에서 **ServerFullName** 을 복사합니다.

---

### <a name="restore-model-database"></a>model 데이터베이스 복원

[복원](analysis-services-backup.md#restore)에 설명된 단계에 따라 model 데이터베이스 .abf 백업을 새 대상 서버로 복원합니다.

선택 사항: model 데이터베이스를 복원한 후 모델 및 테이블을 처리하여 데이터 원본에서 데이터를 새로 고칩니다. SSMS를 사용하여 모델 및 테이블을 처리하려면:

1. SSMS에서 model 데이터베이스를 마우스 오른쪽 단추로 클릭하여 **데이터베이스 처리** 로 이동합니다.

2. **테이블** 을 확장하고 테이블을 마우스 오른쪽 단추로 클릭합니다. **테이블 처리** 에서 모든 테이블을 선택하고 **확인** 을 선택합니다.

## <a name="verify"></a>확인

1. 포털에서 새 대상 서버로 이동합니다.

2. 개요 페이지의 **Analysis Services 서버에 있는 모델** 에서 복원된 모델이 나타나는지 확인합니다.

3. Power BI 또는 Excel과 같은 클라이언트 애플리케이션을 사용하여 새 서버의 모델에 연결합니다. 테이블, 측정값, 계층 구조 등의 모델 개체가 표시되는지 확인합니다. 

4. 모든 자동화 스크립트를 실행합니다. 성공적으로 실행되었는지 확인합니다.

선택 사항: [ALM 도구 키트](http://alm-toolkit.com/)는 Power BI 데이터 세트 *및* Analysis Services 테이블 형식 model 데이터베이스를 비교하고 관리하기 위한 *오픈 소스* 도구입니다. 도구 키트를 사용하여 원본 및 대상 서버 데이터베이스에 모두 연결하고 비교합니다. 데이터베이스 마이그레이션이 성공적으로 수행되면 모델 개체의 정의가 동일해집니다. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM 도구 키트":::

## <a name="clean-up-resources"></a>리소스 정리

클라이언트 애플리케이션에서 새 서버에 연결할 수 있는지 확인하고 자동화 스크립트가 제대로 실행되고 있으면 원본 서버를 삭제합니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

포털에서 원본 서버를 삭제하려면 다음을 수행합니다.

원본 서버의 **개요** 페이지에서 **삭제** 를 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 원본 서버를 삭제하려면 Remove-AzAnalysisServicesServer 명령을 사용합니다.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> 영역 이동을 완료한 후에는 새 대상 서버에서 원본 서버 지역의 스토리지 컨테이너를 사용하지 않고 동일한 백업 지역의 스토리지 컨테이너를 사용하는 것이 좋습니다.
