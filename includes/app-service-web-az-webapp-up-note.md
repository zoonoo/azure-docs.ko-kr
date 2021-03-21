---
title: 파일 포함
description: 포함 파일
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: d285b48606485fbd7f53511a15be1e2a31791829
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244457"
---
> [!NOTE]
> `az webapp up` 명령에는 다음 작업이 포함됩니다.
>
>- 기본 [리소스 그룹](/cli/azure/group#az-group-create)을 만듭니다.
>
>- 기본 [App Service 계획](/cli/azure/appservice/plan#az-appservice-plan-create)을 만듭니다.
>
>- 지정된 이름으로 [앱을 만듭니다](/cli/azure/webapp#az-webapp-create).
>
>- 현재 작업 디렉터리에서 앱까지의 [배포 파일을 압축합니다](../articles/app-service/deploy-zip.md).
>