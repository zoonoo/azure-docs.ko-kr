---
title: 시각적 스튜디오 코드를 사용하여 Azure 함수 개발
description: Visual Studio 코드에 대한 Azure Functions 확장을 사용하여 Azure 함수를 개발하고 테스트하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 2d33b7dddf29d37d174bdb7734e9048bc1658840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277168"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>시각적 스튜디오 코드를 사용하여 Azure 함수 개발

[Visual Studio 코드에 대한 Azure Functions 확장을] 사용하면 함수를 로컬로 개발하고 Azure에 배포할 수 있습니다. Azure Functions를 처음으로 접하는 경우라면 [Azure Functions 소개](functions-overview.md)에서 자세한 내용을 확인할 수 있습니다.

Azure Functions 확장은 다음과 같은 이점을 제공합니다.

* 로컬 개발 컴퓨터에서 함수를 편집, 빌드 및 실행합니다.
* Azure에 직접 Azure Functions 프로젝트를 게시합니다.
* Visual Studio 코드의 이점을 활용하면서 다양한 언어로 함수를 작성합니다.

확장은 버전 2.x부터 Azure Functions 런타임에서 지원하는 다음 언어와 함께 사용할 수 있습니다.

* [C# 컴파일](functions-dotnet-class-library.md)
* [C # 스크립트](functions-reference-csharp.md)<sup>*</sup>
* [자바 스크립트](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [Powershell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>[C# 스크립트를 기본 프로젝트 언어로 설정해야](#c-script-projects)합니다.

이 문서에서는 현재 자바스크립트(Node.js) 및 C# 클래스 라이브러리 함수에대해서만 예제를 사용할 수 있습니다.  

이 문서에서는 Azure Functions 확장을 사용하여 함수를 개발하고 Azure에 게시하는 방법에 대한 자세한 내용을 제공합니다. 이 문서를 읽기 전에 [Visual Studio 코드를 사용하여 첫 번째 함수를 만들어야](functions-create-first-function-vs-code.md)합니다.

> [!IMPORTANT]
> 단일 함수 앱에 대한 로컬 개발 및 포털 개발을 혼합하지 마십시오. 로컬 프로젝트에서 함수 앱에 게시할 때 배포 프로세스는 포털에서 개발한 모든 기능을 덮어씁니다.

## <a name="prerequisites"></a>사전 요구 사항

설치 하 고 Azure [함수 확장]Azure 함수 확장을 실행 하기 전에 Visual[Studio 코드에 대]한 다음 요구 사항을 충족 해야 합니다.

* [지원되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms)중 하나에 설치된 [Visual Studio 코드.](https://code.visualstudio.com/)

* 활성화된 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Azure 저장소 계정과 같이 필요한 다른 리소스는 [Visual Studio 코드를 사용하여 게시할](#publish-to-azure)때 구독에서 만들어집니다.

> [!IMPORTANT]
> 로컬로 함수를 개발하고 로컬에서 함수를 시작하고 실행할 필요 없이 Azure에 게시할 수 있습니다. 함수를 로컬로 실행하려면 Azure Functions 핵심 도구의 자동 다운로드를 비롯한 몇 가지 추가 요구 사항을 충족해야 합니다. 자세한 내용은 [프로젝트를 로컬로 실행하기 위한 추가 요구 사항을](#additional-requirements-for-running-a-project-locally)참조하십시오.

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Azure Functions 프로젝트 만들기

함수 확장을 사용하면 첫 번째 함수와 함께 함수 앱 프로젝트를 만들 수 있습니다. 다음 단계에서는 새 Functions 프로젝트에서 HTTP 트리거 함수를 만드는 방법을 보여 주습니다. [HTTP 트리거는](functions-bindings-http-webhook.md) 시연할 수 있는 가장 간단한 함수 트리거 템플릿입니다.

1. **Azure: 함수,** 함수 **만들기** 아이콘을 선택합니다.

    ![함수 만들기](./media/functions-develop-vs-code/create-function.png)

1. 함수 앱 프로젝트의 폴더를 선택한 다음 **함수 프로젝트의 언어를 선택합니다.**

1. 핵심 도구를 아직 설치하지 않은 경우 설치할 핵심 도구 **버전을 선택하라는** 메시지가 표시됩니다. 버전 2.x 또는 이후 버전을 선택합니다. 

1. HTTP **트리거** 함수 템플릿을 선택하거나 **지금 건너뛰기를** 선택하여 함수 없이 프로젝트를 만들 수 있습니다. 나중에 [언제든지 프로젝트에 함수를 추가할](#add-a-function-to-your-project) 수 있습니다.

    ![HTTP 트리거 템플릿 선택](./media/functions-develop-vs-code/create-function-choose-template.png)

1. 함수 이름에 대해 **HttpExample를** 입력하고 Enter를 선택한 다음 **함수** 권한 부여를 선택합니다. 이 권한 부여 수준에서함수 끝점을 호출할 때 [함수 키를](functions-bindings-http-webhook-trigger.md#authorization-keys) 제공해야 합니다.

    ![기능 권한 부여 선택](./media/functions-develop-vs-code/create-function-auth.png)

    함수는 선택한 언어와 HTTP 트리거 함수의 템플릿에서 만들어집니다.

    ![비주얼 스튜디오 코드에서 HTTP 트리거 된 함수 템플릿](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>생성된 프로젝트 파일

프로젝트 템플릿은 선택한 언어로 프로젝트를 만들고 필요한 종속성을 설치합니다. 모든 언어의 경우 새 프로젝트에는 다음 파일이 있습니다.

* **host.json**: 함수 호스트를 구성할 수 있습니다. 이러한 설정은 로컬에서 함수를 실행할 때와 Azure에서 함수를 실행할 때 적용됩니다. 자세한 내용은 [host.json 참조](functions-host-json.md)를 참조하세요.

* **local.settings.json**: 로컬로 함수를 실행할 때 사용되는 설정을 유지 관리합니다. 이러한 설정은 로컬에서 함수를 실행하는 경우에만 사용됩니다. 자세한 내용은 [로컬 설정 파일을](#local-settings-file)참조하십시오.

    >[!IMPORTANT]
    >local.settings.json 파일에는 비밀이 포함될 수 있으므로 프로젝트 소스 제어에서 제외해야 합니다.

사용자의 언어에 따라 다음과 같은 다른 파일이 만들어집니다.

# <a name="c"></a>[C\#](#tab/csharp)

* [함수를](functions-dotnet-class-library.md#functions-class-library-project) 구현하는 HttpExample.cs 클래스 라이브러리 파일입니다.

# <a name="javascript"></a>[자바 스크립트](#tab/nodejs)

* 루트 폴더의 package.json 파일입니다.

* [function.json 정의 파일및](functions-reference-node.md#folder-structure) [index.js 파일,](functions-reference-node.md#exporting-a-function)함수 코드를 포함하는 Node.js 파일을 포함하는 HttpExample 폴더입니다.

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

이 시점에서 [function.json 파일을 수정하거나](#add-a-function-to-your-project) [C# 클래스 라이브러리 함수에 매개 변수를 추가하여](#add-a-function-to-your-project)함수에 입력 및 출력 바인딩을 추가할 수 있습니다.

[프로젝트에 새 함수를 추가할](#add-a-function-to-your-project)수도 있습니다.

## <a name="install-binding-extensions"></a>바인딩 확장 설치

HTTP 및 타이머 트리거를 제외하고 바인딩은 확장 패키지에서 구현됩니다. 필요한 트리거 및 바인딩에 대한 확장 패키지를 설치해야 합니다. 바인딩 확장을 설치하는 프로세스는 프로젝트의 언어에 따라 다릅니다.

# <a name="c"></a>[C\#](#tab/csharp)

터미널 창에서 [dotnet 패키지 추가](/dotnet/core/tools/dotnet-add-package) 명령을 실행하여 프로젝트에 필요한 확장 패키지를 설치합니다. 다음 명령은 Blob, Queue 및 테이블 저장소에 대한 바인딩을 구현하는 Azure Storage 확장을 설치합니다.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[자바 스크립트](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>프로젝트에 함수 추가

미리 정의된 함수 트리거 템플릿 중 하나를 사용하여 기존 프로젝트에 새 함수를 추가할 수 있습니다. 새 함수 트리거를 추가하려면 F1을 선택하여 명령 팔레트를 연 다음 **Azure Functions: Function**만들기 명령을 검색하고 실행합니다. 프롬프트에 따라 트리거 유형을 선택하고 트리거의 필수 특성을 정의합니다. 트리거에 서비스에 연결하기 위해 액세스 키 또는 연결 문자열이 필요한 경우 함수 트리거를 만들기 전에 준비하십시오.

이 작업의 결과는 프로젝트의 언어에 따라 달라집니다.

# <a name="c"></a>[C\#](#tab/csharp)

새 C# 클래스 라이브러리(.cs) 파일이 프로젝트에 추가됩니다.

# <a name="javascript"></a>[자바 스크립트](#tab/nodejs)

프로젝트에 새 폴더가 만들어집니다. 폴더에는 새 function.json 파일과 새 JavaScript 코드 파일이 포함되어 있습니다.

---

## <a name="add-input-and-output-bindings"></a>입력 및 출력 바인딩 추가

입력 및 출력 바인딩을 추가하여 기능을 확장할 수 있습니다. 바인딩을 추가하는 프로세스는 프로젝트의 언어에 따라 다릅니다. 바인딩에 대한 자세한 내용은 [Azure Functions 트리거 및 바인딩 개념을](functions-triggers-bindings.md)참조하십시오.

다음 예제는 local.settings.json의 `outqueue` `MyStorageConnection` 응용 프로그램 설정에서 저장소 계정에 대한 연결 문자열이 설정된 저장소 큐에 연결합니다.

# <a name="c"></a>[C\#](#tab/csharp)

함수 메서드를 업데이트하여 메서드 정의에 다음 매개 변수를 추가합니다. `Run`

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

이 코드를 사용하려면 다음 `using` 문을 추가해야 합니다.

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

`msg` 매개 변수는 `ICollector<T>` 형식이며, 함수가 완료될 때 출력 바인딩에 작성되는 메시지의 컬렉션을 나타냅니다. 컬렉션에 하나 이상의 메시지를 추가합니다. 이러한 메시지는 함수가 완료되면 큐로 전송됩니다.

자세한 내용은 Queue [저장소 출력 바인딩](functions-bindings-storage-queue-output.md) 설명서를 참조하십시오.

# <a name="javascript"></a>[자바 스크립트](#tab/nodejs)

Visual Studio 코드를 사용하면 편리한 프롬프트 집합에 따라 function.json 파일에 바인딩을 추가할 수 있습니다. 바인딩을 만들려면 함수 폴더의 **function.json** 파일을 마우스 오른쪽 단추로 클릭하고(MacOS를 클릭) **바인딩 추가를**선택합니다.

![기존 자바스크립트 함수에 바인딩 추가 ](media/functions-develop-vs-code/function-add-binding.png)

다음은 새 저장소 출력 바인딩을 정의하는 예제 프롬프트입니다.

| prompt | 값 | 설명 |
| -------- | ----- | ----------- |
| **바인딩 방향 선택** | `out` | 바인딩은 출력 바인딩입니다. |
| **방향이 있는 바인딩 선택** | `Azure Queue Storage` | 바인딩은 Azure Storage 큐 바인딩입니다. |
| **코드에서 이 바인딩을 식별하는 데 사용하는 이름** | `msg` | 코드에서 참조되는 바인딩 매개 변수를 식별하는 이름입니다. |
| **메시지가 전송될 큐** | `outqueue` | 바인딩이 데이터를 쓰는 큐의 이름입니다. 바인딩을 처음 사용할 때 *queueName*이 없으면 바인딩이 알아서 만듭니다. |
| **"local.settings.json"에서 설정 선택** | `MyStorageConnection` | 저장소 계정에 대한 연결 문자열을 포함하는 응용 프로그램 설정의 이름입니다. 이 `AzureWebJobsStorage` 설정에는 함수 앱으로 만든 저장소 계정에 대한 연결 문자열이 포함되어 있습니다. |

이 예제에서는 function.json 파일의 `bindings` 배열에 다음 바인딩이 추가됩니다.

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

동일한 바인딩 정의를 function.json에 직접 추가할 수도 있습니다.

함수 코드에서 `msg` 바인딩은 이 예제에서와 같이 `context`에서 액세스됩니다.

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

자세한 내용은 Queue [저장소 출력 바인딩](functions-bindings-storage-queue-output.md) 참조를 참조하세요.

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Azure에 게시

Visual Studio 코드를 사용하면 함수 프로젝트를 Azure에 직접 게시할 수 있습니다. 프로세스를 통해 함수 앱 및 관련된 리소스를 Azure 구독에서 만듭니다. 함수 앱은 함수를 위한 실행 컨텍스트를 제공합니다. 프로젝트는 패키지되어 Azure 구독에서 새 함수 앱에 배포됩니다.

Visual Studio 코드에서 Azure의 새 함수 앱에 게시하면 빠른 함수 앱 만들기 경로와 고급 경로가 모두 제공됩니다. 

Visual Studio 코드에서 게시할 때 [Zip 배포](functions-deployment-technologies.md#zip-deploy) 기술을 활용합니다. 

### <a name="quick-function-app-create"></a>빠른 기능 응용 프로그램 만들기

**Azure에서 + 새 함수 앱 만들기를**선택하면 확장이 함수 앱에 필요한 Azure 리소스에 대한 값을 자동으로 생성합니다. 이러한 값은 선택한 함수 앱 이름을 기반으로 합니다. Azure의 새 함수 앱에 프로젝트를 게시하기 위해 기본값을 사용하는 예는 [Visual Studio Code 빠른 시작 문서를](functions-create-first-function-vs-code.md#publish-the-project-to-azure)참조하십시오.

생성된 리소스에 대한 명시적 이름을 제공하려면 고급 만들기 경로를 선택해야 합니다.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>고급 옵션을 사용하여 Azure의 새 함수 앱에 프로젝트 게시

다음 단계는 고급 만들기 옵션으로 만든 새 함수 앱에 프로젝트를 게시합니다.

1. **Azure: 함수** 영역에서 **함수 앱에 배포 아이콘을 선택합니다.**

    ![함수 앱 설정](./media/functions-develop-vs-code/function-app-publish-project.png)

1. 로그인하지 않은 경우 Azure 에 **로그인하라는**메시지가 표시됩니다. **무료 Azure 계정을 만들**수도 있습니다. 브라우저에서 로그인한 후 Visual Studio 코드로 돌아갑니다.

1. 구독이 여러 개인 경우 함수 앱에 대한 **구독을 선택한** 다음 **Azure에서 +새 기능 앱 만들기를 선택합니다... _고급_**. 이 _고급_ 옵션을 사용하면 Azure에서 만든 리소스를 보다 세한 제어할 수 있습니다. 

1. 프롬프트에 따라 다음 정보를 제공합니다.

    | prompt | 값 | 설명 |
    | ------ | ----- | ----------- |
    | Azure에서 함수 앱 선택 | Azure에서 새 함수 앱 만들기 | 다음 프롬프트에서 새 함수 앱을 식별하는 전역고유 이름을 입력한 다음 Enter를 선택합니다. 함수 앱 이름에 대한 유효한 문자는 `a-z`, `0-9` 및 `-`입니다. |
    | OS 선택 | Windows | 함수 앱은 Windows에서 실행됩니다. |
    | 호스팅 계획 선택 | 소비 계획 | 서버리스 [소비 계획 호스팅이](functions-scale.md#consumption-plan) 사용됩니다. |
    | 새 앱의 런타임 선택 | 프로젝트 언어 | 런타임은 게시할 프로젝트와 일치해야 합니다. |
    | 새 리소스에 대한 리소스 그룹 선택 | 새 리소스 그룹 만들기 | 다음 프롬프트에서 `myResourceGroup`와 같은 리소스 그룹 이름을 입력한 다음 enter를 선택합니다. 기존 리소스 그룹을 선택할 수도 있습니다. |
    | 저장소 계정 선택 | 새 스토리지 계정 만들기 | 다음 프롬프트에서 함수 앱에서 사용하는 새 저장소 계정에 대해 전역적으로 고유한 이름을 입력한 다음 Enter를 선택합니다. 저장소 계정 이름은 3자에서 24자 사이여야 하며 숫자와 소문자만 포함할 수 있습니다. 기존 계정을 선택할 수도 있습니다. |
    | 새 리소스의 위치 선택 | region | 사용자 근처 또는 함수가 액세스하는 다른 서비스 근처의 [위치를](https://azure.microsoft.com/regions/) 선택합니다. |

    함수 앱을 만들고 배포 패키지를 적용한 후에 알림이 나타납니다. 이 알림에서 **출력 보기**를 선택하여 사용자가 만든 Azure 리소스를 포함한 만들기 및 배포 결과를 표시합니다.

## <a name="republish-project-files"></a>프로젝트 파일 다시 게시

[연속 배포를](functions-continuous-deployment.md)설정하면 연결된 원본 위치에서 원본 파일이 업데이트될 때마다 Azure의 함수 앱이 업데이트됩니다. 지속적인 배포를 권장하지만 Visual Studio 코드에서 프로젝트 파일 업데이트를 다시 게시할 수도 있습니다.

> [!IMPORTANT]
> 기존 함수 앱에 게시하면 Azure에서 해당 앱의 콘텐츠를 덮어씁니다.

1. 시각적 스튜디오 코드에서 F1을 선택하여 명령 팔레트를 엽니다. 명령 팔레트에서 **Azure 함수: 함수 앱에 배포를**검색하고 선택합니다.

1. 로그인하지 않은 경우 Azure 에 **로그인하라는**메시지가 표시됩니다. 브라우저에서 로그인한 후 Visual Studio 코드로 돌아갑니다. 구독이 여러 개인 경우 함수 앱이 포함된 **구독을 선택합니다.**

1. Azure에서 기존 함수 앱을 선택합니다. 함수 앱의 모든 파일을 덮어쓰는 것에 대한 경고가 나오면 **배포를** 선택하여 경고를 인정하고 계속합니다.

프로젝트가 다시 빌드되고 다시 패키징되고 Azure에 업로드됩니다. 기존 프로젝트가 새 패키지로 대체되고 함수 앱이 다시 시작됩니다.

## <a name="get-the-url-of-the-deployed-function"></a>배포된 함수의 URL 받기

HTTP 트리거 함수를 호출하려면 함수 앱에 배포할 때 함수의 URL이 필요합니다. 이 URL에는 필요한 [모든 기능 키가](functions-bindings-http-webhook-trigger.md#authorization-keys)포함됩니다. 확장을 사용하여 배포된 함수에 대해 이러한 URL을 얻을 수 있습니다.

1. 명령 팔레트를 열려면 F1을 선택한 다음 Azure **Function: 함수 URL 복사**명령을 검색하고 실행합니다.

1. 프롬프트에 따라 Azure에서 함수 앱을 선택한 다음 호출할 특정 HTTP 트리거를 선택합니다.

함수 URL은 `code` 쿼리 매개 변수에 의해 전달되는 필수 키와 함께 클립보드에 복사됩니다. HTTP 도구를 사용하여 POST 요청을 제출하거나 원격 함수에 GET 요청을 위한 브라우저를 제출합니다.  

## <a name="run-functions-locally"></a>로컬로 함수 실행

Azure Functions 확장을 사용하면 로컬 개발 컴퓨터에서 함수 프로젝트를 실행할 수 있습니다. 로컬 런타임은 Azure에서 함수 앱을 호스팅하는 런타임과 동일합니다. 로컬 설정은 [local.settings.json 파일에서](#local-settings-file)읽습니다.

### <a name="additional-requirements-for-running-a-project-locally"></a>로컬프로젝트 실행을 위한 추가 요구 사항

Functions 프로젝트를 로컬로 실행하려면 다음 추가 요구 사항을 충족해야 합니다.

* [Azure 함수 핵심 도구의](functions-run-local.md#v2)버전 2.x 이상 설치. 핵심 도구 패키지는 프로젝트를 로컬로 시작할 때 자동으로 다운로드되고 설치됩니다. 핵심 도구에는 전체 Azure Functions 런타임이 포함되어 있으므로 다운로드 및 설치에 다소 시간이 걸릴 수 있습니다.

* 선택한 언어에 대한 특정 요구 사항을 설치합니다.

    | 언어 | 요구 사항 |
    | -------- | --------- |
    | **C #** | [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[.NET Core CLI 도구](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [자바 확장을위한 디버거](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[메이븐 3 이상](https://maven.apache.org/) |
    | **자바 스크립트** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[파이썬 3.6.8](https://www.python.org/downloads/) 권장|

    <sup>*</sup>활성 LTS 및 유지 관리 LTS 버전(8.11.1 및 10.14.1 권장).

### <a name="configure-the-project-to-run-locally"></a>로컬로 실행되도록 프로젝트 구성

함수 런타임은 HTTP 및 웹후크를 제외한 모든 트리거 유형에 대해 내부적으로 Azure Storage 계정을 사용합니다. 따라서 **값.AzureWebJobsStorage** 키를 유효한 Azure Storage 계정 연결 문자열로 설정해야 합니다.

이 섹션에서는 Azure Storage [탐색기를](https://storageexplorer.com/) 사용하여 [Visual Studio 코드에 대한 Azure 저장소 확장을](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) 사용하여 저장소 연결 문자열에 연결하고 검색합니다.

스토리지 계정 연결 문자열을 설정하려면 다음을 수행합니다.

1. Visual Studio에서 **클라우드 탐색기를**열고 **저장소 계정을** > **확장한**다음 **속성을** 선택하고 기본 **연결 문자열** 값을 복사합니다.

2. 사용자 프로젝트에서 local.settings.json 파일을 열고 **AzureWebJobsStorage** 키의 값을 복사한 연결 문자열로 설정합니다.

3. 이전 단계를 반복하여 사용자의 함수에 필요한 다른 연결에 대한 **값** 배열에 고유 키를 추가합니다.

자세한 내용은 [로컬 설정 파일을](#local-settings-file)참조하십시오.

### <a name="debugging-functions-locally"></a>로컬로 디버깅 기능  

함수를 디버깅하려면 F5를 선택합니다. [핵심 도구]Azure 함수[핵심 도구를]아직 다운로드하지 않은 경우 이를 수행하라는 메시지가 표시됩니다. 코어 도구가 설치되고 실행되면 터미널에 출력이 표시됩니다. 이는 터미널에서 `func host start` 핵심 도구 명령을 실행하는 것과 동일하지만 추가 빌드 작업과 연결된 디버거가 있습니다.  

프로젝트가 실행 중일 때 프로젝트가 Azure에 배포될 때와 마찬가지로 함수를 트리거할 수 있습니다. 프로젝트가 디버그 모드에서 실행중일 때 중단점은 예상대로 Visual Studio Code에 표시됩니다.

HTTP 트리거에 대한 요청 URL이 터미널의 출력에 표시됩니다. 프로젝트가 로컬로 실행될 때 HTTP 트리거의 함수 키가 사용되지 않습니다. 자세한 내용은 [Azure Functions에서 코드를 테스트하기 위한 전략](functions-test-a-function.md)을 참조하세요.  

자세한 내용은 [Azure 함수 핵심 도구 Azure]함수 핵심[도구]와 함께 작업 참조 .

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

기본적으로 이러한 설정은 프로젝트가 Azure에 게시될 때 자동으로 마이그레이션되지 않습니다. 게시가 완료되면 local.settings.json에서 Azure의 함수 앱에 설정을 게시할 수 있는 옵션이 제공됩니다. 자세한 내용은 [응용 프로그램 설정 게시를](#publish-application-settings)참조하십시오.

**ConnectionStrings**의 값은 절대 게시되지 않습니다.

함수 응용 프로그램 설정 값은 코드에서 환경 변수로 읽을 수도 있습니다. 자세한 내용은 이러한 언어별 참조 문서의 환경 변수 섹션을 참조하십시오.

* [미리 컴파일된 C#](functions-dotnet-class-library.md#environment-variables)
* [C# 스크립트(.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [자바 스크립트](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Azure의 응용 프로그램 설정

프로젝트의 local.settings.json 파일의 설정은 Azure의 함수 앱의 응용 프로그램 설정과 동일해야 합니다. local.settings.json에 추가하는 모든 설정은 Azure의 함수 앱에도 추가해야 합니다. 이러한 설정은 프로젝트를 게시할 때 자동으로 업로드되지 않습니다. 마찬가지로 포털의 함수 [앱에서](functions-how-to-use-azure-function-app-settings.md#settings) 만든 모든 설정을 로컬 프로젝트에 다운로드해야 합니다.

### <a name="publish-application-settings"></a>응용 프로그램 설정 게시

Azure에서 함수 앱에 필요한 설정을 게시하는 가장 쉬운 방법은 프로젝트를 게시한 후 나타나는 **설정 업로드** 링크를 사용하는 것입니다.

![응용 프로그램 설정 업로드](./media/functions-develop-vs-code/upload-app-settings.png)

**Azure Functions:** 명령 팔레트에서 로컬 설정 명령 업로드를 사용하여 설정을 게시할 수도 있습니다. **Azure Functions: 새 설정 추가** 명령을 사용하여 Azure의 응용 프로그램 설정에 개별 설정을 추가할 수 있습니다.

> [!TIP]
> file.settings.json 파일을 게시하기 전에 저장해야 합니다.

로컬 파일이 암호화되면 해독, 게시 및 다시 암호화됩니다. 두 위치에 값이 충돌하는 설정이 있는 경우 진행 방법을 선택하라는 메시지가 표시됩니다.

구독, 함수 앱 및 **응용 프로그램 설정을**확장하여 **Azure: 함수** 영역에서 기존 앱 설정을 봅니다.

![비주얼 스튜디오 코드에서 기능 앱 설정 보기](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Azure에서 설정 다운로드

Azure에서 응용 프로그램 설정을 만든 경우 **Azure Functions: 원격 설정 다운로드** 명령을 사용하여 local.settings.json 파일에 다운로드할 수 있습니다.

업로드와 마찬가지로 로컬 파일이 암호화되면 해독, 업데이트 및 다시 암호화됩니다. 두 위치에 값이 충돌하는 설정이 있는 경우 진행 방법을 선택하라는 메시지가 표시됩니다.

## <a name="monitoring-functions"></a>함수 모니터링

[로컬로 함수를 실행하면](#run-functions-locally)로그 데이터가 터미널 콘솔로 스트리밍됩니다. Functions 프로젝트가 Azure의 함수 앱에서 실행 중일 때 로그 데이터를 얻을 수도 있습니다. Azure의 스트리밍 로그에 연결하여 거의 실시간 로그 데이터를 보거나 응용 프로그램 인사이트를 활성화하여 함수 앱의 작동 방식을 보다 완벽하게 이해할 수 있습니다.

### <a name="streaming-logs"></a>스트리밍 로그

응용 프로그램을 개발할 때 거의 실시간으로 로깅 정보를 확인하는 것이 유용한 경우가 많습니다. 함수에 의해 생성되는 로그 파일 스트림을 볼 수 있습니다. 이 출력은 HTTP 트리거 함수에 대한 요청에 대한 스트리밍 로그의 예입니다.

![HTTP 트리거에 대한 스트리밍 로그 출력](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

자세한 내용은 [스트리밍 로그를](functions-monitoring.md#streaming-logs)참조하십시오.

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> 스트리밍 로그는 Functions 호스트의 단일 인스턴스만 지원합니다. 함수가 여러 인스턴스로 확장되면 다른 인스턴스의 데이터가 로그 스트림에 표시되지 않습니다. 응용 프로그램 인의 [라이브 메트릭 스트림은](../azure-monitor/app/live-stream.md) 여러 인스턴스를 지원합니다. 스트리밍 분석은 거의 실시간으로 도마에 근접한 [반면 샘플링된 데이터를](functions-monitoring.md#configure-sampling)기반으로 합니다.

### <a name="application-insights"></a>애플리케이션 정보

함수 앱을 응용 프로그램 인사이트와 통합하여 함수 실행을 모니터링하는 것이 좋습니다. Azure 포털에서 함수 앱을 만들 때 이 통합은 기본적으로 발생합니다. Visual Studio 게시 중에 함수 앱을 만들 때 는 응용 프로그램 인사이트를 직접 통합해야 합니다.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요.

## <a name="c-script-projects"></a>C\# 스크립트 프로젝트

기본적으로 모든 C# 프로젝트는 [C# 컴파일된 클래스 라이브러리 프로젝트로](functions-dotnet-class-library.md)만들어집니다. 대신 C# 스크립트 프로젝트로 작업하려면 Azure Functions 확장 설정에서 C# 스크립트를 기본 언어로 선택해야 합니다.

1. **파일** > 기본 설정**설정을****선택합니다.** > 

1. 사용자 **설정** > **확장** > **Azure 함수로**이동합니다.

1. **Azure 함수에서** **C#스크립트를** 선택합니다.

이 단계를 완료한 후 기본 핵심 도구에 `--csx` 대한 호출에는 C# 스크립트(.csx) 프로젝트 파일을 생성하고 게시하는 옵션이 포함됩니다. 이 기본 언어를 지정하면 C# 스크립트 프로젝트로 기본값을 만드는 모든 프로젝트가 지정됩니다. 기본값이 설정되면 프로젝트 언어를 선택하라는 메시지가 표시되지 않습니다. 다른 언어로 프로젝트를 만들려면 이 설정을 변경하거나 user settings.json 파일에서 제거해야 합니다. 이 설정을 제거한 후 프로젝트를 만들 때 언어를 선택하라는 메시지가 다시 표시됩니다.

## <a name="command-palette-reference"></a>명령 팔레트 참조

Azure Functions 확장은 Azure에서 함수 앱과 상호 작용하기 위한 영역에서 유용한 그래픽 인터페이스를 제공합니다. 명령 팔레트(F1)의 명령과 동일한 기능도 사용할 수 있습니다. 이러한 Azure 함수 명령을 사용할 수 있습니다.

|Azure 함수 명령  | 설명  |
|---------|---------|
|**새 설정 추가**  |  Azure에서 새 응용 프로그램 설정을 만듭니다. 자세한 내용은 [응용 프로그램 설정 게시를](#publish-application-settings)참조하십시오. [이 설정을 로컬 설정으로 다운로드해야](#download-settings-from-azure)할 수도 있습니다. |
| **배포 소스 구성** | Azure의 함수 앱을 로컬 Git 리포지토리에 연결합니다. 자세한 내용은 [Azure 함수에 대한 지속적인 배포를](functions-continuous-deployment.md)참조하십시오. |
| **GitHub 리포지토리에 연결** | 함수 앱을 GitHub 리포지토리에 연결합니다. |
| **함수 URL 복사** | Azure에서 실행 중인 HTTP 트리거 함수의 원격 URL을 가져옵니다. 자세한 내용은 [배포된 함수의 URL 받기를](#get-the-url-of-the-deployed-function)참조하십시오. |
| **Azure에서 함수 앱 만들기** | Azure의 구독에 새 함수 앱을 만듭니다. 자세한 내용은 Azure 에서 새 [함수 앱에 게시하는](#publish-to-azure)방법에 대한 섹션을 참조하세요.        |
| **설정 해독** | Azure 함수에 의해 암호화 된 [로컬 설정을](#local-settings-file) **해독: 설정 암호화**.  |
| **함수 앱 삭제** | Azure의 구독에서 함수 앱을 제거합니다. 앱 서비스 요금제에 다른 앱이 없는 경우 해당 앱도 삭제할 수 있는 옵션이 제공됩니다. 저장소 계정 및 리소스 그룹과 같은 다른 리소스는 삭제되지 않습니다. 모든 리소스를 제거하려면 대신 [리소스 그룹을 삭제해야](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources)합니다. 로컬 프로젝트는 영향을 받지 않습니다. |
|**함수 삭제**  | Azure의 함수 앱에서 기존 함수를 제거합니다. 이 삭제는 로컬 프로젝트에 영향을 주지 않으므로 대신 로컬로 함수를 제거한 다음 [프로젝트를 다시 게시하는](#republish-project-files)것이 좋습니다. |
| **프록시 삭제** | Azure의 함수 앱에서 Azure Functions 프록시를 제거합니다. 프록시에 대한 자세한 내용은 [Azure 함수 프록시로 작업](functions-proxies.md)하기 를 참조하십시오. |
| **설정 삭제** | Azure에서 함수 앱 설정을 삭제합니다. 이 삭제는 local.settings.json 파일의 설정에 영향을 주지 않습니다. |
| **리포지토리에서 분리**  | Azure의 함수 앱과 소스 제어 리포지토리 간의 [지속적인 배포](functions-continuous-deployment.md) 연결을 제거합니다. |
| **원격 설정 다운로드** | Azure에서 선택한 함수 앱에서 local.settings.json 파일로 설정을 다운로드합니다. 로컬 파일이 암호화되면 해독, 업데이트 및 다시 암호화됩니다. 두 위치에 값이 충돌하는 설정이 있는 경우 진행 방법을 선택하라는 메시지가 표시됩니다. 이 명령을 실행하기 전에 변경 내용을 local.settings.json 파일에 저장해야 합니다. |
| **설정 편집** | Azure에서 기존 함수 앱 설정의 값을 변경합니다. 이 명령은 local.settings.json 파일의 설정에 영향을 주지 않습니다.  |
| **설정 암호화** | [로컬 설정에서](#local-settings-file)배열의 `Values` 개별 항목을 암호화합니다. 이 파일에서는 `IsEncrypted` 로컬 런타임이 설정을 사용하기 전에 암호를 해독하도록 지정하는 `true`로 설정됩니다. 로컬 설정을 암호화하여 중요한 정보가 유출될 위험을 줄입니다. Azure에서 응용 프로그램 설정은 항상 암호화된 저장됩니다. |
| **지금 기능 실행** | Azure에서 [타이머 트리거 함수를](functions-bindings-timer.md) 수동으로 시작합니다. 이 명령은 테스트에 사용됩니다. Azure에서 비 HTTP 함수를 트리거하는 방법에 대한 자세한 내용은 [HTTP 트리거가 아닌 함수를 수동으로 실행합니다.](functions-manually-run-non-http.md) |
| **VS 코드와 함께 사용할 프로젝트 초기화** | 기존 Functions 프로젝트에 필요한 Visual Studio Code 프로젝트 파일을 추가합니다. 이 명령을 사용하여 핵심 도구를 사용하여 만든 프로젝트로 작업할 수 있습니다. |
| **Azure 기능 핵심 도구 설치 또는 업데이트** | 로컬로 함수를 실행하는 데 사용되는 [Azure Functions 핵심 도구를]설치하거나 업데이트합니다. |
| **재배포**  | 연결된 Git 리포지토리에서 Azure의 특정 배포로 프로젝트 파일을 다시 배포할 수 있습니다. Visual Studio 코드에서 로컬 업데이트를 다시 게시하려면 [프로젝트를 다시 게시합니다.](#republish-project-files) |
| **설정 이름 바꾸기** | Azure에서 기존 함수 앱 설정의 키 이름을 변경합니다. 이 명령은 local.settings.json 파일의 설정에 영향을 주지 않습니다. Azure에서 설정 이름을 변경한 후 [해당 변경 내용을 로컬 프로젝트에 다운로드해야](#download-settings-from-azure)합니다. |
| **다시 시작** | Azure에서 함수 앱을 다시 시작합니다. 업데이트를 배포할 때도 함수 앱을 다시 시작합니다. |
| **AzureWebjobsStorage 설정**| 응용 프로그램 설정의 값을 설정합니다. `AzureWebJobsStorage` 이 설정은 Azure 함수에서 필요합니다. Azure에서 함수 앱을 만들 때 설정됩니다. |
| **시작** | Azure에서 중지된 함수 앱을 시작합니다. |
| **로그 스트리밍 시작** | Azure에서 함수 앱에 대한 스트리밍 로그를 시작합니다. 거의 실시간으로 로깅 정보를 확인해야 하는 경우 Azure에서 원격 트러블슈팅 중에 스트리밍 로그를 사용합니다. 자세한 내용은 [스트리밍 로그를](#streaming-logs)참조하십시오. |
| **중지** | Azure에서 실행 중인 함수 앱을 중지합니다. |
| **로그 스트리밍 중지** | Azure의 함수 앱에 대한 스트리밍 로그를 중지합니다. |
| **슬롯 설정으로 전환** | 활성화된 경우 지정된 배포 슬롯에 대해 응용 프로그램 설정이 유지되도록 합니다. |
| **Azure 기능 핵심 도구 제거** | 확장에 필요한 Azure 함수 핵심 도구를 제거합니다. |
| **로컬 설정 업로드** | local.settings.json 파일에서 Azure에서 선택한 함수 앱으로 설정을 업로드합니다. 로컬 파일이 암호화되면 해독, 업로드 및 다시 암호화됩니다. 두 위치에 값이 충돌하는 설정이 있는 경우 진행 방법을 선택하라는 메시지가 표시됩니다. 이 명령을 실행하기 전에 변경 내용을 local.settings.json 파일에 저장해야 합니다. |
| **GitHub에서 커밋 보기** | 함수 앱이 리포지토리에 연결되어 있을 때 특정 배포에서 최신 커밋을 표시합니다. |
| **배포 로그 보기** | Azure의 함수 앱에 대한 특정 배포에 대한 로그를 표시합니다. |

## <a name="next-steps"></a>다음 단계

Azure 함수 핵심 도구에 대한 자세한 내용은 [Azure 함수 핵심 도구 작업을](functions-run-local.md)참조하십시오.

.NET 클래스 라이브러리인 함수를 개발하는 방법에 대해 자세히 알아보려면 [Azure Functions C# 개발자 참조](functions-dotnet-class-library.md)를 참조하세요. 또한 이 문서에서는 특성을 사용하여 Azure Functions에서 지원하는 다양한 유형의 바인딩을 선언하는 방법에 대한 링크도 제공합니다.

[Visual Studio Code용 Azure Functions 확장]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure 기능 핵심 도구]: functions-run-local.md
