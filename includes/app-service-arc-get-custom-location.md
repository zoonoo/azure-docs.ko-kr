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
ms.openlocfilehash: d4d6783645f719f21d97f18abc9bc900a368fd8f
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112362194"
---
클러스터 관리자에서 사용자 지정 위치에 대한 다음 정보를 가져옵니다([사용자 지정 위치 만들기](../articles/app-service/manage-create-arc-environment.md#create-a-custom-location) 참조).

```azurecli-interactive
customLocationGroup="<resource-group-containing-custom-location>"
customLocationName="<name-of-custom-location>"
```

다음 단계를 위해 사용자 지정 위치 ID를 가져옵니다.

```azurecli-interactive
customLocationId=$(az customlocation show \
    --resource-group $customLocationGroup \
    --name $customLocationName \
    --query id \
    --output tsv)
```
