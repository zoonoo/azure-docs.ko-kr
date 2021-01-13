---
title: Azure Storage 계정에서 blob의 액세스 계층 관리
description: GPv2 또는 Blob Storage 계정에서 blob의 계층을 변경 하는 방법에 대해 알아봅니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 183593022c934eaf52ffe18649c23e8deced34d8
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98166520"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>Azure Storage 계정에서 blob의 액세스 계층 관리

각 blob에는 핫, 쿨 또는 archive의 기본 액세스 계층이 있습니다. Blob는 만들어진 Azure Storage 계정의 기본 액세스 계층을 사용 합니다. Blob Storage 및 GPv2 계정은 계정 수준에서 **액세스 계층** 특성을 노출 합니다. 이 특성은 개체 수준에서 명시적으로 설정 되지 않은 모든 blob에 대 한 기본 액세스 계층을 지정 합니다. 개체 수준에서 계층이 설정 된 개체의 경우 계정 계층이 적용 되지 않습니다. 보관 계층은 개체 수준 에서만 적용할 수 있습니다. 다음 단계를 수행 하 여 언제 든 지 액세스 계층 간을 전환할 수 있습니다.

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>GPv2 또는 Blob Storage 계정에서 blob의 계층 변경

다음 시나리오에서는 Azure Portal 또는 PowerShell을 사용 합니다.

# <a name="portal"></a>[포털](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 **모든 리소스** 를 검색하여 선택합니다.

1. 사용자의 스토리지 계정을 선택합니다.

1. 컨테이너를 선택한 다음 Blob을 선택합니다.

1. **Blob 속성** 에서 **계층 변경** 을 선택합니다.

1. **핫**, **쿨** 또는 **보관** 액세스 계층을 선택 합니다. Blob이 현재 보관 된 상태이 고 온라인 계층으로 리하이드레이션 하는 경우 **표준** 또는 **높음** 의 리하이드레이션 우선 순위를 선택할 수도 있습니다.

1. 아래쪽에서 **저장** 을 선택합니다.

![Azure Portal에서 blob 계층 변경](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 PowerShell 스크립트를 사용 하 여 blob 계층을 변경할 수 있습니다. `$rgName` 변수는 리소스 그룹 이름으로 초기화해야 합니다. `$accountName` 변수는 스토리지 계정 이름으로 초기화해야 합니다. `$containerName` 변수는 컨테이너 이름으로 초기화해야 합니다. `$blobName` 변수는 Blob 이름으로 초기화해야 합니다.

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>다음 단계

- [Azure Storage 계정의 기본 계정 액세스 계층을 관리 하는 방법](../common/manage-account-default-access-tier.md)
- [보관 계층에서 blob 데이터 리하이드레이션에 대 한 자세한 정보](storage-blob-rehydration.md)
- [지역별 Blob Storage 및 GPv2 계정에서 핫, 쿨 및 보관 가격 책정 확인](https://azure.microsoft.com/pricing/details/storage/)
