---
title: Azure CLI를 사용하여 특수 이미지 버전에서 확장 집합 만들기
description: Azure CLI를 사용하여 Shared Image Gallery에서 특수 이미지 버전을 사용하는 확장 집합을 만듭니다.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5fc88c00d548c0a034984976557d316fdac7620f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792348"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Azure CLI를 통해 특수 이미지 버전을 사용하는 확장 집합 만들기

Shared Image Gallery에 저장된 [특수 이미지 버전](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)에서 확장 집합을 만듭니다. 일반 이미지 버전을 사용하는 확장 집합을 만들려는 경우 [일반 이미지에서 확장 집합 만들기](instance-generalized-image-version-cli.md)를 참조하세요.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.4.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

이 예제에서 필요에 따라 리소스 이름을 바꿉니다. 

[az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list)를 사용하여 갤러리에서 이미지 정의를 나열하면 해당 이름과 정의의 ID를 볼 수 있습니다.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

이미지가 특수 이미지임을 나타내는 `--specialized` 매개 변수를 사용하여 [`az vmss create`](/cli/azure/vmss#az_vmss_create)를 사용하는 확장 집합을 만듭니다.

이미지 정의 ID를 `--image`에 사용하여 사용 가능한 최신 버전의 이미지에서 확장 집합 인스턴스를 만듭니다. 또한 `--image`에 대한 이미지 버전 ID를 제공하여 특정 버전에서 확장 집합 인스턴스를 만들 수 있습니다. 특정 이미지 버전이 지역에서 삭제되거나 제거되었기 때문에 사용할 수 없는 경우 특정 이미지 버전을 사용하면 자동화가 실패할 수 있음을 의미합니다. 특정 이미지 버전이 필요하지 않는 한 새 VM을 만들기 위해 이미지 정의 ID를 사용하는 것이 좋습니다.

다음 예제에서는 *myImageDefinition* 이미지의 최신 버전에서 인스턴스를 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```


## <a name="next-steps"></a>다음 단계
[Azure VM Image Builder(미리 보기)](../virtual-machines/image-builder-overview.md)는 이미지 버전 생성 자동화에 도움이 되며, [기존 이미지 버전에서 새 이미지 버전을 만들고](../virtual-machines/linux/image-builder-gallery-update-image-version.md) 업데이트하는 데에도 사용할 수 있습니다. 

또한 템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)