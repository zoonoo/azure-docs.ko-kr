---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurepowershell
ms.openlocfilehash: 833209b2f0216c26cc6df75dd1620cdbccaa6eae
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110721844"
---
## <a name="update-resources"></a>리소스 업데이트

업데이트할 수 있는 항목에는 몇 가지 제한 사항이 있습니다. 다음 항목을 업데이트할 수 있습니다. 

공유 이미지 갤러리:
- Description

이미지 정의:
- 권장 vCPU
- 권장 메모리
- Description
- 수명 주기 끝

이미지 버전:
- 지역 복제본 수
- 대상 지역
- 최신에서 제외
- 수명 주기 끝

복제본 영역을 추가하려는 경우 원본 관리형 이미지를 삭제하지 마세요. 원본 관리형 이미지는 이미지 버전을 추가 지역에 복제하는 데 필요합니다. 

갤러리에 대한 설명을 업데이트하려면 [Update-AzGallery](/powershell/module/az.compute/update-azgallery)를 사용합니다.

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

이 예제에서는 [Update-AzGalleryImageDefinition](/powershell/module/az.compute/update-azgalleryimagedefinition)을 사용하여 이미지 정의의 수명 종료 날짜를 업데이트하는 방법을 보여줍니다.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

이 예제에서는 [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion)을 사용하여 이 이미지 버전을 *최신* 이미지로 사용되지 않도록 제외하는 방법을 보여줍니다.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

이 예제에서는 [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion)을 사용하여 이 이미지 버전을 *최신* 이미지로 고려하는 방법을 보여줍니다.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

## <a name="clean-up-resources"></a>리소스 정리

리소스를 삭제하는 경우 중첩된 리소스의 마지막 항목(이미지 버전)으로 시작해야 합니다. 버전이 삭제되면 이미지 정의를 삭제할 수 있습니다. 갤러리 아래에 있는 모든 리소스가 삭제될 때까지 갤러리를 삭제할 수 없습니다.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```
