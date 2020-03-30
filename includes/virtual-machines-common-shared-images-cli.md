---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814891"
---
## <a name="before-you-begin"></a>시작하기 전에

이 문서의 예제를 완료하려면 일반화된 VM의 기존 관리 이미지가 있어야 합니다. 자세한 내용은 [자습서: Azure CLI를 사용하여 Azure VM의 사용자 지정 이미지 만들기를](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images)참조하십시오. 관리되는 이미지에 데이터 디스크가 포함된 경우 데이터 디스크 크기는 1TB를 초과할 수 없습니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 로 이동하여 별도의 브라우저 탭에서 Cloud [https://shell.azure.com/bash](https://shell.azure.com/bash)Shell을 시작할 수도 있습니다. **복사를** 선택하여 코드 블록을 복사하고 클라우드 셸에 붙여넣은 다음 enter를 눌러 실행합니다.

CLI를 로컬로 설치하고 사용하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하십시오.

## <a name="create-an-image-gallery"></a>이미지 갤러리 만들기 

이미지 갤러리는 이미지 공유를 활성화하는 데 사용되는 기본 리소스입니다. 갤러리 이름에 허용되는 문자는 대문자 또는 소문자, 숫자, 점 및 마침표입니다. 갤러리 이름에는 대시가 포함될 수 없습니다.   갤러리 이름은 구독 내에서 고유해야 합니다. 

[az sig create](/cli/azure/sig#az-sig-create)를 사용하여 이미지 갤러리를 만드세요. 다음 예에서는 *myGalleryRG*에 *myGallery*라는 갤러리를 만듭니다.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>이미지 정의 만들기

이미지 정의는 이미지에 대한 논리적 그룹을 만듭니다. 이러한 버전은 해당 이미지 내에서 생성된 이미지 버전에 대한 정보를 관리하는 데 사용됩니다. 이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성할 수 있습니다. 이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의를](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)참조하십시오.

[az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)를 사용하여 갤러리에 초기 이미지 정의를 만듭니다.

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```


## <a name="create-an-image-version"></a>이미지 버전 만들기 

[az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create)을 사용하여 필요에 따라 이미지의 버전을 만듭니다. 이미지 버전을 만드는 기준선으로 사용할 관리되는 이미지의 ID를 전달해야 합니다. [az image list](/cli/azure/image?view#az-image-list)를 사용하여 리소스 그룹에 있는 이미지에 대한 정보를 가져올 수 있습니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식 : *메이저 버전*. *마이너 버전*. *패치*.

이 예제에서는 이미지 버전이 *1.0.0이며* *미국 중서부* 지역에서 복제본 2개, *미국 중남부* 지역의 복제본 1개, 영역 중복 저장소를 사용하여 *미국 동부 2* 지역에 복제본 1개 를 만듭니다.


```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1=Standard_ZRS" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> 동일한 관리되는 이미지를 사용하여 다른 이미지 버전을 만들려면 이미지 버전이 빌드 및 복제가 완전히 완료될 때까지 기다려야 합니다.
>
> 이미지 버전을 만들 때 추가하여 모든 이미지 버전 복제본을 `--storage-account-type standard_zrs` 영역 중복 [저장소에](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 저장할 수도 있습니다.
>

## <a name="share-the-gallery"></a>갤러리 공유

갤러리 수준에서 다른 사용자와 공유하는 것이 좋습니다. 갤러리의 개체 ID를 얻으려면 [az sig show를](/cli/azure/sig#az-sig-show)사용합니다.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

개체 ID를 전자 메일 주소 및 [az 역할 할당 만들기와](/cli/azure/role/assignment#az-role-assignment-create) 함께 범위로 사용하여 사용자에게 공유 이미지 갤러리에 대한 액세스 권한을 부여합니다.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


