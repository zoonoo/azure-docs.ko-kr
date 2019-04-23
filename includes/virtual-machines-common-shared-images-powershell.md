---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/10/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 91889971e1ab8a9ea8341f6bc57735d973ea0e89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60188323"
---
## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.


## <a name="preview-register-the-feature"></a>미리 보기: 기능 등록

공유 이미지 갤러리는 미리 보기 상태이지만 기능을 등록해야 사용할 수 있습니다. 공유 이미지 갤러리 기능을 등록하려면:

```azurepowershell-interactive
Register-AzProviderFeature `
   -FeatureName GalleryPreview `
   -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="get-the-managed-image"></a>관리되는 이미지 가져오기

[Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage)를 사용하여 리소스 그룹에서 사용할 수 있는 이미지 목록을 볼 수 있습니다. 이미지 이름과 해당 이미지가 어떤 리소스 그룹에 들어 있는지 알 수 있으면 `Get-AzImage`를 다시 사용하여 이미지 개체를 가져오고 나중에 사용할 수 있게 변수에 저장할 수 있습니다. 이 예제에서는 "myResourceGroup" 리소스 그룹에서 *myImage*라는 이미지를 가져온 후 변수 *$managedImage*에 할당합니다. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>이미지 갤러리 만들기 

이미지 갤러리는 이미지 공유를 활성화하는 데 사용되는 기본 리소스입니다. 갤러리 이름은 구독 내에서 고유해야 합니다. [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery)를 사용하여 이미지 갤러리를 만듭니다. 다음 예제에서는 *myGalleryRG* 리소스 그룹에 *myGallery*라는 갤러리를 만듭니다.

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

[New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 갤러리 이미지 정의를 만듭니다. 이 예제에서 갤러리 이미지의 이름은 *myGalleryImage*입니다.

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
### <a name="using-publisher-offer-and-sku"></a>게시자, 제품 및 SKU를 사용 하 여 
공유 이미지를 구현 하려는 고객을 위한 **예정 된 릴리스에서**, 개인 정의 사용할 수 있습니다 **-게시자**에 **-제공** 및 **-Sku** 값을 찾을 이미지 정의 지정 하 고 일치 하는 최신 이미지 버전을 사용 하 여 VM을 만들 이미지 정의 합니다. 예를 들어 다음은 3개의 이미지 정의와 해당 값입니다.

|이미지 정의|게시자|제안|SKU|
|---|---|---|---|
|myImage1|myPublisher|myOffer|mySku|
|myImage2|myPublisher|standardOffer|mySku|
|myImage3|테스트|standardOffer|testSku|

이러한 세 가지 정의는 모두 고유한 값 세트를 갖습니다. 3개 값을 모두 공유하지는 않으면서 1개 또는 2개의 값을 공유하는 이미지 버전을 사용할 수 있습니다. **향후 릴리스에서**, 특정 이미지의 최신 버전을 요청 하기 위해 이러한 값을 결합 하는 일을 할 수 있습니다. **현재 릴리스에서 작동 하지**, 하지만 나중에 사용할 수 있습니다. 원본 이미지를 설정 하려면 다음 구문을 사용 하 여를 사용 해야 릴리스되면 *myImage1* 위 테이블에서.

```powershell
$vmConfig = Set-AzVMSourceImage `
   -VM $vmConfig `
   -PublisherName myPublisher `
   -Offer myOffer `
   -Skus mySku 
```

지정 하는 방법을 현재 사용 하 여 게시자, 제품 및 SKU에 대 한 비슷합니다 [Azure Marketplace 이미지](../articles/virtual-machines/windows/cli-ps-findimage.md) Marketplace 이미지의 최신 버전을 가져올 수 있습니다. 이러한 사실을 토대로, 각 이미지 정의에는 이러한 값의 고유한 세트를 지정해야 합니다.  

## <a name="create-an-image-version"></a>이미지 버전 만들기

[New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 관리되는 이미지에서 이미지 버전을 만듭니다. 이 예제에서 이미지 버전은 *1.0.0*이며, *미국 중서부* 및 *미국 중남부* 데이터 센터 둘 다에 복제됩니다.


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

