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
ms.openlocfilehash: d1f78034c2142bfb0fc787683b7efed22ae2698c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244595"
---
[!INCLUDE [resource group intro text](resource-group.md)]

Cloud Shell에서 [`az group create`](/cli/azure/group#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *서유럽* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다. **기본** 계층에서 Linux의 App Service에 지원되는 모든 위치를 확인하려면 [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice#az-appservice-list-locations) 명령을 실행합니다.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

일반적으로 사용자와 가까운 지역에서 리소스 그룹 및 리소스를 만듭니다. 

명령이 완료되면 JSON 출력이 리소스 그룹 속성을 보여줍니다.