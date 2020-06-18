---
title: 일시 삭제 사용 - Azure 파일 공유
description: 데이터 복구를 수행하고 실수로 삭제하는 경우를 방지하기 위해 Azure 파일 공유에서 일시 삭제를 사용하도록 설정하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 755ccd266b8a88471bf7dbc0d2deb790f35f3457
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83883016"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Azure 파일 공유에서 일시 삭제 사용

이제 Azure Storage는 이제 애플리케이션 또는 다른 스토리지 계정 사용자에 의해 잘못 삭제될 때 데이터를 보다 쉽게 복구할 수 있도록 파일 공유에 대한 일시 삭제를 제공합니다. 일시 삭제에 대해 자세히 알아보려면 [Azure 파일 공유의 우발적 삭제를 방지하는 방법](storage-files-prevent-file-share-deletion.md)을 참조하세요.

다음 섹션에서는 기존 스토리지 계정에서 Azure 파일 공유에 대해 일시 삭제를 사용하도록 설정하고 사용하는 방법을 보여 줍니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 스토리지 계정으로 이동하고 **파일 서비스**에서 **일시 삭제**를 선택합니다.
1. **파일 공유 일시 삭제**에 대해 **사용**을 선택합니다.
1. **파일 공유 보존 기간(일)** 을 선택하고 원하는 기간을 입력합니다.
1. **저장**을 선택하여 데이터 보존 설정을 확인합니다.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="스토리지 계정 일시 삭제 설정 창 스크린샷 파일 공유 섹션, 사용 토글, 보존 기간 설정 및 저장 강조 표시 그러면 스토리지 계정의 모든 파일 공유에 대해 일시 삭제가 사용하도록 설정됩니다.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

일시 삭제를 사용하도록 설정하려면 파일 클라이언트의 서비스 속성을 업데이트해야 합니다. 다음 예제에서는 스토리지 계정의 모든 파일 공유에 대해 일시 삭제를 사용하도록 설정합니다.

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

일시 삭제가 사용하도록 설정되어 있는지 확인하고 다음 명령을 사용하여 보존 정책을 볼 수 있습니다.

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>일시 삭제된 파일 공유 복원

# <a name="portal"></a>[포털](#tab/azure-portal)

일시 삭제된 파일 공유를 복원하려면

1. 스토리지 계정으로 이동하여 **파일 공유**를 선택합니다.
1. 파일 공유 블레이드에서 **삭제된 공유 표시**를 사용하도록 설정하여 일시 삭제된 모든 공유를 표시합니다.

    현재 **삭제됨** 상태인 모든 공유가 표시됩니다.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="이름 열 옆에 있는 상태 열이 삭제됨으로 설정되면 파일 공유가 일시 삭제된 상태입니다. 지정된 보존 기간 후에 영구적으로 삭제됩니다.":::

1. 공유를 선택하고 **삭제 취소**를 선택하면 공유가 복원됩니다.

    공유가 **활성** 상태로 전환되므로 공유가 복원되었는지 확인할 수 있습니다.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="이름 열 옆에 있는 상태 열이 활성으로 설정되면 파일 공유가 복원된 것입니다.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

일시 삭제된 파일 공유를 복원하려면 다음 명령을 사용합니다.

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>일시 삭제 사용 안 함

일시 삭제 사용을 중지하거나 파일 공유를 영구적으로 삭제하려는 경우 다음 지침을 따르세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 스토리지 계정으로 이동하고 **설정**에서 **일시 삭제**를 선택합니다.
1. **파일 공유**에서 **파일 공유 일시 삭제**에 대해 **사용 안 함**을 선택합니다.
1. **저장**을 선택하여 데이터 보존 설정을 확인합니다.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="일시 삭제를 사용하지 않도록 설정하면 원할 때 스토리지 계정의 모든 파일 공유를 즉시 영구적으로 삭제할 수 있습니다.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 명령을 사용하여 스토리지 계정에서 일시 삭제를 사용하지 않도록 설정할 수 있습니다.

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---