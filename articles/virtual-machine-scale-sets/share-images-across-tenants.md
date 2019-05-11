---
title: 갤러리 이미지를 Azure의 테 넌 트에서 공유 | Microsoft Docs
description: 공유 이미지 갤러리를 사용 하 여 Azure 테 넌 트에서 VM 이미지를 공유 하는 방법에 알아봅니다.
services: virtual-machine-scale-sets
author: cynthn
manager: jeconnoc
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: cbaaac629fd013602eed75cc7dc357f13a62e3b1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160127"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Azure 테 넌 트에서 VM 이미지 갤러리 공유

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>확장 집합 Azure CLI를 사용 하 여 만들기

AppID, 앱 키 및 테 넌 트 1의 ID를 사용 하 여 1 테 넌 트의 서비스 주체에 로그인 합니다. 사용할 수 있습니다 `az account show --query "tenantId"` 에 필요한 경우 테 넌 트 Id를 가져옵니다.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
2 appID, 앱 키 및 테 넌 트 2의 ID를 사용 하 여 테 넌 트의 서비스 주체에 로그인 합니다.

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

확장 집합을 만듭니다. 예제에서 정보를 사용자 고유의로 바꿉니다.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>다음 단계

문제 발생 시에는 [공유 이미지 갤러리 문제 해결](troubleshooting-shared-images.md)을 수행할 수 있습니다.