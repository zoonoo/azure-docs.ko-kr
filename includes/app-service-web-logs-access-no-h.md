---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551219"
---
컨테이너 내에서 생성 된 콘솔 로그에 액세스할 수 있습니다. 먼저 Cloud Shell에서 다음 명령을 실행 하 여 컨테이너 로깅 켜기:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

컨테이너 로깅이 설정되면 다음 명령을 실행하여 로그 스트림을 확인합니다.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

콘솔 로그가 즉시 표시되지 않으면 30초 후에 다시 확인합니다.

> [!NOTE]
> 브라우저에서 로그 파일을 검사할 수도 있습니다 `https://<app-name>.scm.azurewebsites.net/api/logs/docker`합니다.

언제 든 지 로그 스트리밍을 중지 하려면 다음을 입력 `Ctrl` + `C`합니다.
