---
title: Azure CLI를 사용 하 여 특수 한 이미지 버전에서 VM 만들기
description: Azure CLI를 사용 하 여 공유 이미지 갤러리에서 특수 이미지 버전을 사용 하 여 VM을 만듭니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 1ccf03deee2a2f72c1eb2008e1acc5bf67d16447
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796773"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Azure CLI와 함께 특수 한 이미지 버전을 사용 하 여 VM 만들기

공유 이미지 갤러리에 저장 된 [특수 이미지 버전](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) 에서 VM을 만듭니다. 일반화 된 이미지 버전을 사용 하 여 VM을 만들려면 일반화 된 [이미지 버전에서 Vm 만들기](vm-generalized-image-version-cli.md)를 참조 하세요.

이 예제에서 필요에 따라 리소스 이름을 바꿉니다. 

[Az sig 이미지 정의 목록을](/cli/azure/sig/image-definition#az-sig-image-definition-list) 사용 하 여 갤러리에서 이미지 정의를 나열 하 여 정의의 이름과 ID를 확인 합니다.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

이미지가 특수 이미지 임을 나타내는--특수 매개 변수를 사용 하 여 [az vm create](/cli/azure/vm#az-vm-create) 를 사용 하 여 vm을 만듭니다. 

의 `--image` 이미지 정의 ID를 사용 하 여 사용 가능한 최신 버전의 이미지에서 VM을 만듭니다. 의 `--image`이미지 버전 ID를 제공 하 여 특정 버전에서 VM을 만들 수도 있습니다. 

이 예제에서는 최신 버전의 *Myimagedefinition* 이미지에서 VM을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>다음 단계
[Azure 이미지 작성기 (미리 보기)](./linux/image-builder-overview.md) 는 이미지 버전 생성을 자동화 하는 데 도움이 될 수 있으며, [기존 이미지 버전에서 새 이미지 버전](./linux/image-builder-gallery-update-image-version.md)을 업데이트 하 고 만드는 데에도 사용할 수 있습니다. 

또한 템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [Shared Image Gallery 만들기](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [이미지 버전에서 VM 만들기](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


