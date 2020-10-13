---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 6e0612a017650f0c6e4c9f63d9a5fd097b0b92c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89303993"
---
## <a name="update-resources"></a>리소스 업데이트

업데이트할 수 있는 항목에 대 한 몇 가지 제한 사항이 있습니다. 다음 항목을 업데이트할 수 있습니다. 

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

복제본 영역을 추가 하려는 경우 원본 관리 이미지를 삭제 하지 마십시오. 원본 관리 이미지는 이미지 버전을 추가 지역에 복제 하는 데 필요 합니다. 

갤러리에 대 한 설명을 업데이트 하려면 [AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery)를 사용 합니다.

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

이 예제에서는 [AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) 를 사용 하 여 이미지 정의의 수명 종료 날짜를 업데이트 하는 방법을 보여 줍니다.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

이 예제에서는 [AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) 를 사용 하 여이 이미지 버전이 *최신* 이미지로 사용 되지 않도록 제외 하는 방법을 보여 줍니다.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

이 예제에서는 [AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) 를 사용 하 여이 이미지 버전을 *최신* 이미지를 고려 하는 것으로 포함 하는 방법을 보여 줍니다.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

## <a name="clean-up-resources"></a>리소스 정리

리소스를 삭제 하는 경우 중첩 된 리소스의 마지막 항목 (이미지 버전)으로 시작 해야 합니다. 버전을 삭제 한 후에는 이미지 정의를 삭제할 수 있습니다. 갤러리 아래의 모든 리소스가 삭제 될 때까지 갤러리를 삭제할 수 없습니다.

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

