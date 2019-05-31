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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145797"
---
## <a name="update-resources"></a>리소스 업데이트

몇 가지 제한 사항이에서 업데이트할 수 있습니다. 다음 항목을 업데이트할 수 있습니다. 

공유 이미지 갤러리:
- 설명

이미지 정의:
- 권장 vCPU
- 권장 되는 메모리
- 설명
- 수명 주기 끝

이미지 버전:
- 지역 복제본 수
- 대상 지역
- 최신에서 제외
- 수명 주기 끝

지역 복제본을 추가 하려는 경우에 소스 관리 되는 이미지를 삭제 하지 마세요. 원본 관리 되는 이미지의 이미지 버전 추가 지역에 복제 하기 위해 필요 합니다. 

갤러리에 대 한 설명을 업데이트를 사용 하 여 [업데이트 AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery)합니다.

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

이 예제에 사용 하는 방법을 보여 줍니다 [업데이트 AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) 이미지 정의 대 한 수명 종료 날짜를 업데이트 합니다.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

이 예제에 사용 하는 방법을 보여 줍니다 [업데이트 AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) 로 사용 되 고이 이미지 버전을 제외 하는 *최신* 이미지입니다.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>리소스 정리

리소스를 삭제 하는 경우에 중첩 된 리소스가-이미지 버전의 마지막 항목부터 시작 해야 합니다. 버전 삭제 되 면 이미지 정의 삭제할 수 있습니다. 그 아래에 있는 모든 리소스가 삭제 될 때까지 갤러리를 삭제할 수 없습니다.

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

