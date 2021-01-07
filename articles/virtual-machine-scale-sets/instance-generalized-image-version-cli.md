---
title: Azure CLI를 사용 하 여 일반화 된 이미지에서 확장 집합 만들기
description: Azure CLI를 사용 하 여 공유 이미지 갤러리에서 일반화 된 이미지를 사용 하 여 확장 집합을 만듭니다.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 841247c03d9f3b151a0c75430715c03b009d831a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91287069"
---
# <a name="create-a-scale-set-from-a-generalized-image-with-azure-cli"></a>Azure CLI를 사용 하 여 일반화 된 이미지에서 확장 집합 만들기

Azure CLI를 사용 하 여 [공유 이미지 갤러리](shared-image-galleries.md) 에 저장 된 일반화 된 이미지 버전에서 확장 집합을 만듭니다. 특수 이미지 버전을 사용 하 여 확장 집합을 만들려면 [특수화 된 이미지에서 확장 집합 인스턴스 만들기](instance-specialized-image-version-cli.md)를 참조 하세요.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서는 Azure CLI 버전 2.4.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

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

을 사용 하 여 확장 집합을 만듭니다 [`az vmss create`](/cli/azure/vmss#az-vmss-create) . 

이미지 정의 ID를 `--image`에 사용하여 사용 가능한 최신 버전의 이미지에서 확장 집합 인스턴스를 만듭니다. 또한 `--image`에 대한 이미지 버전 ID를 제공하여 특정 버전에서 확장 집합 인스턴스를 만들 수 있습니다. 특정 이미지 버전을 사용 하면 특정 이미지 버전이 지역에서 삭제 되거나 제거 되어 해당 이미지 버전이 제공 되지 않는 경우 자동화가 실패할 수 있음을 의미 합니다. 특정 이미지 버전이 필요 하지 않는 한 새 VM을 만들기 위해 이미지 정의 ID를 사용 하는 것이 좋습니다.

이 예제에서는 최신 버전의 *Myimagedefinition* 이미지에서 인스턴스를 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" 
   --admin-username azureuser \
   --generate-ssh-keys
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.

## <a name="next-steps"></a>다음 단계
[Azure 이미지 작성기 (미리 보기)](../virtual-machines/linux/image-builder-overview.md) 는 이미지 버전 생성을 자동화 하는 데 도움이 될 수 있으며, [기존 이미지 버전에서 새 이미지 버전](../virtual-machines/linux/image-builder-gallery-update-image-version.md)을 업데이트 하 고 만드는 데에도 사용할 수 있습니다. 

또한 템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

공유 이미지 갤러리에 대한 자세한 내용은 [개요](shared-image-galleries.md)를 참조하세요. 문제 발생 시에는 [공유 이미지 갤러리 문제 해결](../virtual-machines/troubleshooting-shared-images.md)을 참조하세요.