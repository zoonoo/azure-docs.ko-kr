---
title: Azure CLI를 사용 하 여 일반화 된 이미지에서 VM 만들기
description: Azure CLI를 사용 하 여 일반화 된 이미지 버전에서 VM을 만듭니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.openlocfilehash: 5e59872a4da0136232652008a2980601428eeab6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82796786"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-cli"></a>CLI를 사용 하 여 일반화 된 이미지 버전에서 VM 만들기

공유 이미지 갤러리에 저장 된 [일반화 된 이미지 버전](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) 에서 VM을 만듭니다. 특수 이미지를 사용 하 여 VM을 만들려면 [특수 이미지에서 Vm 만들기](vm-specialized-image-version-powershell.md)를 참조 하세요. 


## <a name="get-the-image-id"></a>이미지 ID 가져오기

[Az sig 이미지 정의 목록을](/cli/azure/sig/image-definition#az-sig-image-definition-list) 사용 하 여 갤러리에서 이미지 정의를 나열 하 여 정의의 이름과 ID를 확인 합니다.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>VM 만들기

[az vm create](/cli/azure/vm#az-vm-create)를 사용하여 VM을 만듭니다. 최신 버전의 이미지를 사용 하려면를 `--image` 이미지 정의의 ID로 설정 합니다. 

이 예제에서 필요에 따라 리소스 이름을 바꿉니다. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

매개 변수의 이미지 버전 ID를 사용 하 여 특정 버전을 사용할 수도 있습니다 `--image` . 예를 들어 이미지 버전 *1.0.0* 을 사용 하려면을 입력 `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` 합니다.

## <a name="next-steps"></a>다음 단계

[Azure 이미지 작성기 (미리 보기)](./linux/image-builder-overview.md) 는 이미지 버전 생성을 자동화 하는 데 도움이 될 수 있으며, [기존 이미지 버전에서 새 이미지 버전](./linux/image-builder-gallery-update-image-version.md)을 업데이트 하 고 만드는 데에도 사용할 수 있습니다. 
