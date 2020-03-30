---
title: Azure 함수에 대한 PowerShell 개발자 참조
description: PowerShell을 사용하여 기능을 개발하는 방법을 이해합니다.
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276739"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure 함수 PowerShell 개발자 가이드

이 문서에서는 PowerShell을 사용하여 Azure 함수를 작성하는 방법에 대한 자세한 내용을 제공합니다.

PowerShell Azure 함수(함수)는 트리거될 때 실행되는 PowerShell 스크립트로 표시됩니다. 각 함수 스크립트에는 `function.json` 함수가 트리거되는 방법 및 입력 및 출력 매개 변수와 같이 함수의 작동 방식을 정의하는 관련 파일이 있습니다. 자세한 내용은 트리거 [및 바인딩 문서를](functions-triggers-bindings.md)참조하십시오. 

다른 종류의 함수와 마찬가지로 PowerShell 스크립트 함수는 파일에 정의된 모든 입력 바인딩의 이름과 `function.json` 일치하는 매개 변수를 사용합니다. 함수를 `TriggerMetadata` 시작한 트리거에 대한 추가 정보가 포함된 매개 변수도 전달됩니다.

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다. 또한 첫 번째 [PowerShell 함수를 만들기 위해 PowerShell에 대한 함수 빠른 시작을](functions-create-first-function-powershell.md) 완료해야 합니다.

## <a name="folder-structure"></a>폴더 구조

PowerShell 프로젝트에 필요한 폴더 구조는 다음과 같습니다. 이 기본값은 변경 가능합니다. 자세한 내용은 아래의 [scriptFile](#configure-function-scriptfile) 섹션을 참조하세요.

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

프로젝트의 루트에는 함수 앱을 구성하는 데 [`host.json`](functions-host-json.md) 사용할 수 있는 공유 파일이 있습니다. 각 함수에는 자체 코드 파일(.ps1) 및 바인딩`function.json`구성 파일()이 있는 폴더가 있습니다. function.json 파일의 상위 디렉터리의 이름은 항상 함수의 이름입니다.

특정 바인딩에는 파일이 `extensions.csproj` 필요합니다. [버전 2.x 및 함수](functions-versions.md) 런타임의 이후 버전에 필요한 바인딩 확장은 `extensions.csproj` `bin` 폴더에 실제 라이브러리 파일과 함께 파일에 정의됩니다. 로컬에서 개발할 때는 [바인딩 확장을 등록](functions-bindings-register.md#extension-bundles)해야 합니다. Azure Portal에서 함수를 개발할 때 이 등록이 자동으로 수행됩니다.

PowerShell 함수 앱에서 함수 앱이 `profile.ps1` 실행되기 시작할 때 선택적으로 실행되는 기능이 있을 수 있습니다(그렇지 않으면 *[콜드 스타트로](#cold-start)* 알고 있음) 자세한 내용은 [PowerShell 프로필을](#powershell-profile)참조하십시오.

## <a name="defining-a-powershell-script-as-a-function"></a>PowerShell 스크립트를 함수로 정의

기본적으로 Functions 런타임은 `run.ps1`에서 함수를 찾습니다. 여기서 `run.ps1`는 해당하는 `function.json`과 동일한 부모 디렉터리를 공유합니다.

스크립트는 실행에 대한 여러 인수를 전달합니다. 이러한 매개 변수를 처리하려면 다음 예제와 `param` 같이 스크립트 맨 위에 블록을 추가합니다.

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>트리거메타데이터 매개 변수

매개 `TriggerMetadata` 변수는 트리거에 대한 추가 정보를 제공하는 데 사용됩니다. 추가 메타데이터는 바인딩에서 바인딩까지 다양하지만 `sys` 모두 다음 데이터를 포함하는 속성을 포함합니다.

```powershell
$TriggerMetadata.sys
```

| 속성   | 설명                                     | Type     |
|------------|-------------------------------------------------|----------|
| 우크나우     | UTC에서 함수가 트리거되었을 때        | DateTime |
| MethodName | 트리거된 함수의 이름입니다.     | 문자열   |
| 랜드가드   | 이 함수 실행에 대한 고유한 지침입니다. | 문자열   |

모든 트리거 유형에는 다른 메타데이터 집합이 있습니다. 예를 들어, `$TriggerMetadata` `QueueTrigger` 에 `InsertionTime`대해 `Id` `DequeueCount`들어 있습니다. 큐 트리거의 메타데이터에 대한 자세한 내용은 [큐 트리거에 대한 공식 설명서를](functions-bindings-storage-queue-trigger.md#message-metadata)참조하십시오. 작업 중인 [트리거에](functions-triggers-bindings.md) 대한 설명서를 확인하여 트리거 메타데이터 내부에 무엇이 들어오는지 확인합니다.

## <a name="bindings"></a>바인딩

PowerShell에서 [바인딩은](functions-triggers-bindings.md) 함수의 function.json에서 구성되고 정의됩니다. Functions는 다양한 방법으로 바인딩과 상호 작용합니다.

### <a name="reading-trigger-and-input-data"></a>읽기 트리거 및 입력 데이터

트리거 및 입력 바인딩은 함수에 전달된 매개 변수로 읽습니다. 입력 바인딩에는 `direction` function.json에 `in` 대한 집합이 있습니다. 에 `name` `function.json` 정의된 속성은 `param` 블록의 매개 변수 이름입니다. PowerShell은 바인딩을 위해 명명된 매개 변수를 사용하므로 매개 변수의 순서는 중요하지 않습니다. 그러나 `function.json`에 정의된 바인딩의 순서를 따르는 것이 좋습니다.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>출력 데이터 쓰기

함수에서 출력 바인딩에는 `direction` `out` function.json에 설정이 있습니다. 함수 런타임에서 사용할 수 `Push-OutputBinding` 있는 cmdlet을 사용하여 출력 바인딩에 쓸 수 있습니다. 모든 경우에 정의된 `name` 바인딩의 속성은 `function.json` `Push-OutputBinding` cmdlet의 `Name` 매개 변수에 해당합니다.

다음은 함수 스크립트에서 `Push-OutputBinding` 호출하는 방법을 보여 줍니다.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

파이프라인을 통해 특정 바인딩에 대한 값을 전달할 수도 있습니다.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding``-Name`에 지정된 값에 따라 다르게 행동합니다.

* 지정된 이름을 유효한 출력 바인딩으로 확인할 수 없는 경우 오류가 throw됩니다.

* 출력 바인딩이 값 컬렉션을 허용하면 반복적으로 `Push-OutputBinding` 호출하여 여러 값을 푸시할 수 있습니다.

* 출력 바인딩이 단일 값만 허용하면 `Push-OutputBinding` 두 번째 호출에 오류가 발생합니다.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` 구문

다음은 호출에 `Push-OutputBinding`대한 유효한 매개 변수입니다.

| 이름 | Type | 위치 | 설명 |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | 설정하려는 출력 바인딩의 이름입니다. |
| **`-Value`** | Object | 2 | 파이프라인 ByValue에서 허용되는 설정하려는 출력 바인딩의 값입니다. |
| **`-Clobber`** | SwitchParameter | named | (선택 사항) 지정하면 지정된 출력 바인딩에 대해 값을 설정하도록 강제합니다. | 

다음과 같은 일반적인 매개 변수도 지원됩니다. 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

자세한 내용은 [공통 매개 변수 정보](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>푸시 출력 바인딩 예제: HTTP 응답

HTTP 트리거는 라는 `response`출력 바인딩을 사용 하 여 응답을 반환 합니다. 다음 예제에서 의 출력 `response` 바인딩에는 "출력 #1"의 값이 있습니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

출력은 단일 톤 값만 허용하는 HTTP이므로 두 번째로 호출될 `Push-OutputBinding` 때 오류가 throw됩니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

단일 톤 값만 허용하는 출력의 경우 `-Clobber` 매개 변수를 사용하여 컬렉션에 추가하는 대신 이전 값을 재정의할 수 있습니다. 다음 예제에서는 이미 값을 추가한 것으로 가정합니다. 다음 `-Clobber`예제의 응답을 사용하여 "출력 #3"의 값을 반환하기 위해 기존 값을 재정의합니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>푸시 출력 바인딩 예제: 큐 출력 바인딩

`Push-OutputBinding`[Azure Queue 저장소 출력 바인딩과](functions-bindings-storage-queue-output.md)같은 출력 바인딩에 데이터를 보내는 데 사용됩니다. 다음 예제에서 큐에 기록된 메시지에는 "출력 #1"의 값이 있습니다.

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

저장소 큐에 대한 출력 바인딩은 여러 출력 값을 허용합니다. 이 경우 첫 번째 쓰기 후 다음 예제를 호출하면 "출력 #1"과 "출력 #2"이라는 두 가지 항목이 있는 목록이 큐에 기록됩니다.

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

다음 예제에서는 이전 두 다음에 호출 될 때 출력 컬렉션에 두 개의 값을 더 추가 합니다.

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

큐에 기록하면 메시지에는 "출력 #1", "출력 #2", "출력 #3", "출력 #4"의 네 가지 값이 포함됩니다.

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` cmdlet

cmdlet을 `Get-OutputBinding` 사용하여 출력 바인딩에 대해 현재 설정된 값을 검색할 수 있습니다. 이 cmdlet은 해당 값의 출력 바인딩 이름을 포함하는 해시 테이블을 검색합니다. 

다음은 현재 바인딩 값을 `Get-OutputBinding` 반환하는 데 사용하는 예입니다.

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`다음 예제와 `-Name`같이 반환된 바인딩을 필터링하는 데 사용할 수 있는 "라는 매개 변수가 포함되어 있습니다.

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

와일드카드(*)는 에서 `Get-OutputBinding`지원됩니다.

## <a name="logging"></a>로깅

PowerShell 에 로그인 기능은 일반 PowerShell 로깅처럼 작동합니다. 로깅 cmdlet을 사용하여 각 출력 스트림에 쓸 수 있습니다. 각 cmdlet은 함수에서 사용하는 로그 수준으로 매핑됩니다.

| 함수 로깅 수준 | 로깅 cmdlet |
| ------------- | -------------- |
| Error | **`Write-Error`** |
| Warning | **`Write-Warning`**  | 
| 정보 | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | 정보 | _정보_ 수준 로깅에 씁니다. |
| 디버그 | **`Write-Debug`** |
| 추적 | **`Write-Progress`** <br /> **`Write-Verbose`** |

이러한 cmdlet 외에도 파이프라인에 기록된 모든 항목이 `Information` 로그 수준으로 리디렉션되고 기본 PowerShell 서식으로 표시됩니다.

> [!IMPORTANT]
> 또는 `Write-Debug` `Write-Verbose` cmdlet을 사용하는 것만으로는 자세한 내용과 디버그 수준 로깅을 볼 수 없습니다. 또한 실제로 관심 있는 로그 수준을 선언하는 로그 수준 임계값을 구성해야 합니다. 자세한 내용은 [함수 앱 로그 수준 구성을](#configure-the-function-app-log-level)참조하십시오.

### <a name="configure-the-function-app-log-level"></a>함수 앱 로그 수준 구성

Azure Functions를 사용하면 함수가 로그에 기록하는 방식을 쉽게 제어할 수 있도록 임계값 수준을 정의할 수 있습니다. 콘솔에 기록된 모든 추적에 대한 임계값을 `logging.logLevel.default` 설정하려면 [ `host.json` file][host.json 참조에서]속성을 사용합니다. 이 설정은 함수 앱의 모든 함수에 적용됩니다.

다음 예제에서는 모든 함수에 대해 자세한 로깅을 사용하도록 임계값을 설정하지만 임계값을 설정하여 다음 이라는 `MyFunction`함수에 대해 디버그 로깅을 사용하도록 설정합니다.

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

기능 앱이 Azure에서 실행 중인 경우 응용 프로그램 통찰력을 사용하여 모니터링할 수 있습니다. 함수 로그 보기 및 쿼리에 대해 자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 읽어보세요.

개발을 위해 함수 앱을 로컬로 실행하는 경우 파일 시스템에 기본으로 로그합니다. 콘솔에서 로그를 보려면 함수 앱을 `AZURE_FUNCTIONS_ENVIRONMENT` 시작하기 `Development` 전에 환경 변수를 설정합니다.

## <a name="triggers-and-bindings-types"></a>트리거 및 바인딩 유형

함수 앱에서 사용할 수 있는 여러 트리거 및 바인딩이 있습니다. 트리거 및 바인딩의 전체 목록은 [여기에서 찾을 수 있습니다.](functions-triggers-bindings.md#supported-bindings)

모든 트리거 및 바인딩은 코드에서 다음과 같은 몇 가지 실제 데이터 유형으로 표시됩니다.

* Hashtable
* 문자열
* byte[]
* int
* double
* HttpRequest컨텍스트
* HttpResponseContext

이 목록의 처음 5가지 유형은 표준 .NET 형식입니다. 마지막 두 개는 [HttpTrigger 트리거에서만](#http-triggers-and-bindings)사용됩니다.

함수의 각 바인딩 매개 변수는 이러한 형식 중 하나여야 합니다.

### <a name="http-triggers-and-bindings"></a>HTTP 트리거 및 바인딩

HTTP, 웹후크 트리거 및 HTTP 출력 바인딩은 요청 및 응답 개체를 사용하여 HTTP 메시지를 나타냅니다.

#### <a name="request-object"></a>요청 개체

스크립트에 전달되는 요청 개체는 다음과 같은 `HttpRequestContext`속성을 가지는 형식입니다.

| 속성  | 설명                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 요청의 본문을 포함하는 개체입니다. `Body`데이터를 기반으로 최상의 유형으로 직렬화됩니다. 예를 들어 데이터가 JSON인 경우 해시 테이블로 전달됩니다. 데이터가 문자열인 경우 문자열로 전달됩니다. | object |
| **`Headers`** | 요청 헤더를 포함하는 사전입니다.                | 사전<문자열,문자열><sup>*</sup> |
| **`Method`** | 요청의 HTTP 메서드입니다.                                | 문자열                    |
| **`Params`**  | 요청의 라우팅 매개 변수를 포함하는 개체입니다. | 사전<문자열,문자열><sup>*</sup> |
| **`Query`** | 쿼리 매개 변수를 포함하는 개체입니다.                  | 사전<문자열,문자열><sup>*</sup> |
| **`Url`** | 요청의 URL입니다.                                        | 문자열                    |

<sup>*</sup>모든 `Dictionary<string,string>` 키는 대/소문자를 구분하지 않습니다.

#### <a name="response-object"></a>응답 개체

다시 보내야 하는 응답 개체는 다음과 `HttpResponseContext`같은 속성을 가지는 형식입니다.

| 속성      | 설명                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | 응답의 본문을 포함하는 개체입니다.           | object                    |
| **`ContentType`** | 응답의 콘텐츠 유형을 설정하기 위한 짧은 손입니다. | 문자열                    |
| **`Headers`** | 응답 헤더를 포함하는 개체입니다.               | 사전 또는 해시 테이블   |
| **`StatusCode`**  | 응답의 HTTP 상태 코드입니다.                       | 문자열 또는 int             |

#### <a name="accessing-the-request-and-response"></a>요청 및 응답 액세스

HTTP 트리거로 작업할 때 다른 입력 바인딩과 동일한 방식으로 HTTP 요청에 액세스할 수 있습니다. 그것은 블록에. `param`

다음과 `HttpResponseContext` 같이 개체를 사용하여 응답을 반환합니다.

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

이 함수를 호출하면 다음과 같은 결과가 나오게 됩니다.

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>트리거 및 바인딩에 대한 형식 캐스팅

Blob 바인딩과 같은 특정 바인딩의 경우 매개 변수의 형식을 지정할 수 있습니다.

예를 들어 Blob 저장소의 데이터를 문자열로 제공하려면 다음 형식 `param` 캐스트를 내 블록에 추가합니다.

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell 프로필

PowerShell에는 PowerShell 프로필의 개념이 있습니다. PowerShell 프로필에 익숙하지 않은 경우 [프로필 정보](/powershell/module/microsoft.powershell.core/about/about_profiles)참조

PowerShell 함수에서 프로필 스크립트는 함수 앱이 시작될 때 실행됩니다. 함수 앱은 처음 배포될 때와 유휴[처리(콜드 시작)가](#cold-start)시작된 후 시작됩니다.

Visual Studio 코드 및 Azure Functions 핵심 도구와 같은 도구를 사용하여 `profile.ps1` 함수 앱을 만들 면 기본값이 만들어집니다. 기본 프로필은 [핵심 도구 GitHub 리포지토리에서](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) 유지되며 다음을 포함합니다.

* Azure에 대한 자동 MSI 인증.
* 원하는 경우 Azure PowerShell `AzureRM` PowerShell 별칭을 켤 수 있는 기능입니다.

## <a name="powershell-version"></a>PowerShell 버전

다음 표에서는 함수 런타임의 각 주요 버전에서 사용하는 PowerShell 버전을 보여 주며 있습니다.

| Functions 버전 | PowerShell 버전                             |
|-------------------|------------------------------------------------|
| 1.x               | 윈도우 파워 쉘 5.1 (런타임에 의해 잠겨) |
| 2.x               | 파워쉘 코어 6                              |

모든 함수에서 인쇄하여 `$PSVersionTable` 현재 버전을 볼 수 있습니다.

## <a name="dependency-management"></a>종속성 관리

함수를 사용하면 [PowerShell 갤러리를](https://www.powershellgallery.com) 활용하여 종속성을 관리할 수 있습니다. 종속성 관리를 사용하도록 설정하면 requirements.psd1 파일이 필요한 모듈을 자동으로 다운로드하는 데 사용됩니다. 다음 예제와 `managedDependency` 같이 `true` [host.json 파일의](functions-host-json.md)루트에 속성을 설정하여 이 동작을 사용하도록 설정합니다.

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

새 PowerShell 함수 프로젝트를 만들 때 Azure [ `Az` 모듈이](/powershell/azure/new-azureps-module-az) 포함된 종속성 관리가 기본적으로 활성화됩니다. 현재 지원되는 모듈의 최대 수는 10개입니다. 지원되는 구문은 _`MajorNumber`_ `.*` 다음 요구 사항과 같이 정확한 모듈 버전입니다.psd1 예제:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

requirements.psd1 파일을 업데이트하면 다시 시작한 후 업데이트된 모듈이 설치됩니다.

> [!NOTE]
> 관리되는 종속성에는 모듈을 다운로드할 www.powershellgallery.com 대한 액세스 권한이 필요합니다. 로컬로 실행하는 경우 필요한 방화벽 규칙을 추가하여 런타임이 이 URL에 액세스할 수 있는지 확인합니다. 

다음 응용 프로그램 설정을 사용하여 관리되는 종속성을 다운로드하고 설치하는 방법을 변경할 수 있습니다. 앱 업그레이드는 `MDMaxBackgroundUpgradePeriod`에서 시작되고 업그레이드 프로세스는 `MDNewSnapshotCheckPeriod`약 에서 완료됩니다.

| 함수 앱 설정              | 기본값             | 설명                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00`(7 일)     | 각 PowerShell 작업자 프로세스는 프로세스 시작 시 PowerShell 갤러리에서 `MDMaxBackgroundUpgradePeriod` 모듈 업그레이드를 확인하고 그 이후에 확인합니다. PowerShell 갤러리에서 새 모듈 버전을 사용할 수 있는 경우 파일 시스템에 설치되어 PowerShell 작업자가 사용할 수 있습니다. 이 값을 줄이면 함수 앱에서 최신 모듈 버전을 더 빨리 얻을 수 있지만 앱 리소스 사용량(네트워크 I/O, CPU, 저장소)도 증가합니다. 이 값을 늘리면 앱의 리소스 사용량이 줄어들지만 새 모듈 버전을 앱에 제공하는 것이 지연될 수도 있습니다. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00`(1시간)       | 새 모듈 버전이 파일 시스템에 설치되면 모든 PowerShell 작업자 프로세스를 다시 시작해야 합니다. PowerShell 작업자를 다시 시작하면 현재 기능 실행을 방해할 수 있으므로 앱 가용성에 영향을 줍니다. 모든 PowerShell 작업자 프로세스가 다시 시작될 때까지 함수 호출은 이전 모듈 버전또는 새 모듈 버전을 사용할 수 있습니다. 모든 PowerShell 작업자가 `MDNewSnapshotCheckPeriod`완료된 을 다시 시작합니다. 이 값을 늘리면 중단 빈도가 줄어들지만 함수 호출에서 이전 모듈 또는 새 모듈 버전을 비결정적으로 사용하는 기간도 늘어설 수 있습니다. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00`(1일)     | 자주 작업자 다시 시작시 과도한 모듈 업그레이드를 방지하기 위해 작업자가 마지막 `MDMinBackgroundUpgradePeriod`체크 인을 이미 시작한 경우 모듈 업그레이드를 확인하지 않습니다. |

사용자 지정 모듈을 활용하는 것은 일반적으로 수행하는 방법과 약간 다릅니다.

로컬 컴퓨터에서 모듈은 `$env:PSModulePath`에서 전역적으로 사용 가능한 폴더 중 하나에 설치됩니다. Azure에서 실행될 때 컴퓨터에 설치된 모듈에 액세스할 수 없습니다. 즉, PowerShell 함수 앱의 `$env:PSModulePath` 경우 `$env:PSModulePath` 일반 PowerShell 스크립트와 다릅니다.

함수에는 다음 `PSModulePath` 두 개의 경로가 포함되어 있습니다.

* 함수 `Modules` 앱의 루트에 있는 폴더입니다.
* PowerShell 언어 `Modules` 작업자가 제어하는 폴더에 대한 경로입니다.

### <a name="function-app-level-modules-folder"></a>함수 앱 `Modules` 수준 폴더

사용자 지정 모듈을 사용하려면 함수가 폴더에 종속된 `Modules` 모듈을 배치할 수 있습니다. 이 폴더에서 모듈은 함수 런타임에 자동으로 사용할 수 있습니다. 함수 앱의 모든 함수는 이러한 모듈을 사용할 수 있습니다. 

> [!NOTE]
> requirements.psd1 파일에 지정된 모듈은 자동으로 다운로드되고 경로에 포함되므로 모듈 폴더에 모듈을 포함할 필요가 없습니다. 클라우드에서 실행할 때 `$env:LOCALAPPDATA/AzureFunctions` 폴더와 `/data/ManagedDependencies` 폴더에 로컬로 저장됩니다.

사용자 지정 모듈 기능을 활용하려면 `Modules` 함수 앱의 루트에 폴더를 만듭니다. 함수에서 사용할 모듈을 이 위치에 복사합니다.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

폴더를 `Modules` 사용하면 함수 앱에 다음 폴더 구조가 있어야 합니다.

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

함수 앱을 시작하면 PowerShell 언어 작업자가 `Modules` 일반 PowerShell 스크립트에서와 마찬가지로 모듈 자동 로드에 의존할 수 `$env:PSModulePath` 있도록 이 폴더를 추가합니다.

### <a name="language-worker-level-modules-folder"></a>언어 작업자 `Modules` 수준 폴더

여러 모듈은 PowerShell 언어 작업자가 일반적으로 사용합니다. 이러한 모듈은 의 마지막 위치에 `PSModulePath`정의됩니다. 

모듈의 현재 목록은 다음과 같습니다.

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): 아카이브 작업( 예: `.zip` `.nupkg`및 기타)으로 작업하는 데 사용되는 모듈입니다.
* **ThreadJob**: PowerShell 작업 API의 스레드 기반 구현입니다.

기본적으로 함수는 이러한 모듈의 최신 버전을 사용합니다. 특정 모듈 버전을 사용하려면 해당 특정 `Modules` 버전을 함수 앱의 폴더에 넣습니다.

## <a name="environment-variables"></a>환경 변수

Functions에서 [앱 설정](functions-app-settings.md)(예: 서비스 연결 문자열)은 실행 중에 환경 변수로 노출됩니다. 다음 예제와 같이 `$env:NAME_OF_ENV_VAR`를 사용하여 이러한 설정에 액세스할 수 있습니다.

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

로컬로 실행하는 경우 앱 설정은 [local.settings.json](functions-run-local.md#local-settings-file) 프로젝트 파일에서 읽습니다.

## <a name="concurrency"></a>동시성

기본적으로 함수 PowerShell 런타임은 한 번에 하나의 함수 호출만 처리할 수 있습니다. 그러나 이 동시성 수준은 다음과 같은 상황에서는 충분하지 않을 수 있습니다.

* 동시에 많은 수의 호출을 처리하려고 할 때
* 동일한 함수 앱 내에서 다른 함수를 호출하는 함수가 있는 경우

다음 환경 변수를 정수 값으로 설정하여 이 동작을 변경할 수 있습니다.

```
PSWorkerInProcConcurrencyUpperBound
```

함수 앱의 앱 [설정에서](functions-app-settings.md) 이 환경 변수를 설정합니다.

### <a name="considerations-for-using-concurrency"></a>동시성 사용에 대한 고려 사항

PowerShell은 기본적으로 _단일 스레드_ 스크립팅 언어입니다. 그러나 동일한 프로세스에서 여러 PowerShell 런스페이스를 사용하여 동시성을 추가할 수 있습니다. 생성된 런스페이스의 양은 PSWorkerInProcConcurrencyUppercurrency바운드 응용 프로그램 설정과 일치합니다. 처리량은 선택한 계획에서 사용할 수 있는 CPU 및 메모리의 양에 의해 영향을 됩니다.

Azure PowerShell은 일부 _프로세스 수준_ 컨텍스트 및 상태를 사용하여 과도한 입력을 방지합니다. 그러나 함수 앱에서 동시성을 켜고 상태를 변경하는 작업을 호출하면 경합 조건이 종료될 수 있습니다. 한 호출은 특정 상태에 의존하고 다른 호출은 상태를 변경했기 때문에 이러한 경합 조건은 디버깅하기가 어렵습니다.

일부 작업에는 상당한 시간이 걸릴 수 있기 때문에 Azure PowerShell과 동시성으로 엄청난 가치가 있습니다. 그러나 신중하게 진행해야 합니다. 경합 조건이 의심되는 경우 PSWorkerInProcConcurrencyUpperCurrencyBound 앱 설정을 `1` 설정하고 대신 동시성을 위해 언어 작업자 프로세스 수준 [격리를](functions-app-settings.md#functions_worker_process_count) 사용합니다.

## <a name="configure-function-scriptfile"></a>기능 구성`scriptFile`

기본적으로 PowerShell 함수는 `run.ps1`에서 실행됩니다 . `function.json`

의 `scriptFile` `function.json` 속성은 다음 예제와 같은 폴더 구조를 얻는 데 사용할 수 있습니다.

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

이 경우 `function.json` for에는 `myFunction` 내보낸 함수가 `scriptFile` 실행되는 파일을 참조하는 속성이 포함됩니다.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>엔트리포인트를 구성하여 PowerShell 모듈 사용

이 문서에서는 템플릿에서 생성된 기본 `run.ps1` 스크립트 파일에 PowerShell 함수를 보여 주어 있습니다.
그러나 PowerShell 모듈에 함수를 포함할 수도 있습니다. function.json 의 구성 파일에서 `scriptFile` 및 `entryPoint` 필드를 사용하여 모듈의 특정 함수 코드를 참조할 수 있습니다.

이 경우 `entryPoint` `scriptFile`에서 참조되는 PowerShell 모듈의 함수 또는 cmdlet의 이름입니다.

다음 폴더 구조를 고려하십시오.

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

다음이 포함된 위치: `PSFunction.psm1`

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

이 예제에서 구성에 `myFunction` 대 `scriptFile` 한 `PSFunction.psm1`다른 폴더에 PowerShell 모듈을 참조 하는 속성을 포함 합니다.  속성은 `entryPoint` 모듈의 `Invoke-PSTestFunc` 진입점인 함수를 참조합니다.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

이 구성을 `Invoke-PSTestFunc` 사용하면 실행됩니다. `run.ps1`

## <a name="considerations-for-powershell-functions"></a>PowerShell 기능에 대한 고려 사항

PowerShell 함수로 작업할 때는 다음 섹션의 고려 사항에 유의하십시오.

### <a name="cold-start"></a>콜드 부팅

[서버없는 호스팅 모델에서](functions-scale.md#consumption-plan)Azure 함수를 개발할 때 콜드 스타트가 현실입니다. *콜드 스타트는* 함수 앱이 요청을 처리하기 위해 실행을 시작하는 데 걸리는 기간을 나타냅니다. 콜드 스타트는 비활성 기간 동안 함수 앱이 종료되므로 소비 계획에서 더 자주 발생합니다.

### <a name="bundle-modules-instead-of-using-install-module"></a>사용 하는 대신 번들 모듈`Install-Module`

스크립트는 모든 호출에서 실행됩니다. 스크립트에서 `Install-Module` 사용하지 마십시오. 대신 `Save-Module` 함수가 모듈을 다운로드하는 데 시간을 낭비할 필요가 없도록 게시하기 전에 사용합니다. 콜드 스타트가 함수에 영향을 미치는 경우 함수 앱을 *항상 켜져* 있거나 [프리미엄 요금제에](functions-scale.md#premium-plan)설정된 앱 서비스 [계획에](functions-scale.md#app-service-plan) 배포하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 자료를 참조하세요.

* [Azure 기능에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure 함수 트리거 및 바인딩](functions-triggers-bindings.md)

[host.json 참조]: functions-host-json.md
