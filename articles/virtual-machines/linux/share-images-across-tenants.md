---
title: Azure의 테넌자 간에 갤러리 이미지 공유
description: 공유 이미지 갤러리를 사용하여 Azure 테넌에서 VM 이미지를 공유하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 4259ca01dbe45463b73cf1ec1c620c3921ab9459
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758475"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Azure 테넌에서 갤러리 VM 이미지 공유

공유 이미지 갤러리를 사용하면 RBAC를 사용하여 이미지를 공유할 수 있습니다. RBAC를 사용하여 테넌트 내에서, 심지어 테넌트 외부의 개인에게도 이미지를 공유할 수 있습니다. 이 간단한 공유 옵션에 대한 자세한 내용은 [갤러리 공유](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery)를 참조하십시오.

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> 포털을 사용하여 다른 Azure 테넌트의 이미지에서 VM을 배포할 수 없습니다. 테넌자 간에 공유되는 이미지에서 VM을 만들려면 Azure CLI 또는 [Powershell을](../windows/share-images-across-tenants.md)사용해야 합니다.

## <a name="create-a-vm-using-azure-cli"></a>Azure CLI를 사용하여 VM 만들기

appID, 앱 키 및 테넌트 1의 ID를 사용하여 테넌트 1의 서비스 주체에 로그인합니다. 필요한 경우 `az account show --query "tenantId"` 테넌트 아이디를 받는 데 사용할 수 있습니다.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
appID, 앱 키 및 테넌트 2의 ID를 사용하여 테넌트 2의 서비스 주체에 로그인하십시오.

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

VM을 만듭니다. 예제의 정보를 사용자 고유의 정보로 바꿉꿉입니다.

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