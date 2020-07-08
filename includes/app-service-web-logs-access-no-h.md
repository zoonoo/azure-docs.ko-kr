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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905870"
---
App Service의 응용 프로그램 코드 내부에서 생성 된 콘솔 로그에 액세스 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 하 여 진단 로깅을 설정 합니다.

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

에 사용할 수 있는 값 `--level` 은 `Error` ,, `Warning` `Info` 및 `Verbose` 입니다. 각 후속 수준에는 이전 수준이 포함 됩니다. 예: `Error` 오류 메시지만 포함 하 고 `Verbose` 모든 메시지를 포함 합니다.

진단 로깅이 설정 되 면 다음 명령을 실행 하 여 로그 스트림을 확인 합니다.

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

콘솔 로그가 즉시 표시되지 않으면 30초 후에 다시 확인합니다.

> [!NOTE]
> `https://<app-name>.scm.azurewebsites.net/api/logs/docker`의 브라우저에서 로그 파일을 검사할 수도 있습니다.

언제든지 로그 스트리밍을 중지하려면 `Ctrl`+`C`를 입력합니다.
