---
title: 로컬로 PowerShell Azure Functions 디버그
description: 로컬로 실행할 때 PowerShell 함수를 디버깅하는 방법에 대해 알아봅니다.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a668024db126c82f96756555aba513b77f7d7366
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422963"
---
# <a name="debug-powershell-azure-functions-locally"></a>로컬로 PowerShell Azure Functions 디버그

Azure Functions를 통해 PowerShell 스크립트로 함수를 개발할 수 있습니다.

다음 표준 개발 도구를 사용하여 PowerShell 스크립트와 마찬가지로 PowerShell 함수를 로컬로 디버그할 수 있습니다.

* [Visual Studio Code](https://code.visualstudio.com/): 전체 PowerShell 개발 환경을 제공하는 PowerShell 확장이 포함된 Microsoft의 무료, 경량 및 오픈 소스 텍스트 편집기입니다.
* PowerShell 콘솔: 다른 PowerShell 프로세스를 디버깅하는 데 사용하는 것과 동일한 명령을 사용하여 디버그합니다.

[Azure Functions Core Tools](functions-run-local.md)는 PowerShell 함수를 비롯한 Azure Functions의 로컬 디버깅을 지원합니다.

## <a name="example-function-app"></a>함수 앱 예

이 문서에 사용된 함수 앱에는 단일 HTTP 트리거 함수와 다음 파일이 있습니다.

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

이 함수 앱은 [PowerShell 빠른 시작](./create-first-function-vs-code-powershell.md)을 완료할 때 얻는 것과 비슷합니다.

`run.ps1`의 함수 코드는 다음 스크립트와 같습니다.

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

PowerShell 함수를 디버깅하려면 디버거를 연결하기 위해 함수를 중지해야 합니다. `Wait-Debugger` cmdlet은 실행을 중지하고 디버거를 대기합니다.

>[!NOTE]
>PowerShell 7을 사용하는 경우 코드에서 `Wait-Debugger` 호출을 추가할 필요가 없습니다.

다음과 같이 `if` 문 바로 위에 `Wait-Debugger` cmdlet에 대한 호출을 추가하기만 하면 됩니다.

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

디버깅은 `if` 문에서 시작합니다. 

이제 `Wait-Debugger`를 사용하여 Visual Studio Code 또는 PowerShell 콘솔을 사용하여 함수를 디버그할 수 있습니다.

## <a name="debug-in-visual-studio-code"></a>Visual Studio Code에서 디버그

Visual Studio Code에서 PowerShell 함수를 디버깅하려면 다음이 설치되어 있어야 합니다.

* [Visual Studio Code용 PowerShell 확장](/powershell/scripting/components/vscode/using-vscode)
* [Visual Studio Code용 Azure Functions 확장](./create-first-function-cli-powershell.md)
* [PowerShell Core 6.2 이상](/powershell/scripting/install/installing-powershell-core-on-windows)

이러한 종속성을 설치한 후 기존 PowerShell 함수 프로젝트를 로드하거나 [첫 번째 Powershell Functions 프로젝트를 만듭니다](./create-first-function-vs-code-powershell.md).

>[!NOTE]
> 프로젝트에 필요한 구성 파일이 없는 경우 추가하라는 메시지가 표시됩니다.

### <a name="set-the-powershell-version"></a>Azure PowerShell 버전 설정

PowerShell Core는 Windows PowerShell과 나란히 설치됩니다. Powershell Core를 Visual Studio Code PowerShell 확장과 함께 사용할 PowerShell 버전으로 설정합니다.

1. F1 키를 눌러 명령 팔레트를 표시한 다음 `Session`을 검색합니다.

1. **PowerShell: 세션 메뉴 표시** 를 선택합니다.

1. **현재 세션** 이 **PowerShell Core 6** 이 아닌 경우 **전환: PowerShell Core 6** 을 선택합니다.

PowerShell 파일이 열려 있으면 창의 오른쪽 아래에 녹색으로 표시된 버전이 표시됩니다. 이 텍스트를 선택하면 세션 메뉴도 표시됩니다. 자세히 알아보려면 [확장과 함께 사용할 PowerShell 버전 선택](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)을 참조하세요.

### <a name="start-the-function-app"></a>함수 앱 시작

디버거를 연결하려는 함수에 `Wait-Debugger`가 설정되어 있는지 확인합니다.  `Wait-Debugger`를 추가하면 Visual Studio Code를 사용하여 함수 앱을 디버그할 수 있습니다.

**디버그** 창을 선택한 다음 **PowerShell 함수에 연결** 합니다.

![디버거](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

F5 키를 눌러 디버깅을 시작할 수도 있습니다.

디버깅 시작 작업은 다음 작업을 수행합니다.

* 터미널에서 `func extensions install`을 실행하여 함수 앱에 필요한 Azure Functions 확장을 설치합니다.
* 터미널에서 `func host start`를 실행하여 함수 호스트에서 함수 앱을 시작합니다.
* Powershell 디버거를 함수 런타임 내의 PowerShell Runspace에 연결합니다.

>[!NOTE]
> Visual Studio Code에서 올바른 디버깅 환경을 보장하려면 PSWorkerInProcConcurrencyUpperBound가 1로 설정되어 있는지 확인해야 합니다. 이것이 기본값입니다.

함수 앱을 실행하는 경우 HTTP 트리거된 함수를 호출하는 별도의 PowerShell 콘솔이 필요합니다.

이 경우 PowerShell 콘솔은 클라이언트입니다. `Invoke-RestMethod`는 함수를 트리거하는 데 사용됩니다.

PowerShell 콘솔에서 다음 명령을 실행합니다.

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

응답이 즉시 반환되지 않는다는 것을 알게 됩니다. `Wait-Debugger`가 디버거를 연결했고 PowerShell 실행이 가능한 한 빨리 중단 모드로 전환되었기 때문입니다. 이는 나중에 설명할 [BreakAll 개념](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place)으로 인해 발생합니다. `continue` 단추를 누르면 디버거가 `Wait-Debugger` 바로 다음 줄에서 중단됩니다.

이 시점에서 디버거가 연결되고 모든 일반 디버거 작업을 수행할 수 있습니다. Visual Studio Code에서 디버거 사용 방법에 대한 자세한 내용은 [공식 문서](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)를 참조하세요.

계속해서 스크립트를 완전히 호출하면 다음을 확인할 수 있습니다.

* `Invoke-RestMethod`을 수행한 PowerShell 콘솔에서 결과를 반환했습니다.
* Visual Studio Code의 PowerShell 통합 콘솔에서 스크립트 실행을 기다리는 중입니다.

나중에 동일한 함수를 호출하면 PowerShell 확장의 디버거가 `Wait-Debugger` 바로 뒤에서 중단됩니다.

## <a name="debugging-in-a-powershell-console"></a>PowerShell 콘솔에서 디버깅

>[!NOTE]
> 이 섹션에서는 [Azure Functions Core Tools 문서](functions-run-local.md)를 읽고 `func host start` 명령을 사용하여 함수 앱을 시작하는 방법을 알고 있다고 가정합니다.

콘솔을 열고 `cd` 함수 앱의 디렉터리에 다음 명령을 실행합니다.

```sh
func host start
```

함수 앱이 실행 중이고 `Wait-Debugger`가 제자리에 있는 경우 프로세스에 연결할 수 있습니다. 두 개 이상의 PowerShell 콘솔이 필요합니다.

콘솔 중 하나가 클라이언트 역할을 합니다. 여기에서 `Invoke-RestMethod`를 호출하여 함수를 트리거합니다. 예를 들어 다음 명령을 실행할 수 있습니다.

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

`Wait-Debugger`의 결과인 응답이 반환되지 않는다는 것을 알 수 있습니다. 이제 PowerShell Runspace는 디버거가 연결될 때까지 대기 중입니다. 이를 연결해 보겠습니다.

다른 PowerShell 콘솔에서 다음 명령을 실행합니다.

```powershell
Get-PSHostProcessInfo
```

이 cmdlet은 다음 출력과 같은 테이블을 반환합니다.

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

`ProcessName`이 `dotnet`인 테이블의 항목에 `ProcessId`를 고려합니다. 이 프로세스는 함수 앱입니다.

그러면 다음 코드 조각을 실행합니다.

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

시작되면 디버거가 중단되고 다음과 같은 출력이 표시됩니다.

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

이 시점에서는 [PowerShell 디버거](/powershell/module/microsoft.powershell.core/about/about_debuggers)의 중단점에서 중지됩니다. 여기에서 일반적인 디버그 작업, 프로시저 단위 실행, 한 단계씩 코드 실행, 계속, 종료 및 기타 작업을 수행할 수 있습니다. 콘솔에서 사용할 수 있는 디버그 명령의 전체 집합을 확인하려면 `h` 또는 `?` 명령을 실행합니다.

`Set-PSBreakpoint` cmdlet을 사용하여 이 수준에서 중단점을 설정할 수도 있습니다.

계속해서 스크립트를 완전히 호출하면 다음을 확인할 수 있습니다.

* 이제 `Invoke-RestMethod`를 실행한 PowerShell 콘솔에서 결과가 반환되었습니다.
* `Debug-Runspace`를 실행한 PowerShell 콘솔에서 스크립트가 실행될 때까지 기다리고 있습니다.

예를 들어 `Invoke-RestMethod`를 사용하여 동일한 함수를 다시 호출할 수 있으며 디버거는 `Wait-Debugger` 명령 바로 뒤에서 중단됩니다.

## <a name="considerations-for-debugging"></a>디버깅 고려 사항

함수 코드를 디버그할 때는 다음 문제를 염두에 두어야 합니다.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`을 사용하면 디버거가 예기치 않은 장소에서 중단될 수 있습니다.

PowerShell 확장은 `Debug-Runspace`를 사용하며, 이는 PowerShell의 `BreakAll` 기능을 사용합니다. 이 기능은 실행되는 첫 번째 명령에서 PowerShell을 중지하도록 지시합니다. 이 동작은 디버깅된 Runspace 내에서 중단점을 설정할 수 있는 기회를 제공합니다.

Azure Functions 런타임은 실제로 `run.ps1` 스크립트를 호출하기 전에 몇 가지 명령을 실행하므로 `Microsoft.Azure.Functions.PowerShellWorker.psm1` 또는 `Microsoft.Azure.Functions.PowerShellWorker.psd1` 내에서 디버거가 중단될 수 있습니다.

이러한 중단이 발생하면 `continue` 또는 `c` 명령을 실행하여 이 중단점을 건너뜁니다. 그런 다음 예상 중단점에서 중지합니다.

## <a name="troubleshooting"></a>문제 해결

디버깅하는 동안 문제가 발생하는 경우 다음 사항을 확인해야 합니다.

| 확인 | 작업 |
|------|------|
| 터미널에서 `func --version`을 실행합니다. `func`을 찾을 수 없다는 오류가 발생하는 경우 지역 `path` 변수에서 핵심 도구(func.exe)가 누락된 것일 수 있습니다.| [핵심 도구를 다시 설치](functions-run-local.md#v2)합니다.|  
| Visual Studio Code 기본 터미널에는 func.exe에 대한 액세스 권한이 있어야 합니다. Linux용 Windows 하위 시스템(WSL)과 같이 핵심 도구를 설치하지 않은 기본 터미널을 사용하고 있는 것은 아닌지 확인합니다.  | Visual Studio Code의 기본 셸을 PowerShell 7(권장) 또는 Windows PowerShell 5.1로 설정합니다.|
  

## <a name="next-steps"></a>다음 단계

Powershell을 이용해 Functions를 개발하는 방법에 대해 자세히 알아보려면 [Azure Functions PowerShell 개발자 가이드](functions-reference-powershell.md)를 참조하세요.
