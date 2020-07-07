---
title: 다른 갤러리에서 이미지 복사
description: Azure PowerShell를 사용 하 여 다른 갤러리에서 이미지를 복사 합니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 10cd8514b529f29f68ea3df14cdc208dd8fdd556
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82796929"
---
# <a name="copy-an-image-from-another-gallery"></a>다른 갤러리에서 이미지 복사

조직에 여러 갤러리가 있는 경우 다른 갤러리에 저장 된 이미지에서 이미지를 만들 수 있습니다. 예를 들어 새 이미지를 만들고 테스트 하기 위한 개발 및 테스트 갤러리가 있을 수 있습니다. 프로덕션 환경에서 사용할 준비가 되 면이 예제를 사용 하 여 프로덕션 갤러리에 복사할 수 있습니다. [Azure CLI](image-version-another-gallery-cli.md)를 사용 하 여 다른 갤러리의 이미지에서 이미지를 만들 수도 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료 하려면 기존 원본 갤러리, 이미지 정의 및 이미지 버전이 있어야 합니다. 대상 갤러리도 있어야 합니다. 

원본 이미지 버전은 대상 갤러리가 있는 지역에 복제 되어야 합니다. 

대상 갤러리에 새 이미지 정의와 이미지 버전이 만들어집니다.


이 문서를 진행할 때 필요한 경우 리소스 이름을 바꿉니다.


## <a name="get-the-source-image"></a>원본 이미지 가져오기 

대상 갤러리에서 복사본을 만들 수 있도록 원본 이미지 정의의 정보가 필요 합니다.

[AzResource](/powershell/module/az.resources/get-azresource) cmdlet을 사용 하 여 기존 갤러리, 이미지 정의 및 이미지 버전에 대 한 정보를 나열 합니다.

결과는 형식 `gallery\image definition\image version` 입니다.

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

필요한 정보가 모두 있으면 [AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion)을 사용 하 여 원본 이미지 버전의 ID를 가져올 수 있습니다. 이 예제에서는 `1.0.0` `myImageDefinition` `myGallery` 리소스 그룹의 원본 갤러리에서 정의의 이미지 버전을 가져옵니다 `myResourceGroup` .

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>이미지 정의 만들기 

원본의 이미지 정의와 일치 하는 새 이미지 정의를 만들어야 합니다. [AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition)를 사용 하 여 이미지 정의를 다시 만드는 데 필요한 모든 정보를 볼 수 있습니다.

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


출력은 다음과 같이 표시됩니다.

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

대상 갤러리에서 [AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) cmdlet 및 위의 출력 정보를 사용 하 여 새 이미지 정의를 만듭니다.


이 *예제에서 이미지* 정의의 이름은 *mydestinationgallery*라는 갤러리에 있습니다.


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-the-image-version"></a>이미지 버전 만들기

[AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)를 사용 하 여 이미지 버전을 만듭니다. `--managed-image`대상 갤러리에서 이미지 버전을 만들기 위해 매개 변수에서 원본 이미지의 ID를 전달 해야 합니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

이 예에서는 *Mydestinationgallery* 리소스 그룹 *West US* 의 *mydestinationgallery*로 대상 갤러리의 이름이 지정 됩니다. 이 이미지의 버전은 *1.0.0* *이며 미국* *서 부* 지역에 2 개의 복제본과 2 개의 복제본을 만듭니다. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -Source $sourceImgVer.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -asJob 
```

이미지를 모든 대상 지역에 복제하는 데는 다소 시간이 소요되므로 진행 상태를 추적할 수 있게 작업을 만들었습니다. 작업의 진행률을 보려면 `$job.State`를 입력합니다.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 동일한 관리 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.
>
> 또한 이미지 버전을 만들 때 `-StorageAccountType Premium_LRS`를 추가하여 프리미엄 스토리지에 추가하거나 `-StorageAccountType Standard_ZRS`를 추가하여 [영역 중복 스토리지](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)를 추가하여 이미지를 저장할 수도 있습니다.
>


## <a name="next-steps"></a>다음 단계

[일반화](vm-generalized-image-version-powershell.md) 된 이미지 버전 또는 [특수](vm-specialized-image-version-powershell.md) 이미지 버전에서 VM을 만듭니다.

[Azure 이미지 작성기 (미리 보기)](./linux/image-builder-overview.md) 는 이미지 버전 생성을 자동화 하는 데 도움이 될 수 있으며, [기존 이미지 버전에서 새 이미지 버전](./linux/image-builder-gallery-update-image-version.md)을 업데이트 하 고 만드는 데에도 사용할 수 있습니다. 