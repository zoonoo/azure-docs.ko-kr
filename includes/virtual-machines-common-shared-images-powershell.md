---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 10c2b447a3f174afe93f56084827756d24d982cc
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792193"
---
## <a name="create-an-image-gallery"></a>이미지 갤러리 만들기 

이미지 갤러리는 이미지 공유를 활성화하는 데 사용되는 기본 리소스입니다. 갤러리 이름에 허용되는 문자는 대문자 또는 소문자, 숫자, 점 및 마침표입니다. 갤러리 이름에는 대시를 사용할 수 없습니다. 갤러리 이름은 구독 내에서 고유해야 합니다. 

[New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery)를 사용하여 이미지 갤러리를 만듭니다. 다음 예제에서는 *myGalleryRG* 리소스 그룹에 *myGallery*라는 갤러리를 만듭니다.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   

## <a name="share-the-gallery"></a>갤러리 공유

이미지 갤러리 수준에서 액세스를 공유 하는 것이 좋습니다. 전자 메일 주소 및 [AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet을 사용 하 여 사용자의 개체 ID를 가져온 다음 [AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) 를 사용 하 여 갤러리에 대 한 액세스 권한을 제공 합니다. 예제 전자 메일 ( alinne_montes@contoso.com 이 예제에서는)을 사용자의 정보로 바꿉니다.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```

