---
title: 대용량 파일 공유 사용 설정 및 만들기 - Azure Files
description: 이 문서에서는 대용량 파일 공유를 사용하도록 설정하고 만드는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c22b3f3164cbb7c1a7ed150d093f77777c7b1023
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501297"
---
# <a name="enable-and-create-large-file-shares"></a>대용량 파일 공유 사용 설정 및 만들기

스토리지 계정에서 대용량 파일 공유를 사용하도록 설정하면 최대 100TiB까지 파일 공유를 스케일 업할 수 있으며 표준 공유에 대한 IOPS 및 처리량 제한을 늘릴 수 있습니다. 기존 파일 공유에 대한 기존 스토리지 계정에서 스케일링을 사용하도록 설정할 수도 있습니다. 자세한 내용은 [파일 공유 및 파일 스케일링 대상](storage-files-scale-targets.md#azure-files-scale-targets)을 참조하세요. 

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Azure CLI를 사용하려면 [최신 버전을 설치](/cli/azure/install-azure-cli)하세요.
- Azure PowerShell 모듈을 사용하려면 [최신 버전을 설치](/powershell/azure/install-az-ps)하세요.

## <a name="restrictions"></a>제한

현재는 대용량 파일 공유 사용 계정에서 LRS(로컬 중복 스토리지) 또는 ZRS(영역 중복 스토리지)만 사용할 수 있습니다. GZRS(지역 영역 중복 스토리지), GRS(지역 중복 스토리지), RA-GRS(읽기 액세스 지역 중복 스토리지), RA-GZRS(읽기 액세스 지역 영역 중복 스토리지)는 사용할 수 없습니다.

계정에서 대용량 파일 공유를 사용하도록 설정하는 작업은 되돌릴 수 없습니다. 사용하도록 설정한 후에는 계정을 GZRS, GRS, RA-GRS, RA-GZRS로 변환할 수 없습니다.

## <a name="create-a-new-storage-account"></a>새 스토리지 계정 만들기

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal에서 **모든 서비스** 를 선택합니다. 
1. 리소스 목록에 **스토리지 계정** 을 입력합니다. 입력하는 대로 입력 내용에 따라 목록이 필터링됩니다. **Storage 계정** 을 선택합니다.
1. 표시되는 **스토리지 계정** 창에서 **추가** 를 선택합니다.
1. 스토리지 계정을 만드는 데 사용할 구독을 선택합니다.
1. **리소스 그룹** 필드 아래에서 **새로 만들기** 를 선택합니다. 새 리소스 그룹의 이름을 입력합니다.

    ![포털에서 리소스 그룹을 만드는 방법을 보여 주는 스크린샷](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 그런 다음, 스토리지 계정의 이름을 입력합니다. 이름은 Azure에서 고유해야 합니다. 또한 이름의 길이가 3~24자여야 하고, 숫자 및 소문자만 포함할 수 있습니다.
1. 스토리지 계정의 위치를 선택합니다.
1. 복제본을 **로컬 중복 스토리지** 또는 **영역 중복 스토리지** 로 설정합니다.
1. 다음 필드는 기본값으로 유지합니다.

   |필드  |값  |
   |---------|---------|
   |배포 모델     |리소스 관리자         |
   |성능     |표준         |
   |계정 종류     |StorageV2(범용 v2)         |
   |액세스 계층     |핫         |

1. **고급** 을 선택한 다음, **대용량 파일 공유** 오른쪽에 있는 **사용** 옵션 단추를 선택합니다.
1. **검토 + 만들기** 를 선택하여 스토리지 계정 설정을 검토하고 계정을 만듭니다.

    ![Azure Portal의 새 스토리지 계정에서 “사용” 옵션 단추가 있는 스크린샷](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. **만들기** 를 선택합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저, 대용량 파일 공유를 사용하도록 설정할 수 있도록 [최신 버전의 Azure CLI를 설치](/cli/azure/install-azure-cli)합니다.

대용량 파일 공유를 사용하는 스토리지 계정을 만들려면 다음 명령을 사용합니다. `<yourStorageAccountName>`, `<yourResourceGroup>`, `<yourDesiredRegion>`을 사용자 정보로 바꿉니다.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

먼저, 대용량 파일 공유를 사용하도록 설정할 수 있도록 [최신 버전의 PowerShell을 설치](/powershell/azure/install-az-ps)합니다.

대용량 파일 공유를 사용하는 스토리지 계정을 만들려면 다음 명령을 사용합니다. `<yourStorageAccountName>`, `<yourResourceGroup>`, `<yourDesiredRegion>`을 사용자 정보로 바꿉니다.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>기존 계정에서 대용량 파일 공유 사용 설정

기존 계정에서 대용량 파일 공유를 사용하도록 설정할 수도 있습니다. 대용량 파일 공유를 사용하도록 설정하는 경우 GZRS, GRS, RA-GRS, RA-GZRS로 변환할 수 없습니다. 해당 스토리지 계정에서 대용량 파일 공유를 사용하도록 설정하면 되돌릴 수 없습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)을 열고 대용량 파일 공유를 사용하도록 설정할 스토리지 계정으로 이동합니다.
1. 스토리지 계정을 열고 **구성** 을 선택합니다.
1. **대용량 파일 공유** 에서 **사용** 을 선택한 다음 **저장** 을 선택합니다.
1. **개요** 를 선택하고 **새로 고침** 을 선택합니다.

![Azure Portal에서 기존 스토리지 계정에서 사용 옵션 단추를 선택하는 모습](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

이제 스토리지 계정에서 대용량 파일 공유를 사용하도록 설정했습니다. 다음으로, 늘어난 용량과 규모를 활용하려면 [기존 공유의 할당량을 업데이트](#expand-existing-file-shares)해야 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

기존 계정에서 대용량 파일 공유를 사용하도록 설정하려면 다음 명령을 사용합니다. `<yourStorageAccountName>`과 `<yourResourceGroup>`을 사용자 정보로 바꿉니다.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

이제 스토리지 계정에서 대용량 파일 공유를 사용하도록 설정했습니다. 다음으로, 늘어난 용량과 규모를 활용하려면 [기존 공유의 할당량을 업데이트](#expand-existing-file-shares)해야 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

기존 계정에서 대용량 파일 공유를 사용하도록 설정하려면 다음 명령을 사용합니다. `<yourStorageAccountName>`과 `<yourResourceGroup>`을 사용자 정보로 바꿉니다.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

이제 스토리지 계정에서 대용량 파일 공유를 사용하도록 설정했습니다. 다음으로, 늘어난 용량과 규모를 활용하려면 [기존 공유의 할당량을 업데이트](#expand-existing-file-shares)해야 합니다.

---

## <a name="create-a-large-file-share"></a>대용량 파일 공유 만들기

스토리지 계정에서 대용량 파일 공유를 사용하도록 설정한 후 할당량을 더 높게 설정하여 해당 계정에서 파일 공유를 만들 수 있습니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

대용량 파일 공유를 만드는 방법은 표준 파일 공유 만들기와 거의 동일합니다. 주요 차이점은 할당량을 최대 100TiB까지 설정할 수 있다는 것입니다.

1. 스토리지 계정에서 **파일 공유** 를 선택합니다.
1. **+ 파일 공유** 를 선택합니다.
1. 파일 공유의 이름을 입력합니다. 최대 100TiB까지 원하는 할당량 크기를 설정할 수 있습니다. 그런 다음 **생성** 를 선택합니다. 

![이름 및 할당량 상자가 표시된 Azure Portal UI](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

대용량 파일 공유를 만들려면 다음 명령을 사용합니다. `<yourStorageAccountName>`, `<yourStorageAccountKey>`, `<yourFileShareName>`을 사용자 정보로 바꿉니다.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

대용량 파일 공유를 만들려면 다음 명령을 사용합니다. `<YourStorageAccountName>`, `<YourStorageAccountKey>`, `<YourStorageAccountFileShareName>`을 사용자 정보로 바꿉니다.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>기존 파일 공유 확장

스토리지 계정에서 대용량 파일 공유를 사용하도록 설정한 후 해당 계정의 기존 파일 공유에 대한 할당량을 더 높게 확장할 수도 있습니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 스토리지 계정에서 **파일 공유** 를 선택합니다.
1. 파일 공유를 마우스 오른쪽 단추로 클릭한 다음 **할당량** 을 선택합니다.
1. 원하는 새 크기를 입력하고 **확인** 을 선택합니다.

![기존 파일 공유의 할당량이 표시된 Azure Portal UI](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

할당량을 최대 크기로 설정하려면 다음 명령을 사용합니다. `<yourStorageAccountName>`, `<yourStorageAccountKey>`, `<yourFileShareName>`을 사용자 정보로 바꿉니다.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

할당량을 최대 크기로 설정하려면 다음 명령을 사용합니다. `<YourStorageAccountName>`, `<YourStorageAccountKey>`, `<YourStorageAccountFileShareName>`을 사용자 정보로 바꿉니다.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>다음 단계

* [Windows에서 파일 공유 연결 및 탑재](storage-how-to-use-files-windows.md)
* [Linux에서 파일 공유 연결 및 탑재](storage-how-to-use-files-linux.md)
* [macOS에서 파일 공유 연결 및 탑재](storage-how-to-use-files-mac.md)