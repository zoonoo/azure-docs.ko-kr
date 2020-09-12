---
title: 테 넌 트 간에 갤러리 이미지 공유
description: Linux 예제를 사용 하 여 공유 이미지 갤러리를 사용 하 여 Azure 테 넌 트 간에 VM 이미지를 공유 하는 방법을 알아봅니다.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: d0770cd9e7ec99765b8f201634e6a857b3cf02e4
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318068"
---
# <a name="share-gallery-vm-images-across-azure-tenants---linux-examples"></a>Azure 테 넌 트 간에 갤러리 VM 이미지 공유-Linux 예제

공유 이미지 갤러리를 사용 하면 RBAC를 사용 하 여 이미지를 공유할 수 있습니다. RBAC를 사용 하 여 테 넌 트 내에서 이미지를 공유 하 고 테 넌 트 외부의 사용자도 공유할 수 있습니다. 이 간단한 공유 옵션에 대 한 자세한 내용은 [갤러리 공유](./shared-images-portal.md#share-the-gallery)를 참조 하세요.

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> 포털을 사용 하 여 다른 azure 테 넌 트의 이미지에서 VM을 배포할 수 없습니다. 테 넌 트 간에 공유 되는 이미지에서 VM을 만들려면 Azure CLI 또는 [Powershell](../windows/share-images-across-tenants.md)을 사용 해야 합니다.

## <a name="create-a-vm-using-azure-cli"></a>Azure CLI를 사용 하 여 VM 만들기

AppID, 앱 키 및 테 넌 트 1의 ID를 사용 하 여 테 넌 트 1에 대 한 서비스 주체를 로그인 합니다. `az account show --query "tenantId"`필요한 경우를 사용 하 여 테 넌 트 id를 가져올 수 있습니다.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
AppID, 앱 키 및 테 넌 트 2의 ID를 사용 하 여 테 넌 트 2에 대 한 서비스 주체를 로그인 합니다.

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

VM을 만듭니다. 예제의 정보를 사용자 고유의으로 바꿉니다.

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
