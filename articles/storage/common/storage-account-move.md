---
title: Azure 저장소 계정을 다른 리전으로 이동 | 마이크로 소프트 문서
description: Azure Storage 계정을 다른 지역으로 이동하는 방법을 보여 주며
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 8ce949ac997ba7ee38cb057752d89f4b4d22388f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838713"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Azure 저장소 계정을 다른 리전으로 이동

저장소 계정을 이동하려면 다른 지역에서 저장소 계정의 복사본을 만듭니다. 그런 다음 AzCopy 또는 선택한 다른 도구를 사용하여 데이터를 해당 계정으로 이동합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> 
> * 템플릿을 내보냅니다.
> * 대상 지역 및 저장소 계정 이름을 추가하여 템플릿을 수정합니다.
> * 템플릿을 배포하여 새 저장소 계정을 만듭니다.
> * 새 저장소 계정을 구성합니다.
> * 데이터를 새 저장소 계정으로 이동합니다.
> * 원본 지역의 리소스를 삭제합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 계정에서 사용하는 서비스 및 기능이 대상 리전에서 지원되도록 합니다.

- 미리 보기 기능의 경우 구독이 대상 지역에 대해 제외되어 있는지 확인합니다.

<a id="prepare" />

## <a name="prepare"></a>준비

리소스 관리자 템플릿을 시작, 내보낸 다음 수정합니다. 

### <a name="export-a-template"></a>템플릿 내보내기

이 템플릿에는 저장소 계정을 설명하는 설정이 포함되어 있습니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 포털을 사용하여 템플릿을 내보내려면 다음을 수행합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

2. **모든 리소스를** 선택한 다음 저장소 계정을 선택합니다.

3. > **설정** > 내보내기 템플릿 을**선택합니다.**

4. **내보내기 템플릿** 블레이드에서 **다운로드를** 선택합니다.

5. 포털에서 다운로드한 .zip 파일을 찾아 해당 파일의 압축을 선택하려는 폴더로 압축을 해제합니다.

   이 zip 파일에는 템플릿을 배포하는 템플릿 및 스크립트를 구성하는 .json 파일이 포함되어 있습니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell을 사용하여 템플릿을 내보내려면 다음을 수행합니다.

1. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지침을 따릅니다.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. ID가 두 개 이상의 구독과 연결된 경우 이동하려는 저장소 계정의 구독으로 활성 구독을 설정합니다.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. 원본 저장소 계정의 템플릿을 내보냅니다. 이러한 명령은 json 템플릿을 현재 디렉터리에 저장합니다.

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

저장소 계정 이름 및 지역을 변경하여 템플릿을 수정합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 포털을 사용하여 템플릿을 배포하려면 다음을 수행합니다.

1. Azure 포털에서 **리소스 만들기를 선택합니다.**

2. **Marketplace 검색**에서 **템플릿 배포**를 입력하고 **ENTER**를 누릅니다.

3. **템플릿 배포**를 선택합니다.

    ![Azure Resource Manager 템플릿 라이브러리](./media/storage-account-move/azure-resource-manager-template-library.png)

4. **만들기**를 선택합니다.

5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.

6. **파일 로드를**선택한 다음 지침에 따라 마지막 섹션에서 다운로드한 **template.json** 파일을 로드합니다.

7. **template.json** 파일에서 저장소 계정 이름의 기본값을 설정하여 대상 저장소 계정의 이름을 지정합니다. 이 예제는 저장소 계정 이름의 기본값을 `mytargetaccount`으로 설정합니다.
    
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
    지역 위치 코드를 얻으려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하십시오.  지역의 코드는 공백이 없는 지역 이름, **미국** = **중부 중앙입니다.**

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell을 사용하여 템플릿을 배포하려면 다음을 수행합니다.

1. **template.json** 파일에서 저장소 계정 이름의 기본값을 설정하여 대상 저장소 계정의 이름을 지정합니다. 이 예제는 저장소 계정 이름의 기본값을 `mytargetaccount`으로 설정합니다.
    
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

2. **template.json** 파일의 **위치** 속성을 대상 영역으로 편집합니다. 이 예제는 대상 `eastus`영역을 로 설정합니다.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) 명령을 실행하여 지역 코드를 가져올 수 있습니다.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>이동

템플릿을 배포하여 대상 지역에 새 저장소 계정을 만듭니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

1. **template.json** 파일을 저장합니다.

2. 속성 값을 입력하거나 선택합니다.

- **구독**: Azure 구독을 선택합니다.

- **리소스 그룹**: **새로 만들기**를 클릭하고 리소스 그룹에 이름을 지정합니다.

- **위치**: Azure 위치를 선택합니다.

3. 위에 **명시된 이용 약관에 동의함을** 클릭한 다음 **구매 선택** 버튼을 클릭합니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. [Get-AzSubscription를](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0)사용하여 대상 공용 IP를 배포하려는 구독 ID를 가져옵니다.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. 다음 명령을 사용하여 템플릿을 배포합니다.

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>새 저장소 계정 구성

일부 기능은 템플릿으로 내보내지 않으므로 새 저장소 계정에 추가해야 합니다. 

다음 표에는 이러한 기능을 새 저장소 계정에 추가하기 위한 지침과 함께 나열되어 있습니다.

| 기능    | 지침    |
|--------|-----------|
| **수명 주기 관리 정책** | [Azure Blob Storage 수명 주기 관리](../blobs/storage-lifecycle-management-concepts.md) |
| **정적 웹 사이트** | [Azure 저장소에서 정적 웹 사이트 호스트](../blobs/storage-blob-static-website-how-to.md) |
| **이벤트 구독** | [Blob Storage 이벤트에 대응](../blobs/storage-blob-event-overview.md) |
| **경고** | [Azure Monitor를 사용하여 활동 로그 경고 생성, 보기 및 관리](../../azure-monitor/platform/alerts-activity-log.md) |
| **CDN(콘텐츠 배달 네트워크)** | [Azure CDN을 사용하여 HTTP를 통한 사용자 지정 도메인으로 Blob 액세스](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> 원본 저장소 계정에 대한 CDN을 설정한 경우 기존 CDN의 원본을 새 계정의 기본 Blob 서비스 끝점(또는 기본 정적 웹 사이트 끝점)으로 변경하기만 하면 됩니다. 

### <a name="move-data-to-the-new-storage-account"></a>데이터를 새 저장소 계정으로 이동

다음은 데이터를 이동하는 몇 가지 방법입니다.

:heavy_check_mark: **Azure 저장소 탐색기**

  사용하기 쉽고 작은 데이터 세트에 적합합니다. 컨테이너 및 파일 공유를 복사한 다음 대상 계정에 붙여넣을 수 있습니다.

  [Azure 저장소 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 참조하십시오.

:heavy_check_mark: **아즈카피**

  이것이 바람직한 방법입니다. 성능에 최적화되었습니다.  더 빠른 한 가지 방법은 데이터가 저장소 서버 간에 직접 복사되므로 AzCopy는 컴퓨터의 네트워크 대역폭을 사용하지 않습니다. 명령줄에서 또는 사용자 지정 스크립트의 일부로 AzCopy를 사용합니다.

  [AzCopy로 시작하기 보기](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

:heavy_check_mark: **Azure 데이터 팩터리** 

  AzCopy의 현재 릴리스에서 지원되지 않는 기능이 필요한 경우에만 이 도구를 사용합니다. 예를 들어 AzCopy의 현재 릴리스에서는 계층적 네임스페이스가 있는 계정 간에 Blob을 복사할 수 없습니다. 또한 AzCopy는 파일 액세스 제어 목록 또는 파일 타임스탬프를 유지하지 않습니다(예: 타임스탬프 작성 및 수정). 

  다음 링크를 참조하십시오.
  - [Azure 데이터 팩터리를 사용하여 Azure Blob 저장소에 데이터를 복사합니다.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2 간에 데이터 복사](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Azure Data Factory를 사용하여 Azure File Storage 간에 데이터 복사](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Azure Data Factory를 사용하여 Azure Table Storage 간 데이터 복사](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>폐기 또는 정리

배포 후 다시 시작하려는 경우 대상 저장소 계정을 삭제하고 이 문서의 [준비](#prepare) 및 [이동](#move) 섹션에 설명된 단계를 반복할 수 있습니다.

변경 내용을 커밋하고 저장소 계정의 이동을 완료하려면 원본 저장소 계정을 삭제합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 포털을 사용하여 저장소 계정을 제거하려면 다음을 수행합니다.

1. Azure 포털에서 왼쪽의 메뉴를 확장하여 서비스 메뉴를 열고 저장소 **계정을** 선택하여 저장소 계정 목록을 표시합니다.

2. 삭제할 대상 저장소 계정을 찾아 목록 의 오른쪽에 있는 **추가** **단추(...**

3. **삭제를**선택하고 확인합니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

새 저장소 계정을 포함하여 리소스 그룹 및 관련 리소스를 제거하려면 [제거-AzStorageAccount](/powershell/module/az.resources/remove-azstorageaccount) 명령을 사용합니다.

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 저장소 계정을 한 리전에서 다른 리전으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 간 리소스 이동 및 재해 복구에 대해 자세히 알아보려면 다음을 참조하십시오.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
