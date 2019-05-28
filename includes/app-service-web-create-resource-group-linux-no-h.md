---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: c70e2c166bee14ac58ee88bd18baf0cc61702767
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66137394"
---
[!INCLUDE [resource group intro text](resource-group.md)]

Cloud Shell에서 [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *유럽 서부* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다. **기본** 계층에서 Linux의 App Service에 지원되는 모든 위치를 확인하려면 [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) 명령을 실행합니다.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

일반적으로 사용자와 가까운 지역에서 리소스 그룹 및 리소스를 만듭니다. 

명령이 완료되면 JSON 출력이 리소스 그룹 속성을 보여줍니다.