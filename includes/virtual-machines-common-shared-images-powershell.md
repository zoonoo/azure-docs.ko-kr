---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bae66078a1bcb1d80f0798b1d501598fa785fb80
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241221"
---
## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.


## <a name="get-the-managed-image"></a>관리되는 이미지 가져오기

[Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage)를 사용하여 리소스 그룹에서 사용할 수 있는 이미지 목록을 볼 수 있습니다. 이미지 이름과 해당 이미지가 어떤 리소스 그룹에 들어 있는지 알 수 있으면 `Get-AzImage`를 다시 사용하여 이미지 개체를 가져오고 나중에 사용할 수 있게 변수에 저장할 수 있습니다. 이 예제에서는 "myResourceGroup" 리소스 그룹에서 *myImage*라는 이미지를 가져온 후 변수 *$managedImage*에 할당합니다. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>이미지 갤러리 만들기 

이미지 갤러리는 이미지 공유를 활성화하는 데 사용되는 기본 리소스입니다. 갤러리 이름에 허용되는 문자는 대문자 또는 소문자, 숫자, 점 및 마침표입니다. 갤러리 이름 대시를 포함할 수 없습니다. 갤러리 이름은 구독 내에서 고유해야 합니다. 

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
   
## <a name="create-an-image-definition"></a>이미지 정의 만들기 

이미지 정의는 이미지에 대 한 논리적 그룹화를 만듭니다. 내에서 만든 이미지 버전에 대 한 정보를 관리 하려면 사용 됩니다. 대문자 또는 소문자, 숫자, 점, 대시 및 마침표의 이미지 정의 이름은 가능 합니다. 이미지 정의에 지정할 수 있는 값에 대 한 자세한 내용은 참조 하세요. [정의 이미지](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)합니다.

사용 하 여 이미지 정의 만듭니다 [새로 만들기-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)합니다. 이 예제에서 갤러리 이미지의 이름은 *myGalleryImage*입니다.

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>이미지 버전 만들기

이미지 버전을 사용 하 여 관리 되는 이미지 만들기 [새로 만들기-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)합니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

이 예제에서 이미지 버전은 *1.0.0*이며, *미국 중서부* 및 *미국 중남부* 데이터 센터 둘 다에 복제됩니다. 복제에 대 한 대상 영역을 선택할 때 기억할는 포함 해야 합니다 *원본* 지역 복제에 대 한 대상으로 합니다.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

이미지를 모든 대상 지역에 복제하는 데는 다소 시간이 소요되므로 진행 상태를 추적할 수 있게 작업을 만들었습니다. 작업의 진행률을 보려면 `$job.State`를 입력합니다.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 이미지 버전을 빌드하고 같은 관리 되는 이미지를 사용 하 여 다른 이미지 버전을 만들려면 전에 복제를 완전히 완료 될 때까지 기다리는 해야 합니다. 
>
> 이미지 버전을 저장할 수도 있습니다 [영역 중복 저장소](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 추가 하 여 `-StorageAccountType Standard_ZRS` 이미지 버전을 만들 때.
>


## <a name="share-the-gallery"></a>갤러리를 공유 합니다.

이미지 갤러리 수준에서 액세스를 공유 하는 것이 좋습니다. 전자 메일 주소를 사용 및 [Get AzADUser](/powershell/module/az.resources/get-azaduser) 사용자에 대 한 개체 ID를 가져오려면 다음 사용 하 여 cmdlet [새로 만들기-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) 갤러리에 액세스를 제공 합니다. 예제에서는 전자 메일, 대체 alinne_montes@contoso.com 이 예제에서는 사용자 고유의 정보로 합니다.

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