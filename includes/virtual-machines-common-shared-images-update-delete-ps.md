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
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67182166"
---
## <a name="update-resources"></a>리소스 업데이트

업데이트할 수 있는 내용에는 몇 가지 제한 사항이 있습니다. 다음 항목을 업데이트할 수 있습니다. 

공유 이미지 갤러리:
- 설명

이미지 정의:
- 권장 vCPU
- 권장 메모리
- 설명
- 수명 주기 끝

이미지 버전:
- 지역 복제본 수
- 대상 지역
- 최신에서 제외
- 수명 주기 끝

복제본 영역을 추가하려는 경우 원본 관리 이미지를 삭제하지 마십시오. 이미지 버전을 추가 영역으로 복제하려면 소스 관리 이미지가 필요합니다. 

갤러리 설명을 업데이트하려면 [Update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery)를 사용합니다.

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

이 예제에서는 [업데이트-AzGalleryImageDefinition를](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) 사용하여 이미지 정의의 수명 종료 날짜를 업데이트하는 방법을 보여 주었습니다.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

이 예제에서는 [Update-AzGalleryImageVersion을](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) 사용하여 이 이미지 버전을 *최신* 이미지로 사용하지 않도록 제외하는 방법을 보여 주며 있습니다.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>리소스 정리

리소스를 삭제할 때 중첩된 리소스의 마지막 항목인 이미지 버전으로 시작해야 합니다. 버전이 삭제되면 이미지 정의를 삭제할 수 있습니다. 갤러리 아래에 있는 모든 리소스가 삭제될 때까지 갤러리를 삭제할 수 없습니다.

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

