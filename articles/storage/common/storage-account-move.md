---
title: Azure Storage 계정을 다른 지역으로 이동 | Microsoft Docs
description: Azure Storage 계정을 다른 지역으로 이동 하는 방법을 보여 줍니다.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: b70beb90fae794eb5512cb8b466524169c4c7b53
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792993"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Azure Storage 계정을 다른 지역으로 이동

스토리지 계정을 이동하려면 다른 지역에 스토리지 계정의 복사본을 만듭니다. 그런 다음 AzCopy 또는 원하는 다른 도구를 사용 하 여 해당 계정으로 데이터를 이동 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> 
> * 템플릿을 내보냅니다.
> * 대상 지역과 저장소 계정 이름을 추가 하 여 템플릿을 수정 합니다.
> * 템플릿을 배포 하 여 새 저장소 계정을 만듭니다.
> * 새 저장소 계정을 구성 합니다.
> * 새 저장소 계정으로 데이터를 이동 합니다.
> * 원본 영역에서 리소스를 삭제 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 계정에서 사용하는 서비스 및 기능이 대상 지역에서 지원되는지 확인합니다.

- 미리 보기 기능의 경우 구독이 대상 지역에 대한 허용 목록에 추가되었는지 확인합니다.

<a id="prepare"></a>

## <a name="prepare"></a>준비

시작 하려면 리소스 관리자 템플릿을 내보낸 다음 수정 합니다. 

### <a name="export-a-template"></a>템플릿 내보내기

이 템플릿에는 스토리지 계정을 설명하는 설정이 포함되어 있습니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 템플릿을 내보내려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **모든 리소스** 를 선택한 다음, 저장소 계정을 선택 합니다.

3. > **설정**  >  **템플릿 내보내기** 를 선택 합니다.

4. **템플릿 내보내기** 블레이드에서 **다운로드** 를 선택 합니다.

5. 포털에서 다운로드 한 .zip 파일을 찾아 원하는 폴더에 해당 파일의 압축을 풉니다.

   이 zip 파일에는 템플릿을 배포 하는 템플릿 및 스크립트를 구성 하는. i n i 파일이 포함 되어 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 템플릿을 내보내려면:

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Id가 둘 이상의 구독과 연결 된 경우 활성 구독을 이동 하려는 저장소 계정의 구독으로 설정 합니다.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. 원본 저장소 계정의 템플릿을 내보냅니다. 이러한 명령은 json 템플릿을 현재 디렉터리에 저장 합니다.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>템플릿 수정 

스토리지 계정 이름 및 지역을 변경하여 템플릿을 수정합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 템플릿을 배포 하려면 다음을 수행 합니다.

1. Azure Portal에서 **리소스 만들기** 를 선택 합니다.

2. **Marketplace 검색** 에서 **템플릿 배포** 를 입력 하 고 **enter** 키를 누릅니다.

3. **템플릿 배포** 를 선택 합니다.

    ![Azure Resource Manager 템플릿 라이브러리](./media/storage-account-move/azure-resource-manager-template-library.png)

4. **만들기** 를 선택합니다.

5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.

6. **파일 로드** 를 선택 하 고 지침에 따라 마지막 섹션에서 다운로드 한 파일 **에template.js** 를 로드 합니다.

7. 파일 **의template.js** 에서 저장소 계정 이름의 기본값을 설정 하 여 대상 저장소 계정의 이름을로 설정 합니다. 이 예에서는 저장소 계정 이름의 기본값을로 설정 `mytargetaccount` 합니다.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요.  영역에 대 한 코드는 공백 없이 **미국 중부**  =  **centralus** 지역 이름입니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 템플릿을 배포 하려면 다음을 수행 합니다.

1. 파일 **의template.js** 에서 저장소 계정 이름의 기본값을 설정 하 여 대상 저장소 계정의 이름을로 설정 합니다. 이 예에서는 저장소 계정 이름의 기본값을로 설정 `mytargetaccount` 합니다.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. 파일 **의template.js** **위치** 속성을 대상 지역으로 편집 합니다. 이 예에서는 대상 지역을로 설정 `eastus` 합니다.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    [AzLocation](/powershell/module/az.resources/get-azlocation) 명령을 실행 하 여 지역 번호를 가져올 수 있습니다.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>이동

템플릿을 배포하여 대상 지역에 새 스토리지 계정을 만듭니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 파일 **에template.js** 을 저장 합니다.

2. 속성 값을 입력 하거나 선택 합니다.

- **구독** : Azure 구독을 선택합니다.

- **리소스 그룹** : **새로 만들기** 를 클릭하고 리소스 그룹에 이름을 지정합니다.

- **위치** : Azure 위치를 선택 합니다.

3. **위에 명시 된 사용 약관에 동의 함** 확인란을 클릭 한 다음 **구매 선택** 단추를 클릭 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [AzSubscription](/powershell/module/az.accounts/get-azsubscription)를 사용 하 여 대상 공용 IP를 배포 하려는 구독 ID를 가져옵니다.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. 다음 명령을 사용 하 여 템플릿을 배포 합니다.

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>새 스토리지 계정 구성

일부 기능은 템플릿으로 내보내지 않으므로 새 스토리지 계정에 추가해야 합니다. 

다음 표에 이러한 기능과 함께 새 스토리지 계정에 이 기능을 추가하기 위한 지침이 나와 있습니다.

| 기능    | 지침    |
|--------|-----------|
| **수명 주기 관리 정책** | [Azure Blob Storage 수명 주기 관리](../blobs/storage-lifecycle-management-concepts.md) |
| **정적 웹 사이트** | [Azure Storage에서 정적 웹 사이트 호스트](../blobs/storage-blob-static-website-how-to.md) |
| **이벤트 구독** | [Blob Storage 이벤트에 응답](../blobs/storage-blob-event-overview.md) |
| **경고** | [Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리하기](../../azure-monitor/platform/alerts-activity-log.md) |
| **CDN(콘텐츠 전송 네트워크)** | [Azure CDN을 사용하여 HTTP를 통한 사용자 지정 도메인으로 Blob 액세스](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> 원본 저장소 계정에 대 한 CDN을 설정 하는 경우 기존 CDN의 원본을 새 계정의 주 blob service 끝점 (또는 기본 정적 웹 사이트 끝점)으로 변경 하면 됩니다. 

### <a name="move-data-to-the-new-storage-account"></a>새 스토리지 계정으로 데이터 이동

AzCopy은 데이터를 이동 하는 데 선호 되는 도구입니다. 성능에 최적화되어 있습니다.  더 빠른 방법 중 하나는 스토리지 서버 간에 데이터를 직접 복사하여 AzCopy에서 컴퓨터의 네트워크 대역폭을 사용하지 않도록 하는 것입니다. 명령줄에서 AzCopy를 사용하거나 사용자 지정 스크립트의 일부로 사용합니다. [AzCopy 시작](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.

Azure Data Factory를 사용 하 여 데이터를 이동할 수도 있습니다. 직관적인 사용자 인터페이스를 제공 합니다. Azure Data Factory를 사용 하려면 다음 링크 중 하나를 참조 하세요.. 

  - [Azure Data Factory를 사용하여 Azure Blob Storage에(서) 데이터 복사](/azure/data-factory/connector-azure-blob-storage)
  - [Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2 간에 데이터 복사](/azure/data-factory/connector-azure-data-lake-storage)
  - [Azure Data Factory를 사용하여 Azure File Storage 간에 데이터 복사](/azure/data-factory/connector-azure-file-storage)
  - [Azure Data Factory를 사용하여 Azure Table Storage 간 데이터 복사](/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>삭제 또는 정리

배포 후에는 다시 시작 하려는 경우 대상 저장소 계정을 삭제 하 고이 문서의 [준비](#prepare) 및 [이동](#move) 섹션에 설명 된 단계를 반복할 수 있습니다.

변경 내용을 커밋하고 스토리지 계정의 이동을 완료하려면 원본 스토리지 계정을 삭제합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 스토리지 계정을 제거하려면:

1. Azure Portal에서 왼쪽의 메뉴를 확장 하 여 서비스의 메뉴를 열고 **저장소 계정** 을 선택 하 여 저장소 계정 목록을 표시 합니다.

2. 삭제할 대상 저장소 계정을 찾고 목록 오른쪽에 있는 **자세히** 단추 ( **...** )를 마우스 오른쪽 단추로 클릭 합니다.

3. **삭제** 를 선택 하 고 확인을 선택 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

새 저장소 계정을 포함 하 여 리소스 그룹 및 관련 리소스를 제거 하려면 [AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) 명령을 사용 합니다.

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure storage 계정을 한 지역에서 다른 지역으로 이동 하 고 원본 리소스를 정리 했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../../site-recovery/azure-to-azure-tutorial-migrate.md)