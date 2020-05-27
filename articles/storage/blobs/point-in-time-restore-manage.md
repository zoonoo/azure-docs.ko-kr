---
title: 블록 Blob에 대한 지정 시간 복원 설정 및 관리(미리 보기)
titleSuffix: Azure Storage
description: 지정 시간 복원(미리 보기)을 사용하여 블록 Blob을 이전 시점의 상태로 복원하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cbfc5667fb35b8f807a3a806dda4647af10e9392
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118213"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>블록 Blob에 대한 지정 시간 복원 설정 및 관리(미리 보기)

지정 시간 복원(미리 보기)을 사용하여 블록 Blob을 이전 시점의 상태로 복원할 수 있습니다. 이 문서에서는 PowerShell을 사용하여 스토리지 계정에 대한 지정 시점 복원을 사용하도록 설정하는 방법을 설명합니다. PowerShell을 사용하여 복원 작업을 수행하는 방법도 보여줍니다.

자세한 내용 및 미리 보기에 등록하는 방법을 알아보려면 [블록 Blob에 대한 지정 시간 복원(미리 보기)](point-in-time-restore-overview.md)을 참조하세요.

> [!CAUTION]
> 지정 시간 복원은 블록 Blob에 대한 작업만 복원하도록 지원합니다. 컨테이너에 대한 작업은 복원할 수 없습니다. 지정 시간 복원 미리 보기 중에 [컨테이너 삭제](/rest/api/storageservices/delete-container) 작업을 호출하여 스토리지 계정에서 컨테이너를 삭제하면 해당 컨테이너는 복원 작업을 통해 복원할 수 없습니다. 미리 보기 중에는, Blob을 복원해야 할 수도 있으면 컨테이너를 삭제하는 대신 개별 Blob을 삭제하는 것이 좋습니다.

> [!IMPORTANT]
> 지정 시간 복원 미리 보기는 비프로덕션 용도로만 사용해야 합니다. 현재 프로덕션 SLA(서비스 수준 계약)는 사용할 수 없습니다.

## <a name="install-the-preview-module"></a>미리 보기 모듈 설치

PowerShell을 사용하여 Azure 지정 시간 복원을 구성하려면 먼저 Az.Storage PowerShell 모듈의 [1.14.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) 버전을 설치합니다. 다음 단계에 따라 미리 보기 모듈을 설치합니다.

1. **설정**에 있는 **앱 및 기능** 설정을 사용하여 이전에 설치한 Azure PowerShell이 있으면 Windows에서 제거합니다.

1. 최신 버전의 PowerShellGet이 설치되어 있는지 확인합니다. Windows PowerShell 창을 열고 다음 명령을 실행하여 최신 버전을 설치합니다.

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. PowerShellGet을 설치한 후 PowerShell 창을 닫았다가 다시 엽니다.

1. Azure PowerShell의 최신 버전을 설치합니다.

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Az.Storage 미리 보기 모듈 설치

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Azure PowerShell을 설치하는 방법에 대한 자세한 내용은 [PowerShellGet을 사용하여 Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

## <a name="enable-and-configure-point-in-time-restore"></a>지정 시간 복원 활성화 및 구성

지정 시간 복원을 사용하도록 설정하고 구성하기 전에 필수 구성 요소(일시 삭제, 변경 피드 및 Blob 버전 관리)를 사용하도록 설정합니다. 이러한 각 기능을 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Blob에 일시 삭제를 사용하도록 설정](soft-delete-enable.md)
- [변경 피드를 사용하거나 사용하지 않도록 설정](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Blob 버전 관리 설정 및 관리](versioning-enable.md)

PowerShell을 사용하여 Azure 지정 시간 복원을 구성하려면 Enable-AzStorageBlobRestorePolicy 명령을 호출합니다. 다음 예제는 일시 삭제를 사용하도록 설정하고, 일시 삭제 보존 기간을 설정하고, 변경 피드를 사용하도록 설정하고, 특정 시점 복원을 사용하도록 설정합니다. 예제를 실행하기 전에 Azure Portal 또는 Azure Resource Manager 템플릿을 사용하여 Blob 버전 관리도 사용하도록 설정합니다.

예제를 실행하는 경우 꺾쇠 괄호의 값을 고유한 값으로 바꿔야 합니다.

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

복원 작업을 시작하려면 복원 지점을 UTC **DateTime** 값으로 지정하는 AzStorageBlobRange 명령을 호출합니다. 복원할 Blob의 범위를 사전순으로 하나 이상 지정하거나 범위를 생략하여 스토리지 계정의 모든 컨테이너에 있는 모든 Blob을 복원할 수 있습니다. 복원 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

복원할 Blob 범위를 지정할 때는 다음 규칙에 유의해야 합니다.

- 시작 범위와 끝 범위에 지정된 컨테이너 패턴은 최소 3자 이상을 포함해야 합니다. 컨테이너 이름을 Blob 이름과 구분하는 데 사용되는 슬래시(/)는 이 최솟값에 포함되지 않습니다.
- 복원 작업당 범위를 하나만 지정할 수 있습니다.
- 와일드카드 문자는 지원되지 않습니다. 표준 문자로 처리됩니다.
- 복원 작업에 전달된 범위에서 명시적으로 지정하여 `$root` 및 `$web` 컨테이너에서 Blob을 복원할 수 있습니다. `$root` 및 `$web` 컨테이너는 명시적으로 지정된 경우에만 복원됩니다. 다른 시스템 컨테이너는 복원할 수 없습니다.

### <a name="restore-all-containers-in-the-account"></a>계정의 모든 컨테이너 복원

스토리지 계정의 모든 Blob과 컨테이너를 복원하려면 `-BlobRestoreRange` 매개 변수를 생략하고 Restore-AzStorageBlobRange 명령을 호출합니다. 다음 예는 스토리지 계정의 컨테이너를 현재 시점 12시간 전의 상태로 복원합니다.

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>단일 블록 Blob 범위 복원

Blob 범위를 복원하려면 Restore-AzStorageBlobRange 명령을 호출하고 `-BlobRestoreRange` 매개 변수에 대한 컨테이너 및 Blob 이름 범위를 사전순으로 지정합니다. 범위의 시작은 포함되고 범위의 끝은 제외됩니다.

예를 들어 *sample-container*라는 단일 컨테이너에 Blob을 복원하려면 *sample-container*로 시작하고 *sample-container1*로 끝나는 범위를 지정하면 됩니다. 시작 및 끝 범위에 명명된 컨테이너가 존재할 필요는 없습니다. 범위의 끝은 제외되기 때문에 스토리지 계정에 *sample-container1*이라는 컨테이너가 포함되어 있어도 *sample-container*라는 컨테이너만 복원됩니다.

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

복원할 컨테이너에서 Blob의 하위 집합을 지정하려면 슬래시(/)를 사용하여 컨테이너 이름을 Blob 패턴과 구분합니다. 예를 들어 다음 범위는 단일 컨테이너에서 이름이 *d*~*f* 문자로 시작하는 Blob을 선택합니다.

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

그런 다음, Restore-AzStorageBlobRange 명령에 범위를 제공합니다. `-TimeToRestore` 매개 변수에 UTC **DateTime** 값을 제공하여 복원 지점을 지정합니다. 다음 예제는 지정된 범위의 Blob을 현재 시점에서 3일 전의 상태로 복원합니다.

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>여러 블록 Blob 범위 복원

여러 블록 Blob 범위를 복원하려면 `-BlobRestoreRange` 매개 변수에 범위 배열을 지정합니다. 다음 예제는 *container1*과 *container4*의 전체 콘텐츠를 복원합니다.

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>다음 단계

- [블록 Blob에 대한 지정 시간 복원(미리 보기)](point-in-time-restore-overview.md)
- [일시 삭제](soft-delete-overview.md)
- [변경 피드(미리 보기)](storage-blob-change-feed.md)
- [Blob 버전 관리(미리 보기)](versioning-overview.md)
