---
title: Azure Functions에 대 한 PowerShell 개발자 참조
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
ms.openlocfilehash: 46b1e5c99dd86fed6f87ac3b8f0ff6555187899b
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833513"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure PowerShell 함수 개발자 가이드

이 문서에서는 PowerShell을 사용 하 여 Azure Functions를 작성 하는 방법에 대 한 정보를 제공 합니다.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

PowerShell Azure function (함수)는 트리거될 때 실행 되는 PowerShell 스크립트로 표시 됩니다. 함수 스크립트 마다 관련 `function.json` 트리거됩니다 방법 등의 함수 동작 방식을 정의 하는 파일 및 해당 입력 및 출력 매개 변수입니다. 자세한 내용은 참조는 [트리거 및 바인딩 문서](functions-triggers-bindings.md)합니다. 

다른 종류의 함수 처럼 PowerShell 스크립트 함수에 정의 된 모든 입력 바인딩의 이름과 일치 하는 매개 변수에서 사용 된 `function.json` 파일입니다. `TriggerMetadata` 매개 변수는 함수를 시작 하는 트리거 추가 정보를 포함 하는 또한으로 전달 합니다.

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다. 도 완료 해야 합니다 [powershell Functions 빠른 시작](functions-create-first-function-powershell.md) 첫 번째 PowerShell 함수를 만들려고 합니다.

## <a name="folder-structure"></a>폴더 구조

PowerShell 프로젝트에 대 한 필요한 폴더 구조는 다음과 같습니다. 이 기본값은 변경 가능합니다. 자세한 내용은 아래의 [scriptFile](#configure-function-scriptfile) 섹션을 참조하세요.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

프로젝트의 루트에 없는 공유 [ `host.json` ](functions-host-json.md) 함수 앱을 구성 하는 파일입니다. 각 함수에는 고유한 코드 파일 (.ps1) 및 바인딩 구성 파일을 사용 하 여 폴더 (`function.json`). Function.json 파일의 부모 디렉터리의 이름은 항상 함수의 이름입니다.

특정 바인딩이 필요는 `extensions.csproj` 파일입니다. 바인딩 확장에 필요 [버전 2.x](functions-versions.md) Functions 런타임의 정의 된 합니다 `extensions.csproj` 파일의 실제 라이브러리 파일을 사용 하 여를 `bin` 폴더. 로컬에서 개발할 때는 [바인딩 확장을 등록](functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles)해야 합니다. Azure Portal에서 함수를 개발할 때 이 등록이 자동으로 수행됩니다.

PowerShell 함수 앱에서 필요에 따라 했을 수 있습니다는 `profile.ps1` 함수 앱을 실행 하기 시작할 때 실행 되는 (그렇지 않은 경우으로 알고 있는  *[콜드](#cold-start)* 합니다. 자세한 내용은 [PowerShell 프로필](#powershell-profile)합니다.

## <a name="defining-a-powershell-script-as-a-function"></a>PowerShell 스크립트를 함수로 정의

기본적으로 Functions 런타임은 `run.ps1`에서 함수를 찾습니다. 여기서 `run.ps1`는 해당하는 `function.json`과 동일한 부모 디렉터리를 공유합니다.

스크립트 실행 개수의 인수를 전달 됩니다. 이러한 매개 변수를 처리 하려면 추가 `param` 블록 다음 예제와 같이 스크립트의 맨 위로 이동 합니다.

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata 매개 변수

`TriggerMetadata` 매개 변수를 사용 하는 트리거에 대 한 추가 정보를 제공 합니다. 추가 메타 데이터를 바인딩 바인딩 다르지만 포함을 `sys` 다음 데이터가 포함 된 속성:

```powershell
$TriggerMetadata.sys
```

| 자산   | Description                                     | Type     |
|------------|-------------------------------------------------|----------|
| utcNow     | 시기, utc에서는 함수가 트리거되는        | DateTime |
| MethodName | 트리거된 함수 이름     | 문자열   |
| RandGuid   | 함수 실행이 고유 guid | 문자열   |

모든 트리거 형식은 다양 한 메타 데이터입니다. 예를 들어를 `$TriggerMetadata` 에 대 한 `QueueTrigger` 포함 합니다 `InsertionTime`, `Id`, `DequeueCount`, 무엇 보다도 합니다. 큐 트리거 메타 데이터에 대 한 자세한 내용은 이동 합니다 [큐 트리거에 대 한 공식 설명서](functions-bindings-storage-queue.md#trigger---message-metadata)합니다. 설명서를 확인 합니다 [트리거](functions-triggers-bindings.md) 트리거 메타 데이터 내에서 무엇이 참조를 사용 하는 합니다.

## <a name="bindings"></a>바인딩

PowerShell에서 [바인딩](functions-triggers-bindings.md) 구성 되 고 함수의 function.json에서 정의 합니다. Functions는 다양한 방법으로 바인딩과 상호 작용합니다.

### <a name="reading-trigger-and-input-data"></a>읽기 트리거 및 입력 데이터

트리거 및 입력된 바인딩을 함수에 전달 하는 매개 변수로 읽힙니다. 입력 바인딩에 `direction` 로 `in` function.json에서. 합니다 `name` 에 정의 된 속성 `function.json` 매개 변수 이름에는 `param` 블록입니다. 바인딩에 대 한 명명 된 매개 변수를 사용 하는 PowerShell에서 매개 변수의 순서가 중요 하지 않습니다. 그러나 것이 좋습니다에 정의 된 바인딩 순서에 따라는 `function.json`합니다.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>출력 데이터 쓰기

출력 바인딩을 함수를 `direction` 로 `out` function.json의 합니다. 출력 바인딩을 사용 하 여 쓸 수는 `Push-OutputBinding` Functions 런타임을 사용할 수 있는 cmdlet입니다. 모든 경우에는 `name` 속성에 정의 된 대로 바인딩의 `function.json` 에 해당 하는 `Name` 의 매개 변수는 `Push-OutputBinding` cmdlet.

다음 호출 하는 방법을 보여 줍니다 `Push-OutputBinding` 함수 스크립트에서:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

파이프라인을 통해 특정 바인딩에 대 한 값에서도 전달할 수 있습니다.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` 지정 된 값에 따라 다르게 동작 `-Name`:

* 올바른 출력 바인딩에 지정 된 이름을 확인할 수 없으면, 오류가 throw 됩니다.

* 출력 바인딩은 값의 컬렉션을 허용 하는 경우를 호출할 수 있습니다 `Push-OutputBinding` 반복 해 서 여러 값을 푸시합니다.

* 출력 바인딩은 singleton 값을 허용 합니다 호출 `Push-OutputBinding` 를 한 번에 오류를 발생 시킵니다.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` 구문

다음은 호출에 대 한 올바른 매개 변수 `Push-OutputBinding`:

| Name | Type | Position | 설명 |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | 설정 하려는 출력 바인딩의 이름입니다. |
| **`-Value`** | Object | 2 | 출력 바인딩의 사용할 값을 설정 하려면 ByValue 파이프라인에서 허용 되는 합니다. |
| **`-Clobber`** | SwitchParameter | named | (선택 사항) 지정 하면 지정 된 출력 바인딩에 대 한 설정 값을 강제로 수행 합니다. | 

다음 일반 매개 변수 에서도 지원 됩니다. 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

자세한 내용은 [에 대 한 CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)합니다.

#### <a name="push-outputbinding-example-http-responses"></a>푸시 OutputBinding 예제: HTTP 응답

HTTP 트리거 출력 바인딩을 사용 하 여 응답을 반환 합니다. `response`합니다. 다음 예제에서 출력 바인딩을 `response` "output 1"의 값은:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

오류가 때 throw 되는 단일 값만을 허용 하는 HTTP 출력 되므로 `Push-OutputBinding` 를 두 번 호출 됩니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

단일 값만 허용 하는 출력을 사용할 수 있습니다는 `-Clobber` 매개 변수를 컬렉션에 추가 하려고 하는 대신 이전 값을 재정의 합니다. 다음 예제에서는 값을 이미 추가 했다고 가정 합니다. 사용 하 여 `-Clobber`, 다음 예제에서 응답 "출력 3"의 값을 반환 하려면 기존 값을 재정의 합니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>푸시 OutputBinding 예제: 큐 출력 바인딩

`Push-OutputBinding` 와 같은 출력 바인딩으로 설정 데이터를 보내는 데 사용 되는 [Azure Queue storage 출력 바인딩](functions-bindings-storage-queue.md#output)합니다. 다음 예제에서는 큐에 기록 되는 메시지에는 "output 1"의 값:

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

저장소 큐 출력 바인딩에 여러 출력 값을 허용 합니다. 이 경우 첫 번째 목록 항목 두 개를 사용 하 여 큐에 쓴 후 다음 예제를 호출 합니다. "output 1" 및 "출력 #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

다음 예제에서는 이전 두 호출을 출력 컬렉션에 두 가지 이상의 가치를 추가 합니다.

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

큐에 기록 될 때 메시지 이러한 네 가지 값을 포함 합니다. "output 1", "출력 #2", "3 출력" 및 "#4 output"입니다.

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` cmdlet

사용할 수는 `Get-OutputBinding` cmdlet 출력 바인딩에 대 한 현재 설정 값을 검색 합니다. 이 cmdlet에는 해당 값을 사용 하 여 출력 바인딩의 이름을 포함 하는 해시 테이블을 검색 합니다. 

다음은 사용 하는 예로 `Get-OutputBinding` 현재 바인딩 값을 반환 합니다.

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` 호출 매개 변수도 포함 `-Name`, 다음 예제와 같이 반환 된 바인딩, 필터를 사용할 수 있는:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

와일드 카드 (*)에서 지원 됩니다 `Get-OutputBinding`합니다.

## <a name="logging"></a>로깅

일반 PowerShell 로깅 같은 작동 PowerShell 함수를 로그인 합니다. 각 출력 스트림에 쓸 로깅 cmdlet을 사용할 수 있습니다. 각 cmdlet 함수에 의해 사용 되는 로그 수준에 매핑됩니다.

| 로깅 수준 함수 | 로깅 cmdlet |
| ------------- | -------------- |
| 오류 | **`Write-Error`** |
| Warning | **`Write-Warning`**  | 
| 정보 | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | 정보 | 쓸 _정보_ 수준 로깅. |
| 디버그 | **`Write-Debug`** |
| 추적 | **`Write-Progress`** <br /> **`Write-Verbose`** |

이러한 cmdlet 외에도 파이프라인으로 작성 된 모든 항목으로 리디렉션되는지를 `Information` 로그 수준 및 기본 PowerShell 형식으로 표시 합니다.

> [!IMPORTANT]
> 사용 하는 `Write-Verbose` 또는 `Write-Debug` cmdlet은 자세한 정보를 참조 하 고 디버그 수준 로깅을 사용 하기에 충분 합니다. 실제로 중요 한 로그 수준을 선언 하는 로그 수준 임계값을 구성 해야 합니다. 자세한 내용은 참조 하세요 [함수 앱 로그 수준 구성](#configure-the-function-app-log-level)합니다.

### <a name="configure-the-function-app-log-level"></a>함수 앱 로그 수준 구성

함수를 사용 하면 쉽게 컨트롤 방식으로 함수 로그를 쓸 수 있도록 임계값 수준을 정의할 수 있습니다. 콘솔에 기록 되는 모든 추적에 대 한 임계값을 설정 하려면 합니다 `logging.logLevel.default` 속성에는 [ `host.json` 파일][host.json 참조]합니다. 이 설정은 함수 앱의 모든 함수에 적용됩니다.

다음 예제에서는 모든 함수에 대 한 자세한 정보 로깅을 사용 하도록 설정 된 임계값을 설정 하지만 라는 함수에 대 한 디버그 로깅을 사용 하도록 설정 하려면 임계값 설정 `MyFunction`:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

자세한 내용은 [host.json 참조]를 참조하세요.

### <a name="viewing-the-logs"></a>로그 보기

함수 앱을 Azure에서 실행 중인 경우 Application Insights 모니터링을 사용할 수 있습니다. 함수 로그 보기 및 쿼리에 대해 자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 읽어보세요.

로컬 개발을 위한 함수 앱을 실행 하는 경우 기본 파일 시스템에 기록 합니다. 콘솔에서 로그를 보려면를 설정 합니다 `AZURE_FUNCTIONS_ENVIRONMENT` 환경 변수를 `Development` 함수 앱을 시작 하기 전에 합니다.

## <a name="triggers-and-bindings-types"></a>트리거 및 바인딩 형식

함수 앱을 사용 하는 데 사용할 수 있는 트리거 및 바인딩 여러 가지가 있습니다. 전체 목록은 트리거 및 바인딩 [확인할 수 있습니다](functions-triggers-bindings.md#supported-bindings)합니다.

모든 트리거 및 바인딩 코드에 몇 가지 실제 데이터 형식으로 표현 됩니다.

* 해시 테이블
* 문자열
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

이 목록에는 처음 다섯 개의 형식은 표준.NET 형식을 사용 하는 것입니다. 마지막 두 에서만 사용 되는 [HttpTrigger 트리거](#http-triggers-and-bindings)합니다.

각 바인딩 매개 변수를 함수에는 이러한 형식 중 하나 여야 합니다.

### <a name="http-triggers-and-bindings"></a>HTTP 트리거 및 바인딩

HTTP, 웹후크 트리거 및 HTTP 출력 바인딩은 요청 및 응답 개체를 사용하여 HTTP 메시지를 나타냅니다.

#### <a name="request-object"></a>요청 개체

스크립트에 전달 되는 요청 개체는 유형이 `HttpRequestContext`, 다음 속성이 있는:

| 자산  | Description                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 요청의 본문을 포함하는 개체입니다. `Body` 데이터를 기반으로 최상의 형식으로 serialize 됩니다. 예를 들어, 데이터가 JSON 인 경우 전달 됩니다에 해시 테이블로. 데이터 문자열인 경우에 문자열로 전달 됩니다. | 개체 |
| **`Headers`** | 요청 헤더를 포함 하는 사전입니다.                | Dictionary < 문자열, 문자열 ><sup>*</sup> |
| **`Method`** | 요청의 HTTP 메서드입니다.                                | 문자열                    |
| **`Params`**  | 요청의 라우팅 매개 변수를 포함하는 개체입니다. | Dictionary < 문자열, 문자열 ><sup>*</sup> |
| **`Query`** | 쿼리 매개 변수를 포함하는 개체입니다.                  | Dictionary < 문자열, 문자열 ><sup>*</sup> |
| **`Url`** | 요청의 URL입니다.                                        | 문자열                    |

<sup>*</sup> 모든 `Dictionary<string,string>` 키는 대/소문자 구분 합니다.

#### <a name="response-object"></a>응답 개체

다시 전송 해야 하는 응답 개체는 유형이 `HttpResponseContext`, 다음 속성이 있는:

| 자산      | Description                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | 응답의 본문을 포함하는 개체입니다.           | 개체                    |
| **`ContentType`** | 응답에 대 한 내용 유형을 설정 하는 것에 대 한 짧은 손 모양입니다. | 문자열                    |
| **`Headers`** | 응답 헤더를 포함하는 개체입니다.               | 사전 이나 해시 테이블   |
| **`StatusCode`**  | 응답의 HTTP 상태 코드입니다.                       | 문자열 또는 int             |

#### <a name="accessing-the-request-and-response"></a>요청 및 응답 액세스

HTTP 트리거를 사용 하 여 작업할 때 HTTP 요청을 다른 입력된 바인딩을 사용 하면 동일한 방식으로 액세스할 수 있습니다. 에 `param` 블록입니다.

사용 하 여는 `HttpResponseContext` 다음에 표시 된 대로 응답을 반환할 개체입니다.

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

이 함수를 호출의 결과 다음과 같습니다.

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>트리거 및 바인딩에 대 한 형식 캐스팅

특정 blob 바인딩과 마찬가지로 바인딩 여러분이 매개 변수의 형식을 지정할 수 있습니다.

예를 들어 문자열 변수로 제공 하는 Blob storage에서 데이터에 있는 추가 다음 형식으로 캐스팅 내 `param` 블록:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell 프로필

PowerShell에서 PowerShell 프로필을 개념이 있습니다. PowerShell 프로필을 사용 하 여 잘 모르는 경우 [프로필에 대 한](/powershell/module/microsoft.powershell.core/about/about_profiles)합니다.

PowerShell 함수에서 함수 앱이 시작 될 때 프로필 스크립트를 실행 합니다. 처음 배포 하 고 유휴 되 후 시작 하는 함수 앱 ([콜드](#cold-start)).

Visual Studio Code 및 Azure Functions 핵심 도구에는 기본와 같은 도구를 사용 하 여 함수 앱을 만들 때 `profile.ps1` 만들어집니다. 기본 프로필이 유지 되므로 [Core 도구 GitHub 리포지토리에서](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) 포함:

* Azure로 자동 MSI 인증 합니다.
* Azure PowerShell을 설정 하는 기능 `AzureRM` 원하는 경우 PowerShell 별칭입니다.

## <a name="powershell-version"></a>PowerShell 버전

다음 표에서 각 주 버전의 Functions 런타임 사용 되는 PowerShell 버전을 보여 줍니다.

| Functions 버전 | PowerShell 버전                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (런타임에 의해 잠김) |
| 2.x               | PowerShell Core 6                              |

인쇄 하 여 현재 버전을 확인할 수 있습니다 `$PSVersionTable` 함수에서.

## <a name="dependency-management"></a>종속성 관리

PowerShell 함수는 서비스에서 Azure 모듈 관리를 지원 합니다. Host.json를 수정 하 고 managedDependency 활성화 속성을 true로 설정 하 여 requirements.psd1 파일 처리 됩니다. 최신 Azure 모듈로 자동으로 다운로드 되 고 함수에서 사용할 수 있습니다.

host.json
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

사용자 고유의 사용자 지정을 활용 하 여 모듈 또는 모듈에서 합니다 [PowerShell 갤러리](https://powershellgallery.com) 방법을 수행 하는 일반적으로 보다 약간 다릅니다.

로컬 컴퓨터에 모듈을 설치 하는 경우에 전역적으로 사용 가능한 폴더 중 하나에 이동에 `$env:PSModulePath`입니다. Azure에서 함수 실행 되므로 컴퓨터에 설치 된 모듈에 대 한 액세스를 않아도 됩니다. 에서는이 `$env:PSModulePath` PowerShell 함수에 대 한 앱에서 다른 `$env:PSModulePath` 일반 PowerShell 스크립트에서.

함수에서 `PSModulePath` 두 경로 포함 합니다.

* `Modules` 함수 앱의 루트에 있는 폴더입니다.
* 에 대 한 경로 `Modules` 있는 PowerShell 언어 작업자 폴더입니다.

### <a name="function-app-level-modules-folder"></a>함수 앱 수준 `Modules` 폴더

PowerShell 갤러리에서 사용자 지정 모듈 또는 PowerShell 모듈을 사용 하는 함수에 종속 되는 모듈을 배치할 수 있습니다는 `Modules` 폴더입니다. 이 폴더에서 모듈 functions 런타임은 자동으로 제공 됩니다. 함수 앱의 모든 함수는 이러한 모듈을 사용할 수 있습니다.

이 기능을 활용 하려면 만듭니다는 `Modules` 함수 앱의 루트에서 폴더입니다. 이 위치에 함수에 사용 하려는 모듈을 저장 합니다.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

사용 하 여 `Save-Module` 함수를 사용 하 여 모듈의 모든 저장 하거나 사용자 고유의 사용자 지정 모듈을 복사 하 여 `Modules` 폴더. 모듈 폴더를 사용 하 여 함수 앱에는 다음 폴더 구조가 있어야 합니다.

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

PowerShell 언어 작업자 추가이 함수 앱을 시작 하면 `Modules` 폴더를 `$env:PSModulePath` 일반 PowerShell 스크립트에서와 마찬가지로 모듈 자동 로드에 사용할 수 있도록 합니다.

### <a name="language-worker-level-modules-folder"></a>언어 작업자 수준 `Modules` 폴더

여러 모듈이 PowerShell 언어에서 자주 사용 됩니다. 이러한 모듈의 마지막 위치에 정의 된 `PSModulePath`합니다. 

모듈의 현재 목록 아래와 같습니다.

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): 예: 보관, 작업에 사용 되는 모듈 `.zip`, `.nupkg`, 등.
* **ThreadJob**: PowerShell 작업 Api의 스레드 기반 구현입니다.

이러한 모듈의 최신 버전은 함수에 의해 사용 됩니다. 이러한 모듈의 특정 버전을 사용 하려면 특정 버전에 넣을 수 있습니다는 `Modules` 함수 앱의 폴더입니다.

## <a name="environment-variables"></a>환경 변수

Functions에서 [앱 설정](functions-app-settings.md)(예: 서비스 연결 문자열)은 실행 중에 환경 변수로 노출됩니다. 사용 하 여 이러한 설정에 액세스할 수 있습니다 `$env:NAME_OF_ENV_VAR`다음 예제에서와 같이:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

로컬로 실행하는 경우 앱 설정은 [local.settings.json](functions-run-local.md#local-settings-file) 프로젝트 파일에서 읽습니다.

## <a name="concurrency"></a>동시성

기본적으로 함수 PowerShell 런타임에 한 번에 하나의 함수 호출에만 처리할 수 있습니다. 그러나이 동시성 수준을 다음과 같은 상황에서 부족할 수 있습니다.

* 때 동시 호출 수가 많은 처리 하려고 합니다.
* 경우 동일한 함수 앱 내의 다른 함수를 호출 하는 함수입니다.

다음 환경 변수는 정수 값을 설정 하 여이 동작을 변경할 수 있습니다.

```
PSWorkerInProcConcurrencyUpperBound
```

이 환경 변수를 설정 합니다 [앱 설정](functions-app-settings.md) 함수 앱의 합니다.

### <a name="considerations-for-using-concurrency"></a>동시성 사용 시 고려 사항

PowerShell은는 _단일 스레드_ 기본적으로 스크립트 언어입니다. 그러나 동일한 프로세스에서 여러 PowerShell runspace를 사용 하 여 동시성을 추가할 수 있습니다. 이 기능은 Azure Functions PowerShell 런타임 작동 방식입니다.

몇 가지 단점이 있습니다가이 방법 사용 합니다.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>동시성은 우수한에서 실행 중인 컴퓨터만

함수 앱에서 실행 중인 경우는 [App Service 계획](functions-scale.md#app-service-plan) 만 지 원하는 단일 코어를 동시성 큰 도움이 되지 않습니다. 분산 하는 데 추가 코어 없습니다 부하 수 때문입니다. 이 경우 단일 코어 runspace 간의 컨텍스트 전환에 있으면 성능이 달라질 수 있습니다.

합니다 [소비 계획](functions-scale.md#consumption-plan) 하므로 동시성을 활용할 수 없습니다. 하나의 코어를 사용 하 여 실행 합니다. 동시성을 활용 하려는 경우 함수 대신 충분 한 코어를 사용 하 여 전용된 App Service 계획에서 실행 되는 함수 앱을 배포 합니다.

#### <a name="azure-powershell-state"></a>Azure PowerShell의 상태

Azure PowerShell을 사용 하 여 일부 _프로세스 수준_ 컨텍스트 및 상태 초과 입력에서 절약할 수 있도록 합니다. 그러나 함수 앱에서 동시성을 설정 하 고 상태를 변경 하는 동작 호출 될 수 있습니다 경합 상태를 사용 하 여 합니다. 이러한 경합 상태는 한 번 특정 상태에 의존 하 고 다른 호출의 상태를 변경 때문에 디버깅 하기 어렵습니다.

만한 가치가 거 대 한 Azure PowerShell을 사용 하 여 동시성 하므로 일부 작업에는 상당한 시간이 걸릴 수 있습니다. 그러나 있습니다 각별히 주의 해야 합니다. 경합 상태가 발생 하는 의심 되는 경우 동시성 설정 다시 `1` 요청을 다시 시도 하십시오.

## <a name="configure-function-scriptfile"></a>구성 함수 `scriptFile`

기본적으로 PowerShell 함수에서 실행 됩니다 `run.ps1`, 해당 같은 부모 디렉터리를 공유 하는 파일 `function.json`합니다.

`scriptFile` 속성에는 `function.json` 다음 예제와 같이 폴더 구조를 가져오는 데 사용할 수 있습니다.

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

이 경우에 `function.json` 에 대 한 `myFunction` 포함를 `scriptFile` 실행 하는 내보낸된 함수를 사용 하 여 파일을 참조 하는 속성입니다.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>EntryPoint를 구성 하 여 PowerShell 모듈을 사용 합니다.

이 문서는 기본에서 PowerShell 함수 주었습니다 `run.ps1` 템플릿에서 생성 된 스크립트 파일입니다.
그러나 PowerShell 모듈의 함수를 포함할 수도 있습니다. 사용 하 여 모듈의 특정 함수 코드를 참조할 수 있습니다 합니다 `scriptFile` 및 `entryPoint` function.json의 필드 ' 구성 파일입니다.

이 예에서 `entryPoint` 함수 또는 cmdlet에서 참조 되는 PowerShell 모듈에서의 이름인 `scriptFile`합니다.

다음 폴더 구조를 고려 합니다.

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

여기서 `PSFunction.psm1` 포함 되어 있습니다.

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

이 예제에서는 구성 `myFunction` 포함을 `scriptFile` 를 참조 하는 속성 `PSFunction.psm1`, PowerShell 모듈을 다른 폴더에는 합니다.  `entryPoint` 속성 참조는 `Invoke-PSTestFunc` 함수, 모듈의 진입점입니다.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

이 구성을 사용 하 여는 `Invoke-PSTestFunc` 와 동일 하 게 실행 되는 `run.ps1` 됩니다.

## <a name="considerations-for-powershell-functions"></a>PowerShell 함수에 대 한 고려 사항

PowerShell 함수를 사용 하 여 작업할 때 다음 섹션에서 고려 사항을 고려해 야 합니다.

### <a name="cold-start"></a>콜드 부팅

Azure Functions를 개발 하는 경우는 [서버 리스 호스팅 모델](functions-scale.md#consumption-plan), 콜드 시작은을 현실로 만드세요. *콜드 시작* 참조 기간에는 요청 처리를 실행 하려면 함수 앱에 대 한 합니다. 콜드 시작 함수 앱을 가져옵니다 비활성화 기간 동안 종료 되기 때문에 계획 하는 사용량은에서 더 자주 발생 합니다.

### <a name="bundle-modules-instead-of-using-install-module"></a>사용 하는 대신 번들 모듈 `Install-Module`

스크립트는 모든 호출에서 실행 됩니다. 사용 하지 않도록 `Install-Module` 스크립트에 있습니다. 대신 사용 하 여 `Save-Module` 함수 모듈을 다운로드 하는 시간을 낭비 하지 않아도 되도록 게시 하기 전에 합니다. 콜드 함수에 영향을, 함수 앱을 배포 해 봅니다를 [App Service 계획](functions-scale.md#app-service-plan) 로 설정 *on* 또는 [프리미엄 요금제](functions-scale.md#premium-plan-public-preview)합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)

[host.json 참조]: functions-host-json.md
