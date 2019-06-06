---
title: PowerShell Azure Functions를 로컬로 디버그
description: PowerShell을 사용 하 여 functions를 개발 하는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha, glenga
ms.openlocfilehash: f02d2eed2030a5d2f54b3bee85885bbb572ac762
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476680"
---
# <a name="debug-powershell-azure-functions-locally"></a>PowerShell Azure Functions를 로컬로 디버그

Azure Functions를 사용 하면 PowerShell 스크립트와 함수를 개발할 수 있습니다.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

다음 표준 개발 도구를 사용 하 여 모든 PowerShell 스크립트와 마찬가지로 PowerShell 함수를 로컬로 디버깅할 수 있습니다.

* [Visual Studio Code](https://code.visualstudio.com/): 전체 PowerShell 개발 환경을 제공 하는 PowerShell 확장을 사용 하 여 Microsoft의 무료 및 오픈 소스 텍스트 편집기입니다.
* PowerShell 콘솔: 다른 PowerShell 프로세스를 디버깅 하는 데 사용 하는 동일한 명령을 사용 하 여 디버그 합니다.

[Azure Functions 핵심 도구](functions-run-local.md) PowerShell 함수를 포함 하 여 Azure Functions의 로컬 디버깅을 지원 합니다.

## <a name="example-function-app"></a>함수 앱의 예

이 문서에 사용 되는 함수 앱에는 단일 HTTP에서 트리거한 함수가 있고 다음 파일:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

이 함수 앱을 완료 하면 비슷합니다는 [PowerShell 빠른 시작](functions-create-first-function-powershell.md)합니다.

함수 코드에서 `run.ps1` 다음 스크립트는 같습니다.

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>연결 지점 설정

모든 PowerShell 함수를 디버그 하려면 함수를 연결할 디버거에 대 한 중지 해야 합니다. `Wait-Debugger` cmdlet 실행을 중지 하 고 디버거를 기다립니다.

호출을 추가 하기만 하면 되는 `Wait-Debugger` cmdlet 바로 위에 `if` 문은 다음과 같이:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

부터 디버깅을 `if` 문입니다. 

사용 하 여 `Wait-Debugger` 곳에서 디버깅할 수 있습니다 이제 Visual Studio Code 또는 PowerShell 콘솔을 사용 하는 함수입니다.

## <a name="debug-in-visual-studio-code"></a>Visual Studio Code에서 디버깅

Visual Studio Code에서 PowerShell 함수를 디버깅 하려면 다음이 설치 되어 있어야 합니다.

* [Visual Studio Code 용 PowerShell 확장](/powershell/scripting/components/vscode/using-vscode)
* [Visual Studio Code용 Azure Functions 확장](functions-create-first-function-vs-code.md)
* [PowerShell Core 6.2 이상](/powershell/scripting/install/installing-powershell#powershell-core)

이러한 종속성을 설치한 후 기존 PowerShell Functions 프로젝트를 로드 하거나 [첫 번째 PowerShell 함수 프로젝트를 만들](functions-create-first-function-powershell.md)합니다.

>[!NOTE]
> 프로젝트가 없어야 필요한 구성 파일을 추가할 표시 됩니다.

### <a name="set-the-powershell-version"></a>PowerShell 버전 설정

PowerShell Core는 Windows PowerShell와 함께 설치 됩니다. PowerShell Core Visual Studio Code에 대 한 PowerShell 확장을 사용 하는 PowerShell 버전으로 설정 합니다.

1. 명령 팔레트를 표시 한 다음 검색 F1 키를 눌러 `Session`합니다.

1. 선택 **PowerShell: 세션 메뉴 표시**합니다.

1. 경우에 **현재 세션** 되지 **PowerShell Core 6**, 선택 **전환: PowerShell Core 6**합니다.

PowerShell 파일이 열려 있으면 창의 오른쪽 맨 아래에서 녹색으로 표시 되는 버전을 표시 됩니다. 세션 메뉴를 표시도이 텍스트를 선택 합니다. 자세한 내용은 참조는 [확장을 사용 하는 데 powershell 버전 선택](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)합니다.

### <a name="start-the-function-app"></a>함수 앱 시작

확인 `Wait-Debugger` 디버거를 연결 하려는 함수에 설정 됩니다.  사용 하 여 `Wait-Debugger` 추가 Visual Studio Code를 사용 하 여 함수 앱을 디버그할 수 있습니다.

선택 된 **디버그** 창 차례로 **PowerShell 함수를 연결할**합니다.

![디버거](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

또한 디버깅을 시작 하려면 F5 키를 눌러 수 있습니다.

다음 작업을 수행 하는 작업을 디버깅을 시작 합니다.

* 실행 `func extensions install` 함수 앱에 필요한 모든 Azure Functions 확장을 설치 하려면 터미널에서.
* 실행 `func host start` Functions 호스트의 함수 앱을 시작 하려면 터미널에서.
* 함수 런타임 내에서 PowerShell runspace에 PowerShell 디버거를 연결 합니다.

실행 중인 함수 앱을 사용 하 여 HTTP 트리거 함수를 호출 하는 별도 PowerShell 콘솔을 해야 합니다.

이 경우 PowerShell 콘솔은 클라이언트. `Invoke-RestMethod` 함수를 트리거하는 합니다.

PowerShell 콘솔에서 다음 명령을 실행 합니다.

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

보면 응답을 즉시 반환 되지 않습니다. 있기 때문입니다 `Wait-Debugger` 디버거 및 PowerShell에 연결 수 있으므로 즉시 실행을 중단 모드로 발생 합니다. 때문에 이것이 합니다 [BreakAll 개념](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), 나중에 설명 되어 있는 합니다. 키를 누르면 합니다 `continue` 단추를 디버거에서 이제 줄에서 중단 한 후 오른쪽 `Wait-Debugger`.

이 시점에서 디버거가 연결 되어 있고 모든 일반 디버거 작업을 수행할 수 있습니다. Visual Studio Code에서 디버거 사용에 대 한 자세한 내용은 참조 하세요. [공식 설명서](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)합니다.

계속 하 고 스크립트를 완벽 하 게 호출 후 알려줍니다.

* PowerShell 콘솔을 수행 하는 `Invoke-RestMethod` 결과 반환 했습니다
* Visual Studio Code에서 PowerShell 통합 콘솔을 실행 하는 스크립트를 기다리는 중입니다.

나중에 바로 뒤 중단 PowerShell 확장에서 디버거 같은 함수를 호출 하는 경우는 `Wait-Debugger`합니다.

## <a name="debugging-in-a-powershell-console"></a>PowerShell 콘솔에서 디버깅

>[!NOTE]
> 이 섹션에서는 읽었다고 가정 합니다 [Azure Functions 핵심 도구 docs](functions-run-local.md) 사용 하는 방법을 배우고는 `func host start` 함수 앱을 시작 하는 명령을 합니다.

콘솔을 열고 `cd` 다음 명령 실행 하 고 함수 앱의 디렉터리에 있습니다.

```sh
func host start
```

실행 중인 함수 앱을 사용 하 여 및 `Wait-Debugger` 준비에서 프로세스에 연결할 수 있습니다. 두 개의 자세한 PowerShell 콘솔을 수행 해야 합니다.

콘솔 중 클라이언트 처럼 작동합니다. 호출에서이 `Invoke-RestMethod` 함수를 트리거할 수 있습니다. 예를 들어, 다음 명령을 실행할 수 있습니다.

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

결과 응답을 반환 하지 않기를 보면의 `Wait-Debugger`합니다. PowerShell runspace 이제 기다리고 디버거를 연결할 수 있습니다. 연결을 살펴보겠습니다.

다른 PowerShell 콘솔에서 다음 명령을 실행 합니다.

```powershell
Get-PSHostProcessInfo
```

이 cmdlet은 다음 출력과 같은 테이블을 반환 합니다.

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

기록해는 `ProcessId` 표에 항목에 대 한는 `ProcessName` 으로 `dotnet`입니다. 이 프로세스는 함수 앱.

다음 코드 조각을 실행 합니다.

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

시작 되 면 디버거가 중단 하 고 다음 출력과 같이 보여 줍니다.

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

이 시점에서 중단점에서 중지 하는 [PowerShell 디버거](/powershell/module/microsoft.powershell.core/about/about_debuggers)합니다. 여기에서 있습니다 수 일반적인 디버깅 작업의 모든 작업을 수행, 프로시저 단위로 실행, 한 단계씩 코드 실행, 계속, 종료, 등입니다. 콘솔에서 사용할 수 있는 디버그 명령의 전체 집합을 실행 합니다 `h` 또는 `?` 명령입니다.

사용 하 여이 수준에서 중단점을 설정할 수도 있습니다는 `Set-PSBreakpoint` cmdlet.

계속 하 고 스크립트를 완벽 하 게 호출 되 면 알려줍니다.

* PowerShell 콘솔을 실행 하는 위치 `Invoke-RestMethod` 이제 결과 반환 했습니다.
* PowerShell 콘솔을 실행 하는 위치 `Debug-Runspace` 실행할 스크립트를 기다리는 중입니다.

동일한 함수를 다시 호출할 수 있습니다 (사용 하 여 `Invoke-RestMethod` 예를 들어) 직후에 디버거가 중단을 `Wait-Debugger` 명령입니다.

## <a name="considerations-for-debugging"></a>디버깅에 대 한 고려 사항

다음과 같은 문제가 때 염두 함수 코드를 디버깅 합니다.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` 예기치 않은 위치에서 중단 되도록 디버거를 발생할 수 있습니다.

PowerShell 확장 사용 `Debug-Runspace`에 PowerShell의 요소에 의존 하는데 `BreakAll` 기능입니다. 이 기능은 PowerShell에서 실행 되는 첫 번째 명령은 중지 하도록 지시 합니다. 이 동작은 디버깅된 runspace 내에서 중단점을 설정할 수 있는 기회를 제공 합니다.

Azure Functions 런타임은 실제로 호출 하기 전에 몇 가지 명령을 실행 하 `run.ps1` 디버거 내에서 주요 끝나고 있으므로 스크립트를 `Microsoft.Azure.Functions.PowerShellWorker.psm1` 또는 `Microsoft.Azure.Functions.PowerShellWorker.psd1`합니다.

실행이 중단 발생 해야 합니다 `continue` 또는 `c` 명령을이 중단점을 건너뜁니다. 그런 다음 예상된 중단점에서 중지 합니다.

## <a name="next-steps"></a>다음 단계

PowerShell을 사용 하 여 함수를 개발 하는 방법에 대 한 자세한 내용은 참조 하세요 [Azure Functions PowerShell 개발자 가이드](functions-reference-powershell.md)합니다.
