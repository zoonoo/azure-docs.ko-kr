---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 74e14e36b1ac0979da31203a2d16e2396ed821d0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132165"
---
## <a name="run-the-function-locally"></a>로컬에서 함수 실행

다음 명령은 함수 앱을 시작합니다. 앱은 Azure에 있는 동일한 Azure Functions 런타임을 사용하여 실행됩니다.

```bash
func host start --build
```

`--build` 옵션은 C# 프로젝트를 컴파일하는 데 필요합니다. JavaScript 프로젝트에는 이 옵션이 필요하지 않습니다.

함수 호스트가 시작되면 다음 출력과 같이 보기 편하도록 나눠서 작성합니다.

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

런타임 출력에서 `HttpTrigger` 함수의 URL을 복사하고 브라우저의 주소 표시줄에 붙여넣습니다. 이 URL에 쿼리 문자열 `?name=<yourname>`을 추가하고 요청을 실행합니다. 다음은 로컬 함수에서 반환된 GET 요청에 대한 브라우저의 응답을 보여줍니다.

![브라우저에서 로컬로 테스트](./media/functions-run-function-test-local/functions-test-local-browser.png)

이제 함수를 로컬로 실행했으므로 Azure에서 함수 앱 및 기타 필요한 리소스를 만들 수 있습니다.