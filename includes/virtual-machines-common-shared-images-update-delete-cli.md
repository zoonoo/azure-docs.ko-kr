---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 213fec9e7d9da56d34f79fee7e677b0e6bbd7a63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89303987"
---
## <a name="update-resources"></a>리소스 업데이트

업데이트할 수 있는 항목에 대 한 몇 가지 제한 사항이 있습니다. 다음 항목을 업데이트할 수 있습니다. 

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

복제본 영역을 추가 하려는 경우 원본 관리 이미지를 삭제 하지 마십시오. 원본 관리 이미지는 이미지 버전을 추가 지역에 복제 하는 데 필요 합니다. 

([Az sig update](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update)를 사용 하 여 갤러리에 대 한 설명을 업데이트 합니다. 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


[Az sig 이미지 정의 업데이트](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)를 사용 하 여 이미지 정의에 대 한 설명을 업데이트 합니다.

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

[Az sig image 버전 업데이트](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)를 사용 하 여 복제할 지역을 추가 하도록 이미지 버전을 업데이트 합니다. 이 변경 내용은 이미지가 새 지역에 복제 될 때까지 시간이 오래 걸립니다.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

이 예제에서는 [az sig image 버전 업데이트](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) 를 사용 하 여이 이미지 버전이 *최신* 이미지로 사용 되지 않도록 제외 하는 방법을 보여 줍니다.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

이 예제에서는 [az sig image 버전 업데이트](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) 를 사용 하 여이 이미지 버전이 *최신* 이미지를 고려 하는 것으로 포함 하는 방법을 보여 줍니다.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>리소스 삭제

먼저 이미지 버전을 삭제 하 여 리소스를 역순으로 삭제 해야 합니다. 모든 이미지 버전이 삭제되면 이미지 정의를 삭제할 수 있습니다. 모든 이미지 정의가 삭제되면 갤러리를 삭제할 수 있습니다. 

[Az sig image-version delete](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete)를 사용 하 여 이미지 버전을 삭제 합니다.

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

[Az sig 이미지 정의 삭제](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete)를 사용 하 여 이미지 정의를 삭제 합니다.

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


[Az sig delete](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete)를 사용 하 여 이미지 갤러리를 삭제 합니다.

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
