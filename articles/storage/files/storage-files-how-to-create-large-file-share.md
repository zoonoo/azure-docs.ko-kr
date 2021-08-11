---
title: 대용량 파일 공유 사용 설정 및 만들기 - Azure Files
description: 이 문서에서는 대용량 파일 공유를 사용하도록 설정하고 만드는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a53f964020583c41e2400d97ad244bacd33813bc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818263"
---
# <a name="enable-and-create-large-file-shares"></a>대용량 파일 공유 사용 설정 및 만들기
스토리지 계정에서 대용량 파일 공유를 사용하도록 설정하면 Azure 파일 공유가 100TiB까지 확장될 수 있습니다. 대용량 파일 공유를 사용하도록 설정하면 파일 공유의 IOPS 및 처리량 한도도 늘어날 수 있습니다. 기존 파일 공유와 새 파일 공유에 대한 기존 스토리지 계정에서 크기 조정을 사용하도록 설정할 수도 있습니다. 성능 차이에 대한 자세한 내용은 [파일 공유 및 파일 스케일링 대상](storage-files-scale-targets.md#azure-files-scale-targets)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Azure CLI를 사용하려면 [최신 버전을 설치](/cli/azure/install-azure-cli)하세요.
- Azure PowerShell 모듈을 사용하려면 [최신 버전을 설치](/powershell/azure/install-az-ps)하세요.

## <a name="restrictions"></a>제한
현재는 대용량 파일 공유를 사용할 수 있는 스토리지 계정에서만 LRS(로컬 중복 스토리지) 또는 ZRS(영역 중복 스토리지)를 사용할 수 있습니다. GZRS(지역 영역 중복 스토리지), GRS(지역 중복 스토리지), RA-GRS(읽기 액세스 지역 중복 스토리지), RA-GZRS(읽기 액세스 지역 영역 중복 스토리지)는 사용할 수 없습니다.

계정에서 대용량 파일 공유를 사용하도록 설정하는 작업은 되돌릴 수 없습니다. 사용하도록 설정한 후에는 계정을 GZRS, GRS, RA-GRS, RA-GZRS로 변환할 수 없습니다.

## <a name="create-a-new-storage-account"></a>새 스토리지 계정 만들기

# <a name="portal"></a>[포털](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal에서 **모든 서비스** 를 선택합니다. 
1. 리소스 목록에 **스토리지 계정** 을 입력합니다. 입력하는 대로 입력 내용에 따라 목록이 필터링됩니다. **Storage 계정** 을 선택합니다.
1. 표시되는 **스토리지 계정** 블레이드에서 **+ 새로 만들기** 를 선택합니다.
1. 기본 블레이드에서 원하는 대로 선택 항목을 작성합니다.
1. **성능** 이 **표준** 으로 설정되어 있는지 확인합니다.
1. **중복도** 를 **로컬 중복 스토리지** 또는 **영역 중복 스토리지** 로 설정합니다.
1. **고급** 블레이드를 선택하고 **대용량 파일 공유** 오른쪽에 있는 **사용** 옵션 단추를 선택합니다.
1. **검토 + 만들기** 를 선택하여 스토리지 계정 설정을 검토하고 계정을 만듭니다.
1. **만들기** 를 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
대용량 파일 공유를 사용하는 스토리지 계정을 만들려면 다음 명령을 사용합니다. `<yourStorageAccountName>`, `<yourResourceGroup>`, `<yourDesiredRegion>`을 사용자 정보로 바꿉니다.

```powershell
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -Location <yourDesiredRegion> `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
대용량 파일 공유를 사용하는 스토리지 계정을 만들려면 다음 명령을 사용합니다. `<yourStorageAccountName>`, `<yourResourceGroup>`, `<yourDesiredRegion>`을 사용자 정보로 바꿉니다.

```azurecli-interactive
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
az storage account create \
    --name <yourStorageAccountName> \
    -g <yourResourceGroup> \
    -l <yourDesiredRegion> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --enable-large-file-share
```

---

대용량 파일 공유를 사용하도록 설정된 스토리지 계정을 만든 후에는 대용량 및 규모 한도를 활용하는 파일 공유를 만들 수 있습니다. 스토리지 계정과 Azure 파일 공유를 만드는 방법에 대한 자세한 내용은 [Azure 파일 공유 만들기](storage-how-to-create-file-share.md)를 참조하세요.

## <a name="enable-large-files-shares-on-an-existing-account"></a>기존 계정에서 대용량 파일 공유 사용 설정
기존 LRS 또는 ZRS 계정에서 대용량 파일 공유를 사용하도록 설정할 수도 있습니다. GRS, GZRS, RA-GRS 또는 RA GZRS 계정이 있는 경우 계속하기 전에 LRS 계정으로 변환해야 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com)을 열고 대용량 파일 공유를 사용하도록 설정할 스토리지 계정으로 이동합니다.
1. 스토리지 계정을 열고 **파일 공유** 를 선택합니다.
1. **대용량 파일 공유** 에서 **사용** 을 선택한 다음 **저장** 을 선택합니다.
1. **개요** 를 선택하고 **새로 고침** 을 선택합니다.
1. **용량 공유** 를 선택하고 **100TiB** 를 선택한 다음, **저장** 을 선택합니다.

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="100TiB 공유가 강조 표시된 Azure Storage 계정, 파일 공유 블레이드 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
기존 계정에서 대용량 파일 공유를 사용하도록 설정하려면 다음 명령을 사용합니다. `<yourStorageAccountName>`과 `<yourResourceGroup>`을 사용자 정보로 바꿉니다.

```powershell
Set-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
기존 계정에서 대용량 파일 공유를 사용하도록 설정하려면 다음 명령을 사용합니다. `<yourStorageAccountName>`과 `<yourResourceGroup>`을 사용자 정보로 바꿉니다.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

---

이제 스토리지 계정에서 대용량 파일 공유를 사용하도록 설정했습니다. 다음으로, 늘어난 용량과 규모를 활용하려면 [기존 공유의 할당량을 업데이트](#expand-existing-file-shares)해야 합니다. 

> [!Important]  
> 할당량이 변경되지 않는 한 기존 파일 공유는 대용량 파일 공유에 대해 알려진 한도까지 확장되지 않습니다.

## <a name="expand-existing-file-shares"></a>기존 파일 공유 확장
스토리지 계정에서 대용량 파일 공유를 사용하도록 설정한 후에는 해당 스토리지 계정에서 기존 파일 공유를 확장하여 늘어난 용량과 규모를 활용해야 합니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)
1. 스토리지 계정에서 **파일 공유** 를 선택합니다.
1. 파일 공유를 마우스 오른쪽 단추로 클릭한 다음 **할당량** 을 선택합니다.
1. 원하는 새 크기를 입력하고 **확인** 을 선택합니다.

![기존 파일 공유의 할당량이 표시된 Azure Portal UI](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
할당량을 최대 크기로 설정하려면 다음 명령을 사용합니다. `<YourResourceGroupName>`, `<YourStorageAccountName>`, `<YourStorageAccountFileShareName>`을 사용자 정보로 바꿉니다.

```powershell
$resourceGroupName = "<YourResourceGroupName>"
$storageAccountName = "<YourStorageAccountName>"
$shareName="<YourStorageAccountFileShareName>"

# update quota
Set-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 102400
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
할당량을 최대 크기로 설정하려면 다음 명령을 사용합니다. `<yourResourceGroupName>`, `<yourStorageAccountName>`, `<yourFileShareName>`을 사용자 정보로 바꿉니다.

```azurecli-interactive
az storage share-rm update \
    --resource-group <yourResourceGroupName> \
    --storage-account <yourStorageAccountName> \
    --name <yourFileShareName> \
    --quota 102400
```

---

## <a name="next-steps"></a>다음 단계
* [Windows에서 파일 공유 연결 및 탑재](storage-how-to-use-files-windows.md)
* [Linux에서 파일 공유 연결 및 탑재](storage-how-to-use-files-linux.md)
* [macOS에서 파일 공유 연결 및 탑재](storage-how-to-use-files-mac.md)