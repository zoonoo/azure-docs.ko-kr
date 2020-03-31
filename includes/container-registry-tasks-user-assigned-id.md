---
title: 포함 파일
description: 포함 파일
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ceda7bd6bd165df1eece555c6ce8a9a6c863b2c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77112307"
---
### <a name="create-a-user-assigned-identity"></a>사용자 할당 ID 만들기

az ID [create][az-identity-create] 명령을 사용하여 구독에서 *myACRTasksId라는* ID를 만듭니다. 이전에 사용한 것과 동일한 리소스 그룹을 사용하여 컨테이너 레지스트리 또는 다른 레지스트리를 만들 수 있습니다.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

다음 단계에서 사용자 할당된 ID를 구성하려면 [az ID 표시][az-identity-show] 명령을 사용하여 ID의 리소스 ID, 보안 주체 ID 및 클라이언트 ID를 변수에 저장합니다.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show