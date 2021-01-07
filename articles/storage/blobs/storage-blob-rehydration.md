---
title: 보관 계층의 Blob 데이터 리하이드레이션
description: Blob 데이터에 액세스할 수 있도록 보관 저장소에서 blob을 리하이드레이션 합니다. 온라인 계층에 보관 된 blob을 복사 합니다.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: f74d4ffdd724039354a311234317dac889cd7cfe
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545946"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>보관 계층의 Blob 데이터 리하이드레이션

Blob이 보관 액세스 계층에 있는 동안에는 오프라인으로 간주되며 읽거나 수정할 수 없습니다. Blob 메타데이터는 온라인 상태로 유지되며 사용할 수 있으므로 Blob 및 해당 속성을 나열할 수 있습니다. Blob 데이터 읽기 및 수정은 핫 또는 쿨과 같은 온라인 계층에서만 사용할 수 있습니다. 보관 액세스 계층에 저장된 데이터를 검색하고 액세스하는 두 가지 옵션이 있습니다.

1. [보관 Blob을 온라인 계층으로 리하이드레이션](#rehydrate-an-archived-blob-to-an-online-tier) - [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업을 사용하여 계층을 변경하여 핫 또는 쿨로 보관 Blob을 리하이드레이션합니다.
2. [보관된 Blob을 온라인 계층으로 복사](#copy-an-archived-blob-to-an-online-tier) - [Blob 복사](/rest/api/storageservices/copy-blob) 작업을 사용하여 보관 Blob의 새 복사본을 만듭니다. 핫 또는 쿨의 다른 Blob 이름 및 대상 계층을 지정합니다.

 액세스 계층에 대한 자세한 내용은 [Azure Blob 스토리지: 핫, 쿨 및 보관 스토리지 계층](storage-blob-storage-tiers.md)을 참조하세요.

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>보관된 Blob을 온라인 계층으로 리하이드레이션

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>보관된 Blob을 온라인 계층으로 복사

보관 Blob을 리하이드레이션하지 않으려면 [Blob 복사](/rest/api/storageservices/copy-blob) 작업 수행을 선택할 수 있습니다. 원본 Blob은 보관 계층에 수정되지 않은 상태로 유지되고 온라인 핫 또는 쿨 계층에서 작업할 새 Blob이 생성됩니다. Blob 복사 작업에서 선택적 *x-ms-rehydrate-priority* 속성을 표준 또는 높음으로 설정하여 만든 Blob 복사본의 우선 순위를 지정할 수도 있습니다.

스토리지에서 Blob을 복사하는 작업은 선택된 리하이드레이션 우선 순위에 따라 완료하는 데 몇 시간 정도 걸릴 수 있습니다. 백그라운드에서 **Blob 복사** 작업은 보관 원본 Blob을 읽어서 선택한 대상 계층에 새 온라인 Blob을 만듭니다. Blob을 나열할 때 새 Blob이 표시될 수 있지만 원본 보관 Blob에서 읽기를 완료하고 새 온라인 대상 Blob에 데이터를 쓸 때까지 데이터를 사용할 수 없습니다. 새 Blob은 독립된 복사본이며 이에 대한 수정 또는 삭제는 원본 보관 Blob에 영향을 주지 않습니다.

> [!IMPORTANT]
> 대상에서 복사가 성공적으로 완료될 때까지 원본 Blob을 삭제하지 마세요. 원본 Blob이 삭제된 경우 대상 Blob은 복사를 완료하지 못할 수 있으며 비어 있게 됩니다. *x-ms-copy-status* 를 확인하여 복사 작업의 상태를 확인할 수 있습니다.

보관 Blob은 동일한 스토리지 계정 내에서 온라인 대상 계층으로만 복사할 수 있습니다. 보관 Blob을 다른 보관 Blob에 복사하는 것은 지원되지 않습니다. 다음 표는 CopyBlob의 기능을 나타냅니다.

|                                           | **핫 계층 원본**   | **쿨 계층 원본** | **보관 계층 원본**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **핫 계층 대상**                  | 지원됨             | 지원됨            | 동일한 계정 내에서 지원됩니다. 보류 중인 리하이드레이션               |
| **쿨 계층 대상**                 | 지원됨             | 지원됨            | 동일한 계정 내에서 지원됩니다. 보류 중인 리하이드레이션               |
| **보관 계층 대상**              | 지원됨             | 지원됨            | 지원되지 않음         |

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

Blob을 보관에서 핫 또는 쿨 계층으로 리하이드레이션하면 읽기 작업 및 데이터 검색으로 요금이 청구됩니다. 높은 우선 순위를 사용하는 경우 표준 우선 순위보다 작업 및 데이터 검색 비용이 더 높습니다. 높은 우선 순위 리하이드레이션은 청구서에 별도의 항목으로 표시됩니다. 몇 기가바이트의 보관 Blob을 반환하는 높은 우선 순위 요청이 5시간 이상 걸리는 경우 높은 우선 순위 검색 요금이 부과되지는 않습니다. 리하이드레이션은 다른 요청보다 우선 순위가 높게 지정되었으므로 표준 검색 요금이 여전히 적용됩니다.

Blob을 보관에서 핫 또는 쿨 계층으로 복사하면 읽기 작업 및 데이터 검색으로 요금이 청구됩니다. 쓰기 작업에는 새 Blob 복사본 만들기에 대한 요금이 부과됩니다. 원본 Blob이 보관 계층에서 수정되지 않은 상태로 유지되기 때문에 온라인 Blob에 복사하는 경우 조기 삭제 요금은 적용되지 않습니다. 선택한 경우 높은 우선 순위 검색 요금은 적용됩니다.

보관 계층의 Blob은 최소 180일 동안 저장해야 합니다. 180일 전에 보관된 Blob을 삭제하거나 리하이드레이션하면 조기 삭제 요금이 발생합니다.

> [!NOTE]
> 블록 Blob 및 데이터 리하이드레이션에 대한 가격 책정에 대한 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요. 아웃바운드 데이터 전송 요금에 대한 자세한 내용은 [데이터 전송 가격 책정 정보](https://azure.microsoft.com/pricing/details/data-transfers/)를 참조하세요.

## <a name="quickstart-scenarios"></a>빠른 시작 시나리오

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>보관 Blob을 온라인 계층으로 리하이드레이션
# <a name="portal"></a>[포털](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 **모든 리소스** 를 검색하여 선택합니다.

1. 사용자의 스토리지 계정을 선택합니다.

1. 컨테이너를 선택한 다음 Blob을 선택합니다.

1. **Blob 속성** 에서 **계층 변경** 을 선택합니다.

1. **핫** 또는 **쿨** 액세스 계층을 선택합니다. 

1. **Standard표준** 또는 **높음** 의 리하이드레이션 우선 순위를 선택합니다.

1. 아래쪽에서 **저장** 을 선택합니다.

![스토리지 계정 계층 변경](media/storage-tiers/blob-access-tier.png)
![리하이드레이션 상태 확인](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
다음 PowerShell 스크립트를 사용하여 보관 Blob의 Blob 계층을 변경할 수 있습니다. `$rgName` 변수는 리소스 그룹 이름으로 초기화해야 합니다. `$accountName` 변수는 스토리지 계정 이름으로 초기화해야 합니다. `$containerName` 변수는 컨테이너 이름으로 초기화해야 합니다. `$blobName` 변수는 Blob 이름으로 초기화해야 합니다. 
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
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>온라인 계층을 사용하여 새 Blob에 보관 Blob 복사
다음 PowerShell 스크립트를 사용하여 보관 Blob을 동일한 스토리지 계정 내의 새 Blob에 복사할 수 있습니다. `$rgName` 변수는 리소스 그룹 이름으로 초기화해야 합니다. `$accountName` 변수는 스토리지 계정 이름으로 초기화해야 합니다. `$srcContainerName` 및 `$destContainerName` 변수를 컨테이너 이름으로 초기화해야 합니다. `$srcBlobName` 및 `$destBlobName` 변수는 Blob 이름으로 초기화해야 합니다. 
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

* [Blob Storage 계층에 대한 자세한 정보](storage-blob-storage-tiers.md)
* [지역별 Blob Storage 및 GPv2 계정에서 핫, 쿨 및 보관 가격 책정 확인](https://azure.microsoft.com/pricing/details/storage/)
* [Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)
* [데이터 전송 가격 확인](https://azure.microsoft.com/pricing/details/data-transfers/)