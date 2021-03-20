---
title: 파일 포함
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 520a32ed44951a711dcb1d0975fb452829530c4f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244979"
---
[!INCLUDE [resource group intro text](resource-group.md)]

Cloud Shell에서 [`az group create`](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *서유럽* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다. **체험** 계층에서 App Service에 지원되는 모든 위치를 확인하려면 [`az appservice list-locations --sku FREE`](/cli/azure/appservice) 명령을 실행합니다.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

일반적으로 사용자와 가까운 지역에서 리소스 그룹 및 리소스를 만듭니다. 

명령이 완료되면 JSON 출력이 리소스 그룹 속성을 보여줍니다.