---
title: 포함 파일
description: 포함 파일
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 2b1d9b7f9ff07a3e0c7745191decc3e82181553e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708022"
---
## <a name="create-azure-container-registry"></a>Azure 컨테이너 레지스트리 만들기

컨테이너 레지스트리를 만들려면 컨테이너 레지스트리를 배포할 *리소스 그룹*이 필요합니다. 리소스 그룹은 모든 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

리소스 그룹을 만든 후에는 [az acr create][az-acr-create] 명령을 사용하여 Azure 컨테이너 레지스트리를 만듭니다. 컨테이너 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. `<acrName>`를 레지스트리의 고유한 이름으로 바꿉니다.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

다음은 *mycontainerregistry082*라는 새 Azure 컨테이너 레지스트리에 대한 부분 출력입니다.

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

이 자습서의 나머지 부분에서는 `<acrName>`을 이 단계에서 선택하는 컨테이너 레지스트리 이름의 자리 표시자로 참조합니다.

## <a name="log-in-to-container-registry"></a>컨테이너 레지스트리에 로그인

Azure Container Registry 인스턴스에 로그인해야 이미지를 푸시할 수 있습니다. [az acr login][az-acr-login] 명령을 사용하여 작업을 완료합니다. 컨테이너 레지스트리를 만들 때 선택한 고유의 이름을 입력해야 합니다.

```azurecli
az acr login --name <acrName>
```

다음은 그 예입니다. 

```azurecli
az acr login --name mycontainerregistry082
```

완료되면 이 명령은 `Login Succeeded`를 반환합니다.

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create