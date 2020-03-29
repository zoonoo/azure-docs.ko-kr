---
title: 디버그 PowerShell Azure 함수 로컬
description: PowerShell을 사용하여 기능을 개발하는 방법을 이해합니다.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163763"
---
# <a name="debug-powershell-azure-functions-locally"></a>디버그 PowerShell Azure 함수 로컬

Azure 함수를 사용하면 기능을 PowerShell 스크립트로 개발할 수 있습니다.

다음과 같은 표준 개발 도구를 사용하는 모든 PowerShell 스크립트와 마찬가지로 PowerShell 함수를 로컬로 디버깅할 수 있습니다.

* [비주얼 스튜디오 코드](https://code.visualstudio.com/): 전체 PowerShell 개발 경험을 제공하는 PowerShell 확장 과 마이크로 소프트의 무료, 경량, 오픈 소스 텍스트 편집기.
* PowerShell 콘솔: 다른 PowerShell 프로세스를 디버깅하는 데 사용하는 것과 동일한 명령을 사용하여 디버그합니다.

[Azure Functions 핵심 도구는](functions-run-local.md) PowerShell 함수를 포함하여 Azure 함수의 로컬 디버깅을 지원합니다.

## <a name="example-function-app"></a>예제 함수 앱

이 문서에 사용된 함수 앱에는 단일 HTTP 트리거 함수가 있으며 다음 파일이 있습니다.

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

이 기능 응용 프로그램은 [PowerShell 빠른 시작을](functions-create-first-function-powershell.md)완료 할 때 당신이 얻을 것과 유사하다.

의 `run.ps1` 함수 코드는 다음과 같습니다.

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

## <a name="set-the-attach-point"></a>부착 점 설정

PowerShell 함수를 디버깅하려면 디버거가 연결되려면 함수를 중지해야 합니다. cmdlet 실행을 `Wait-Debugger` 중지 하 고 디버거에 대 한 대기.

다음과 같이 명령문 바로 위의 `Wait-Debugger` cmdlet에 호출을 추가하기만 하면 됩니다. `if`

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

디버깅은 명령문에서 `if` 시작됩니다. 

이제 `Wait-Debugger` Visual Studio 코드 또는 PowerShell 콘솔을 사용하여 함수를 디버깅할 수 있습니다.

## <a name="debug-in-visual-studio-code"></a>비주얼 스튜디오 코드의 디버그

Visual Studio 코드에서 PowerShell 기능을 디버깅하려면 다음을 설치해야 합니다.

* [비주얼 스튜디오 코드용 PowerShell 확장](/powershell/scripting/components/vscode/using-vscode)
* [Visual Studio Code용 Azure Functions 확장](functions-create-first-function-vs-code.md)
* [파워쉘 코어 6.2 이상](/powershell/scripting/install/installing-powershell-core-on-windows)

이러한 종속성을 설치한 후 기존 PowerShell Functions 프로젝트를 로드하거나 [첫 번째 PowerShell Functions 프로젝트를 만듭니다.](functions-create-first-function-powershell.md)

>[!NOTE]
> 프로젝트에 필요한 구성 파일이 없는 경우 추가하라는 메시지가 표시됩니다.

### <a name="set-the-powershell-version"></a>파워쉘 버전 설정

PowerShell 코어는 Windows PowerShell과 나란히 설치합니다. 비주얼 스튜디오 코드에 대한 PowerShell 확장과 함께 사용할 PowerShell 코어를 PowerShell 버전으로 설정합니다.

1. F1을 눌러 명령 팔레트를 표시한 `Session`다음 을 검색합니다.

1. **PowerShell: 세션 메뉴 표시 를 선택합니다.**

1. 현재 **세션이** **PowerShell 코어 6이**아닌 경우 **스위치를 선택합니다: PowerShell 코어 6**.

PowerShell 파일이 열려 있으면 창 오른쪽 하단에 녹색으로 표시된 버전이 표시됩니다. 이 텍스트를 선택하면 세션 메뉴도 표시됩니다. 자세한 내용은 [확장과 함께 사용할 PowerShell 버전 선택을](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)참조하십시오.

### <a name="start-the-function-app"></a>함수 앱 시작

`Wait-Debugger` 디버거를 연결하려는 함수에 설정되어 있는지 확인합니다.  추가된 `Wait-Debugger` 기능을 사용하면 Visual Studio 코드를 사용하여 함수 앱을 디버깅할 수 있습니다.

**디버그** 창을 선택한 다음 **PowerShell 기능에 연결합니다.**

![디버거](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

F5 키를 눌러 디버깅을 시작할 수도 있습니다.

시작 디버깅 작업은 다음 작업을 수행합니다.

* 터미널에서 실행되어 `func extensions install` 함수 앱에 필요한 Azure Functions 확장을 설치합니다.
* 터미널에서 실행되어 `func host start` Functions 호스트에서 함수 앱을 시작합니다.
* PowerShell 디버거를 함수 런타임 내의 PowerShell 런스페이스에 연결합니다.

>[!NOTE]
> 비주얼 스튜디오 코드에서 올바른 디버깅 환경을 보장하려면 PSWorkerInProcConcurrencyUpperBound가 1로 설정되어 있는지 확인해야 합니다. 이것이 기본값입니다.

함수 앱이 실행되면 HTTP 트리거 함수를 호출하려면 별도의 PowerShell 콘솔이 필요합니다.

이 경우 PowerShell 콘솔은 클라이언트입니다. 는 `Invoke-RestMethod` 함수를 트리거하는 데 사용됩니다.

PowerShell 콘솔에서 다음 명령을 실행합니다.

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

응답이 즉시 반환되지 않는다는 것을 알 수 있습니다. 디버거를 `Wait-Debugger` 연결하였고 PowerShell 실행이 가능한 한 빨리 중단 모드로 전환했기 때문입니다. 이는 나중에 설명하는 [BreakAll 개념](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place)때문입니다. 단추를 `continue` 누르면 디버거가 바로 다음 `Wait-Debugger`줄에 끊어집니다.

이 시점에서 디버거가 연결되며 모든 일반 디버거 작업을 수행할 수 있습니다. Visual Studio 코드에서 디버거 사용에 대한 자세한 내용은 [공식 설명서를](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)참조하십시오.

스크립트를 계속 계속 호출하면 다음 사항을 확인할 수 있습니다.

* 결과를 반환한 `Invoke-RestMethod` PowerShell 콘솔
* 비주얼 스튜디오 코드의 PowerShell 통합 콘솔이 스크립트가 실행되기를 기다리고 있습니다.

나중에 동일한 함수를 호출하면 PowerShell 확장의 디버거가 바로 `Wait-Debugger`다음 에서 끊어집니다.

## <a name="debugging-in-a-powershell-console"></a>파워쉘 콘솔에서 디버깅

>[!NOTE]
> 이 섹션에서는 Azure Functions 핵심 도구 문서를 읽고 `func host start` 함수 앱을 시작하는 명령을 사용하는 방법을 알고 있다고 [가정합니다.](functions-run-local.md)

콘솔을 열고 `cd` 함수 앱의 디렉토리에 넣고 다음 명령을 실행합니다.

```sh
func host start
```

함수 앱이 실행되고 `Wait-Debugger` 제자리에 있으면 프로세스에 연결할 수 있습니다. 두 개의 PowerShell 콘솔이 더 필요합니다.

콘솔 중 하나가 클라이언트 역할을 합니다. 이로부터 함수를 `Invoke-RestMethod` 트리거하기 위해 호출합니다. 예를 들어 다음 명령을 실행할 수 있습니다.

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

의 결과인 응답을 반환하지 않는다는 것을 알 수 `Wait-Debugger`있습니다. PowerShell 런스페이스는 이제 디버거가 연결되기를 기다리고 있습니다. 의 그 연결하자.

다른 PowerShell 콘솔에서 다음 명령을 실행합니다.

```powershell
Get-PSHostProcessInfo
```

이 cmdlet은 다음과 같은 출력과 같은 테이블을 반환합니다.

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

테이블의 항목에 `ProcessId` 대한 항목을 `ProcessName` 으로 `dotnet`기록합니다. 이 프로세스는 함수 앱입니다.

그런 다음 다음 스니펫을 실행합니다.

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

일단 시작되면 디버거는 다음과 같은 출력을 중단하고 보여줍니다.

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

이 시점에서 [PowerShell 디버거](/powershell/module/microsoft.powershell.core/about/about_debuggers). 여기에서 일반적인 디버그 작업을 모두 수행하여 단계별로 단계별로 전환하고 계속, 종료 및 기타 작업을 수행할 수 있습니다. 콘솔에서 사용할 수 있는 디버그 명령의 전체 `h` 집합을 보려면 또는 `?` 명령을 실행합니다.

`Set-PSBreakpoint` cmdlet을 사용하면 이 수준에서 중단점을 설정할 수도 있습니다.

스크립트를 계속 계속 호출하면 다음 사항을 확인할 수 있습니다.

* 실행한 `Invoke-RestMethod` PowerShell 콘솔에서 결과를 반환했습니다.
* 실행한 `Debug-Runspace` PowerShell 콘솔에서 스크립트가 실행되기를 기다리고 있습니다.

동일한 함수를 다시 호출할 수 `Invoke-RestMethod` 있으며(예: 사용) 명령 바로 `Wait-Debugger` 후에 디버거가 끊어집니다.

## <a name="considerations-for-debugging"></a>디버깅에 대한 고려 사항

Functions 코드를 디버깅할 때 다음 문제를 염두에 두어야 합니다.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`디버거가 예기치 않은 장소에서 끊어질 수 있습니다.

PowerShell 확장은 `Debug-Runspace`PowerShell의 `BreakAll` 기능을 사용합니다. 이 기능은 PowerShell이 실행되는 첫 번째 명령에서 중지하도록 지시합니다. 이 동작을 사용하면 디버깅된 런스페이스 내에서 중단점을 설정할 수 있습니다.

Azure Functions 런타임은 `run.ps1` 스크립트를 실제로 호출하기 전에 몇 가지 명령을 실행하므로 디버거가 `Microsoft.Azure.Functions.PowerShellWorker.psm1` 또는 . `Microsoft.Azure.Functions.PowerShellWorker.psd1`

이 중단이 발생하면 `continue` 또는 `c` 명령을 실행하여 이 중단점을 건너뜁니다. 그런 다음 예상되는 중단점에서 중지합니다.

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 함수 개발에 대해 자세히 알아보려면 [Azure Functions PowerShell 개발자 가이드를](functions-reference-powershell.md)참조하십시오.
