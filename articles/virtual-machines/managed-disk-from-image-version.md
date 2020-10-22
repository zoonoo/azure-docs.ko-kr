---
title: 이미지 버전에서 관리 디스크 만들기
description: 공유 이미지 갤러리의 이미지 버전에서 관리 디스크를 만듭니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 35edcfb4bdb0715245f4a3190fb22638b1162429
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370987"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>이미지 버전에서 관리 디스크 만들기

필요한 경우 공유 이미지 갤러리에 저장 된 이미지 버전에서 관리 디스크를 만들 수 있습니다.


## <a name="cli"></a>CLI

변수를 `source` 이미지 버전의 ID로 설정한 다음 [az disk create](/cli/azure/disk#az_disk_create) 를 사용 하 여 관리 디스크를 만듭니다. 


[Az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list)를 사용 하 여 목록 이미지 버전을 볼 수 있습니다. 이 예제에서는 *Mygallery* 이미지 갤러리에서 *myimagedefinition* 이미지 정의의 일부인 모든 이미지 버전을 찾습니다.

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


이 예제에서는 *Myresourcegroup*이라는 리소스 그룹에서 *mymanageddisk*라는 관리 되는 디스크를 *e미국* 지역에 만듭니다. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

디스크가 데이터 디스크인 경우를 추가 `--gallery-image-reference-lun` 하 여 LUN을 지정 합니다.

## <a name="powershell"></a>PowerShell

[AzResource](/powershell/module/az.resources/get-azresource)를 사용 하 여 모든 이미지 버전을 나열할 수 있습니다. 

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

디스크 정보에 대 한 몇 가지 변수를 설정 합니다.

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

원본 이미지 버전 ID를 사용 하 여 디스크 구성 및 디스크를 만듭니다. 의 경우에는 `-GalleryImageReference` 원본이 데이터 디스크인 경우에만 LUN이 필요 합니다.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

디스크를 만듭니다.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>다음 단계

[Azure CLI](image-version-managed-image-cli.md) 또는 [PowerShell](image-version-managed-image-powershell.md)을 사용 하 여 관리 디스크에서 이미지 버전을 만들 수도 있습니다.


