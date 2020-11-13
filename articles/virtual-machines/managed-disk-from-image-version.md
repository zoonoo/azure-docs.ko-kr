---
title: 이미지 버전에서 관리 디스크 만들기
description: 공유 이미지 갤러리의 이미지 버전에서 관리 디스크를 만듭니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 1fbdae2480caef5bf1d190124ca3be6c5b97a2f9
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576362"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>이미지 버전에서 관리 디스크 만들기

필요한 경우 공유 이미지 갤러리에 저장 된 이미지 버전에서 이미지 버전의 OS 또는 단일 데이터 디스크를 관리 디스크로 내보낼 수 있습니다.


## <a name="cli"></a>CLI

[Az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list)를 사용 하 여 갤러리의 이미지 버전을 나열 합니다. 이 예제에서는 *Mygallery* 이미지 갤러리에서 *myimagedefinition* 이미지 정의의 일부인 모든 이미지 버전을 찾습니다.

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

변수를 `source` 이미지 버전의 ID로 설정한 다음 [az disk create](//cli/azure/disk#az_disk_create) 를 사용 하 여 관리 디스크를 만듭니다. 

이 예제에서는 이미지 버전의 OS 디스크를 내보내 *Myresourcegroup* 이라는 리소스 그룹에 있는 *myManagedOSDisk* 라는 관리 되는 디스크를 *eastus* 지역에 만듭니다. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



이미지 버전에서 데이터 디스크를 내보내려는 경우를 추가 `--gallery-image-reference-lun` 하 여 내보낼 데이터 디스크의 LUN 위치를 지정 합니다. 

이 예제에서는 이미지 버전의 LUN 0에 있는 데이터 디스크를 내보내 *Myresourcegroup* 이라는 리소스 그룹의 *Mymanageddatadisk* 라는 관리 되는 디스크를 *eastus* 영역에 만듭니다. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

[AzResource](/powershell/module/az.resources/get-azresource)를 사용 하 여 갤러리의 이미지 버전을 나열 합니다. 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

필요한 모든 정보가 있는 경우 [AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) 를 사용 하 여 사용 하려는 원본 이미지 버전을 가져와 변수에 할당할 수 있습니다. 이 예제에서는 `1.0.0` `myImageDefinition` `myGallery` 리소스 그룹의 원본 갤러리에서 정의의 이미지 버전을 가져옵니다 `myResourceGroup` .

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

`source`변수를 이미지 버전의 ID로 설정한 후에는 [AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) 를 사용 하 여 디스크 구성을 만들고 [AzDisk](/powershell/module/az.compute/new-azdisk) 를 사용 하 여 디스크를 만듭니다. 

이 예제에서는 이미지 버전의 OS 디스크를 내보내 *Myresourcegroup* 이라는 리소스 그룹에 있는 *myManagedOSDisk* 라는 관리 되는 디스크를 *eastus* 지역에 만듭니다. 

디스크 구성을 만듭니다.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

디스크를 만듭니다.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

이미지 버전의 데이터 디스크를 내보내려는 경우 디스크 구성에 LUN ID를 추가 하 여 내보낼 데이터 디스크의 LUN 위치를 지정 합니다. 

이 예제에서는 이미지 버전의 LUN 0에 있는 데이터 디스크를 내보내 *Myresourcegroup* 이라는 리소스 그룹의 *Mymanageddatadisk* 라는 관리 되는 디스크를 *eastus* 영역에 만듭니다. 

디스크 구성을 만듭니다.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

디스크를 만듭니다.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>다음 단계

[Azure CLI](image-version-managed-image-cli.md) 또는 [PowerShell](image-version-managed-image-powershell.md)을 사용 하 여 관리 디스크에서 이미지 버전을 만들 수도 있습니다.


