---
title: 파일 포함
description: 포함 파일
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 012800806aeff81939baa2cee88e78191e4fb6c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195267"
---
### <a name="create-a-user-assigned-identity"></a>사용자 할당 ID 만들기

[Az identity create][az-identity-create] 명령을 사용 하 여 구독에 *Myacrtasksid* 라는 id를 만듭니다. 이전에 사용한 것과 동일한 리소스 그룹을 사용 하 여 컨테이너 레지스트리를 만들 수 있습니다.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

다음 단계에서 사용자 할당 id를 구성 하려면 [az identity show][az-identity-show] 명령을 사용 하 여 id의 리소스 id, 보안 주체 ID 및 클라이언트 id를 변수에 저장 합니다.

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
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show