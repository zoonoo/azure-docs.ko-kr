---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b49dc6ef2bfee311bc3ca524a5ccb0a4e4b5ca9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793654"
---
## <a name="create-an-image-gallery"></a>이미지 갤러리 만들기 

이미지 갤러리는 이미지 공유를 활성화하는 데 사용되는 기본 리소스입니다. 

갤러리 이름에 허용되는 문자는 대문자 또는 소문자, 숫자, 점 및 마침표입니다. 갤러리 이름에 대시를 사용할 수 없습니다.   갤러리 이름은 구독 내에서 고유해야 합니다. 

[az sig create](/cli/azure/sig#az-sig-create)를 사용하여 이미지 갤러리를 만드세요. 다음 예제에서는 *미국 동부*에 *myGalleryRG*라는 리소스 그룹 및 *myGallery*라는 갤러리를 만듭니다.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>갤러리 공유

이미지는 RBAC(역할 기반 액세스 제어)를 사용하여 여러 구독에서 공유할 수 있습니다. 갤러리, 이미지 정의 또는 이미지 버전 수준에서 이미지를 공유할 수 있습니다. 이미지 버전에 대한 읽기 권한이 있는 모든 사용자는 구독 간에도 이미지 버전을 사용하여 VM을 배포할 수 있습니다.

갤러리 수준에서 다른 사용자와 공유하는 것이 좋습니다. 갤러리의 개체 ID를 가져오려면 [az sig show](/cli/azure/sig#az-sig-show)를 사용합니다.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

이메일 주소 및 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)와 함께 개체 ID를 범위로 사용하여 사용자에게 공유 이미지 갤러리에 대한 액세스 권한을 부여합니다. `<email-address>` 및 `<gallery iD>`를 사용자 고유의 정보로 바꿉니다.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

RBAC를 사용하여 리소스를 공유하는 방법에 대한 자세한 내용은 [RBAC 및 Azure CLI를 사용하여 액세스 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)를 참조하세요.
