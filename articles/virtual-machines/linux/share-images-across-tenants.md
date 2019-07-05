---
title: 갤러리 이미지를 Azure의 테 넌 트에서 공유 | Microsoft Docs
description: 공유 이미지 갤러리를 사용 하 여 Azure 테 넌 트에서 VM 이미지를 공유 하는 방법에 알아봅니다.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 88596a4bda3c9ebae99403e95b3539f068cad665
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465471"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Azure 테 넌 트에서 VM 이미지 갤러리 공유

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> 다른 azure 테 넌 트의 이미지에서 VM을 배포 하려면 포털을 사용할 수 없습니다. 테 넌 트 간에 공유 되는 이미지에서 VM을 만들려면 Azure CLI를 사용 해야 하거나 [Powershell](../windows/share-images-across-tenants.md)합니다.

## <a name="create-a-vm-using-azure-cli"></a>Azure CLI를 사용 하 여 VM 만들기

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

VM을 만듭니다. 예제에서 정보를 사용자 고유의로 바꿉니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>다음 단계

문제 발생 시에는 [공유 이미지 갤러리 문제 해결](troubleshooting-shared-images.md)을 수행할 수 있습니다.