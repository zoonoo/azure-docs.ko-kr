---
title: 포함 파일
description: 포함 파일
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 70bd79ef944e5918d750a130bd2e2b2c2b656bf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781147"
---
### <a name="create-a-user-assigned-identity"></a>사용자 할당 ID 만들기

[az identity create][az-identity-create] 명령을 사용하여 구독에서 *myACRTasksId* 라는 ID를 만듭니다. 이전에 사용한 것과 동일한 리소스 그룹을 사용하여 컨테이너 레지스트리 또는 다른 리소스 그룹을 만들 수 있습니다.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

다음 단계에서 사용자 할당 ID를 구성하려면 [az identity show][az-identity-show] 명령을 사용하여 ID의 리소스 ID, 보안 주체 ID 및 클라이언트 ID를 변수에 저장합니다.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
