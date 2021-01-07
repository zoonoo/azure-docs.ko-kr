---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424878"
---
## <a name="deploy-the-function-project-to-azure"></a>Azure에 함수 프로젝트 배포

Azure에서 함수 앱을 성공적으로 만들었으면 이제 [func azure functionapp publish](../articles/azure-functions/functions-run-local.md#project-file-deployment) 명령을 사용하여 로컬 함수 프로젝트를 배포할 준비가 되었습니다.  

다음 예제에서 `<APP_NAME>`을 앱 이름으로 바꿉니다.

```console
func azure functionapp publish <APP_NAME>
```

publish 명령은 다음 출력과 비슷한 결과를 표시합니다(간단히 하기 위해 잘랐음).

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
