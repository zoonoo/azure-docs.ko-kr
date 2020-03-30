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
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67182215"
---
## <a name="update-resources"></a>리소스 업데이트

업데이트할 수 있는 내용에는 몇 가지 제한 사항이 있습니다. 다음 항목을 업데이트할 수 있습니다. 

공유 이미지 갤러리:
- 설명

이미지 정의:
- 권장 vCPU
- 권장 메모리
- 설명
- 수명 주기 끝

이미지 버전:
- 지역 복제본 수
- 대상 지역
- 최신에서 제외
- 수명 주기 끝

복제본 영역을 추가하려는 경우 원본 관리 이미지를 삭제하지 마십시오. 이미지 버전을 추가 영역으로 복제하려면 소스 관리 이미지가 필요합니다. 

(az[sig 업데이트)](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update)를 사용하여 갤러리의 설명을 업데이트합니다. 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


az sig 이미지 정의 업데이트를 사용하여 이미지 정의 설명을 [업데이트합니다.](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

[az sig 이미지 버전 업데이트를](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)사용하여 복제할 영역을 추가하도록 이미지 버전을 업데이트합니다. 이 변경 은 이미지가 새 영역에 복제될 때 시간이 걸릴 것입니다.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>리소스 삭제

먼저 이미지 버전을 삭제하여 리소스를 역순으로 삭제해야 합니다. 모든 이미지 버전이 삭제되면 이미지 정의를 삭제할 수 있습니다. 모든 이미지 정의가 삭제되면 갤러리를 삭제할 수 있습니다. 

az sig 이미지 버전 삭제를 사용하여 [이미지 버전을 삭제합니다.](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete)

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

az sig 이미지 정의 삭제를 사용하여 [이미지 정의를 삭제합니다.](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete)

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


az sig 삭제를 사용하여 이미지 [갤러리를 삭제합니다.](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete)

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
