---
title: 대용량 파일 공유 활성화 및 생성 - Azure 파일
description: 이 문서에서는 큰 파일 공유를 활성화하고 만드는 방법을 배웁니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c683e9847864de4e3409fb6dbd533497a5ae3cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061007"
---
# <a name="enable-and-create-large-file-shares"></a>대용량 파일 공유 활성화 및 생성

저장소 계정에 대용량 파일 공유를 사용하도록 설정하면 파일 공유를 최대 100TiB까지 확장할 수 있습니다. 기존 파일 공유에 대해 기존 저장소 계정에서 이 크기 조정을 활성화할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Azure CLI를 사용하려는 경우 [최신 버전을 설치합니다.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Azure PowerShell을 사용하려는 경우 [최신 버전을 설치합니다.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="restrictions"></a>제한

지금은 대용량 파일 공유 지원 계정에서 로컬 중복 저장소(LRS) 또는 영역 중복 저장소(ZRS)만 사용할 수 있습니다. 지리 영역 중복 저장소(GZRS), 지리적 중복 저장소(GRS) 또는 읽기 액세스 지리적 중복 저장소(RA-GRS)는 사용할 수 없습니다.
계정에서 대용량 파일 공유를 사용하도록 설정하는 것은 되돌릴 수 없는 프로세스입니다. 활성화하면 계정을 GZRS, GRS 또는 RA-GRS로 변환할 수 없습니다.

## <a name="create-a-new-storage-account"></a>새 스토리지 계정 만들기

### <a name="portal"></a>포털

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. Azure 포털에서 **모든 서비스를**선택합니다. 
1. 리소스 목록에서 **저장소 계정을**입력합니다. 입력하는 대로 입력 내용에 따라 목록이 필터링됩니다. **Storage 계정**을 선택합니다.
1. 표시되는 **저장소 계정** 창에서 **추가**를 선택합니다.
1. 저장소 계정을 만드는 데 사용할 구독을 선택합니다.
1. **리소스 그룹** 필드 아래에서 **새로 만들기**를 선택합니다. 새 리소스 그룹에 대한 이름을 입력합니다.

    ![포털에서 리소스 그룹을 만드는 방법을 보여주는 스크린샷](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 그런 다음, 스토리지 계정의 이름을 입력합니다. 이름은 Azure에서 고유해야 합니다. 또한 이름은 길이가 3~24자여야 하며 숫자와 소문자만 가질 수 있습니다.
1. 저장소 계정의 위치를 선택하고 [대용량 파일 공유에 대해 지원되는 복제 중 하나인지](storage-files-planning.md#regional-availability)확인합니다.
1. 복제를 **로컬 중복 저장소** 또는 영역 중복 **저장소로**설정합니다.
1. 이러한 필드를 기본값으로 둡니다.

   |필드  |값  |
   |---------|---------|
   |배포 모델     |리소스 관리자         |
   |성능     |Standard         |
   |계정 종류     |StorageV2(범용 v2)         |
   |액세스 계층     |핫         |

1. **고급**을 선택한 다음 **큰 파일 공유의**오른쪽에 있는 **사용 옵션** 단추를 선택합니다.
1. **검토 + 만들기**를 선택하여 스토리지 계정 설정을 검토하고 계정을 만듭니다.

    ![Azure 포털의 새 저장소 계정에 "사용" 옵션 버튼이 있는 스크린샷](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. **만들기**를 선택합니다.

### <a name="cli"></a>CLI

먼저 대용량 파일 공유를 사용하도록 설정할 수 있도록 [Azure CLI의 최신 버전을 설치합니다.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

큰 파일 공유를 사용하도록 설정한 저장소 계정을 만들려면 다음 명령을 사용합니다. `<yourStorageAccountName>`을 `<yourResourceGroup>`대체하고 `<yourDesiredRegion>` 정보를 입력합니다.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

먼저 최신 [버전의 PowerShell을 설치하여](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) 큰 파일 공유를 사용하도록 설정할 수 있습니다.

큰 파일 공유를 사용하도록 설정한 저장소 계정을 만들려면 다음 명령을 사용합니다. `<yourStorageAccountName>`을 `<yourResourceGroup>`대체하고 `<yourDesiredRegion>` 정보를 입력합니다.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>기존 계정에서 대용량 파일 공유 사용

기존 계정에서 큰 파일 공유를 사용하도록 설정할 수도 있습니다. 대용량 파일 공유를 사용하도록 설정하면 GZRS, GRS 또는 RA-GRS로 변환할 수 없습니다. 이 저장소 계정에서는 대용량 파일 공유를 사용하도록 설정하는 것은 되돌릴 수 없습니다.

### <a name="portal"></a>포털

1. Azure [포털을](https://portal.azure.com)열고 대용량 파일 공유를 사용하도록 설정하려는 저장소 계정으로 이동합니다.
1. 저장소 계정을 열고 **구성을**선택합니다.
1. **대용량 파일 공유에서** **활성화를** 선택한 다음 **저장을**선택합니다.
1. **개요를** 선택하고 **새로 고침을**선택합니다.

![Azure 포털의 기존 저장소 계정에서 사용 가능한 옵션 단추 선택](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

이제 저장소 계정에 대용량 파일 공유를 사용하도록 설정했습니다. 다음으로 증가된 용량 및 확장 기능을 활용하려면 기존 공유 할당량을 업데이트해야 합니다.

"대용량 파일 공유가 아직 계정에 사용할 수 없음"이라는 오류 메시지가 발생하면 해당 지역이 롤아웃을 완료하는 중일 수 있습니다. 대용량 파일 공유가 긴급해야 하는 경우 지원팀에 문의하십시오.

### <a name="cli"></a>CLI

기존 계정에서 큰 파일 공유를 사용하려면 다음 명령을 사용합니다. 정보를 `<yourStorageAccountName>` `<yourResourceGroup>` 교체하고 정보로 바꿉습니다.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

기존 계정에서 큰 파일 공유를 사용하려면 다음 명령을 사용합니다. 정보를 `<yourStorageAccountName>` `<yourResourceGroup>` 교체하고 정보로 바꿉습니다.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>대용량 파일 공유 만들기

저장소 계정에 큰 파일 공유를 사용하도록 설정한 후 할당량이 높은 해당 계정에서 파일 공유를 만들 수 있습니다. 

### <a name="portal"></a>포털

큰 파일 공유를 만드는 것은 표준 파일 공유를 만드는 것과 거의 동일합니다. 주요 차이점은 할당량을 최대 100TiB까지 설정할 수 있다는 것입니다.

1. 저장소 계정에서 파일 **공유를**선택합니다.
1. +파일 공유 를 **선택합니다.**
1. 파일 공유의 이름을 입력합니다. 원하는 할당량 크기를 최대 100TiB까지 설정할 수도 있습니다. 그런 다음 **을 선택합니다.** 

![이름 및 할당량 상자를 표시하는 Azure 포털 UI](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

큰 파일 공유를 만들려면 다음 명령을 사용합니다. `<yourStorageAccountName>`을 `<yourStorageAccountKey>`대체하고 `<yourFileShareName>` 정보를 입력합니다.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

큰 파일 공유를 만들려면 다음 명령을 사용합니다. `<YourStorageAccountName>`을 `<YourStorageAccountKey>`대체하고 `<YourStorageAccountFileShareName>` 정보를 입력합니다.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>기존 파일 공유 확장

저장소 계정에 대 용량 파일 공유를 사용하도록 설정한 후 해당 계정의 기존 파일 공유를 더 높은 할당량으로 확장할 수도 있습니다. 

### <a name="portal"></a>포털

1. 저장소 계정에서 파일 **공유를**선택합니다.
1. 파일 공유를 마우스 오른쪽 단추로 클릭한 다음 **할당량을**선택합니다.
1. 원하는 새 크기를 입력한 다음 **확인을**선택합니다.

![기존 파일 공유 할당량이 있는 Azure 포털 UI](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

할당량을 최대 크기로 설정하려면 다음 명령을 사용합니다. `<yourStorageAccountName>`을 `<yourStorageAccountKey>`대체하고 `<yourFileShareName>` 정보를 입력합니다.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

할당량을 최대 크기로 설정하려면 다음 명령을 사용합니다. `<YourStorageAccountName>`을 `<YourStorageAccountKey>`대체하고 `<YourStorageAccountFileShareName>` 정보를 입력합니다.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>다음 단계

* [Windows에서 파일 공유 연결 및 마운트](storage-how-to-use-files-windows.md)
* [리눅스에서 파일 공유를 연결하고 마운트](storage-how-to-use-files-linux.md)
* [macOS에서 파일 공유 연결 및 마운트](storage-how-to-use-files-mac.md)
