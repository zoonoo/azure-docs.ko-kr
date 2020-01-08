---
title: Azure PowerShell를 사용 하 여 Azure에 vhd 업로드
description: Azure 관리 디스크에 vhd를 업로드 하 고 직접 업로드를 통해 Azure PowerShell를 사용 하 여 여러 지역에 관리 되는 디스크를 복사 하는 방법에 대해 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 8a7e5243428eb88a2757b675c7d66dbfb3c66a30
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459989"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure에 vhd 업로드

이 문서에서는 로컬 컴퓨터에서 Azure 관리 디스크로 vhd를 업로드 하는 방법을 설명 합니다. 이전에는 저장소 계정에 데이터를 준비 하 고 저장소 계정을 관리 하는 추가 관련 프로세스를 수행 해야 했습니다. 이제 저장소 계정을 관리 하지 않아도 되며, vhd를 업로드 하기 위해 데이터를 준비 해야 합니다. 대신, 빈 관리 디스크를 만들고이 디스크에 직접 vhd를 업로드 합니다. 이는 온-프레미스 Vm을 Azure로 업로드 하는 작업을 간소화 하 고, 최대 32 TiB의 vhd를 대량 관리 디스크로 직접 업로드할 수 있게 해줍니다.

Azure에서 IaaS Vm에 대 한 백업 솔루션을 제공 하는 경우 직접 업로드를 사용 하 여 고객 백업을 관리 디스크로 복원 하는 것이 좋습니다. Azure 외부의 컴퓨터에서 VHD를 업로드 하는 경우의 속도는 로컬 대역폭에 따라 달라 집니다. Azure VM을 사용 하는 경우 대역폭은 표준 Hdd와 동일 합니다.

현재 직접 업로드는 표준 HDD, 표준 SSD 및 프리미엄 SSD 관리 디스크에 대해 지원 됩니다. 아직 ultra Ssd에 대해 지원 되지 않습니다.

## <a name="prerequisites"></a>필수 조건

- [AzCopy v10의 최신 버전](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)을 다운로드 합니다.
- [Azure PowerShell 모듈을 설치](/powershell/azure/install-Az-ps)합니다.
- 온-프레미스에서 VHD를 업로드 하려는 경우: [Azure에 대해 준비](prepare-for-upload-vhd-image.md)된 vhd는 로컬에 저장 됩니다.
- 또는 복사 작업을 수행 하려는 경우 Azure에서 관리 되는 디스크입니다.

## <a name="create-an-empty-managed-disk"></a>빈 관리 디스크 만들기

Azure에 vhd를 업로드 하려면이 업로드 프로세스에 대해 구성 된 빈 관리 디스크를 만들어야 합니다. 계정을 만들기 전에 이러한 디스크에 대해 알아야 하는 추가 정보가 있습니다.

이러한 종류의 관리 디스크에는 두 가지 고유한 상태가 있습니다.

- ReadToUpload는 디스크가 업로드를 받을 준비가 되었지만 SAS ( [보안 액세스 서명](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) )가 생성 되지 않았음을 의미 합니다.
- ActiveUpload-디스크가 업로드를 받을 준비가 되었으며 SAS가 생성 되었음을 의미 합니다.

이러한 상태 중 하나에서 관리 디스크는 실제 디스크 유형에 상관 없이 [표준 HDD 가격](https://azure.microsoft.com/pricing/details/managed-disks/)으로 청구 됩니다. 예를 들어 P10는 S10로 청구 됩니다. 이는 디스크를 VM에 연결 하는 데 필요한 관리 디스크에서 `revoke-access`가 호출 될 때까지 적용 됩니다.

업로드할 빈 표준 HDD를 만들기 전에 업로드 하려는 vhd의 파일 크기 (바이트)가 필요 합니다. 예제 코드는 다음을 사용 하 여 직접 수행할 수 있습니다. `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`을 사용 하면 됩니다. 이 값은 **-UploadSizeInBytes** 매개 변수를 지정할 때 사용 됩니다.

이제 로컬 셸에서 **-createoption** 매개 변수의 **업로드** 설정과 [AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) cmdlet의 **-UploadSizeInBytes** 매개 변수를 지정 하 여 업로드할 빈 표준 HDD를 만듭니다. 그런 다음 [AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) 를 호출 하 여 디스크를 만듭니다.

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

프리미엄 SSD 또는 표준 SSD를 업로드 하려면 **Standard_LRS** **Premium_LRS** 또는 **StandardSSD_LRS**으로 바꿉니다. 울트라 SSD은 아직 지원 되지 않습니다.

이제 업로드 프로세스를 위해 구성 된 빈 관리 디스크를 만들었습니다. 디스크에 vhd를 업로드 하려면 업로드할 대상으로 참조할 수 있도록 쓰기 가능한 SAS가 필요 합니다.

비어 있는 관리 디스크의 쓰기 가능한 SAS를 생성 하려면 다음 명령을 사용 합니다.

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Vhd 업로드

이제 빈 관리 디스크에 대 한 SAS가 있으므로이를 사용 하 여 업로드 명령의 대상으로 관리 디스크를 설정할 수 있습니다.

AzCopy v10를 사용 하 여 생성 한 SAS URI를 지정 하 여 로컬 VHD 파일을 관리 되는 디스크에 업로드 합니다.

이 업로드는 동일한 [표준 HDD](disks-types.md#standard-hdd)와 동일한 처리량을 갖습니다. 예를 들어 S4와 동일한 크기의 경우 최대 60 s p s/s의 처리량이 있습니다. 그러나 S70에 해당 하는 크기의 경우 최대 500 m b/s의 처리량이 있습니다.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

업로드 중에 SAS가 만료 되 고 `revoke-access` 아직 호출 하지 않은 경우 새 SAS를 사용 하 여 `grant-access`를 사용 하 여 업로드를 계속 진행할 수 있습니다.

업로드가 완료 되 고 더 이상 디스크에 더 이상 데이터를 쓸 필요가 없으면 SAS를 해지 합니다. SAS를 해지 하면 관리 디스크의 상태가 변경 되 고 해당 디스크를 VM에 연결할 수 있습니다.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>관리 디스크 복사

또한 직접 업로드는 관리 디스크를 복사 하는 프로세스를 간소화 합니다. 동일한 지역 내에 복사 하거나 다른 지역에 복사할 수 있습니다.

다음 스크립트는 사용자를 위해이 작업을 수행 합니다 .이 프로세스는 앞에서 설명한 단계와 비슷하며 기존 디스크로 작업 하기 때문에 몇 가지 차이점이 있습니다.

> [!IMPORTANT]
> Azure에서 관리 디스크의 디스크 크기 (바이트)를 제공 하는 경우 512의 오프셋을 추가 해야 합니다. 이는 Azure에서 디스크 크기를 반환할 때 바닥글이 생략 되기 때문입니다. 이렇게 하지 않으면 복사가 실패 합니다. 다음 스크립트는 이미이를 위해이를 수행 합니다.

`<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourOSTypeHere>` 및 `<yourTargetLocationHere>` (위치 값의 예: uswest2)를 값으로 바꾼 후 관리 디스크를 복사 하기 위해 다음 스크립트를 실행 합니다.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>다음 단계

이제 관리 디스크에 vhd를 성공적으로 업로드 했으므로 VM에 디스크를 연결 하 고 사용을 시작할 수 있습니다.

VM에 데이터 디스크를 연결 하는 방법에 대 한 자세한 내용은 [Azure PowerShell을 사용 하 여 WINDOWS VM에 데이터 디스크 연결](attach-disk-ps.md)주제에서 문서를 참조 하세요. 디스크를 OS 디스크로 사용 하려면 [특수 디스크에서 WINDOWS VM 만들기](create-vm-specialized.md#create-the-new-vm)를 참조 하세요.
