---
title: 블록 blob에 대 한 지정 시간 복원 설정 및 관리 (미리 보기)
titleSuffix: Azure Storage
description: 지정 시간 복원 (미리 보기)을 사용 하 여 블록 blob을 이전 시점의 상태로 복원 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cbfc5667fb35b8f807a3a806dda4647af10e9392
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118213"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>블록 blob에 대 한 지정 시간 복원 설정 및 관리 (미리 보기)

지정 시간 복원 (미리 보기)을 사용 하 여 블록 blob을 이전 시점의 상태로 복원할 수 있습니다. 이 문서에서는 PowerShell을 사용 하 여 저장소 계정에 대 한 지정 시간 복원을 사용 하도록 설정 하는 방법을 설명 합니다. 또한 PowerShell을 사용 하 여 복원 작업을 수행 하는 방법을 보여 줍니다.

자세한 내용 및 미리 보기를 등록 하는 방법에 대 한 자세한 내용은 [블록 blob에 대 한 지정 시간 복원 (미리 보기)](point-in-time-restore-overview.md)을 참조 하세요.

> [!CAUTION]
> 지정 시간 복원은 블록 blob에 대 한 작업 복원만 지원 합니다. 컨테이너에 대 한 작업은 복원할 수 없습니다. 지정 시간 복원 미리 보기 중에 컨테이너 [삭제](/rest/api/storageservices/delete-container) 작업을 호출 하 여 저장소 계정에서 컨테이너를 삭제 하는 경우 복원 작업을 사용 하 여 해당 컨테이너를 복원할 수 없습니다. 미리 보기 중에는 컨테이너를 삭제 하는 대신 개별 blob을 복원 하는 것이 좋습니다.

> [!IMPORTANT]
> 특정 시점 복원 미리 보기는 비프로덕션 용도로만 사용 됩니다. 현재 프로덕션 SLA(서비스 수준 계약)는 사용할 수 없습니다.

## <a name="install-the-preview-module"></a>Preview 모듈 설치

PowerShell을 사용 하 여 Azure 지정 시간 복원을 구성 하려면 먼저 Az. Storage PowerShell 모듈의 [1.14.1이-preview](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) 버전을 설치 합니다. 미리 보기 모듈을 설치 하려면 다음 단계를 따르세요.

1. **설정**아래의 **앱 & 기능** 설정을 사용 하 여 Windows에서 이전에 설치 된 Azure PowerShell를 제거 합니다.

1. 최신 버전의 PowerShellGet이 설치되어 있는지 확인합니다. Windows PowerShell 창을 열고 다음 명령을 실행하여 최신 버전을 설치합니다.

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. PowerShellGet을 설치한 후 PowerShell 창을 닫았다가 다시 엽니다.

1. Azure PowerShell의 최신 버전을 설치합니다.

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Az. Storage 미리 보기 모듈을 설치 합니다.

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Azure PowerShell를 설치 하는 방법에 대 한 자세한 내용은 [PowerShellGet을 사용 하 여 Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조 하세요.

## <a name="enable-and-configure-point-in-time-restore"></a>지정 시간 복원 사용 및 구성

지정 시간 복원을 사용 하도록 설정 하 고 구성 하기 전에 해당 필수 구성 요소를 사용 하도록 설정 합니다. 일시 삭제, 변경 피드 및 blob 버전 관리입니다. 이러한 각 기능을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Blob에 대해 일시 삭제 사용](soft-delete-enable.md)
- [변경 피드 사용 및 사용 안 함](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Blob 버전 관리 사용 및 관리](versioning-enable.md)

PowerShell을 사용 하 여 Azure 지정 시간 복원을 구성 하려면 AzStorageBlobRestorePolicy 명령을 호출 합니다. 다음 예에서는 일시 삭제를 사용 하도록 설정 하 고 일시 삭제 보존 기간을 설정 하 고, 변경 피드를 사용 하도록 설정 하 고, 특정 시점 복원을 사용 하도록 설정 합니다. 예제를 실행 하기 전에 Azure Portal 또는 Azure Resource Manager 템플릿을 사용 하 여 blob 버전 관리도 사용 하도록 설정 합니다.

예제를 실행 하는 경우 꺾쇠 괄호의 값을 고유한 값으로 바꾸어야 합니다.

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>복원 작업 수행

복원 작업을 시작 하려면 복원 지점을 UTC **날짜/시간** 값으로 지정 하 여 AzStorageBlobRange 명령을 호출 합니다. 하나 이상의 사전순으로 복원할 blob 범위를 지정 하거나, 범위를 생략 하 여 저장소 계정의 모든 컨테이너에 있는 모든 blob를 복원할 수 있습니다. 복원 작업을 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

복원할 blob 범위를 지정 하는 경우 다음 규칙을 염두에 두어야 합니다.

- 시작 범위와 끝 범위에 지정 된 컨테이너 패턴은 최소 3 자를 포함 해야 합니다. 컨테이너 이름을 blob 이름과 구분 하는 데 사용 되는 슬래시 (/)는이 최소값으로 계산 되지 않습니다.
- 복원 작업당 하나의 범위만 지정할 수 있습니다.
- 와일드카드 문자는 지원되지 않습니다. 표준 문자로 처리 됩니다.
- 및 컨테이너의 blob은 `$root` `$web` 복원 작업에 전달 된 범위에 명시적으로 지정 하 여 복원할 수 있습니다. `$root`및 `$web` 컨테이너는 명시적으로 지정 된 경우에만 복원 됩니다. 다른 시스템 컨테이너는 복원할 수 없습니다.

### <a name="restore-all-containers-in-the-account"></a>계정의 모든 컨테이너 복원

저장소 계정의 모든 컨테이너와 blob을 복원 하려면 AzStorageBlobRange 명령을 호출 하 고 매개 변수를 생략 합니다 `-BlobRestoreRange` . 다음 예에서는 저장소 계정에 있는 컨테이너를 현재 시간 12 시간 이전 상태로 복원 합니다.

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>블록 blob의 단일 범위 복원

Blob 범위를 복원 하려면 AzStorageBlobRange 명령을 호출 하 고 매개 변수에 대 한 사전순의 컨테이너 및 blob 이름을 지정 `-BlobRestoreRange` 합니다. 범위의 시작은 포함 되 고 범위 끝은 제외입니다.

예를 들어 *sample 컨테이너*라는 단일 컨테이너에서 blob을 복원 하려면 *샘플 컨테이너로* 시작 하 고 *container1*로 끝나는 범위를 지정할 수 있습니다. 시작 범위와 끝 범위에서 이름이 지정 된 컨테이너에 대 한 요구 사항은 없습니다. 범위의 끝은 배타적 이므로 저장소 계정에 *container1*라는 컨테이너가 포함 된 경우에도 *sample-container* 라는 컨테이너가 복원 됩니다.

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

제거할 컨테이너에서 blob의 하위 집합을 지정 하려면 슬래시 (/)를 사용 하 여 blob 패턴과 컨테이너 이름을 구분 합니다. 예를 들어 다음 범위는 이름이 *d* 에서 *f*문자로 시작 하는 단일 컨테이너에서 blob을 선택 합니다.

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

그런 다음 AzStorageBlobRange 명령에 범위를 제공 합니다. 매개 변수에 UTC **날짜/시간** 값을 제공 하 여 복원 지점을 지정 `-TimeToRestore` 합니다. 다음 예에서는 지정 된 범위에 있는 blob을 현재 시간에서 3 일 전의 상태로 복원 합니다.

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>블록 blob의 여러 범위 복원

블록 blob의 여러 범위를 복원 하려면 매개 변수의 범위 배열을 지정 `-BlobRestoreRange` 합니다. 다음 예에서는 *container1* 및 *container4*의 전체 콘텐츠를 복원 합니다.

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>다음 단계

- [블록 blob에 대 한 지정 시간 복원 (미리 보기)](point-in-time-restore-overview.md)
- [일시 삭제](soft-delete-overview.md)
- [변경 피드 (미리 보기)](storage-blob-change-feed.md)
- [Blob 버전 관리 (미리 보기)](versioning-overview.md)
