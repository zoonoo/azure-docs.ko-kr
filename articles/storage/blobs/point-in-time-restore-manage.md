---
title: 블록 blob 데이터에 지정 시간 복원 수행
titleSuffix: Azure Storage
description: 지정 시간 복원을 사용 하 여 지정 된 시점에 블록 blob 집합을 이전 상태로 복원 하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2350177373bc99907c437d814d8f01193f18f3fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95895726"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>블록 blob 데이터에 지정 시간 복원 수행

지정 시간 복원을 사용 하 여 하나 이상의 블록 blob 집합을 이전 상태로 복원할 수 있습니다. 이 문서에서는 저장소 계정에 지정 시간 복원을 사용 하도록 설정 하는 방법과 복원 작업을 수행 하는 방법을 설명 합니다.

지정 시간 복원에 대 한 자세한 내용은 [블록 blob에 대 한 지정 시간 복원](point-in-time-restore-overview.md)을 참조 하세요.

> [!CAUTION]
> 지정 시간 복원은 블록 Blob에 대한 작업만 복원하도록 지원합니다. 컨테이너에 대한 작업은 복원할 수 없습니다. 컨테이너 [삭제](/rest/api/storageservices/delete-container) 작업을 호출 하 여 저장소 계정에서 컨테이너를 삭제 하는 경우 해당 컨테이너는 복원 작업을 통해 복원할 수 없습니다. 컨테이너를 삭제 하는 대신, 개별 blob을 복원 하는 것이 좋습니다.

## <a name="enable-and-configure-point-in-time-restore"></a>지정 시간 복원 활성화 및 구성

지정 시간 복원을 사용 하도록 설정 하 고 구성 하기 전에 저장소 계정에 대 한 필수 구성 요소 (일시 삭제, 변경 피드 및 blob 버전 관리)를 사용 하도록 설정 합니다. 이러한 각 기능을 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Blob에 일시 삭제를 사용하도록 설정](./soft-delete-blob-enable.md)
- [변경 피드를 사용하거나 사용하지 않도록 설정](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Blob 버전 관리 설정 및 관리](versioning-enable.md)

> [!IMPORTANT]
> 일시 삭제, 변경 피드 및 blob 버전 관리를 사용 하도록 설정 하면 추가 요금이 발생할 수 있습니다. 자세한 내용은 [blob에 대 한 일시 삭제](soft-delete-blob-overview.md), [Azure Blob Storage의 변경 피드 지원](storage-blob-change-feed.md)및 [Blob 버전 관리](versioning-overview.md)를 참조 하세요.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal를 사용 하 여 지정 시간 복원을 구성 하려면 다음 단계를 수행 합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정** 아래에서 **데이터 보호** 를 선택 합니다.
1. 지정 **시간 복원 켜기를** 선택 합니다. 이 옵션을 선택 하면 blob, 버전 관리 및 변경 피드에 대 한 일시 삭제도 사용 하도록 설정 됩니다.
1. 지정 시간 복원에 대 한 최대 복원 지점 (일)을 설정 합니다. 이 숫자는 blob 일시 삭제에 대해 지정 된 보존 기간 보다 적어도 1 일 미만 이어야 합니다.
1. 변경 내용을 저장합니다.

다음 이미지에서는 7 일 전 복원 지점을 사용 하 여 지정 시간 복원에 대해 구성 된 저장소 계정과 14 일의 blob 일시 삭제에 대 한 보존 기간을 보여 줍니다.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Azure Portal에서 지정 시간 복원을 구성 하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 지정 시간 복원을 구성 하려면 먼저 [Az. Storage](https://www.powershellgallery.com/packages/Az.Storage) 모듈 버전 2.6.0 이상을 설치 합니다. 그런 다음 Enable-AzStorageBlobRestorePolicy 명령을 호출 하 여 저장소 계정에 대 한 지정 시간 복원을 사용 하도록 설정 합니다.

다음 예에서는 일시 삭제를 사용 하도록 설정 하 고 일시 삭제 보존 기간을 설정 하 고, 변경 피드 및 버전 관리를 사용 하도록 설정 하 고, 특정 시점 복원을 사용 하도록 설정 합니다.    예제를 실행하는 경우 꺾쇠 괄호의 값을 고유한 값으로 바꿔야 합니다.

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

---

## <a name="perform-a-restore-operation"></a>복원 작업 수행

복원 작업을 수행 하는 경우에는 복원 지점을 UTC **날짜/시간** 값으로 지정 해야 합니다. 컨테이너 및 blob는 해당 날짜와 시간에 해당 상태로 복원 됩니다. 복원 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

저장소 계정의 모든 컨테이너를 복원 하거나 하나 이상의 컨테이너에서 blob 범위를 복원할 수 있습니다. Blob 범위는 사전순으로로 정의 되며,이는 사전 순서에서 의미가 있습니다. 복원 작업당 최대 10 개의 사전순 범위가 지원 됩니다. 범위의 시작은 포함 이며 범위의 끝은 제외입니다.

시작 범위와 끝 범위에 지정된 컨테이너 패턴은 최소 3자 이상을 포함해야 합니다. 컨테이너 이름을 Blob 이름과 구분하는 데 사용되는 슬래시(/)는 이 최솟값에 포함되지 않습니다.

와일드 카드 문자는 사전순 범위에서 지원 되지 않습니다. 모든 와일드 카드 문자는 표준 문자로 처리 됩니다.

복원 작업에 전달된 범위에서 명시적으로 지정하여 `$root` 및 `$web` 컨테이너에서 Blob을 복원할 수 있습니다. `$root` 및 `$web` 컨테이너는 명시적으로 지정된 경우에만 복원됩니다. 다른 시스템 컨테이너는 복원할 수 없습니다.

블록 blob만 복원 됩니다. 페이지 blob 및 추가 blob은 복원 작업에 포함 되지 않습니다. 추가 blob과 관련 된 제한 사항에 대 한 자세한 내용은 [블록 blob에 대 한 지정 시간 복원](point-in-time-restore-overview.md)을 참조 하세요.

> [!IMPORTANT]
> 복원 작업을 수행 하는 경우 Azure Storage은 작업 기간 동안 복원 되는 범위의 blob에 대 한 데이터 작업을 차단 합니다. 읽기, 쓰기 및 삭제 작업은 기본 위치에서 차단 됩니다. 이러한 이유로 복원 작업이 진행 되는 동안 Azure Portal의 컨테이너 나열 등의 작업이 예상 대로 수행 되지 않을 수 있습니다.
>
> 저장소 계정이 지리적으로 복제 되는 경우 보조 위치에서 읽기 작업은 복원 작업 중에 진행할 수 있습니다.

### <a name="restore-all-containers-in-the-account"></a>계정의 모든 컨테이너 복원

저장소 계정의 모든 컨테이너를 복원 하 여 지정 된 시점에 이전 상태로 되돌릴 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

저장소 계정의 모든 컨테이너와 blob을 Azure Portal 복원 하려면 다음 단계를 수행 합니다.

1. 저장소 계정에 대 한 컨테이너의 목록으로 이동 합니다.
1. 도구 모음에서 **컨테이너 복원**, **모두 복원** 을 차례로 선택 합니다.
1. **모든 컨테이너 복원** 창에서 날짜 및 시간을 제공 하 여 복원 지점을 지정 합니다.
1. 확인란을 선택 하 여 계속 진행할 것인지 확인 합니다.
1. 복원 **을 선택 하** 여 복원 작업을 시작 합니다.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="모든 컨테이너를 지정 된 복원 지점으로 복원 하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 저장소 계정의 모든 컨테이너와 blob을 복원 하려면 **AzStorageBlobRange** 명령을 호출 합니다. 기본적으로 **AzStorageBlobRange** 명령은 비동기적으로 실행 되며 복원 작업의 상태를 확인 하는 데 사용할 수 있는 **PSBlobRestoreStatus** 형식의 개체를 반환 합니다.

다음 예제에서는 저장소 계정의 컨테이너를 현재 순간 12 시간 상태로 비동기적으로 복원 하 고 복원 작업의 속성 중 일부를 확인 합니다.

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

복원 작업을 동기적으로 실행 하려면 명령에 **-waitforcomplete** 매개 변수를 포함 합니다. **-Waitforcomplete** 매개 변수가 있으면 PowerShell에서 작업에 대 한 복원 ID를 포함 하는 메시지를 표시 한 다음 복원 작업이 완료 될 때까지 실행을 차단 합니다. 복원 작업에 필요한 시간은 복원 되는 데이터의 양에 따라 다르며, 대량 복원 작업은 완료 하는 데 최대 한 시간이 걸릴 수 있습니다.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

---

### <a name="restore-ranges-of-block-blobs"></a>블록 blob의 범위 복원

단일 컨테이너 또는 여러 컨테이너에 있는 하나 이상의 사전순으로 blob 범위를 복원 하 여 해당 blob을 지정 된 시점에 이전 상태로 되돌릴 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal를 사용 하 여 하나 이상의 컨테이너에 있는 blob 범위를 복원 하려면 다음 단계를 수행 합니다.

1. 저장소 계정에 대 한 컨테이너의 목록으로 이동 합니다.
1. 복원할 컨테이너를 선택 합니다.
1. 도구 모음에서 **컨테이너 복원**, **선택한 복원** 을 차례로 선택 합니다.
1. **선택한 컨테이너 복원** 창에서 날짜 및 시간을 제공 하 여 복원 지점을 지정 합니다.
1. 복원할 범위를 지정 합니다. 슬래시 (/)를 사용 하 여 blob 접두사에서 컨테이너 이름을 구분할 수 있습니다.
1. 기본적으로 **선택한 컨테이너 복원** 창은 컨테이너의 모든 blob을 포함 하는 범위를 지정 합니다. 전체 컨테이너를 복원 하지 않으려면이 범위를 삭제 합니다. 기본 범위는 다음 이미지에 나와 있습니다.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="사용자 지정 범위를 지정 하기 전에 삭제할 기본 blob 범위를 보여 주는 스크린샷":::

1. 확인란을 선택 하 여 계속 진행할 것인지 확인 합니다.
1. 복원 **을 선택 하** 여 복원 작업을 시작 합니다.

다음 이미지는 범위 집합에 대 한 복원 작업을 보여 줍니다.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="하나 이상의 컨테이너에서 blob 범위를 복원 하는 방법을 보여 주는 스크린샷":::

이미지에 표시 된 복원 작업은 다음 작업을 수행 합니다.

- *Container1* 의 전체 콘텐츠를 복원 합니다.
- *Container2* 에서 *blob5* 를 통해 사전순으로 *blob1.txt* 범위에서 blob을 복원 합니다. 이 범위는 *blob1.txt*, *blob11*, *blob100*, *blob2* 등의 이름으로 blob을 복원 합니다. 범위 끝은 배타적 이므로 이름이 *blob4* 로 시작 하는 blob을 복원 하지만 이름이 *blob5* 로 시작 하는 blob은 복원 하지 않습니다.
- *Container3* 및 *container4* 의 모든 blob을 복원 합니다. 범위 끝은 배타적 이므로이 범위는 *container5* 을 복원 하지 않습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

단일 범위의 blob을 복원 하려면 **AzStorageBlobRange** 명령을 호출 하 고 매개 변수에 대해 사전순으로 컨테이너 및 blob 이름을 지정 `-BlobRestoreRange` 합니다. 예를 들어 *container1* 라는 단일 컨테이너에서 blob을 복원 하려면 *container1* 로 시작 하 고 *container2* 로 끝나는 범위를 지정할 수 있습니다. 시작 및 끝 범위에 명명된 컨테이너가 존재할 필요는 없습니다. 범위의 끝은 배타적 이므로 저장소 계정에 *container2* 이라는 컨테이너가 포함 된 경우에도 *container1* 라는 컨테이너가 복원 됩니다.

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

제거할 컨테이너에서 blob의 하위 집합을 지정 하려면 슬래시 (/)를 사용 하 여 컨테이너 이름을 blob 접두사 패턴과 구분 합니다. 예를 들어 다음 범위는 단일 컨테이너에서 이름이 *d*~*f* 문자로 시작하는 Blob을 선택합니다.

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

그런 다음 **AzStorageBlobRange** 명령에 범위를 제공 합니다. `-TimeToRestore` 매개 변수에 UTC **DateTime** 값을 제공하여 복원 지점을 지정합니다. 다음 예제는 지정된 범위의 Blob을 현재 시점에서 3일 전의 상태로 복원합니다.

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

기본적으로 **AzStorageBlobRange** 명령은 비동기적으로 실행 됩니다. 복원 작업을 비동기적으로 시작 하면 PowerShell에서 작업에 대 한 속성 테이블을 즉시 표시 합니다.  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

여러 블록 Blob 범위를 복원하려면 `-BlobRestoreRange` 매개 변수에 범위 배열을 지정합니다. 다음 예에서는 *container1* 및 *container4* 의 전체 내용을 24 시간 전 상태로 복원 하는 두 범위를 지정 하 고 결과를 변수에 저장 합니다.

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

복원 작업을 동기적으로 실행 하 고 완료 될 때까지 실행을 차단 하려면 명령에 **-waitforcomplete** 매개 변수를 포함 합니다.

---

## <a name="next-steps"></a>다음 단계

- [블록 blob에 대 한 지정 시간 복원](point-in-time-restore-overview.md)
- [일시 삭제](./soft-delete-blob-overview.md)
- [변경 피드](storage-blob-change-feed.md)
- [Blob 버전 관리](versioning-overview.md)