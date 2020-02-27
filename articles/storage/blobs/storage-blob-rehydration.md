---
title: 보관 계층에서 blob 데이터 리하이드레이션
description: 데이터에 액세스할 수 있도록 보관 저장소에서 blob을 리하이드레이션 합니다.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 0a7012d9daa808933a51ac05862a8a9aa4cfcf77
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614793"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>보관 계층에서 blob 데이터 리하이드레이션

Blob이 보관 액세스 계층에 있는 동안에는 오프 라인으로 간주 되며 읽거나 수정할 수 없습니다. Blob 메타 데이터는 온라인 상태를 유지 하므로 blob 및 해당 속성을 나열할 수 있습니다. Blob 데이터 읽기 및 수정은 핫 또는 쿨와 같은 온라인 계층 에서만 사용할 수 있습니다. 보관 액세스 계층에 저장 된 데이터를 검색 하 고 액세스 하는 두 가지 옵션이 있습니다.

1. [Blob 계층 설정](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) 작업을 사용 하 여 계층을 변경 하 여 보관 blob을 [온라인 계층으로 리하이드레이션](#rehydrate-an-archived-blob-to-an-online-tier) 하 여 보관 blob을 핫 또는 쿨로 리하이드레이션 합니다.
2. [온라인 계층에 보관 된 Blob 복사](#copy-an-archived-blob-to-an-online-tier) - [blob 복사](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 작업을 사용 하 여 보관 blob의 새 복사본을 만듭니다. 핫 또는 쿨의 다른 blob 이름 및 대상 계층을 지정 하십시오.

 계층에 대 한 자세한 내용은 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](storage-blob-storage-tiers.md)을 참조 하세요.

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>온라인 계층에 보관 된 blob 리하이드레이션

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>온라인 계층에 보관 된 blob 복사

보관 blob을 리하이드레이션 하지 않으려는 경우 [Blob 복사](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 작업을 수행 하도록 선택할 수 있습니다. 원본 blob은 작업을 수행할 수 있도록 온라인 핫 또는 쿨 계층에서 새 blob이 생성 되는 동안에는 수정 되지 않은 상태로 유지 됩니다. Blob 복사 작업에서 선택적 리하이드레이션 *-priority* 속성을 Standard 또는 High (미리 보기)로 설정 하 여 Blob 복사본을 만들 우선 순위를 지정할 수도 있습니다.

보관 blob은 동일한 저장소 계정 내에서 온라인 대상 계층 으로만 복사할 수 있습니다. 보관 blob을 다른 보관 blob에 복사 하는 것은 지원 되지 않습니다.

저장소에서 blob을 복사 하는 작업은 선택 된 리하이드레이션 우선 순위에 따라 완료 하는 데 몇 시간이 걸릴 수 있습니다. 백그라운드에서 **Blob 복사** 작업은 보관 원본 blob을 읽어 선택한 대상 계층에 새 온라인 blob을 만듭니다. Blob을 나열할 때 새 blob이 표시 될 수 있지만 원본 보관 blob에서 읽기를 완료 하 고 새 온라인 대상 blob에 데이터를 쓸 때까지 데이터를 사용할 수 없습니다. 새 blob은 독립 된 복사본으로, 수정 또는 삭제는 원본 보관 blob에 영향을 주지 않습니다.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

핫 또는 쿨 계층으로 보관 된 리하이드레이션 blob은 읽기 작업 및 데이터 검색으로 요금이 청구 됩니다. 높은 우선 순위 (미리 보기)를 사용 하는 경우 표준 우선 순위와 비교 하 여 작업 및 데이터 검색 비용이 더 높습니다. 높은 우선 순위 리하이드레이션 청구서에 별도의 품목으로 표시 됩니다. 저장소 blob을 반환 하는 우선 순위가 높은 요청 수가 5 시간을 초과 하는 경우 우선 순위가 높은 검색 속도로 부과 되지 않습니다. 그러나 표준 검색 요금은 리하이드레이션 다른 요청 보다 우선 순위가 지정 된 경우에도 적용 됩니다.

보관에서 핫 또는 쿨 계층으로 blob을 복사 하는 작업은 읽기 작업 및 데이터 검색으로 청구 됩니다. 쓰기 작업에는 새 blob 복사본 만들기에 대 한 요금이 부과 됩니다. 원본 blob이 보관 계층에서 수정 되지 않은 상태로 유지 되기 때문에 온라인 blob에 복사 하는 경우 초기 삭제 요금은 적용 되지 않습니다. 선택한 경우 높은 우선 순위 검색 요금이 적용 됩니다.

보관 계층의 blob은 최소 180 일 동안 저장 해야 합니다. 180 일 전에 보관 된 blob을 삭제 하거나 리하이드레이션 하면 초기 삭제 요금이 발생 합니다.

> [!NOTE]
> 블록 blob 및 데이터 리하이드레이션의 가격 책정에 대 한 자세한 내용은 [가격 책정을 Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/)참조 하세요. 아웃 바운드 데이터 전송 요금에 대 한 자세한 내용은 [데이터 전송 가격 정보](https://azure.microsoft.com/pricing/details/data-transfers/)를 참조 하세요.

## <a name="quickstart-scenarios"></a>빠른 시작 시나리오

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>온라인 계층에 보관 blob 리하이드레이션
# <a name="portal"></a>[포털](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 **모든 리소스**를 검색 하 고 선택 합니다.

1. 사용자의 스토리지 계정을 선택합니다.

1. 컨테이너를 선택 하 고 blob을 선택 합니다.

1. **Blob 속성**에서 **계층 변경**을 선택 합니다.

1. **핫** 또는 **쿨** 액세스 계층을 선택 합니다. 

1. **표준** 또는 **높음**의 리하이드레이션 우선 순위를 선택 합니다.

1. 아래쪽에서 **저장** 을 선택 합니다.

![저장소 계정 계층 변경](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
다음 PowerShell 스크립트를 사용 하 여 보관 blob의 blob 계층을 변경할 수 있습니다. `$rgName` 변수는 리소스 그룹 이름으로 초기화 해야 합니다. `$accountName` 변수는 저장소 계정 이름으로 초기화 해야 합니다. `$containerName` 변수는 컨테이너 이름으로 초기화 해야 합니다. `$blobName` 변수는 blob 이름으로 초기화 해야 합니다. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>온라인 계층을 사용 하 여 새 blob에 보관 blob 복사
다음 PowerShell 스크립트를 사용 하 여 보관 blob을 동일한 저장소 계정 내의 새 blob에 복사할 수 있습니다. `$rgName` 변수는 리소스 그룹 이름으로 초기화 해야 합니다. `$accountName` 변수는 저장소 계정 이름으로 초기화 해야 합니다. `$srcContainerName` 및 `$destContainerName` 변수를 컨테이너 이름으로 초기화 해야 합니다. `$srcBlobName` 및 `$destBlobName` 변수는 blob 이름으로 초기화 해야 합니다. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>다음 단계

* [Blob Storage 계층에 대 한 자세한 정보](storage-blob-storage-tiers.md)
* [지역별 Blob Storage 및 GPv2 계정에서 핫, 쿨 및 보관 가격 책정 확인](https://azure.microsoft.com/pricing/details/storage/)
* [Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)
* [데이터 전송 가격 확인](https://azure.microsoft.com/pricing/details/data-transfers/)
