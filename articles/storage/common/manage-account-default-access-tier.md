---
title: Azure Storage 계정의 기본 액세스 계층 관리
description: GPv2 또는 Blob Storage 계정의 기본 액세스 계층을 변경 하는 방법에 대해 알아봅니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 637f748882b3ac84127c8b71761a06629e1e0957
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653835"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Azure Storage 계정의 기본 액세스 계층 관리

각 Azure Storage 계정에는 핫 또는 쿨의 기본 액세스 계층이 있습니다. 저장소 계정을 만들 때 액세스 계층을 할당 합니다. 기본 액세스 계층은 활성 상태입니다.

저장소 계정에 **액세스 계층** 특성을 설정 하 여 기본 계정 계층을 변경할 수 있습니다. 계정 계층 변경은 명시적 계층 집합이 없는 계정에 저장 된 모든 개체에 적용 됩니다. 계정 계층을 핫에서 쿨로 전환 하면 GPv2 계정의 집합 계층 없이 모든 blob에 대해 쓰기 작업 (1만 당)이 발생 하 고 쿨에서 핫으로 전환 하면 Blob Storage 및 GPv2 계정의 모든 blob에 대 한 읽기 작업 (1만 당) 및 데이터 검색 (GB 당) 요금이 발생 합니다.

계층을 개체 수준에서 설정한 blob의 경우 계정 계층이 적용 되지 않습니다. 보관 계층은 개체 수준에서만 적용할 수 있습니다. 언제 든 지 액세스 계층 간을 전환할 수 있습니다.

저장소 계정을 만든 후에 다음 단계를 수행 하 여 기본 액세스 계층을 변경할 수 있습니다.

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>GPv2 또는 Blob Storage 계정의 기본 계정 액세스 계층 변경

다음 시나리오에서는 Azure Portal 또는 PowerShell을 사용 합니다.

# <a name="portal"></a>[포털](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 **모든 리소스** 를 검색하여 선택합니다.

1. 사용자의 스토리지 계정을 선택합니다.

1. **설정** 에서 **구성** 을 선택 하 여 계정 구성을 확인 하 고 변경 합니다.

1. 요구 사항에 적합 한 액세스 계층을 선택 합니다. **액세스 계층** 을 **쿨** 또는 **핫** 으로 설정 합니다.

1. 위쪽에서 **저장** 을 클릭 합니다.

![Azure Portal에서 기본 계정 계층 변경](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 PowerShell 스크립트를 사용 하 여 계정 계층을 변경할 수 있습니다. `$rgName` 변수는 리소스 그룹 이름으로 초기화해야 합니다. `$accountName` 변수는 스토리지 계정 이름으로 초기화해야 합니다.

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>다음 단계

- [Azure Storage 계정에서 blob 계층을 관리 하는 방법](../blobs/manage-access-tier.md)
- [프리미엄 성능이 앱에 이익이 되는지 확인](../blobs/storage-blob-performance-tiers.md)
- [Azure Storage 메트릭을 활성화하여 현재 Storage 계정의 사용 현황 평가](../blobs/monitor-blob-storage.md)
