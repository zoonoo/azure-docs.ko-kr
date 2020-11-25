---
title: 포함 파일
description: 포함 파일
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 08458bd170707b28c69fdad1d8aa115a7ad245a5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560596"
---
> [!NOTE]
> `az webapp up` 명령에는 다음 작업이 포함됩니다.
>
>- 기본 [리소스 그룹](/cli/azure/group?view=azure-cli-latest#az-group-create)을 만듭니다.
>
>- 기본 [App Service 계획](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)을 만듭니다.
>
>- 지정된 이름으로 [앱을 만듭니다](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create).
>
>- 현재 작업 디렉터리에서 앱까지의 [배포 파일을 압축합니다](../articles/app-service/deploy-zip.md).
>