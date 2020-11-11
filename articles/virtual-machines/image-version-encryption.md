---
title: 미리 보기 - 사용자 고유 키로 암호화된 이미지 버전 만들기
description: 고객이 관리 하는 암호화 키를 사용 하 여 공유 이미지 갤러리에서 이미지 버전을 만듭니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: b19dab8dffaa0c9c888e8a9974a43cbb48006fd7
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504325"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>미리 보기: 이미지 암호화를 위해 고객 관리형 키 사용

공유 이미지 갤러리의 이미지는 스냅숏으로 저장 되므로 서버 쪽 암호화를 통해 자동으로 암호화 됩니다. 서버 쪽 암호화는 사용 가능한 가장 강력한 블록 암호화 중 하나인 256 비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용 합니다. 서버 쪽 암호화는 FIPS 140-2 규격 이기도 합니다. 암호화 모듈 기본 Azure managed disks에 대 한 자세한 내용은 [암호화 API: 차세대](/windows/desktop/seccng/cng-portal)를 참조 하세요.

플랫폼 관리 키를 사용 하 여 이미지를 암호화 하거나 고유한 키를 사용할 수 있습니다. 이중 암호화를 위해 함께 사용할 수도 있습니다. 사용자 고유의 키를 사용하여 암호화를 관리하는 경우 이미지의 모든 데이터를 암호화 및 암호 해독하는 데 사용할 고객 관리형 키를 지정할 수 있습니다. 

고객이 관리 하는 키를 통한 서버 쪽 암호화는 Azure Key Vault을 사용 합니다. [Rsa 키](../key-vault/keys/hsm-protected-keys.md) 를 주요 자격 증명 모음으로 가져오거나 Azure Key Vault에서 새 rsa 키를 생성할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 이미지를 복제 하려는 각 지역에 디스크 암호화가 설정 되어 있어야 합니다.

- 고객 관리 키만 사용 하려면 [Azure Portal](./disks-enable-customer-managed-keys-portal.md) 또는 [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-your-azure-key-vault-and-diskencryptionset)을 사용 하 여 서버 쪽 암호화에서 고객이 관리 하는 키를 사용 하는 방법에 대 한 문서를 참조 하세요.

- 플랫폼 관리 및 고객 관리 키를 모두 사용 하려면 (이중 암호화의 경우) [Azure Portal](./disks-enable-double-encryption-at-rest-portal.md) 또는 [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md)을 사용 하 여 미사용에서 이중 암호화를 사용 하도록 설정 하는 방법에 대 한 문서를 참조 하세요.

   > [!IMPORTANT]
   > Azure Portal에 액세스 하려면 링크를 사용 해야 합니다 [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) . 해당 링크를 사용 하지 않는 한, 미사용의 이중 암호화는 현재 공용 Azure Portal에 표시 되지 않습니다.

## <a name="limitations"></a>제한 사항

공유 이미지 갤러리에서 이미지를 암호화 하는 데 고객이 관리 하는 키를 사용 하는 경우 다음과 같은 제한 사항이 적용 됩니다.   

- 암호화 키 집합은 이미지와 동일한 구독에 있어야 합니다.

- 암호화 키 집합은 지역별 리소스 이므로 각 지역에는 다른 암호화 키 집합이 필요 합니다.

- 고객 관리 키를 사용 하는 이미지를 복사 하거나 공유할 수 없습니다. 

- 사용자 고유의 키를 사용 하 여 디스크나 이미지를 암호화 한 후에는 플랫폼 관리 키를 사용 하 여 해당 디스크나 이미지를 암호화할 수 없습니다.


> [!IMPORTANT]
> 고객 관리 키를 통한 암호화는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="powershell"></a>PowerShell

공개 미리 보기의 경우 먼저 기능을 등록 해야 합니다.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

등록을 완료 하는 데 몇 분 정도 걸립니다. `Get-AzProviderFeature`을 사용 하 여 기능 등록의 상태를 확인 합니다.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

`RegistrationState`가 반환 되 면 `Registered` 다음 단계로 이동할 수 있습니다.

공급자 등록을 확인합니다. `Registered`를 반환하는지 확인합니다.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

반환 되지 않으면 `Registered` 다음 코드를 사용 하 여 공급자를 등록 합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

이미지 버전에 대해 설정 된 디스크 암호화를 지정 하려면 매개 변수와 함께  [AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) 를 사용 합니다 `-TargetRegion` . 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>VM 만들기

공유 이미지 갤러리에서 VM (가상 머신)을 만들고, 고객 관리 키를 사용 하 여 디스크를 암호화할 수 있습니다. 구문은 이미지에서 [일반화](vm-generalized-image-version-powershell.md) 된 VM 또는 [특수](vm-specialized-image-version-powershell.md) 한 VM을 만드는 것과 동일 합니다. 확장 매개 변수 집합을 사용 하 여 `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` VM 구성에 추가 합니다.

데이터 디스크의 경우 AzVMDataDisk를 `-DiskEncryptionSetId $setID` 사용할 때 매개 변수 [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk)를 추가 합니다.


## <a name="cli"></a>CLI 

공개 미리 보기의 경우 먼저 기능에 등록 해야 합니다. 등록은 30 분 정도 걸립니다.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

기능 등록의 상태를 확인 합니다.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

이 코드가 반환 되 면 `"state": "Registered"` 다음 단계로 이동할 수 있습니다.

등록을 확인 합니다.

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

등록 되지 않은 경우 다음 명령을 실행 합니다.

```azurecli-interactive
az provider register -n Microsoft.Compute
```


이미지 버전에 대해 설정 된 디스크 암호화를 지정 하려면 매개 변수와 함께 [az image gallery-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) 을 사용 `--target-region-encryption` 합니다. 의 형식은 `--target-region-encryption` OS 및 데이터 디스크를 암호화 하기 위한 쉼표로 구분 된 키 목록입니다. `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`와 비슷한 형식이어야 합니다. 

OS 디스크의 원본이 관리 디스크 또는 VM인 경우 `--managed-image`를 사용하여 이미지 버전의 원본을 지정합니다. 이 예제에서 원본은 OS 디스크 및 LUN 0에 데이터 디스크를 포함 하는 관리 되는 이미지입니다. OS 디스크는 DiskEncryptionSet1을 사용 하 여 암호화 되 고 데이터 디스크는 DiskEncryptionSet2로 암호화 됩니다.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

OS 디스크의 원본이 스냅샷이면 `--os-snapshot`을 사용하여 OS 디스크를 지정합니다. 이미지 버전의 일부로도 포함 되어야 하는 데이터 디스크 스냅숏이 있는 경우 해당 스냅숏을 추가 합니다. `--data-snapshot-luns`을 사용 하 여 LUN을 지정 하 고를 사용 하 여 `--data-snapshots` 스냅숏을 지정 합니다.

이 예제에서 원본은 디스크 스냅샷입니다. LUN 0에는 OS 디스크와 데이터 디스크가 있습니다. OS 디스크는 DiskEncryptionSet1을 사용 하 여 암호화 되 고 데이터 디스크는 DiskEncryptionSet2로 암호화 됩니다.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>VM 만들기

Shared Image Gallery에서 VM을 만들고, 고객 관리형 키를 사용하여 디스크를 암호화할 수 있습니다. 구문은 이미지에서 [일반화](vm-generalized-image-version-cli.md) 된 VM 또는 [특수](vm-specialized-image-version-cli.md) 한 VM을 만드는 것과 동일 합니다. `--os-disk-encryption-set`암호화 집합의 ID를 사용 하 여 매개 변수를 추가 하기만 하면 됩니다. 데이터 디스크의 경우 `--data-disk-encryption-sets` 데이터 디스크에 대 한 디스크 암호화 집합의 공백으로 구분 된 목록을 추가 합니다.


## <a name="portal"></a>포털

포털에서 이미지 버전을 만들 때 **암호화** 탭을 사용 하 여 저장소 암호화 집합을 적용할 수 있습니다.

> [!IMPORTANT]
> 이중 암호화를 사용 하려면 링크를 사용 하 여 [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) Azure Portal에 액세스 해야 합니다. 해당 링크를 사용 하지 않는 한, 미사용의 이중 암호화는 현재 공용 Azure Portal에 표시 되지 않습니다.


1. **이미지 버전 만들기** 페이지에서 **암호화** 탭을 선택 합니다.
2. **암호화 유형** 에서 **고객이 관리 하는 키를 사용 하 여 미사용 암호화** 를 선택 하거나 **플랫폼 관리 및 고객 관리 키를 사용 하는 이중 암호화** 를 선택 합니다. 
3. 이미지에 있는 각 디스크에 대해 **디스크 암호화 집합** 드롭다운 목록에서 암호화 집합을 선택 합니다. 

### <a name="create-the-vm"></a>VM 만들기

이미지 버전에서 VM을 만들고 고객이 관리 하는 키를 사용 하 여 디스크를 암호화할 수 있습니다. 포털에서 VM을 만들 때 **디스크** 탭에서 **고객이 관리 하는 키를 사용 하 여 미사용 암호화** 를 선택 하거나 **암호화 유형에** 대 한 **플랫폼 관리 및 고객 관리 키를 사용 하 여 이중 암호화** 를 선택 합니다. 그런 다음 드롭다운 목록에서 암호화 집합을 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[서버 쪽 디스크 암호화](./windows/disk-encryption.md)에 대해 알아봅니다.

구매 계획 정보를 제공 하는 방법에 대 한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조 하세요.
