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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182215"
---
## <a name="update-resources"></a>리소스 업데이트

몇 가지 제한 사항이에서 업데이트할 수 있습니다. 다음 항목을 업데이트할 수 있습니다. 

공유 이미지 갤러리:
- 설명

이미지 정의:
- 권장 vCPU
- 권장 되는 메모리
- 설명
- 수명 주기 끝

이미지 버전:
- 지역 복제본 수
- 대상 지역
- 최신에서 제외
- 수명 주기 끝

지역 복제본을 추가 하려는 경우에 소스 관리 되는 이미지를 삭제 하지 마세요. 원본 관리 되는 이미지의 이미지 버전 추가 지역에 복제 하기 위해 필요 합니다. 

사용 하 여 갤러리의 설명을 업데이트 ([az sig 업데이트](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update)합니다. 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


사용 하 여 이미지 정의에 대 한 설명을 업데이트할 [az sig 이미지 정의 업데이트](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)합니다.

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

복제를 사용 하 여 하위 지역을 추가 하는 이미지 버전을 업데이트 [az sig 이미지 버전 업데이트](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)합니다. 이미지를 새 지역으로 복제 하는 대로이 변경 시간이 걸립니다.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>리소스 삭제

이미지 버전을 먼저 삭제 하 여 역순으로 리소스를 삭제 해야 합니다. 모든 이미지 버전을 삭제 하면 이미지 정의 삭제할 수 있습니다. 모든 이미지 정의 삭제 한 후에 갤러리를 삭제할 수 있습니다. 

사용 하 여 이미지 버전을 삭제 [az sig 이미지 버전 삭제](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete)합니다.

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

사용 하 여 이미지 정의 삭제 [az sig 이미지 정의 삭제](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete)합니다.

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


사용 하 여 이미지 갤러리 삭제할 [az sig 삭제](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete)합니다.

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
