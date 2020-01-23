---
title: 포함 파일
description: 포함 파일
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 343321b43d219401f0b494ab3f6a54074fbc36f3
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279297"
---
## <a name="run-the-function-locally"></a>로컬에서 함수 실행

다음 명령은 함수 앱을 시작합니다. 앱은 Azure에 있는 동일한 Azure Functions 런타임을 사용하여 실행됩니다. 시작 명령은 프로젝트 언어에 따라 달라집니다.

### <a name="c"></a>C\#

```command
func start --build
```

### <a name="javascript"></a>JavaScript

```command
func start
```

### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

Functions 호스트가 시작되면 다음과 같이 보기 편하도록 분할된 출력이 표시됩니다.

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
