---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 07/13/2021
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 766e135cbccf86884786c2b888fcb1ea9d98ecc7
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113688668"
---
App Service의 애플리케이션 코드 내부에서 생성된 콘솔 로그에 액세스하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행하여 진단 로깅을 켭니다.

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --docker-container-logging filesystem --level Verbose
```

`--level`에 대한 가능한 값은 `Error`, `Warning`, `Info` 및 `Verbose`입니다. 각 후속 수준에는 이전 수준이 포함됩니다. 예를 들어 `Error`에는 오류 메시지만 포함하고 `Verbose`에는 모든 메시지를 포함합니다.

진단 로깅이 설정되면 다음 명령을 실행하여 로그 스트림을 확인합니다.

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

콘솔 로그가 즉시 표시되지 않으면 30초 후에 다시 확인합니다.

> [!NOTE]
> `https://<app-name>.scm.azurewebsites.net/api/logs/docker`의 브라우저에서 로그 파일을 검사할 수도 있습니다.

언제든지 로그 스트리밍을 중지하려면 `Ctrl`+`C`를 입력합니다.
