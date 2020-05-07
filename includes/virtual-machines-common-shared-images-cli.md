---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: de1a22ed6e9707767c0d097a9250f0bdd31414d5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788963"
---
## <a name="create-an-image-gallery"></a>이미지 갤러리 만들기 

이미지 갤러리는 이미지 공유를 활성화하는 데 사용되는 기본 리소스입니다. 

갤러리 이름에 허용되는 문자는 대문자 또는 소문자, 숫자, 점 및 마침표입니다. 갤러리 이름에는 대시를 사용할 수 없습니다.   갤러리 이름은 구독 내에서 고유해야 합니다. 

[az sig create](/cli/azure/sig#az-sig-create)를 사용하여 이미지 갤러리를 만드세요. 다음 예제에서는 *미국 동부*에 *myGalleryRG* 라는 이름의 리소스 그룹과 *mygallery*라는 갤러리를 만듭니다.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>갤러리 공유

RBAC (역할 기반 Access Control)를 사용 하 여 여러 구독에서 이미지를 공유할 수 있습니다. 갤러리, 이미지 정의 또는 이미지 버전 leve에서 이미지를 공유할 수 있습니다. 구독 간에도 이미지 버전에 대 한 읽기 권한이 있는 모든 사용자는 이미지 버전을 사용 하 여 VM을 배포할 수 있습니다.

갤러리 수준에서 다른 사용자와 공유 하는 것이 좋습니다. 갤러리의 개체 ID를 가져오려면 [az sig show](/cli/azure/sig#az-sig-show)를 사용 합니다.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

사용자에 게 공유 이미지 갤러리에 대 한 액세스 권한을 부여 하려면 전자 메일 주소 및 [az role 할당 만들기](/cli/azure/role/assignment#az-role-assignment-create) 와 함께 개체 ID를 범위로 사용 합니다. `<email-address>` 및 `<gallery iD>`을 사용자 정보로 바꿉니다.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

RBAC를 사용하여 리소스를 공유하는 방법에 대한 자세한 내용은 [RBAC 및 Azure CLI를 사용하여 액세스 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)를 참조하세요.
