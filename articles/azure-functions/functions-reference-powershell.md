---
title: Azure Functions에 대 한 PowerShell 개발자 참조
description: PowerShell을 사용 하 여 함수를 개발 하는 방법을 이해 합니다.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 8c6f13f85b692d2405928fe06605d8b2ac0ec8e7
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012708"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell 개발자 가이드

이 문서에서는 PowerShell을 사용 하 여 Azure Functions를 작성 하는 방법에 대해 자세히 설명 합니다.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

PowerShell Azure 함수 (함수)는 트리거될 때 실행 되는 PowerShell 스크립트로 표시 됩니다. 각 함수 스크립트에는 함수의 `function.json` 동작 방식 (예: 트리거되는 방법 및 해당 입력 및 출력 매개 변수)을 정의 하는 관련 파일이 있습니다. 자세한 내용은 [트리거 및 바인딩 문서](functions-triggers-bindings.md)를 참조 하세요. 

다른 종류의 함수와 마찬가지로 PowerShell 스크립트 함수는 `function.json` 파일에 정의 된 모든 입력 바인딩의 이름과 일치 하는 매개 변수를 사용 합니다. 함수를 시작한 트리거에 대 한 추가 정보를 포함 하는 매개변수도전달됩니다.`TriggerMetadata`

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다. 또한 [powershell에 대 한 빠른 시작 함수](functions-create-first-function-powershell.md) 를 완료 하 여 첫 번째 powershell 함수를 만들었습니다.

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

프로젝트의 루트에는 함수 앱을 구성 하는 [`host.json`](functions-host-json.md) 데 사용할 수 있는 공유 파일이 있습니다. 각 함수에는 고유한 코드 파일 (ps1) 및 바인딩 구성 파일 (`function.json`)이 있는 폴더가 있습니다. 함수의 이름입니다. json 파일의 부모 디렉터리는 항상 함수의 이름입니다.

특정 바인딩에는 `extensions.csproj` 파일이 있어야 합니다. 함수 런타임의 [버전](functions-versions.md) 2.x에 필요한 바인딩 확장은 `extensions.csproj` `bin` 폴더의 실제 라이브러리 파일을 사용 하 여 파일에 정의 됩니다. 로컬에서 개발할 때는 [바인딩 확장을 등록](functions-bindings-register.md#extension-bundles)해야 합니다. Azure Portal에서 함수를 개발할 때 이 등록이 자동으로 수행됩니다.

PowerShell 함수 앱에는 함수 앱이 실행 되기 `profile.ps1` 시작할 때 실행 되는를 선택적으로 사용할 수 있습니다 (그렇지 않은 경우 *[콜드 시작](#cold-start)* 으로 인식 됨). 자세한 내용은 [PowerShell 프로필](#powershell-profile)을 참조 하세요.

## <a name="defining-a-powershell-script-as-a-function"></a>PowerShell 스크립트를 함수로 정의

기본적으로 Functions 런타임은 `run.ps1`에서 함수를 찾습니다. 여기서 `run.ps1`는 해당하는 `function.json`과 동일한 부모 디렉터리를 공유합니다.

스크립트에 실행 시 많은 인수가 전달 되었습니다. 이러한 매개 변수를 처리 하려면 다음 `param` 예제와 같이 스크립트의 맨 위에 블록을 추가 합니다.

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata 매개 변수

`TriggerMetadata` 매개 변수는 트리거에 대 한 추가 정보를 제공 하는 데 사용 됩니다. 추가 메타 데이터는 바인딩에 대 한 바인딩과 다르지만 모두 다음 데이터를 `sys` 포함 하는 속성을 포함 합니다.

```powershell
$TriggerMetadata.sys
```

| 속성   | Description                                     | 형식     |
|------------|-------------------------------------------------|----------|
| UtcNow     | UTC에서 함수가 트리거된 경우        | DateTime |
| MethodName | 트리거된 함수의 이름     | string   |
| RandGuid   | 이 함수 실행에 대 한 고유 guid입니다. | string   |

모든 트리거 형식에는 서로 다른 메타 데이터 집합이 있습니다. 예 `$TriggerMetadata` `InsertionTime`를 들어의에는, `Id` ,등이포함됩니다.`DequeueCount` `QueueTrigger` 큐 트리거의 메타 데이터에 대 한 자세한 내용은 [큐 트리거의 공식 설명서](functions-bindings-storage-queue.md#trigger---message-metadata)로 이동 하세요. 작업 중인 [트리거에](functions-triggers-bindings.md) 대 한 설명서를 확인 하 여 트리거 메타 데이터 내에 있는 항목을 확인 합니다.

## <a name="bindings"></a>바인딩

PowerShell에서 [바인딩은](functions-triggers-bindings.md) 함수의 함수 json에서 구성 및 정의 됩니다. Functions는 다양한 방법으로 바인딩과 상호 작용합니다.

### <a name="reading-trigger-and-input-data"></a>읽기 트리거 및 입력 데이터

트리거 및 입력 바인딩은 함수로 전달 되는 매개 변수로 읽혀집니다. 입력 바인딩에는 함수 `direction` json에서 `in` 로 설정 된가 있습니다. `param` 에 `name` 정의된속성은블록에있는매개`function.json` 변수의 이름입니다. PowerShell에서 바인딩에 명명 된 매개 변수를 사용 하므로 매개 변수의 순서가 중요 하지 않습니다. 그러나에 `function.json`정의 된 바인딩의 순서를 따르는 것이 가장 좋습니다.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>출력 데이터 쓰기

함수에서 출력 바인딩은 `direction` json에서를로 `out` 설정 합니다. `Push-OutputBinding` Cmdlet을 사용 하 여 함수 런타임에 사용할 수 있는 출력 바인딩에 쓸 수 있습니다. 모든 경우에 `function.json` 정의 된 `name` 대로 바인딩의 속성은 `Push-OutputBinding` cmdlet의 `Name` 매개 변수에 해당 합니다.

다음은 함수 스크립트에서를 `Push-OutputBinding` 호출 하는 방법을 보여 줍니다.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

파이프라인을 통해 특정 바인딩에 대 한 값을 전달할 수도 있습니다.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`는에 대해 지정 된 값에 따라 `-Name`다르게 동작 합니다.

* 지정 된 이름을 유효한 출력 바인딩으로 확인할 수 없으면 오류가 throw 됩니다.

* 출력 바인딩에서 값의 컬렉션을 허용 하는 경우에는를 `Push-OutputBinding` 반복적으로 호출 하 여 여러 값을 푸시할 수 있습니다.

* 출력 바인딩에서 singleton 값만 허용 하는 경우 두 번째 `Push-OutputBinding` 를 호출 하면 오류가 발생 합니다.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding`구문과

다음은를 호출 `Push-OutputBinding`하기 위한 유효한 매개 변수입니다.

| 이름 | 형식 | 위치 | 설명 |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | 설정 하려는 출력 바인딩의 이름입니다. |
| **`-Value`** | Object | 2 | 파이프라인 ByValue에서 허용 되는 설정 하려는 출력 바인딩의 값입니다. |
| **`-Clobber`** | SwitchParameter | 명명됨 | 필드 지정 된 경우 지정 된 출력 바인딩에 대해 값이 설정 되도록 합니다. | 

또한 다음과 같은 일반 매개 변수가 지원 됩니다. 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

자세한 내용은 [About CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)을 (를) 참조 하세요.

#### <a name="push-outputbinding-example-http-responses"></a>푸시 OutputBinding 예: HTTP 응답

HTTP 트리거는 이라는 `response`출력 바인딩을 사용 하 여 응답을 반환 합니다. 다음 예제에서의 `response` 출력 바인딩에는 "output #1" 값이 있습니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

출력은 단일 항목만 허용 하는 HTTP에 대 한 것 이므로를 두 번째로 호출 하면 `Push-OutputBinding` 오류가 throw 됩니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Singleton 값만 허용 하는 출력의 경우 컬렉션에를 `-Clobber` 추가 하는 대신 매개 변수를 사용 하 여 이전 값을 재정의할 수 있습니다. 다음 예에서는 값을 이미 추가 했다고 가정 합니다. 다음 예제의 `-Clobber`응답은를 사용 하 여 기존 값을 재정의 하 여 "output #3" 값을 반환 합니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>푸시 OutputBinding 예: 큐 출력 바인딩

`Push-OutputBinding`는 [Azure Queue storage 출력 바인딩과](functions-bindings-storage-queue.md#output)같은 출력 바인딩에 데이터를 전송 하는 데 사용 됩니다. 다음 예제에서는 큐에 기록 된 메시지의 값이 "output #1"입니다.

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

저장소 큐에 대 한 출력 바인딩은 여러 출력 값을 허용 합니다. 이 경우 첫 번째 이후에 다음 예제를 호출 하면 "output #1" 및 "output #2"의 두 항목이 포함 된 목록이 큐에 기록 됩니다.

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

다음 예제는 이전 두 번째 이후에 호출 될 때 출력 컬렉션에 두 개의 값을 더 합니다.

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

큐에 기록 될 때 메시지는 "output #1", "output #2", "output #3" 및 "output #4"의 네 가지 값을 포함 합니다.

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding`#a0

Cmdlet을 `Get-OutputBinding` 사용 하 여 출력 바인딩에 대해 현재 설정 된 값을 검색할 수 있습니다. 이 cmdlet은 해당 값을 사용 하 여 출력 바인딩의 이름을 포함 하는 해시 테이블을 검색 합니다. 

다음은를 사용 `Get-OutputBinding` 하 여 현재 바인딩 값을 반환 하는 예입니다.

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`또한에는 다음 예제 `-Name`와 같이 반환 된 바인딩을 필터링 하는 데 사용할 수 있는 라는 매개 변수가 포함 되어 있습니다.

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

와일드 카드 (*)는에서 `Get-OutputBinding`지원 됩니다.

## <a name="logging"></a>로깅

PowerShell 함수의 로깅은 일반적인 PowerShell 로깅과 같은 방식으로 작동 합니다. 로깅 cmdlet을 사용 하 여 각 출력 스트림에 쓸 수 있습니다. 각 cmdlet은 함수에서 사용 하는 로그 수준에 매핑됩니다.

| 함수 로깅 수준 | 로깅 cmdlet |
| ------------- | -------------- |
| Error | **`Write-Error`** |
| 경고 | **`Write-Warning`**  | 
| 정보 | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | 정보 | _정보_ 수준 로깅에 씁니다. |
| 디버그 | **`Write-Debug`** |
| 추적 | **`Write-Progress`** <br /> **`Write-Verbose`** |

이러한 cmdlet 외에도 파이프라인에 쓰여진 모든 항목은 `Information` 로그 수준으로 리디렉션되고 기본 PowerShell 서식으로 표시 됩니다.

> [!IMPORTANT]
> `Write-Verbose` 또는`Write-Debug` cmdlet을 사용 하는 것 만으로는 자세한 정보와 디버그 수준 로깅을 볼 수 없습니다. 또한 실제로 관심 있는 로그 수준을 선언 하는 로그 수준 임계값을 구성 해야 합니다. 자세히 알아보려면 [함수 앱 로그 수준 구성](#configure-the-function-app-log-level)을 참조 하세요.

### <a name="configure-the-function-app-log-level"></a>함수 앱 로그 수준 구성

Azure Functions를 사용 하면 함수에서 로그에 쓰는 방식을 쉽게 제어할 수 있도록 임계값 수준을 정의할 수 있습니다. 콘솔에 기록 된 모든 추적에 대 한 임계값을 설정 하려면 `logging.logLevel.default` [ `host.json` 파일][host.json 참조]에서 속성을 사용 합니다. 이 설정은 함수 앱의 모든 함수에 적용됩니다.

다음 예에서는 모든 함수에 대해 자세한 정보 로깅을 사용 하도록 임계값을 설정 하지만 라는 `MyFunction`함수에 대해 디버그 로깅을 사용 하도록 임계값을 설정 합니다.

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

함수 앱 Azure에서 실행 중인 경우 Application Insights를 사용 하 여 모니터링할 수 있습니다. 함수 로그 보기 및 쿼리에 대해 자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 읽어보세요.

개발을 위해 함수 앱를 로컬로 실행 하는 경우 로그는 기본적으로 파일 시스템에 기록 됩니다. 콘솔에서 로그를 보려면 함수 앱를 시작 하기 전에 `AZURE_FUNCTIONS_ENVIRONMENT` 환경 변수를 `Development` 로 설정 합니다.

## <a name="triggers-and-bindings-types"></a>트리거 및 바인딩 형식

함수 앱에서 사용할 수 있는 여러 가지 트리거와 바인딩이 있습니다. 트리거와 바인딩의 전체 목록은 [여기에서 찾을 수 있습니다](functions-triggers-bindings.md#supported-bindings).

모든 트리거와 바인딩은 코드에서 몇 가지 실제 데이터 형식으로 표현 됩니다.

* 테이블
* string
* byte[]
* ssNoversion
* double
* HttpRequestContext
* HttpResponseContext

이 목록에서 처음 5 개 형식은 표준 .NET 형식입니다. 마지막 두 개는 [httptrigger 트리거에서](#http-triggers-and-bindings)만 사용 됩니다.

함수의 각 바인딩 매개 변수는 다음 형식 중 하나 여야 합니다.

### <a name="http-triggers-and-bindings"></a>HTTP 트리거 및 바인딩

HTTP, 웹후크 트리거 및 HTTP 출력 바인딩은 요청 및 응답 개체를 사용하여 HTTP 메시지를 나타냅니다.

#### <a name="request-object"></a>요청 개체

스크립트에 전달 되는 request 개체의 형식은 `HttpRequestContext`다음과 같습니다.

| 속성  | Description                                                    | 형식                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 요청의 본문을 포함하는 개체입니다. `Body`는 데이터에 따라 가장 적합 한 형식으로 직렬화 됩니다. 예를 들어 데이터가 JSON 인 경우 hashtable로 전달 됩니다. 데이터가 문자열 인 경우 문자열로 전달 됩니다. | 개체(object) |
| **`Headers`** | 요청 헤더를 포함 하는 사전입니다.                | 사전 < 문자열, 문자열 ><sup>*</sup> |
| **`Method`** | 요청의 HTTP 메서드입니다.                                | string                    |
| **`Params`**  | 요청의 라우팅 매개 변수를 포함하는 개체입니다. | 사전 < 문자열, 문자열 ><sup>*</sup> |
| **`Query`** | 쿼리 매개 변수를 포함하는 개체입니다.                  | 사전 < 문자열, 문자열 ><sup>*</sup> |
| **`Url`** | 요청의 URL입니다.                                        | string                    |

<sup>*</sup>모든 `Dictionary<string,string>` 키는 대/소문자를 구분 하지 않습니다.

#### <a name="response-object"></a>응답 개체

다시 전송 해야 하는 응답 개체는 다음과 같은 속성을 `HttpResponseContext`포함 하는 형식입니다.

| 속성      | Description                                                 | 형식                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | 응답의 본문을 포함하는 개체입니다.           | 개체(object)                    |
| **`ContentType`** | 응답의 콘텐츠 형식을 설정 하는 데 사용할 짧은 손입니다. | string                    |
| **`Headers`** | 응답 헤더를 포함하는 개체입니다.               | 사전 또는 해시 테이블   |
| **`StatusCode`**  | 응답의 HTTP 상태 코드입니다.                       | 문자열 또는 int             |

#### <a name="accessing-the-request-and-response"></a>요청 및 응답 액세스

HTTP 트리거를 사용 하는 경우 다른 입력 바인딩과 동일한 방식으로 HTTP 요청에 액세스할 수 있습니다. `param` 블록에 있습니다.

다음과 같이 `HttpResponseContext` 개체를 사용 하 여 응답을 반환 합니다.

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

이 함수를 호출한 결과는 다음과 같습니다.

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>트리거 및 바인딩에 대 한 형식 캐스팅

Blob 바인딩과 같은 특정 바인딩의 경우 매개 변수의 형식을 지정할 수 있습니다.

예를 들어 문자열로 제공 된 Blob storage의 데이터를 포함 하려면 내 `param` 블록에 다음 형식 캐스트를 추가 합니다.

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell 프로필

Powershell에는 PowerShell 프로필 이라는 개념이 있습니다. PowerShell 프로필에 대해 잘 모르는 경우 [프로필 정보](/powershell/module/microsoft.powershell.core/about/about_profiles)를 참조 하세요.

PowerShell 함수에서 프로필 스크립트는 함수 앱이 시작 될 때 실행 됩니다. 함수 앱은 처음 배포 될 때 및 유휴 상태 ([콜드](#cold-start)부팅) 후에 시작 됩니다.

Visual Studio Code 및 Azure Functions Core Tools와 같은 도구를 사용 하 여 함수 앱을 만들면 기본값이 `profile.ps1` 만들어집니다. 기본 프로필은 [핵심 도구 GitHub 리포지토리에서](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) 유지 관리 되며 다음을 포함 합니다.

* Azure에 대 한 자동 MSI 인증.
* 원하는 경우 Azure PowerShell `AzureRM` PowerShell 별칭을 켜는 기능입니다.

## <a name="powershell-version"></a>PowerShell 버전

다음 표에서는 함수 런타임의 각 주 버전에서 사용 하는 PowerShell 버전을 보여 줍니다.

| Functions 버전 | PowerShell 버전                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (런타임에 의해 잠김) |
| 2.x               | PowerShell Core 6                              |

모든 함수에서 인쇄 `$PSVersionTable` 하 여 현재 버전을 확인할 수 있습니다.

## <a name="dependency-management"></a>종속성 관리

PowerShell 함수는 서비스에의 한 Azure 모듈 관리를 지원 합니다. Host-a를 수정 하 고 managedDependency enabled 속성을 true로 설정 하면 psd1 파일이 처리 됩니다. 최신 Azure 모듈은 자동으로 다운로드 되 고 함수에서 사용할 수 있게 됩니다.

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

사용자 고유의 사용자 지정 모듈 또는 모듈을 [PowerShell 갤러리](https://powershellgallery.com) 에서 활용 하는 것은 일반적으로 수행 하는 방법과 약간 다릅니다.

로컬 컴퓨터에 모듈을 설치 하면에서 `$env:PSModulePath`전역적으로 사용 가능한 폴더 중 하나로 이동 합니다. 함수가 Azure에서 실행 되기 때문에 컴퓨터에 설치 된 모듈에는 액세스할 수 없습니다. 이를 위해서는 `$env:PSModulePath` powershell 함수 앱에 대 한이 일반 `$env:PSModulePath` powershell 스크립트의와 달라 야 합니다.

함수에는 `PSModulePath` 두 개의 경로가 포함 되어 있습니다.

* 함수 앱의 루트에 있는 폴더입니다.`Modules`
* PowerShell 언어 작업자 내 `Modules` 에 상주 하는 폴더에 대 한 경로입니다.

### <a name="function-app-level-modules-folder"></a>함수 앱 수준 `Modules` 폴더

PowerShell 갤러리에서 사용자 지정 모듈 또는 PowerShell 모듈을 사용 하려면 함수가 `Modules` 폴더에 종속 되는 모듈을 저장할 수 있습니다. 이 폴더에서 모듈은 함수 런타임에 자동으로 사용할 수 있습니다. 함수 앱의 모든 함수는 이러한 모듈을 사용할 수 있습니다.

이 기능을 활용 하려면 함수 앱의 루트 `Modules` 에 폴더를 만듭니다. 이 위치의 함수에서 사용 하려는 모듈을 저장 합니다.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

를 `Save-Module` 사용 하 여 함수에서 사용 하는 모든 모듈을 저장 하거나 사용자 지정 모듈 `Modules` 을 해당 폴더에 복사 합니다. Modules 폴더를 사용 하는 경우 함수 앱은 다음과 같은 폴더 구조를 가져야 합니다.

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

함수 앱을 시작 하는 경우 powershell 언어 작업자는이 `Modules` 폴더 `$env:PSModulePath` 를에 추가 하므로 일반 powershell 스크립트에서와 마찬가지로 모듈 자동 로드에 의존할 수 있습니다.

### <a name="language-worker-level-modules-folder"></a>언어 작업자 수준 `Modules` 폴더

여러 모듈은 PowerShell 언어 작업자에서 일반적으로 사용 됩니다. 이러한 모듈은의 `PSModulePath`마지막 위치에 정의 됩니다. 

모듈의 현재 목록은 다음과 같습니다.

* [Microsoft. PowerShell Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): `.zip`, `.nupkg`등의 보관 작업에 사용 되는 모듈입니다.
* **ThreadJob**: PowerShell 작업 Api의 스레드 기반 구현입니다.

이러한 모듈의 최신 버전은 함수에서 사용 됩니다. 이러한 모듈의 특정 버전을 사용 하려면 함수 앱의 `Modules` 폴더에 특정 버전을 배치할 수 있습니다.

## <a name="environment-variables"></a>환경 변수

Functions에서 [앱 설정](functions-app-settings.md)(예: 서비스 연결 문자열)은 실행 중에 환경 변수로 노출됩니다. 다음 예제와 같이를 사용 `$env:NAME_OF_ENV_VAR`하 여 이러한 설정에 액세스할 수 있습니다.

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

로컬로 실행하는 경우 앱 설정은 [local.settings.json](functions-run-local.md#local-settings-file) 프로젝트 파일에서 읽습니다.

## <a name="concurrency"></a>동시성

기본적으로 함수 PowerShell 런타임은 한 번에 하나의 함수 호출만 처리할 수 있습니다. 그러나 다음과 같은 경우에는이 동시성 수준으로 충분 하지 않을 수 있습니다.

* 동시에 다 수의 호출을 처리 하려고 하는 경우
* 동일한 함수 앱 내에서 다른 함수를 호출 하는 함수가 있는 경우

다음 환경 변수를 정수 값으로 설정 하 여이 동작을 변경할 수 있습니다.

```
PSWorkerInProcConcurrencyUpperBound
```

함수 앱의 [앱 설정](functions-app-settings.md) 에서이 환경 변수를 설정 합니다.

### <a name="considerations-for-using-concurrency"></a>동시성 사용에 대 한 고려 사항

PowerShell은 기본적으로 _단일 스레드_ 스크립팅 언어입니다. 그러나 동일한 프로세스에서 여러 PowerShell runspace을 사용 하 여 동시성을 추가할 수 있습니다. 이 기능은 Azure Functions PowerShell 런타임이 작동 하는 방식입니다.

이 접근 방식에는 몇 가지 단점이 있습니다.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>동시성은 실행 중인 컴퓨터와 동일 하 게 작동 합니다.

함수 앱이 단일 코어만 지 원하는 [App Service 계획](functions-scale.md#app-service-plan) 에서 실행 되는 경우에는 동시성이 그다지 유용 하지 않습니다. 부하를 분산 하는 데 도움이 되는 추가 코어가 없기 때문입니다. 이 경우 단일 코어가 runspace 간에 컨텍스트 전환 되어야 하는 경우 성능이 달라질 수 있습니다.

[소비 계획](functions-scale.md#consumption-plan) 은 코어를 하나만 사용 하 여 실행 되므로 동시성을 활용할 수 없습니다. 동시성을 최대한 활용 하려면 충분 한 코어가 있는 전용 App Service 계획에서 실행 되는 함수 앱에 함수를 배포 합니다.

#### <a name="azure-powershell-state"></a>Azure PowerShell 상태

Azure PowerShell은 몇 가지 _프로세스 수준_ 컨텍스트 및 상태를 사용 하 여 과도 한 입력을 저장 하는 데 도움이 됩니다. 그러나 함수 앱에서 동시성을 설정 하 고 상태를 변경 하는 작업을 호출 하는 경우 경합 상태가 발생할 수 있습니다. 이러한 경합 상태는 한 호출이 특정 상태를 사용 하 고 다른 호출이 상태를 변경 하기 때문에 디버깅 하기 어렵습니다.

일부 작업에는 상당한 시간이 걸릴 수 있으므로 Azure PowerShell 동시성에 때 엄청난 값이 있습니다. 그러나 주의 해야 합니다. 경합 상태가 발생 한 것으로 의심 되는 경우 PSWorkerInProcConcurrencyUpperBound 앱 설정을로 `1` 설정 하 고 대신 동시성에 대해 [언어 작업자 프로세스 수준 격리](functions-app-settings.md#functions_worker_process_count) 를 사용 합니다.

## <a name="configure-function-scriptfile"></a>Configure 함수`scriptFile`

기본적으로 PowerShell 함수는 해당 `run.ps1` `function.json`와 동일한 부모 디렉터리를 공유 하는 파일에서 실행 됩니다.

의 속성을 사용 하 여 다음 예제와 같은 폴더 구조를 가져올 수있습니다.`function.json` `scriptFile`

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

이 경우의 `myFunction` 에는 `function.json` 실행할 내보낸 함수가 `scriptFile` 있는 파일을 참조 하는 속성이 포함 됩니다.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>EntryPoint를 구성 하 여 PowerShell 모듈 사용

이 문서에서는 템플릿에서 생성 된 기본 `run.ps1` 스크립트 파일에 PowerShell 함수를 보여 줍니다.
그러나 PowerShell 모듈에 함수를 포함할 수도 있습니다. 함수 json 구성 파일의 및 `scriptFile` `entryPoint` 필드를 사용 하 여 모듈에서 특정 함수 코드를 참조할 수 있습니다.

이 경우 `entryPoint` 는에서 `scriptFile`참조 되는 PowerShell 모듈에 있는 함수 또는 cmdlet의 이름입니다.

다음 폴더 구조를 고려 하십시오.

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

여기 `PSFunction.psm1` 에는 다음이 포함 됩니다.

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

이 예제에서에 대 한 `myFunction` 구성에는 다른 폴더의 PowerShell 모듈인를 참조 `PSFunction.psm1`하는 `scriptFile` 속성이 포함 되어 있습니다.  속성 `entryPoint` 은 모듈의 `Invoke-PSTestFunc` 진입점 인 함수를 참조 합니다.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

이 구성을 `Invoke-PSTestFunc` 사용 하면가 그대로 `run.ps1` 실행 됩니다.

## <a name="considerations-for-powershell-functions"></a>PowerShell 함수에 대 한 고려 사항

PowerShell 함수를 사용 하는 경우 다음 섹션의 고려 사항에 유의 하세요.

### <a name="cold-start"></a>콜드 부팅

서버를 사용 하지 않는 [호스팅 모델](functions-scale.md#consumption-plan)에서 Azure Functions를 개발 하는 경우 콜드 시작은 현실입니다. *콜드 시작* 은 함수 앱이 요청을 처리 하기 위해 실행 되기 시작 하는 데 걸리는 시간을 나타냅니다. 비활성 기간 동안 함수 앱이 종료 되기 때문에 소비 계획에서 콜드 시작이 더 자주 발생 합니다.

### <a name="bundle-modules-instead-of-using-install-module"></a>를 사용 하는 대신 번들 모듈`Install-Module`

모든 호출에서 스크립트가 실행 됩니다. 스크립트에서 `Install-Module` 를 사용 하지 마십시오. 대신 함수 `Save-Module` 에서 모듈 다운로드 시간을 낭비 하지 않아도 되도록 게시 하기 전에를 사용 합니다. 콜드 시작이 함수에 영향을 주는 경우에는 *always on* 또는 [프리미엄 계획](functions-scale.md#premium-plan)으로 설정 된 [App Service 계획](functions-scale.md#app-service-plan) 에 함수 앱을 배포 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하십시오.

* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)

[host.json 참조]: functions-host-json.md
