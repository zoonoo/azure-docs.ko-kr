---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: a20a4a4b3c618a03ba7b5f788395bb541d2c143d
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371989"
---
클러스터 관리자에서 사용자 지정 위치에 대한 다음 정보를 가져옵니다([사용자 지정 위치 만들기](../articles/app-service/manage-create-arc-environment.md#create-a-custom-location) 참조).

```azurecli-interactive
customLocationGroup="<resource-group-containing-custom-location>"
customLocationName=<name-of-custom-location>
```

다음 단계를 위해 사용자 지정 위치 ID를 가져옵니다.

```azurecli-interactive
customLocationId=$(az customlocation show \
    --resource-group $customLocationGroup \
    --name $customLocationName \
    --query id \
    --output tsv)
```