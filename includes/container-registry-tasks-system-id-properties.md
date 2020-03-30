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
ms.openlocfilehash: 700dbfde3be2f24eb57acbdeb9d2841ef2bdfe44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77112297"
---
명령 출력에서 섹션은 태스크에 `identity` 설정된 `SystemAssigned` 형식의 ID를 보여 주십습니다. `principalId` 는 작업 ID의 주요 ID입니다.

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 
az [acr 태스크 쇼][az-acr-task-show] 명령을 사용하여 principalId를 변수에 저장하고 이후 명령에서 사용합니다. 다음 명령에서 작업 및 레지스트리의 이름을 대체합니다.

```azurecli
principalID=$(az acr task show --name mytask --registry myregistry --query identity.principalId --output tsv)
```

<!-- LINKS - Internal -->
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
