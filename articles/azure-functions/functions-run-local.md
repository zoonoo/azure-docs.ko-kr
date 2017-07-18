---
title: "로컬로 Azure Functions 개발 및 실행 | Microsoft Docs"
description: "Azure Functions에서 실행하기 전에 로컬 컴퓨터에서 Azure Functions를 코딩 및 테스트하는 방법을 알아봅니다."
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2016
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 478c2ffbb0bfe5b1595bc6ea1bebb2144aebc728
ms.contentlocale: ko-kr
ms.lasthandoff: 06/09/2017


---
# <a name="code-and-test-azure-functions-locally"></a>Azure Functions를 로컬로 코딩 및 테스트

즐겨 찾는 코드 편집기와 로컬 개발 도구를 사용하여 Azure Functions 런타임을 로컬로 실행할 수 있습니다. Azure에서 이벤트를 트리거하고 C# 및 JavaScript 함수를 디버그합니다.

시작하려면 npm에서 [Azure Functions 핵심 도구]를 설치합니다. Azure Functions 핵심 도구는 로컬 Windows 컴퓨터에서 실행할 수 있는 Azure Functions 런타임의 로컬 버전입니다. 에뮬레이터 또는 시뮬레이터가 아닙니다. Azure에서 실행되는 동일한 런타임입니다.

[Azure Functions 핵심 도구]는 다음 명령 별칭을 추가합니다.
- `func`
- `azfun`
- `azurefunctions`

Azure Functions 핵심 도구는 [오픈 소스이며 GitHub에서 호스팅](https://github.com/azure/azure-functions-cli)됩니다. 버그 또는 기능 요청을 제출하려면 [GitHub 문제를 개설](https://github.com/azure/azure-functions-cli/issues)합니다.

## <a name="create-a-local-functions-project"></a>로컬 Functions 프로젝트 만들기

로컬로 실행하는 경우 Functions 프로젝트는 host.json 및 local.settings.json 파일이 있는 디렉터리입니다. 이 디렉터리는 Azure의 함수 앱에 해당합니다. Azure Functions 폴더 구조에 대한 자세한 내용은 [Azure Functions 개발자 가이드](functions-reference.md#folder-structure)를 참조하세요.

명령 프롬프트에서 다음 명령을 실행합니다.

```
func init MyFunctionProj
```

출력은 다음 예제와 유사합니다.

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Git 리포지토리 만들기를 옵트아웃하려면 `--no-source-control [-n]` 옵션을 사용합니다.

<a name="local-settings"></a>

## <a name="local-settings-file"></a>로컬 설정 파일

local.settings.json 파일은 앱 설정, 연결 문자열 및 Azure Functions 핵심 도구에 대한 설정을 저장합니다. 구조는 다음과 같습니다.

```json
{
  "IsEncrypted": false,   // If set to true, all values are encrypted by using a local machine key. Use with "func settings" commands.
  "Values": {
    "AzureWebJobsStorage": "<connection string>",   // This is required for all triggers except HTTP.
    "AzureWebJobsDashboard": "<connection string>", // Optional, controls whether to log to the Monitor tab in the portal.
  },
  "Host": {
    "LocalHttpPort": 7071, // If specified, this is the default port for "host start" and "run". Can be overridden by using the --port command-line option.
    "CORS": "*"            // Origins to allow in the CORS setting.
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```

**값** 컬렉션에서 앱 설정을 지정합니다. 이러한 설정을 환경 변수로 읽을 수 있습니다. C#에서는 `System.Environment.GetEnvironmentVariable` 또는 `ConfigurationManager.AppSettings`를 사용합니다. JavaScript에서는 `process.env`를 사용합니다. 시스템 환경 변수와 동일한 설정을 지정한 경우 local.settings.json의 값보다 우선합니다.

앱 설정 **AzureWebJobsStorage**는 HTTP 이외의 모든 트리거에 대해 Azure Functions 런타임에 필요한 특별한 설정입니다. 내부적으로 런타임은 이 저장소 계정에서 트리거를 관리하는 큐를 만듭니다. **AzureWebJobsStorage** 값을 지정하지 않고 HTTP가 아닌 다른 트리거를 사용하면 다음과 같은 메시지가 나타납니다.

*local.settings.json에 AzureWebJobsStorage 값이 없습니다. 이 값은 HTTP 이외의 모든 트리거에 필요합니다. 'func azure functionapp fetch-app-settings <functionAppName>'를 실행하거나 local.settings.json에서 연결 문자열을 지정할 수 있습니다.*

> [!NOTE]
> 연결 문자열 "AzureWebJobsStorage": "UseDevelopmentStorage=true"를 통해 로컬 저장소 에뮬레이터를 사용할 수 있습니다. 그러나 Azure Storage 서비스에 비해 동작의 차이가 있을 수 있습니다.

다음 설정은 로컬 Functions 호스트를 사용자 지정합니다.
- `LocalHttpPort`. `func host start` `func run`에 사용할 기본 포트입니다. `--port` 명령줄 옵션이 이 값보다 우선합니다.
- `CORS`. CORS에서 허용하는 원본으로, 공백 없이 쉼표로 구분된 목록입니다. 모두를 허용하려면 "*"를 사용합니다.

`ConnectionStrings` 개체에서 연결 문자열을 제공할 수 있습니다. 이러한 연결 문자열은 공급자 이름이 **System.Data.SqlClient**인 환경에 추가됩니다.

대부분의 트리거와 바인딩에는 local.settings.json의 앱 설정 또는 환경 변수 이름인 **Connection** 속성이 있습니다. 앱 설정 값이 누락된 경우 다음 메시지가 표시됩니다.

*경고: local.settings.json에서 'BlobTriggerCSharp\function.json'의 'BlobTrigger'에 설정된 'connection' 속성과 일치하는 'MyStorageConnection' 값을 찾을 수 없습니다. 'func azure functionapp fetch-app-settings <functionAppName>'를 실행하거나 local.settings.json에서 연결 문자열을 지정할 수 있습니다.*

local.settings.json 파일은 Azure Functions 핵심 도구에서만 사용됩니다. Azure에서 앱 설정 및 연결 문자열을 설정하려면 **응용 프로그램 설정** 블레이드를 사용합니다.

### <a name="configure-app-settings"></a>앱 설정 구성

연결 문자열 값을 설정하려면 다음 중 하나를 수행합니다.
- [Azure Storage 탐색기](http://storageexplorer.com/)에서 연결 문자열을 수동으로 입력합니다.
- **func azure functionapp fetch-app-settings \<FunctionAppName\>**을 사용합니다. **Azure 로그인**이 필요합니다.
- **func azure functionapp storage fetch-connection-string \<StorageAccountName\>**을 사용합니다. **Azure 로그인**이 필요합니다.

## <a name="create-a-function"></a>함수 만들기

함수를 만들려면 `func new`를 실행합니다. 이 명령에는 다음과 같은 선택적 인수가 있습니다.

- `--language [-l]`. C#, F# 또는 JavaScript와 같은 템플릿 프로그래밍 언어
- `--template [-t]`. 템플릿 이름
- `--name [-n]`. 함수 이름

예를 들어 JavaScript HTTP 트리거를 만들려면 다음을 실행합니다.

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

큐 트리거 함수를 만들려면 다음을 실행합니다.

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```

## <a name="run-functions-locally"></a>로컬로 함수 실행

Functions 프로젝트를 실행하려면 Functions 호스트를 실행합니다. 이 호스트는 프로젝트의 모든 함수에 대한 트리거를 활성화합니다.

```
func host start
```

`func host start`와 함께 다음 옵션을 사용할 수 있습니다.

- `--port [-p]`. 수신 대기할 로컬 포트입니다. 기본값: 7071
- `--debug <type>`. 옵션은 VSCode 및 VS입니다.
- `--cors`. CORS 원본의 공백 없이 쉼표로 구분된 목록입니다.
- `--nodeDebugPort [-n]`. 사용할 노드 디버거의 포트입니다. 기본값: launch.json 값 또는 5858
- `--debugLevel [-d]`. 콘솔 추적 수준입니다(해제, 자세한 정보 표시, 정보, 경고 또는 오류). 기본값: 정보
- `--timeout [-t]`. Functions 호스트를 시작할 제한 시간(초)입니다. 기본값: 20초
- `--useHttps`. http://localhost:{port}가 아니라 https://localhost:{port}에 바인딩합니다. 기본적으로 이 옵션은 사용자 컴퓨터에 신뢰할 수 있는 인증서를 만듭니다.
- `--pause-on-error`. 프로세스를 종료하기 전에 추가 입력에 대해 일시 중지합니다. IDE(통합 개발 환경)에서 Azure Functions 핵심 도구를 시작할 때 유용합니다.

Functions 호스트가 시작되면 HTTP 트리거 함수의 URL이 출력됩니다.

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug"></a>디버그

디버거를 연결하려면 `--debug` 인수를 전달합니다. JavaScript 함수를 디버그하려면 Visual Studio Code를 사용합니다. C# 함수의 경우 Visual Studio를 사용합니다.

C# 함수를 디버그하려면 `--debug vs`를 사용합니다. 또는 [Azure Functions Visual Studio 2017 도구](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/)를 사용합니다. 

호스트를 시작하고 JavaScript 디버깅을 설정하려면 다음을 실행합니다.

```
func host start --debug vscode
```

그런 다음 Visual Studio Code의 **디버그** 뷰에서 **Azure Functions에 연결**을 선택합니다. 중단점을 연결하고, 변수를 검사하고, 코드를 단계별로 실행할 수 있습니다.

![Visual Studio Code를 사용한 JavaScript 디버깅](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="call-a-function-by-using-test-data"></a>테스트 데이터를 사용하여 함수 호출

`func run <FunctionName>`을 사용하여 함수를 직접 호출할 수도 있습니다. 이 명령은 함수에 대한 입력 데이터를 제공할 수 있는 Azure Portal의 **테스트** 탭과 유사합니다. 이 명령은 전체 Functions 호스트를 시작합니다.

`func run`과 함께 다음 옵션을 사용할 수 있습니다.

- `--content [-c]`. 인라인 콘텐츠입니다.
- `--debug [-d]`. 함수를 실행하기 전에 호스트 프로세스에 디버거를 연결합니다.
- `--timeout [-t]`. 로컬 Functions 호스트가 준비될 때까지의 대기 시간(초)입니다.
- `--file [-f]`. 콘텐츠로 사용할 파일 이름입니다.
- `--no-interactive`. 입력에 대한 메시지를 표시하지 않습니다. 자동화 시나리오에 유용합니다.

예를 들어 HTTP 트리거 함수를 호출하고 콘텐츠 본문을 전달하려면 다음 명령을 실행합니다.

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-a-function-app"></a>함수 앱 게시

Azure의 함수 앱에 Functions 프로젝트를 게시하려면 `publish` 명령을 사용합니다.

```
func azure functionapp publish <FunctionAppName>
```

다음 옵션을 사용할 수 있습니다.

- `--publish-local-settings [-i]`.  local.settings.json의 설정을 Azure에 게시하고, 설정이 이미 있는 경우 덮어쓸지 묻습니다.
- `--overwrite-settings [-y]`. `-i`와 함께 사용해야 합니다. Azure의 AppSettings을 로컬 값으로 덮어씁니다(서로 다른 경우). 기본값은 프롬프트입니다.

`publish` 명령은 Functions 프로젝트 디렉터리의 콘텐츠를 업로드합니다. 파일을 로컬로 삭제하는 경우 이 명령은 Azure에서 해당 파일을 삭제하지 않습니다. 이러한 파일을 삭제하려면 Azure Functions Portal에서 Kudu를 사용합니다. Kudu를 시작하려면 Azure Functions Portal에서 **플랫폼 기능** > **고급 도구(Kudu)**를 선택합니다. 


<!-- LINKS -->

[Azure Functions 핵심 도구]: https://www.npmjs.com/package/azure-functions-core-tools
