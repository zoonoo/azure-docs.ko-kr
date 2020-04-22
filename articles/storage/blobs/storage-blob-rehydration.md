---
title: 아카이브 계층에서 Blob 데이터에 수화
description: 데이터에 액세스할 수 있도록 아카이브 저장소에서 Blob을 다시 수화합니다.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 82ea4ad23e3207f5641ade196f69595cd1e7b323
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684090"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>아카이브 계층에서 Blob 데이터에 수화

Blob이 아카이브 액세스 계층에 있는 동안 오프라인으로 간주되며 읽거나 수정할 수 없습니다. Blob 메타데이터는 온라인 상태로 유지되므로 Blob 및 해당 속성을 나열할 수 있습니다. Blob 데이터를 읽고 수정하는 것은 핫 또는 쿨과 같은 온라인 계층에서만 사용할 수 있습니다. 아카이브 액세스 계층에 저장된 데이터를 검색하고 액세스하는 두 가지 옵션이 있습니다.

1. [보관된 Blob을 온라인 계층으로 재수화](#rehydrate-an-archived-blob-to-an-online-tier) - [Blob 계층 설정](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) 작업을 사용하여 해당 계층을 변경하여 아카이브 Blob을 뜨겁게 또는 시원하게 재수화합니다.
2. [보관된 Blob을 온라인 계층에 복사](#copy-an-archived-blob-to-an-online-tier) - [Blob 복사](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 작업을 사용하여 아카이브 Blob의 새 복사본을 만듭니다. 다른 Blob 이름과 핫 또는 쿨의 대상 계층을 지정합니다.

 계층에 대한 자세한 내용은 [Azure Blob 저장소: 핫, 쿨 및 아카이브 액세스 계층을](storage-blob-storage-tiers.md)참조하십시오.

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>보관된 Blob을 온라인 계층으로 재수화

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>보관된 Blob을 온라인 계층에 복사

아카이브 Blob에 수분을 다시 공급하지 않으려면 [Blob 복사](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 작업을 수행하도록 선택할 수 있습니다. 새 Blob이 온라인 핫 또는 쿨 계층에서 만들어지는 동안 원본 Blob은 아카이브에서 수정되지 않은 상태로 유지됩니다. Blob 복사 작업에서 선택적 *x-ms-rehydrate 우선 순위 속성을* 표준 또는 높음으로 설정하여 Blob 복사본을 만들 우선 순위를 지정할 수도 있습니다.

아카이브에서 Blob을 복사하는 것은 선택한 재수화 우선 순위에 따라 완료하는 데 몇 시간이 걸릴 수 있습니다. 뒤에서 **Blob 복사** 작업은 아카이브 소스 Blob을 읽고 선택한 대상 계층에서 새 온라인 Blob을 만듭니다. Blob을 나열할 때 새 Blob이 표시될 수 있지만 원본 아카이브 Blob에서 읽기가 완료되고 데이터가 새 온라인 대상 Blob에 기록될 때까지 데이터를 사용할 수 없습니다. 새 Blob은 독립 복사본으로 작동하며 수정하거나 삭제해도 원본 아카이브 Blob에는 영향을 주지 않습니다.

보관 할 부블은 동일한 저장소 계정 내에서 온라인 대상 계층에복사할 수 있습니다. 아카이브 Blob을 다른 아카이브 Blob에 복사하는 것은 지원되지 않습니다. 다음 표는 CopyBlob의 기능을 나타냅니다.

|                                           | **핫 계층 소스**   | **쿨 티어 소스** | **아카이브 계층 소스**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **핫 티어 대상**                  | 지원됨             | 지원됨            | 동일한 계정 내에서 지원됩니다. 보류 중인 재수화               |
| **쿨 티어 대상**                 | 지원됨             | 지원됨            | 동일한 계정 내에서 지원됩니다. 보류 중인 재수화               |
| **아카이브 계층 대상**              | 지원됨             | 지원됨            | 지원되지 않음         |

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

아카이브에서 핫 또는 쿨 계층으로 재수화하는 작업은 읽기 작업 및 데이터 검색시 청구됩니다. 높은 우선 순위를 사용하면 표준 우선 순위에 비해 작업 및 데이터 검색 비용이 더 높습니다. 우선 순위가 높은 리하이드레이션은 청구서에 별도의 광고 항목으로 표시됩니다. 몇 기가바이트의 아카이브 Blob을 반환하는 우선 순위가 높은 요청이 5시간 이상 걸리는 경우 높은 우선 순위 검색 요금이 부과되지 않습니다. 그러나 다른 요청보다 재수화의 우선 순위가 지정되었기 때문에 표준 검색 속도는 여전히 적용됩니다.

아카이브에서 Blob을 핫 또는 쿨 계층으로 복사하는 작업은 읽기 작업 및 데이터 검색시 청구됩니다. 쓰기 작업은 새 Blob 복사본을 만드는 데 청구됩니다. 원본 Blob이 아카이브 계층에서 수정되지 않은 상태로 유지되므로 온라인 Blob에 복사할 때 조기 삭제 수수료가 적용되지 않습니다. 선택한 경우 우선 순위가 높은 검색 요금이 적용됩니다.

보관 계층의 Blob은 최소 180일 동안 저장해야 합니다. 보관된 Blob을 180일 전에 삭제하거나 재수화하면 조기 삭제 수수료가 부과됩니다.

> [!NOTE]
> 블록 Blob 및 데이터 재수화에 대한 가격 책정에 대한 자세한 내용은 [Azure 저장소 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하십시오. 아웃바운드 데이터 전송 요금에 대한 자세한 내용은 [데이터 전송 가격 세부 정보를](https://azure.microsoft.com/pricing/details/data-transfers/)참조하십시오.

## <a name="quickstart-scenarios"></a>빠른 시작 시나리오

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>아카이브 Blob을 온라인 계층으로 다시 수화
# <a name="portal"></a>[포털](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 포털에서 모든 리소스를 검색하고 **선택합니다.**

1. 사용자의 스토리지 계정을 선택합니다.

1. 컨테이너를 선택한 다음 Blob을 선택합니다.

1. **Blob 속성에서** **계층 변경을**선택합니다.

1. **핫** 또는 **쿨** 액세스 계층을 선택합니다. 

1. **표준** 또는 **높음의**수화물 재수화물 우선 순위를 선택합니다.

1. 하단에 **저장을** 선택합니다.

![저장소 계정](media/storage-tiers/blob-access-tier.png)
![계층 변경 확인 다시 수화물 상태 확인](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
다음 PowerShell 스크립트를 사용하여 아카이브 Blob의 Blob 계층을 변경할 수 있습니다. 변수는 `$rgName` 리소스 그룹 이름으로 초기화되어야 합니다. 변수는 `$accountName` 저장소 계정 이름으로 초기화해야 합니다. 변수는 `$containerName` 컨테이너 이름으로 초기화되어야 합니다. 변수는 `$blobName` Blob 이름으로 초기화되어야 합니다. 
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

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>온라인 계층이 있는 새 Blob에 아카이브 Blob 복사
다음 PowerShell 스크립트를 사용하여 아카이브 Blob을 동일한 저장소 계정 내의 새 Blob에 복사할 수 있습니다. 변수는 `$rgName` 리소스 그룹 이름으로 초기화되어야 합니다. 변수는 `$accountName` 저장소 계정 이름으로 초기화해야 합니다. `$srcContainerName` 및 `$destContainerName` 변수는 컨테이너 이름으로 초기화되어야 합니다. `$srcBlobName` 및 `$destBlobName` 변수는 Blob 이름으로 초기화되어야 합니다. 
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

* [Blob 스토리지 계층에 대해 알아보기](storage-blob-storage-tiers.md)
* [지역별 Blob 저장소 및 GPv2 계정에서 핫, 쿨 및 아카이브 가격 확인](https://azure.microsoft.com/pricing/details/storage/)
* [Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)
* [데이터 전송 가격 확인](https://azure.microsoft.com/pricing/details/data-transfers/)
