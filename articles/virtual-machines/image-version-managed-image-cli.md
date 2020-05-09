---
title: Azure CLI를 사용 하 여 관리 되는 이미지에서 이미지 버전으로 마이그레이션
description: Azure CLI를 사용 하 여 관리 되는 이미지에서 공유 이미지 갤러리의 이미지 버전으로 마이그레이션하는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 6f49ece874ea52227e6531193fc53b3bea525702
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796604"
---
# <a name="migrate-from-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Azure CLI를 사용 하 여 관리 되는 이미지에서 이미지 버전으로 마이그레이션
공유 이미지 갤러리로 마이그레이션할 기존 관리 이미지가 있는 경우 관리 되는 이미지에서 직접 공유 이미지 갤러리 이미지를 만들 수 있습니다. 새 이미지를 테스트 한 후에는 원본 관리 이미지를 삭제할 수 있습니다. [PowerShell](image-version-managed-image-powershell.md)을 사용 하 여 관리 되는 이미지에서 공유 이미지 갤러리로 마이그레이션할 수도 있습니다.

이미지 갤러리의 이미지에는 다음 예제에서 만들 두 가지 구성 요소가 있습니다.
- 이미지 **정의** 는 이미지 및 사용에 대 한 요구 사항에 대 한 정보를 전달 합니다. 여기에는 이미지가 Windows 또는 Linux 인지, 특수 하거나 일반화 되었는지, 릴리스 정보, 최소 및 최대 메모리 요구 사항이 포함 됩니다. 이미지의 형식 정의입니다. 
- **이미지 버전** 은 공유 이미지 갤러리를 사용할 때 VM을 만드는 데 사용 됩니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. VM을 만들 때 이미지 버전은 VM에 대 한 새 디스크를 만드는 데 사용 됩니다. 이미지 버전은 여러 번 사용할 수 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료 하려면 기존 [공유 이미지 갤러리가](shared-images-cli.md)있어야 합니다. 

이 문서의 예제를 완료하려면 일반화된 VM의 기존 관리 이미지가 있어야 합니다. 자세한 내용은 [관리 되는 이미지 캡처](./linux/capture-image.md)를 참조 하세요. 관리 되는 이미지에 데이터 디스크가 포함 되어 있는 경우 데이터 디스크 크기는 1TB를 넘을 수 없습니다.

이 문서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.



## <a name="create-an-image-definition"></a>이미지 정의 만들기

관리 이미지는 항상 일반화 된 이미지 이므로 일반화 된 이미지에 대해를 사용 하 `--os-state generalized` 여 이미지 정의를 만듭니다.

이미지 정의 이름은 대문자, 숫자, 점, 대시 및 마침표로 구성 될 수 있습니다. 

이미지 정의에 대해 지정할 수 있는 값에 대 한 자세한 내용은 [이미지 정의](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)를 참조 하세요.

[Az sig image definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)를 사용 하 여 갤러리에서 이미지 정의를 만듭니다.

이 예제에서 이미지 정의 이름은 *Myimagedefinition*이며 [일반화](./linux/shared-image-galleries.md#generalized-and-specialized-images) 된 Linux OS 이미지를 위한 것입니다. Windows OS를 사용 하 여 이미지에 대 한 정의를 `--os-type Windows`만들려면를 사용 합니다. 

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state generalized
```


## <a name="create-the-image-version"></a>이미지 버전 만들기

[Az image gallery 만들기-이미지-버전](/cli/azure/sig/image-version#az-sig-image-version-create)을 사용 하 여 버전을 만듭니다. 이미지 버전을 만드는 기준선으로 사용할 관리되는 이미지의 ID를 전달해야 합니다. [Az image list](/cli/azure/image?view#az-image-list) 를 사용 하 여 이미지의 id를 가져올 수 있습니다. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*. *MinorVersion*. *패치*.

이 예제에서 이미지의 버전은 *1.0.0* 이며, 미국 동부 2 *지역에서* 영역 중복 저장소를 사용 하 여 1 개의 복제본과 *미국 동부 2* 지역에 1 개의 복제본을 만듭니다. 복제를 위한 대상 영역을 선택할 때 *원본* 지역을 복제 대상으로 포함 해야 합니다.

`--managed-image` 매개 변수에서 관리 되는 이미지의 ID를 전달 합니다.


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> 동일한 관리 되는 이미지를 사용 하 여 다른 이미지 버전을 만들려면 먼저 이미지 버전이 빌드되고 복제 될 때까지 기다려야 합니다.
>
> 이미지 버전을 만들 때를 추가 `--storage-account-type standard_zrs` 하 여 모든 이미지 버전 복제본을 [영역 중복 저장소](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 에 저장할 수도 있습니다.
>

## <a name="next-steps"></a>다음 단계

[일반화 된 이미지 버전](vm-generalized-image-version-cli.md)에서 VM을 만듭니다.