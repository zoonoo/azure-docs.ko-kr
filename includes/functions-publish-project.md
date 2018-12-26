---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/27/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 1b553cbd720fcb76899844712ce5053af46f7ccb
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452958"
---
## <a name="deploy-the-function-app-project-to-azure"></a>함수 앱 프로젝트를 Azure에 배포

Azure에서 함수 앱이 만들어지면 [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) 명령을 사용하여 프로젝트 코드를 Azure에 배포할 수 있습니다.

```bash
func azure functionapp publish <FunctionAppName>
```

다음 출력과 같이 보기 편하도록 나눠서 표시됩니다.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

이제 Azure에서 함수를 테스트할 수 있습니다.