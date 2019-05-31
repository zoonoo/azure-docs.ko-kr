---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 48bb91b3b2e9a31de63e515edb857bc2a170ea79
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132249"
---
## <a name="deploy-the-function-app-project-to-azure"></a>함수 앱 프로젝트를 Azure에 배포

Azure에서 함수 앱이 만들어지면 [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core Tools 명령을 사용하여 프로젝트 코드를 Azure에 배포할 수 있습니다. 다음 명령에서 `<APP_NAME>`을 이전 단계의 앱 이름으로 바꿉니다.

```bash
func azure functionapp publish <APP_NAME>
```

보기 편하게 잘린 다음과 유사한 출력이 표시됩니다.

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

이제 Azure에서 함수를 테스트하는 데 사용할 수 있는 프로그램 HttpTrigger의 호출 URL 값을 복사합니다. 이 URL에는 함수 키인 `code` 쿼리 문자열 값이 포함되어 있습니다. 이 키는 다른 사람이 Azure에서 HTTP 트리거 엔드포인트를 호출하기 어렵게 만듭니다.