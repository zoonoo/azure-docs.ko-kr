---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/12/2019
ms.author: glenga
ms.custom: include file, fasttrack-edit
ms.openlocfilehash: 5b009fafc818a06bdda309b3e025251cc0997e47
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737192"
---
## <a name="update-the-function"></a>함수 업데이트

기본적으로 템플릿은 요청을 생성할 때 기능 키를 필요로 하는 함수를 만듭니다. Azure에서 함수를 쉽게 테스트하려면 익명 액세스를 허용하도록 함수를 업데이트해야 합니다. 이 변경을 만드는 방식은 함수 프로젝트 언어에 따라 달라집니다.

### <a name="c"></a>C\#

새 함수인 MyHttpTrigger.cs 코드 파일을 열고 함수 정의의 **AuthorizationLevel** 특성을 `Anonymous`의 값으로 업데이트하고 변경 내용을 저장합니다.

```csharp
[FunctionName("MyHttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    ILogger log)
```

### <a name="javascript"></a>JavaScript

텍스트 편집기에서 새 함수에 대한 function.json 파일을 열고 **바인딩**의 **authLevel** 속성을 `anonymous`로 업데이트하고, 변경 내용을 저장합니다.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

이제 함수 키를 제공하지 않고도 Azure에서 함수를 호출할 수 있습니다. 로컬로 실행하는 경우 함수 키는 필요하지 않습니다.
