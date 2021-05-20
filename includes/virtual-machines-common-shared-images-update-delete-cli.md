---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 4392e7f146f13e581f722b94f13038ad8abff0ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244682"
---
## <a name="update-resources"></a>리소스 업데이트

업데이트할 수 있는 항목에는 몇 가지 제한 사항이 있습니다. 다음 항목을 업데이트할 수 있습니다. 

공유 이미지 갤러리:
- Description

이미지 정의:
- 권장 vCPU
- 권장 메모리
- Description
- 수명 주기 끝

이미지 버전:
- 지역 복제본 수
- 대상 지역
- 최신에서 제외
- 수명 주기 끝

복제본 영역을 추가하려는 경우 원본 관리형 이미지를 삭제하지 마세요. 원본 관리형 이미지는 이미지 버전을 추가 지역에 복제하는 데 필요합니다. 

([az sig update](/cli/azure/sig#az-sig-update)를 사용하여 갤러리에 대한 설명을 업데이트합니다. 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


[az sig image-definition update](/cli/azure/sig/image-definition#az-sig-image-definition-update)를 사용하여 이미지 정의에 대한 설명을 업데이트합니다.

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

[az sig image-version update](/cli/azure/sig/image-definition#az-sig-image-definition-update)를 사용하여 복제할 지역을 추가하도록 이미지 버전을 업데이트합니다. 이 변경 내용은 이미지가 새 지역에 복제될 때까지 시간이 오래 걸립니다.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

이 예제에서는 [az sig image-version update](/cli/azure/sig/image-definition#az-sig-image-definition-update)를 사용하여 이 이미지 버전을 *최신* 이미지로 사용되지 않도록 제외하는 방법을 보여줍니다.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

이 예제에서는 [az sig image-version update](/cli/azure/sig/image-definition#az-sig-image-definition-update)를 사용하여 이 이미지 버전을 *최신* 이미지로 고려하는 방법을 보여줍니다.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>리소스 삭제

먼저 이미지 버전을 삭제하여 리소스를 역순으로 삭제해야 합니다. 모든 이미지 버전이 삭제되면 이미지 정의를 삭제할 수 있습니다. 모든 이미지 정의가 삭제되면 갤러리를 삭제할 수 있습니다. 

[az sig image-version delete](/cli/azure/sig/image-version#az-sig-image-version-delete)를 사용하여 이미지 버전을 삭제합니다.

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

[az sig image-definition delete](/cli/azure/sig/image-definition#az-sig-image-definition-delete)를 사용하여 이미지 정의를 삭제합니다.

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


[az sig delete](/cli/azure/sig#az-sig-delete)를 사용하여 이미지 갤러리를 삭제합니다.

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```