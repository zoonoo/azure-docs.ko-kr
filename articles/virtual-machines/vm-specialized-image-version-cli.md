---
title: Azure CLI를 사용하여 특수화된 이미지 버전에서 VM 만들기
description: Azure CLI를 사용하여 Shared Image Gallery 특수화된 이미지 버전을 사용하여 VM을 만듭니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: ad4ac7d8ac45f8d0c673c43416b68a790f0e3020
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114204178"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Azure CLI를 사용하여 특수화된 이미지 버전을 통해 VM 만들기

Shared Image Gallery에 저장된 [특수화된 이미지 버전](./shared-image-galleries.md#generalized-and-specialized-images)에서 VM을 만듭니다. 일반화된 이미지 버전에서 VM을 만들려는 경우 [일반화된 이미지 버전에서 VM 만들기](vm-generalized-image-version-cli.md)를 참조하세요.

이 예제에서 필요에 따라 리소스 이름을 바꿉니다. 

[az sig image-definition 목록](/cli/azure/sig/image-definition#az_sig_image_definition_list)으로 갤러리의 이미지 정의를 나열하여 정의의 이름과 ID를 확인합니다.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

이미지가 특수 이미지임을 나타내는 --specialized 매개 변수를 사용하는 [az vm create](/cli/azure/vm#az_vm_create)를 사용하여 VM을 만듭니다. 

이미지 정의 ID를 `--image`에 사용하여 사용 가능한 최신 버전의 이미지에서 VM을 만듭니다. 또한 `--image`에 대한 이미지 버전 ID를 제공하여 특정 버전에서 VM을 만들 수 있습니다. 

다음 예제에서는 최신 버전의 *myImageDefinition* 이미지에서 VM을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>다음 단계
[Azure Image Builder(미리 보기)](./image-builder-overview.md)는 이미지 버전 생성을 자동화하는 데 도움이 되며, [기존 이미지 버전에서 새 이미지를 생성](./linux/image-builder-gallery-update-image-version.md)하고 업데이트하는 데도 사용할 수 있습니다. 

또한 템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
