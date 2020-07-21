---
title: PowerShell-공유 이미지 갤러리에서 스냅숏 또는 VHD에서 이미지 만들기
description: PowerShell을 사용 하 여 공유 이미지 갤러리에서 스냅숏 또는 VHD에서 이미지를 만드는 방법에 대해 알아봅니다.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 315c635ba0864dc1565fd7ba5ccc450223d87ac9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494720"
---
# <a name="create-an-image-from-a-vhd-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>PowerShell을 사용 하 여 공유 이미지 갤러리의 VHD 또는 스냅숏에서 이미지 만들기

공유 이미지 갤러리로 마이그레이션하는 기존 스냅숏 또는 VHD가 있는 경우 VHD 나 스냅숏에서 직접 공유 이미지 갤러리 이미지를 만들 수 있습니다. 새 이미지를 테스트 한 후에는 원본 VHD 또는 스냅숏을 삭제할 수 있습니다. [Azure CLI](image-version-snapshot-cli.md)를 사용 하 여 공유 이미지 갤러리의 VHD 또는 스냅숏에서 이미지를 만들 수도 있습니다.

이미지 갤러리의 이미지에는 다음 예제에서 만들 두 가지 구성 요소가 있습니다.
- 이미지 **정의** 는 이미지 및 사용에 대 한 요구 사항에 대 한 정보를 전달 합니다. 여기에는 이미지가 Windows 또는 Linux 인지, 특수 하거나 일반화 되었는지, 릴리스 정보, 최소 및 최대 메모리 요구 사항이 포함 됩니다. 이미지의 형식 정의입니다. 
- **이미지 버전** 은 공유 이미지 갤러리를 사용할 때 VM을 만드는 데 사용 됩니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. VM을 만들 때 이미지 버전은 VM에 대 한 새 디스크를 만드는 데 사용 됩니다. 이미지 버전은 여러 번 사용할 수 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료 하려면 스냅숏 또는 VHD가 있어야 합니다. 

데이터 디스크를 포함 하려는 경우 데이터 디스크 크기는 1TB 이상일 수 없습니다.

이 문서를 진행할 때 필요한 경우 리소스 이름을 바꿉니다.


## <a name="get-the-snapshot-or-vhd"></a>스냅숏 또는 VHD 가져오기

[AzSnapshot](/powershell/module/az.compute/get-azsnapshot)를 사용 하 여 리소스 그룹에서 사용할 수 있는 스냅숏 목록을 볼 수 있습니다. 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

스냅숏 이름 및 리소스 그룹을 확인 한 후에는를 다시 사용 하 여 `Get-AzSnapshot` 스냅숏 개체를 가져온 후 나중에 사용할 수 있도록 변수에 저장할 수 있습니다. 이 예제에서는 "myResourceGroup" 리소스 그룹에서 *mySnapshot* 라는 스냅숏을 가져와서 *$source*변수에 할당 합니다. 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

스냅숏 대신 VHD를 사용할 수도 있습니다. VHD를 가져오려면 [AzDisk](/powershell/module/az.compute/get-azdisk)을 사용 합니다. 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

그런 다음 VHD를 가져와 `$source` 변수에 할당 합니다.

```azurepowershell-interactive
$source = Get-AzDisk `
   -SnapshotName mySnapshot
   -ResourceGroupName myResourceGroup
```

동일한 cmdlet을 사용 하 여 이미지에 포함 하려는 데이터 디스크를 가져올 수 있습니다. 변수에 할당 한 다음 나중에 이미지 버전을 만들 때 이러한 변수를 사용 합니다.


## <a name="get-the-gallery"></a>갤러리 가져오기

모든 갤러리 및 이미지 정의를 이름으로 나열할 수 있습니다. 결과는 형식 `gallery\image definition\image version` 입니다.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

오른쪽 갤러리를 찾았으면 나중에 사용할 변수를 만듭니다. 이 예제에서는 *Mygallery* 리소스 그룹에서 *mygallery* 라는 갤러리를 가져옵니다.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>이미지 정의 만들기 

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 이미지에 대 한 정보를 관리 하는 데 사용 됩니다. 이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 

이미지 정의를 만들 때에 올바른 정보가 모두 있는지 확인 합니다. 이 예제에서는 스냅숏이 사용 중인 VM에서 스냅숏 또는 VHD를 사용 하 고 있으며 일반화 되지 않았다고 가정 합니다. Windows 또는 [waagent](https://github.com/Azure/WALinuxAgent) 용 Sysprep 또는 Linux 용으로 실행 한 후 VHD 또는 스냅숏이 일반화 된 OS를 사용 하는 경우 `-deprovision` `-deprovision+user` `-OsState` 를로 변경 합니다 `generalized` . 

이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](./windows/shared-image-galleries.md#image-definitions)를 참조하세요.

[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 이미지 정의를 만듭니다. 이 예제에서 이미지 정의 이름은 *Myimagedefinition*이며 특수 한 Windows OS를 위한 것입니다. Linux OS를 사용 하 여 이미지에 대 한 정의를 만들려면를 사용 `-OsType Linux` 합니다. 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

### <a name="purchase-plan-information"></a>구매 계획 정보

경우에 따라 Azure Marketplace 이미지를 기반으로 하는 이미지에서 VM을 만들 때 구매 계획 정보를 전달 해야 합니다. 이러한 경우에는 이미지 정의에 구매 계획 정보를 포함 하는 것이 좋습니다. 이 경우 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조 하세요.


## <a name="create-an-image-version"></a>이미지 버전 만들기

[AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)를 사용 하 여 스냅숏에서 이미지 버전을 만듭니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

OS 디스크 외에도 이미지에 데이터 디스크를 포함 하려면 매개 변수를 추가 하 `-DataDiskImage` 고 데이터 디스크 스냅숏 또는 VHD의 ID로 설정 합니다.

이 예제에서 이미지 버전은 *1.0.0*이며, *미국 중서부* 및 *미국 중남부* 데이터 센터 둘 다에 복제됩니다. 복제를 위한 대상 영역을 선택할 때 *원본* 지역을 복제 대상으로 포함 해야 합니다.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

이미지를 모든 대상 지역에 복제하는 데는 다소 시간이 소요되므로 진행 상태를 추적할 수 있게 작업을 만들었습니다. 작업의 진행률을 보려면 `$job.State`를 입력합니다.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 동일한 스냅숏을 사용 하 여 다른 이미지 버전을 만들기 전에 이미지 버전이 완전히 완료 될 때까지 대기 해야 합니다. 
>
> 이미지 버전을 만들 때를 추가 하 여 [영역 중복 저장소](../storage/common/storage-redundancy.md) 에 이미지 버전을 저장할 수도 있습니다 `-StorageAccountType Standard_ZRS` .
>

## <a name="delete-the-source"></a>원본 삭제

새 이미지 버전이 제대로 작동 하는지 확인 한 후에는 [AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) 또는 [AzDisk](/powershell/module/az.compute/remove-azdisk)를 사용 하 여 이미지의 원본을 삭제할 수 있습니다.


## <a name="next-steps"></a>다음 단계

복제가 완료 되었는지 확인 한 후에는 [특수화 된 이미지](vm-specialized-image-version-powershell.md)에서 VM을 만들 수 있습니다.

구매 계획 정보를 제공 하는 방법에 대 한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조 하세요.
