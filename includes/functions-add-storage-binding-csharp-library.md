---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 4db460a5dcefb49de3ad2b594d3957cbcf7445c3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592783"
---
C# 클래스 라이브러리 프로젝트에서 바인딩은 함수 메서드의 바인딩 특성으로 정의됩니다. 그런 다음, *function.json* 파일이 이러한 특성을 기반으로 자동으로 생성됩니다.

*HttpTrigger.cs* 프로젝트 파일을 열고 다음 `using` 문을 추가합니다.

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

다음 매개 변수를 `Run` 메서드 정의에 추가합니다.

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

`msg` 매개 변수는 `ICollector<T>` 형식이며, 함수가 완료될 때 출력 바인딩에 작성되는 메시지의 컬렉션을 나타냅니다. 이 경우 출력은 `outqueue`라는 스토리지 큐입니다. 스토리지 계정에 대한 연결 문자열은 `StorageAccountAttribute`로 설정됩니다. 이 특성은 스토리지 계정 연결 문자열을 포함하는 설정을 나타내며 클래스, 메서드 또는 매개 변수 수준에서 적용될 수 있습니다. 이 경우 이미 기본 스토리지 계정을 사용하고 있으므로 `StorageAccountAttribute`를 생략할 수 있습니다.

Run 메서드 정의는 이제 다음과 같이 표시됩니다.  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```
