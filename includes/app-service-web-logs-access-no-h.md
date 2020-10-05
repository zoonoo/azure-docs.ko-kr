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
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "84905870"
---
App Service의 애플리케이션 코드 내부에서 생성된 콘솔 로그에 액세스하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행하여 진단 로깅을 켭니다.

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
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
