---
title: 포함 파일
description: 포함 파일
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/18/2018
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 458782011623721bb44771d42caa32130e23bbc9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182690"
---
[!INCLUDE [resource group intro text](resource-group.md)]

Cloud Shell에서 [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *미국 중남부* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다. **체험** 계층에서 App Service에 지원되는 모든 위치를 확인하려면 [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) 명령을 실행합니다.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

일반적으로 사용자와 가까운 지역에서 리소스 그룹 및 리소스를 만듭니다. 

명령이 완료되면 JSON 출력이 리소스 그룹 속성을 보여줍니다.