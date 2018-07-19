---
title: Azure Functions 핵심 도구 작업 | Microsoft Docs
description: Azure Functions에서 실행하기 전에 로컬 머신의 명령 프롬프트 및 터미널에서 Azure Functions를 코딩하고 테스트하는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/26/2018
ms.author: glenga
ms.openlocfilehash: 5c582b080ec6f2cff801758fc4bff4f7d07fd7df
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083072"
---
# <a name="work-with-azure-functions-core-tools"></a>Azure Functions 핵심 도구 작업

Azure Functions 핵심 도구를 사용하여 명령 프롬프트 또는 터미널의 로컬 머신에서 함수를 개발하고 테스트할 수 있습니다. 로컬 함수는 라이브 Azure 서비스에 연결할 수 있고 사용자는 전체 Functions 런타임을 사용하여 로컬 머신에서 함수를 디버깅할 수 있습니다. Azure 구독에 함수 앱을 배포할 수도 있습니다.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

## <a name="core-tools-versions"></a>핵심 도구 버전

Azure Functions 핵심 도구에는 두 가지 버전이 있습니다. 사용 중인 버전은 로컬 개발 환경, 선택한 언어 및 필요한 지원 수준에 따라 달라집니다.

+ [버전 1.x](#v1): GA(일반 공급) 상태인 1.x 버전의 런타임을 지원합니다. 이 버전의 도구는 Windows 컴퓨터에서만 지원되며 [npm 패키지](https://docs.npmjs.com/getting-started/what-is-npm)에서 설치됩니다. 이 버전을 사용하면 공식적으로 지원되지 않는 실험적 언어로 함수를 만들 수 있습니다. 자세한 내용은 [Azure Functions에서 지원되는 언어](supported-languages.md)를 참조하세요.

+ [버전 2.x](#v2): 런타임 버전 2.x를 지원합니다. 이 버전은 [Windows](#windows-npm), [macOS](#brew) 및 [Linux](#linux)를 지원합니다. 설치에 플랫폼별 패키지 관리자 또는 npm을 사용합니다. 2.x 런타임과 같이 이 버전의 핵심 도구는 현재 미리 보기 상태입니다.

언급이 없는 경우 이 아티클의 예제는 2.x 버전용입니다.

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions 핵심 도구 설치

[Azure Functions 핵심 도구]에는 로컬 개발 컴퓨터에서 실행할 수 있는 Azure Functions 런타임을 제공하는 동일한 런타임 버전이 포함됩니다. 또한 함수를 만들고, Azure에 연결하고, 함수 프로젝트를 배포하는 명령을 제공합니다.

### <a name="v1"></a>버전 1.x

원래 버전의 도구는 Functions 1.x 런타임을 사용합니다. 이 버전은 .NET Framework(4.7.1)를 사용하며 Windows 컴퓨터에서만 지원됩니다. 버전 1.x 도구를 설치하려면 먼저 npm이 포함된 [NodeJS를 설치](https://docs.npmjs.com/getting-started/installing-node)해야 합니다.

다음 명령을 사용하여 버전 1.x 도구를 설치합니다.

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>버전 2.x

>[!NOTE]
> Azure Functions 런타임 2.0은 미리 보기 상태이며, 현재 Azure Functions의 일부 기능은 지원되지 않습니다. 자세한 내용은 [Azure Functions 버전](functions-versions.md)을 참조하세요. 

버전 2.x 도구는 .NET Core를 기반으로 하는 Azure Functions 런타임 2.x를 사용합니다. 이 버전은 [Windows](#windows-npm), [macOS](#brew) 및 [Linux](#linux)를 포함하여 .NET Core 2.x에서 지원하는 모든 플랫폼에서 지원됩니다.

#### <a name="windows-npm"></a>Windows

다음 단계에서는 npm을 사용하여 Windows에 핵심 도구를 설치합니다. [Chocolatey](https://chocolatey.org/)를 사용할 수도 있습니다. 자세한 내용은 [핵심 도구 추가 정보](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)를 참조하세요.

1. [Windows용 .NET Core 2.0](https://www.microsoft.com/net/download/windows)을 설치합니다.

2. [Node.js](npm 포함)를 설치합니다. 버전 2.x 도구의 경우 Node.js 8.5 이상 버전만 지원됩니다.

3. 다음과 같이 핵심 도구 패키지를 설치합니다.

    ```bash
    npm install -g azure-functions-core-tools@core
    ```

#### <a name="brew"></a>Homebrew가 있는 MacOS

다음 단계에서는 Homebrew를 사용하여 macOS에 핵심 도구를 설치합니다.

1. [macOS용 .NET Core 2.0](https://www.microsoft.com/net/download/macos)을 설치합니다.

2. 아직 설치되지 않은 경우 [Homebrew](https://brew.sh/)를 설치합니다.

3. 다음과 같이 핵심 도구 패키지를 설치합니다.

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a>APT가 있는 Linux(Debian/Ubuntu)

다음 단계에서는 [APT](https://wiki.debian.org/Apt)를 사용하여 Ubuntu/Debian Linux 배포판에 핵심 도구를 설치합니다. 다른 Linux 배포판의 경우 [핵심 도구 추가 정보](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux)를 참조하세요.

1. [Linux용 .NET Core 2.0](https://www.microsoft.com/net/download/linux)을 설치합니다.

2. Microsoft 제품 키를 신뢰할 수 있는 키로 등록합니다.

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Ubuntu 서버가 아래 표에서 적절한 버전 중 하나를 실행하고 있는지 확인합니다. Apt 원본을 추가하려면 다음을 실행합니다.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Linux 배포 | 버전 |
    | --------------- | ----------- |
    | Ubuntu 17.10    | `artful`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

4. 다음과 같이 핵심 도구 패키지를 설치합니다.

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

## <a name="create-a-local-functions-project"></a>로컬 Functions 프로젝트 만들기

함수 프로젝트 디렉터리에는 개별 함수에 대한 코드가 포함된 하위 폴더와 함께 [host.json](functions-host-json.md) 및 [local.settings.json](#local-settings-file) 파일이 포함됩니다. 이 디렉터리는 Azure의 함수 앱에 해당합니다. Functions 폴더 구조에 대한 자세한 내용은 [Azure Functions 개발자 가이드](functions-reference.md#folder-structure)를 참조하세요.

버전 2.x는 초기화될 때 프로젝트에 대한 기본 언어를 선택해야 하고 추가된 모든 기능은 기본 언어 템플릿을 사용합니다. 버전 1.x에서는 함수를 만들 때마다 언어를 지정합니다.

터미널 창이나 명령 프롬프트에서 다음 명령을 실행하여 프로젝트 및 로컬 Git 리포지토리를 만듭니다.

```bash
func init MyFunctionProj
```

버전 2.x에서는 명령을 실행할 때 프로젝트에 대한 런타임을 선택해야 합니다. JavaScript 함수를 개발하려는 경우 **노드**를 선택합니다.

```output
Select a worker runtime:
dotnet
node
```

위쪽/아래쪽 화살표 키를 사용하여 언어를 선택한 다음, Enter 키를 누릅니다. 출력은 JavaScript 프로젝트에 대한 다음 예제와 유사합니다.

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

로컬 Git 리포지토리 없이 프로젝트를 만들려면 `--no-source-control [-n]` 옵션을 사용합니다.

## <a name="register-extensions"></a>확장 등록

Azure Functions 런타임의 2.x 버전에서는 함수 앱에서 사용하는 바인딩 확장(바인딩 형식)을 명시적으로 등록해야 합니다.

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

자세한 내용은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md#register-binding-extensions)을 참조하세요.

## <a name="local-settings-file"></a>로컬 설정 파일

local.settings.json 파일은 앱 설정, 연결 문자열 및 Azure Functions 핵심 도구에 대한 설정을 저장합니다. 구조는 다음과 같습니다.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*"
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```

| 설정      | 설명                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | **true**로 설정하면 모든 값은 로컬 컴퓨터 키를 사용하여 암호화됩니다. `func settings` 명령과 함께 사용됩니다. 기본값은 **false**입니다. |
| **값** | 로컬에서 실행될 때 사용되는 연결 문자열 및 응용 프로그램 설정의 컬렉션입니다. 이러한 값은 **AzureWebJobsStorage** 및 **AzureWebJobsDashboard**와 같은 Azure의 함수 앱에서 앱 설정에 해당합니다. 많은 트리거와 바인딩에는 [Blob 저장소 트리거](functions-bindings-storage-blob.md#trigger---configuration)에 대한 **Connection**과 같은 연결 문자열 앱 설정을 참조하는 속성이 있습니다. 이러한 속성의 경우 **Values** 배열에 정의된 응용 프로그램 설정이 필요합니다. <br/>**AzureWebJobsStorage**는 HTTP 이외의 트리거에 대한 필수 앱 설정입니다. [Azure Storage 에뮬레이터](../storage/common/storage-use-emulator.md)를 로컬로 설치한 경우 **AzureWebJobsStorage**를 `UseDevelopmentStorage=true`에 설정할 수 있으며 이 에뮬레이터는 핵심 도구가 사용합니다. 개발 중에 유용하지만 배포 전에 실제 저장소 연결을 테스트해야 합니다. |
| **호스트** | 이 섹션의 설정은 로컬에서 실행할 때 Functions 호스트 프로세스를 사용자 지정합니다. |
| **LocalHttpPort** | 로컬 Functions 호스트(`func host start` 및 `func run`)를 실행할 때 사용되는 기본 포트를 설정합니다. `--port` 명령줄 옵션이 이 값보다 우선합니다. |
| **CORS** | [CORS(원본 간 리소스 공유)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)에 허용된 원본을 정의합니다. 원본은 공백 없이 쉼표로 구분된 목록으로 제공됩니다. 와일드카드 값(\*)이 지원되므로 모든 원본에서 요청할 수 있습니다. |
| **ConnectionStrings** | 함수 바인딩에서 사용하는 연결 문자열에 대해 이 컬렉션을 사용하지 마십시오. 이 컬렉션은 [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx) 같은 구성 파일의 **ConnectionStrings** 섹션에서 연결 문자열을 가져와야 하는 프레임 워크에서만 사용합니다. 이 개체의 연결 문자열은 공급자 유형이 [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)인 환경에 추가됩니다. 이 컬렉션의 항목은 다른 앱 설정을 사용하여 Azure에 게시되지 않습니다. 이러한 값을 함수 앱에 대한 **응용 프로그램 설정**의 **연결 문자열** 섹션에 명시적으로 추가해야 합니다. |

이 함수 앱 설정 값은 코드에서 환경 변수로 읽을 수도 있습니다. 자세한 내용은 다음 언어별 참조 항목의 Environment 변수 섹션을 참조하세요.

+ [미리 컴파일된 C#](functions-dotnet-class-library.md#environment-variables)
+ [C# 스크립트(.csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

local.settings.json 파일의 설정은 로컬에서 실행할 때 Functions 도구에서만 사용됩니다. 기본적으로 이러한 설정은 프로젝트가 Azure에 게시될 때 자동으로 마이그레이션되지 않습니다. [게시할 때](#publish) `--publish-local-settings` 스위치를 사용하여 이러한 설정이 Azure의 함수 앱에 추가되었는지 확인합니다. **ConnectionStrings**의 값은 절대 게시되지 않습니다.

**AzureWebJobsStorage**에 유효한 저장소 연결 문자열이 설정되어 있지 않고 에뮬레이터가 사용되지 않으면 다음 오류 메시지가 표시됩니다.  

>local.settings.json에 AzureWebJobsStorage 값이 없습니다. 이 값은 HTTP 이외의 모든 트리거에 필요합니다. 'func azure functionapp fetch-app-settings <functionAppName>'를 실행하거나 local.settings.json에서 연결 문자열을 지정할 수 있습니다. 

### <a name="get-your-storage-connection-strings"></a>저장소 연결 문자열 가져오기

개발을 위해 저장소 에뮬레이터를 사용하는 경우라도 실제 저장소 연결을 테스트하고 싶을 수 있습니다. 이미 [저장소 계정을 만든](../storage/common/storage-create-storage-account.md) 것으로 가정하면 다음 방법 중 하나에서 유효한 저장소 연결 문자열을 가져올 수 있습니다.

+ [Azure Portal] 저장소 계정으로 이동하여 **설정**에서 **액세스 키**를 선택한 다음, **연결 문자열** 값 중 하나를 복사합니다.

  ![Azure Portal에서 연결 문자열 복사](./media/functions-run-local/copy-storage-connection-portal.png)

+ [Azure Storage 탐색기](http://storageexplorer.com/)를 사용하여 Azure 계정에 연결합니다. **탐색기**에서 구독을 확장하고, 저장소 계정을 선택하고, 기본 또는 보조 연결 문자열을 복사합니다. 

  ![Storage 탐색기에서 연결 문자열 복사](./media/functions-run-local/storage-explorer.png)

+ 다음 중 한 명령을 사용하여 Azure에서 연결 문자열을 다운로드하려면 핵심 도구를 사용합니다.

    + 기존 함수 앱에서 모든 설정을 다운로드합니다.

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    + 특정 저장소 계정에 대한 연결 문자열을 가져옵니다.

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    
    Azure에 로그인하지 않은 경우 수행하라는 메시지가 나타납니다.

## <a name="create-func"></a>함수 만들기

함수를 만들려면 다음 명령을 실행합니다.

```bash
func new
```

버전 2.x에서 `func new`를 실행하는 경우 함수 앱의 기본 언어에서 템플릿을 선택하라는 메시지가 나타난 다음, 함수에 대한 이름을 선택하라는 메시지도 표시됩니다. 버전 1.x에서는 언어를 선택하라는 메시지도 표시됩니다.

```output
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
```

함수 코드는 다음 큐 트리거 출력에서 알 수 있듯이 제공된 함수 이름으로 하위 폴더에 생성됩니다.

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

다음 인수를 사용하는 명령에서 이러한 옵션을 지정할 수도 있습니다.

| 인수     | 설명                            |
| ------------------------------------------ | -------------------------------------- |
| **`--language -l`**| C#, F# 또는 JavaScript와 같은 템플릿 프로그래밍 언어 이 옵션은 버전 1.x에서 필요합니다. 버전 2.x에서는 이 옵션을 사용하지 않거나 프로젝트의 기본 언어를 선택합니다. |
| **`--template -t`** | 템플릿 이름은 다음 값 중 하나일 수 있습니다.<br/><ul><li>`Blob trigger`</li><li>`Cosmos DB trigger`</li><li>`Event Grid trigger`</li><li>`HTTP trigger`</li><li>`Queue trigger`</li><li>`SendGrid`</li><li>`Service Bus Queue trigger`</li><li>`Service Bus Topic trigger`</li><li>`Timer trigger`</li></ul> |
| **`--name -n`** | 함수 이름 |

예를 들어 단일 명령에서 JavaScript HTTP 트리거를 만들려면 다음을 실행합니다.

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

단일 명령에서 큐 트리거 함수를 만들려면 다음을 실행합니다.

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>로컬로 함수 실행

Functions 프로젝트를 실행하려면 Functions 호스트를 실행합니다. 이 호스트는 프로젝트의 모든 함수에 대한 트리거를 활성화합니다.

```bash
func host start
```

`func host start`는 다음 옵션을 지원합니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | 수신 대기할 로컬 포트입니다. 기본값: 7071 |
| **`--debug <type>`** | [Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started) 또는 [Visual Studio 2017](functions-dotnet-class-library.md)에서 **func.exe** 프로세스에 연결할 수 있도록 디버그 포트를 열어둔 호스트를 시작합니다. *\<형식\>* 옵션은 `VSCode` 및 `VS`입니다.  |
| **`--cors`** | CORS 원본의 공백 없이 쉼표로 구분된 목록입니다. |
| **`--nodeDebugPort -n`** | 사용할 노드 디버거의 포트입니다. 기본값: launch.json 값 또는 5858 |
| **`--debugLevel -d`** | 콘솔 추적 수준입니다(해제, 자세한 정보 표시, 정보, 경고 또는 오류). 기본값: 정보|
| **`--timeout -t`** | Functions 호스트를 시작할 제한 시간(초)입니다. 기본값: 20초|
| **`--useHttps`** | `http://localhost:{port}`가 아닌 `https://localhost:{port}`에 바인딩합니다. 기본적으로 이 옵션은 사용자 컴퓨터에 신뢰할 수 있는 인증서를 만듭니다.|
| **`--pause-on-error`** | 프로세스를 종료하기 전에 추가 입력에 대해 일시 중지합니다. Visual Studio 또는 VS Code에서 핵심 도구를 시작할 때 사용합니다.|

Functions 호스트가 시작되면 HTTP 트리거 함수의 URL이 출력됩니다.

```bash
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="passing-test-data-to-a-function"></a>테스트 데이터를 함수에 전달

로컬로 함수를 테스트하려면 [Functions 호스트를 시작](#start)하고 HTTP 요청을 사용하여 로컬 서버에서 끝점을 호출합니다. 호출하는 끝점은 함수의 형식에 따라 달라집니다.

>[!NOTE]  
> 이 항목의 예제에서는 cURL 도구를 사용하여 터미널 또는 명령 프롬프트의 HTTP 요청을 보냅니다. 로컬 서버에 HTTP 요청을 보내도록 선택한 도구를 사용할 수 있습니다. cURL 도구는 Linux 기반 시스템에서 기본적으로 사용할 수 있습니다. Windows에서는 먼저 [cURL 도구](https://curl.haxx.se/)를 다운로드한 후 설치해야 합니다.

함수를 테스트하는 방법에 대한 일반적인 내용은 [Azure Functions에서 코드를 테스트하기 위한 전략](functions-test-a-function.md)을 참조하세요.

#### <a name="http-and-webhook-triggered-functions"></a>HTTP 및 웹후크 트리거된 함수

다음 끝점을 호출하여 HTTP 및 웹후크 트리거된 함수를 로컬로 실행합니다.

    http://localhost:{port}/api/{function_name}

Functions 호스트가 수신 대기 중인 동일한 서버 이름 및 포트를 사용하도록 합니다. Function 호스트를 시작할 때 생성된 출력에서 이 항목을 확인합니다. 트리거에서 지원하는 HTTP 메서드를 사용하여 이 URL을 호출할 수 있습니다. 

다음 cURL 명령은 _이름_ 매개 변수를 쿼리 문자열에 전달한 GET 요청에서 `MyHttpTrigger` 빠른 시작 함수를 트리거합니다. 

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
다음 예제는 요청 본문에서 _이름_을 전달하는 POST 요청에서 호출되는 동일한 함수입니다.

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

쿼리 문자열에서 데이터를 전달하는 브라우저에서 GET 요청을 수행할 수 있습니다. 다른 모든 HTTP 메서드에서 cURL, Fiddler, Postman 또는 비슷한 HTTP 테스트 도구를 사용해야 합니다.  

#### <a name="non-http-triggered-functions"></a>HTTP가 아닌 트리거된 함수

HTTP 트리거와 웹후크가 아닌 다른 모든 종류의 함수에서 관리 끝점을 호출하여 로컬로 함수를 테스트할 수 있습니다. 로컬 서버에서 HTTP POST 요청으로 이 끝점을 호출하면 함수를 트리거합니다. 필요에 따라 POST 요청의 본문에서 실행에 테스트 데이터를 전달할 수 있습니다. 이 기능은 Azure Portal에서 **테스트** 탭과 비슷합니다.  

다음 관리자 끝점을 호출하여 HTTP가 아닌 함수를 트리거합니다.

    http://localhost:{port}/admin/functions/{function_name}

함수의 관리자 끝점에 테스트 데이터를 전달하려면 POST 요청 메시지의 본문에서 데이터를 제공해야 합니다. 메시지 본문에는 다음 JSON 형식이 필요합니다.

```JSON
{
    "input": "<trigger_input>"
}
````

`<trigger_input>` 값에는 함수에 필요한 형식의 데이터가 포함됩니다. 다음 cURL 예제는 `QueueTriggerJS` 함수에 대한 POST 요청입니다. 이 경우에 입력은 큐에 위치해야 하는 메시지에 해당하는 문자열입니다.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>1.x 버전에서 `func run` 명령 사용

>[!IMPORTANT]  
> `func run` 명령은 도구의 2.x 버전에서 지원되지 않습니다. 자세한 내용은 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md) 항목을 참조하세요.

`func run <FunctionName>`을 사용하여 함수를 직접 호출하고 함수에 대한 입력 데이터를 제공할 수도 있습니다. 이 명령은 Azure Portal에서 **테스트** 탭을 사용하여 함수를 실행하는 것과 비슷합니다. 

`func run`은 다음 옵션을 지원합니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | 인라인 콘텐츠입니다. |
| **`--debug -d`** | 함수를 실행하기 전에 호스트 프로세스에 디버거를 연결합니다.|
| **`--timeout -t`** | 로컬 Functions 호스트가 준비될 때까지의 대기 시간(초)입니다.|
| **`--file -f`** | 콘텐츠로 사용할 파일 이름입니다.|
| **`--no-interactive`** | 입력에 대한 메시지를 표시하지 않습니다. 자동화 시나리오에 유용합니다.|

예를 들어 HTTP 트리거 함수를 호출하고 콘텐츠 본문을 전달하려면 다음 명령을 실행합니다.

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>로컬로 로그 파일 보기

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Azure에 게시

Azure의 함수 앱에 Functions 프로젝트를 게시하려면 `publish` 명령을 사용합니다.

```bash
func azure functionapp publish <FunctionAppName>
```

다음 옵션을 사용할 수 있습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  local.settings.json의 설정을 Azure에 게시하고, 설정이 이미 있는 경우 덮어쓸지 묻습니다. 저장소 에뮬레이터를 사용하는 경우 앱 설정을 [실제 저장소 연결](#get-your-storage-connection-strings)로 변경합니다. |
| **`--overwrite-settings -y`** | `-i`와 함께 사용해야 합니다. Azure의 AppSettings을 로컬 값으로 덮어씁니다(서로 다른 경우). 기본값은 프롬프트입니다.|

이 명령은 Azure에서 기존 함수 앱에 게시합니다. `<FunctionAppName>`이 구독에 없으면 오류가 발생합니다. Azure CLI를 사용하여 명령 프롬프트 또는 터미널 창에서 함수 앱을 만드는 방법을 알아보려면 [서버를 사용하지 않고 실행하기 위한 함수 앱 만들기](./scripts/functions-cli-create-serverless.md)를 참조하세요.

`publish` 명령은 Functions 프로젝트 디렉터리의 콘텐츠를 업로드합니다. 파일을 로컬로 삭제하는 경우 `publish` 명령은 Azure에서 해당 파일을 삭제하지 않습니다. [Azure Portal]에서 [Kudu 도구](functions-how-to-use-azure-function-app-settings.md#kudu)를 사용하여 Azure에서 파일을 삭제할 수 있습니다.  

>[!IMPORTANT]  
> Azure에서 함수 앱을 만드는 경우 기본적으로 Function 런타임 버전 1.x가 사용됩니다. 함수 앱이 런타임 버전 2.x를 사용하도록 하려면 응용 프로그램 설정 `FUNCTIONS_EXTENSION_VERSION=beta`를 추가합니다.  
이 설정을 함수 앱에 추가하려면 다음 Azure CLI 코드를 사용합니다.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>다음 단계

Azure Functions 핵심 도구는 [오픈 소스이며 GitHub에서 호스팅](https://github.com/azure/azure-functions-cli)됩니다.  
버그 또는 기능 요청을 제출하려면 [GitHub 문제를 개설](https://github.com/azure/azure-functions-cli/issues)합니다.

<!-- LINKS -->

[Azure Functions 핵심 도구]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
