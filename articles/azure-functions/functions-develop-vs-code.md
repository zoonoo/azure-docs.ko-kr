---
title: Visual Studio Code를 사용하여 Azure Functions 개발
description: Visual Studio Code용 Azure Functions 확장을 사용하여 Azure Functions를 개발하고 테스트하는 방법을 알아봅니다.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: c2869b2b30722495523a9f0dfb2d70a17a205854
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871276"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Visual Studio Code를 사용하여 Azure Functions 개발

[Visual Studio Code용 Azure Functions 확장]을 사용하면 로컬에서 함수를 개발하고 Azure에 배포할 수 있습니다. Azure Functions를 처음으로 접하는 경우라면 [Azure Functions 소개](functions-overview.md)에서 자세한 내용을 확인할 수 있습니다.

Azure Functions 확장은 다음과 같은 이점을 제공합니다.

* 로컬 개발 컴퓨터에서 함수를 편집, 빌드 및 실행합니다.
* Azure에 직접 Azure Functions 프로젝트를 게시합니다.
* Visual Studio Code의 이점을 활용하면서 다양한 언어로 함수를 작성합니다.

확장은 다음 언어와 함께 사용할 수 있습니다. 이 언어는 버전 2.x부터 Azure Functions 런타임에서 지원됩니다.

* [C# 컴파일됨](functions-dotnet-class-library.md)
* [C#(스크립트)](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>[C# 스크립트를 기본 프로젝트 언어로 설정](#c-script-projects)해야 합니다.

이 문서에서 예는 현재 JavaScript(Node.js) 및 C# 클래스 라이브러리 함수에 대해서만 사용할 수 있습니다.  

이 문서에서는 Azure Functions 확장을 사용하여 함수를 개발하고 Azure에 게시하는 방법에 대한 세부 정보를 제공합니다. 이 문서를 읽기 전에 [Visual Studio Code를 사용하여 첫 번째 함수를 생성](./create-first-function-vs-code-csharp.md)해야 합니다.

> [!IMPORTANT]
> 단일 함수 앱에 대한 로컬 개발 및 포털 개발을 혼합하지 않도록 합니다. 로컬 프로젝트에서 함수 앱에 게시할 때 배포 프로세스는 포털에서 개발한 모든 기능을 덮어씁니다.

## <a name="prerequisites"></a>필수 조건

[Azure Functions 확장][Visual Studio Code용 Azure Functions 확장]을 설치하고 실행하기 전에 다음 요구 사항을 충족해야 합니다.

* [지원되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms) 중 하나에 [Visual Studio Code](https://code.visualstudio.com/)가 설치됩니다.

* 활성화된 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Azure Storage 계정과 같이 필요한 기타 리소스는 [Visual Studio Code를 사용하여 게시](#publish-to-azure)할 때 구독에 생성됩니다. 

### <a name="run-local-requirements"></a>로컬 요구 사항 실행

이러한 필수 조건은 [로컬에서 함수를 실행 및 디버그](#run-functions-locally)하는 경우에만 필요합니다. Azure Functions에 대한 프로젝트를 만들거나 게시하지 않아도 됩니다.

# <a name="c"></a>[C\#](#tab/csharp)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 버전 2.x 이상 프로젝트를 로컬로 시작하면 Core Tools 패키지가 자동으로 다운로드되어 설치됩니다. Core Tools는 전체 Azure Functions 런타임을 포함하므로 다운로드 및 설치에 시간이 걸릴 수 있습니다.

+ Visual Studio Code용 [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp). 

+ [.NET Core CLI 도구](/dotnet/core/tools/?tabs=netcore2x).  

# <a name="java"></a>[Java](#tab/java)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 버전 2.x 이상 프로젝트를 로컬로 시작하면 Core Tools 패키지가 자동으로 다운로드되어 설치됩니다. Core Tools는 전체 Azure Functions 런타임을 포함하므로 다운로드 및 설치에 시간이 걸릴 수 있습니다.

+ [Java 확장에 대한 디버거](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug).

+ [Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)이 권장됩니다. 지원되는 다른 버전은 [Java 버전](functions-reference-java.md#java-versions)을 참조하세요.

+ [Maven 3 이상](https://maven.apache.org/)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 버전 2.x 이상 프로젝트를 로컬로 시작하면 Core Tools 패키지가 자동으로 다운로드되어 설치됩니다. Core Tools는 전체 Azure Functions 런타임을 포함하므로 다운로드 및 설치에 시간이 걸릴 수 있습니다.

+ [Node.js](https://nodejs.org/), 활성 LTS 및 유지 관리 LTS 버전(10.14.1 권장). `node --version` 명령을 사용하여 버전을 확인합니다. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 버전 2.x 이상 프로젝트를 로컬로 시작하면 Core Tools 패키지가 자동으로 다운로드되어 설치됩니다. Core Tools는 전체 Azure Functions 런타임을 포함하므로 다운로드 및 설치에 시간이 걸릴 수 있습니다.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows) 권장. 버전 정보는 [PowerShell 버전](functions-reference-powershell.md#powershell-versions)을 참조하세요.

+ [.NET Core 3.1 런타임](https://dotnet.microsoft.com/download) 및 [.NET Core 2.1 런타임](https://dotnet.microsoft.com/download/dotnet/2.1) 모두  

+ [Visual Studio Code용 PowerShell 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

# <a name="python"></a>[Python](#tab/python)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 버전 2.x 이상 프로젝트를 로컬로 시작하면 Core Tools 패키지가 자동으로 다운로드되어 설치됩니다. Core Tools는 전체 Azure Functions 런타임을 포함하므로 다운로드 및 설치에 시간이 걸릴 수 있습니다.

+ [Python 3.x](https://www.python.org/downloads/). 버전 정보는 Azure Functions 런타임별 [Python 버전](functions-reference-python.md#python-version)을 참조하세요.

+ Visual Studio Code용 [Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

---

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Azure Functions 프로젝트 만들기

Functions 확장을 사용하면 첫 번째 함수와 함께 함수 앱 프로젝트를 만들 수 있습니다. 다음 단계에서는 새 Functions 프로젝트에서 HTTP 트리거 함수를 만드는 방법을 보여 줍니다. [HTTP 트리거](functions-bindings-http-webhook.md)는 시연할 가장 간단한 함수 트리거 템플릿입니다.

1. **Azure: Functions** 에서 **함수 만들기** 아이콘을 선택합니다.

    ![함수 만들기](./media/functions-develop-vs-code/create-function.png)

1. 함수 앱 프로젝트의 폴더를 선택한 다음 **함수 프로젝트의 언어를 선택** 합니다.

1. **HTTP 트리거** 함수 템플릿을 선택하거나 **지금 건너뛰기** 를 선택하여 함수 없이 프로젝트를 만들 수 있습니다. 나중에 언제든지 [프로젝트에 함수를 추가](#add-a-function-to-your-project)할 수 있습니다.

    ![HTTP 트리거 템플릿 선택](./media/functions-develop-vs-code/create-function-choose-template.png)

1. 함수 이름으로 **HttpExample** 을 입력하고 Enter 키를 선택한 다음 **Function** 권한 부여를 선택합니다. 이 권한 부여 수준에서는 함수 엔드포인트를 호출할 때 [함수 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 제공해야 합니다.

    ![함수 권한 부여 선택](./media/functions-develop-vs-code/create-function-auth.png)

    HTTP 트리거 함수에 대한 템플릿을 사용하여 선택한 언어로 함수가 만들어집니다.

    ![Visual Studio Code에서 HTTP 트리거 함수 템플릿](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>생성된 프로젝트 파일

프로젝트 템플릿은 선택한 언어로 프로젝트를 만들고 필요한 종속성을 설치합니다. 모든 언어에 대해 새 프로젝트에는 다음 파일이 있습니다.

* **host.json**: 함수 호스트를 구성할 수 있습니다. 이러한 설정은 로컬에서 함수를 실행하는 경우와 Azure에서 함수를 실행하는 경우에 적용됩니다. 자세한 내용은 [host.json 참조](functions-host-json.md)를 참조하세요.

* **local.settings.json**: 함수를 로컬로 실행할 때 사용되는 설정을 유지합니다. 이러한 설정은 함수를 로컬로 실행하는 경우에만 사용됩니다. 자세한 내용은 [로컬 설정 파일](#local-settings-file)을 참조하세요.

    >[!IMPORTANT]
    >local.settings.json 파일에 암호가 있을 수 있으므로 프로젝트 원본 제어에서 해당 파일을 제외해야 합니다.

언어에 따라 다음과 같은 다른 파일이 만들어집니다.

# <a name="c"></a>[C\#](#tab/csharp)

* 함수를 구현하는 [HttpExample.cs 클래스 라이브러리 파일](functions-dotnet-class-library.md#functions-class-library-project).

# <a name="java"></a>[Java](#tab/java)

+ 프로젝트 종속성과 [Java 버전](functions-reference-java.md#java-versions)을 포함하여 프로젝트 및 배포 매개 변수를 정의하는 루트 폴더의 pom.xml 파일. pom.xml에는 배포 중에 만들어지는 Azure 리소스에 대한 정보도 포함되어 있습니다.   

+ 함수를 구현하는 src 경로의 [Functions.java 파일](functions-reference-java.md#triggers-and-annotations).

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* 루트 폴더의 package.json 파일.

* [function.json 정의 파일](functions-reference-node.md#folder-structure) 및 함수 코드가 포함된 Node.js 파일인 [index.js 파일](functions-reference-node.md#exporting-a-function)을 포함하는 HttpExample 폴더.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* [function.json 정의 파일](functions-reference-powershell.md#folder-structure) 및 함수 코드가 포함된 run.ps1 파일을 포함하는 HttpExample 폴더.
 
# <a name="python"></a>[Python](#tab/python)
    
* Functions에 필요한 패키지를 나열하는 프로젝트 수준 requirements.txt 파일.
    
* [function.json 정의 파일](functions-reference-python.md#folder-structure) 및 함수 코드가 포함된 \_\_init\_\_.py 파일을 포함하는 HttpExample 폴더.

---

여기서 함수에 [입력 및 출력 결합을 추가](#add-input-and-output-bindings)할 수 있습니다. [프로젝트에 새 함수를 추가](#add-a-function-to-your-project)할 수도 있습니다.

## <a name="install-binding-extensions"></a>바인딩 확장 설치

HTTP 및 타이머 트리거를 제외하고는 바인딩이 확장 패키지로 구현됩니다. 필요한 트리거 및 바인딩에 대한 확장 패키지를 설치해야 합니다. 바인딩 확장을 설치하는 프로세스는 프로젝트의 언어에 따라 달라집니다.

# <a name="c"></a>[C\#](#tab/csharp)

터미널 창에서 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 명령을 실행하여 프로젝트에 필요한 확장 패키지를 설치합니다. 다음 명령은 Blob, 큐 및 테이블 스토리지에 대한 바인딩을 구현하는 Azure Storage 확장을 설치합니다.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="java"></a>[Java](#tab/java)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>프로젝트에 함수 추가

미리 정의된 Functions 트리거 템플릿 중 하나를 사용하여 기존 프로젝트에 새 함수를 추가할 수 있습니다. 새 함수 트리거를 추가하려면 F1 키를 선택하여 명령 팔레트를 연 다음 **Azure Functions: Create Function** 명령을 검색하여 실행합니다. 프롬프트에 따라 트리거 유형을 선택하고 트리거의 필수 특성을 정의합니다. 서비스에 연결하기 위해 트리거에 액세스 키 또는 연결 문자열이 필요한 경우 함수 트리거를 만들기 전에 준비합니다.

이 작업의 결과는 프로젝트의 언어에 따라 달라집니다.

# <a name="c"></a>[C\#](#tab/csharp)

새 C# 클래스 라이브러리(.cs) 파일이 프로젝트에 추가됩니다.

# <a name="java"></a>[Java](#tab/java)

새 Java(java) 파일이 프로젝트에 추가됩니다.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

프로젝트에 새 폴더가 생성됩니다. 이 폴더에는 새 function.json 파일 및 새 JavaScript 코드 파일이 포함되어 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

프로젝트에 새 폴더가 생성됩니다. 이 폴더에는 새 function.json 파일 및 새 PowerShell 코드 파일이 포함되어 있습니다.

# <a name="python"></a>[Python](#tab/python)

프로젝트에 새 폴더가 생성됩니다. 이 폴더에는 새 function.json 파일 및 새 Python 코드 파일이 포함되어 있습니다.

---

## <a name="connect-to-services"></a><a name="add-input-and-output-bindings"></a>서비스에 연결

입력 및 출력 바인딩을 추가하여 함수를 다른 Azure 서비스에 연결할 수 있습니다. 바인딩은 연결 코드를 작성하지 않고도 함수를 다른 서비스에 연결합니다. 바인딩을 추가하는 프로세스는 프로젝트의 언어에 따라 달라집니다. 바인딩에 대해 자세히 알아보려면 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조하세요.

다음 예는 `outqueue`이라는 스토리지 큐에 연결합니다. 여기서 스토리지 계정에 대한 연결 문자열은 local.settings.json의 `MyStorageConnection` 애플리케이션 설정에서 지정됩니다.

# <a name="c"></a>[C\#](#tab/csharp)

`Run` 메서드 정의에 다음 매개 변수를 추가하도록 함수 메서드를 업데이트합니다.

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

`msg` 매개 변수는 `ICollector<T>` 형식이며, 함수가 완료될 때 출력 바인딩에 작성되는 메시지의 컬렉션을 나타냅니다. 다음 코드는 컬렉션에 메시지를 추가합니다.

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="30-31":::

 함수가 완료되면 큐에 메시지가 전송됩니다.

자세한 내용은 [Queue Storage 출력 바인딩 참조 문서](functions-bindings-storage-queue-output.md?tabs=csharp) 설명서를 참조하세요. 함수에 추가할 수 있는 바인딩에 대한 일반적인 내용은 [Azure Functions에서 기존 함수에 바인딩 추가](add-bindings-existing-function.md?tabs=csharp)를 참조하세요. 

# <a name="java"></a>[Java](#tab/java)

`Run` 메서드 정의에 다음 매개 변수를 추가하도록 함수 메서드를 업데이트합니다.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

`msg` 매개 변수는 `OutputBinding<T>` 유형이며, 여기서 `T`는 함수가 완료될 때 출력 바인딩에 기록되는 문자열입니다. 다음 코드는 출력 바인딩에서 메시지를 설정합니다.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33-34":::

이 메시지는 함수가 완료되면 큐에 전송됩니다.

자세한 내용은 [Queue Storage 출력 바인딩 참조 문서](functions-bindings-storage-queue-output.md?tabs=java) 설명서를 참조하세요. 함수에 추가할 수 있는 바인딩에 대한 일반적인 내용은 [Azure Functions에서 기존 함수에 바인딩 추가](add-bindings-existing-function.md?tabs=java)를 참조하세요. 

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

함수 코드에서 `msg` 바인딩은 다음 예와 같이 `context`에서 액세스합니다.

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

이 메시지는 함수가 완료되면 큐에 전송됩니다.

자세한 내용은 [Queue Storage 출력 바인딩 참조 문서](functions-bindings-storage-queue-output.md?tabs=javascript) 설명서를 참조하세요. 함수에 추가할 수 있는 바인딩에 대한 일반적인 내용은 [Azure Functions에서 기존 함수에 바인딩 추가](add-bindings-existing-function.md?tabs=javascript)를 참조하세요. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

이 메시지는 함수가 완료되면 큐에 전송됩니다.

자세한 내용은 [Queue Storage 출력 바인딩 참조 문서](functions-bindings-storage-queue-output.md?tabs=powershell) 설명서를 참조하세요. 함수에 추가할 수 있는 바인딩에 대한 일반적인 내용은 [Azure Functions에서 기존 함수에 바인딩 추가](add-bindings-existing-function.md?tabs=powershell)를 참조하세요. 

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

정의가 다음 예와 같이 보이도록 `Main` 정의를 업데이트하여 출력 매개 변수 `msg: func.Out[func.QueueMessage]`를 추가합니다.

:::code language="python" range="6" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

다음 코드에서는 요청에서 출력 큐에 문자열 데이터를 추가합니다.

:::code language="python" range="18" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

이 메시지는 함수가 완료되면 큐에 전송됩니다.

자세한 내용은 [Queue Storage 출력 바인딩 참조 문서](functions-bindings-storage-queue-output.md?tabs=python) 설명서를 참조하세요. 함수에 추가할 수 있는 바인딩에 대한 일반적인 내용은 [Azure Functions에서 기존 함수에 바인딩 추가](add-bindings-existing-function.md?tabs=python)를 참조하세요. 

---

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Azure에 게시

Visual Studio Code를 사용하면 Azure에 직접 함수 프로젝트를 게시할 수 있습니다. 프로세스를 통해 함수 앱 및 관련된 리소스를 Azure 구독에서 만듭니다. 함수 앱은 함수를 위한 실행 컨텍스트를 제공합니다. 프로젝트는 패키지되어 Azure 구독에서 새 함수 앱에 배포됩니다.

Visual Studio Code에서 Azure의 새 함수 앱으로 게시할 때 기본값을 사용하여 빠른 함수 앱 만들기 경로를 선택하거나 생성된 원격 리소스를 더 많이 제어할 수 있는 고급 경로를 선택할 수 있습니다. 

Visual Studio Code에서 게시할 때 [Zip 배포](functions-deployment-technologies.md#zip-deploy) 기술을 활용합니다. 

### <a name="quick-function-app-create"></a>빠른 함수 앱 만들기

**+ Azure에서 새 함수 앱 만들기...** 를 선택하면 확장이 함수 앱에 필요한 Azure 리소스에 대한 값을 자동으로 생성합니다. 이러한 값은 선택한 함수 앱 이름을 기반으로 합니다. 기본값을 사용하여 Azure의 새 함수 앱에 프로젝트를 게시하는 예는 [Visual Studio Code 빠른 시작 문서](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure)를 참조하세요.

만들어진 리소스에 대한 명시적 이름을 제공하려면 고급 만들기 경로를 선택해야 합니다.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>고급 옵션을 사용하여 Azure의 새 함수 앱에 프로젝트 게시

다음 단계에서는 고급 만들기 옵션을 사용하여 만든 새 함수 앱에 프로젝트를 게시합니다.

1. 명령 팔레트에 **Azure Functions: Deploy to function app** 을 입력합니다.

1. 로그인하지 않은 경우 **Azure에 로그인** 하라는 메시지가 표시됩니다. 또한 **무료 Azure 계정을 만들** 수 있습니다. 브라우저에서 로그인한 후 Visual Studio Code로 돌아갑니다.

1. 여러 구독이 있다면 함수 앱에 대해 **구독을 선택** 한 다음, **+ Azure에서 새 함수 앱 만들기 _고급_** 을 선택합니다. 이 _고급_ 옵션을 사용하면 Azure에서 생성하는 리소스를 더 세부적으로 제어할 수 있습니다. 

1. 프롬프트에 따라 다음 정보를 제공합니다.

    | prompt | 값 | Description |
    | ------ | ----- | ----------- |
    | Azure에서 함수 앱 선택 | Azure에서 새 함수 앱 만들기 | 다음 프롬프트에서 새 함수 앱을 식별하는 전역적으로 고유한 이름을 입력한 다음 Enter 키를 선택합니다. 함수 앱 이름에 대한 유효한 문자는 `a-z`, `0-9` 및 `-`입니다. |
    | OS 선택 | Windows | Windows에서 함수 앱이 실행됩니다. |
    | 호스팅 계획 선택 | 소비 계획 | 서버리스 [사용 계획 호스팅](consumption-plan.md)이 사용됩니다. |
    | 새 앱에 대한 런타임 선택 | 프로젝트 언어 | 런타임은 게시 중인 프로젝트와 일치해야 합니다. |
    | 새 리소스에 대한 리소스 그룹 선택 | 새 리소스 그룹 만들기 | 다음 프롬프트에서 `myResourceGroup`과 같은 리소스 그룹 이름을 입력하고 Enter 키를 선택합니다. 기존 리소스 그룹을 선택할 수도 있습니다. |
    | 스토리지 계정 선택 | 새 스토리지 계정 만들기 | 다음 프롬프트에서 함수 앱에서 사용하는 새 스토리지 계정에 대한 전역적으로 고유한 이름을 입력한 다음 Enter 키를 선택합니다. 스토리지 계정 이름은 3~24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 기존 계정을 선택할 수도 있습니다. |
    | 새 리소스의 위치 선택 | region | 사용자 또는 함수가 액세스하는 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)의 위치를 선택합니다. |

    함수 앱을 만들고 배포 패키지가 적용된 후 알림이 표시됩니다. 이 알림에서 **출력 보기** 를 선택하여 사용자가 만든 Azure 리소스를 포함한 만들기 및 배포 결과를 표시합니다.

### <a name="get-the-url-of-an-http-triggered-function-in-azure"></a><a name="get-the-url-of-the-deployed-function"></a>Azure에서 HTTP 트리거 함수의 URL 가져오기

클라이언트에서 HTTP로 트리거되는 함수를 호출하려면 함수 앱에 배포될 때 함수의 URL이 필요합니다. 이 URL에는 필요한 모든 함수 키가 포함되어 있습니다. 확장을 사용하여 배포된 함수에 대한 이러한 URL을 가져올 수 있습니다. Azure에서 원격 함수를 실행하려면 [지금 함수 실행 확장 기능을 사용합니다](#run-functions-in-azure).

1. F1 키를 선택하여 명령 팔레트를 연 다음 **Azure Functions: Copy Function URL** 명령을 검색하여 실행합니다.

1. 프롬프트에 따라 Azure에서 함수 앱을 선택하고 호출하려는 특정 HTTP 트리거를 선택합니다.

함수 URL은 `code` 쿼리 매개 변수에 의해 전달된 필수 키와 함께 클립보드에 복사됩니다. POST 요청을 제출하려면 HTTP 도구를 사용하고 원격 함수에 대한 GET 요청을 수행하려면 브라우저를 사용합니다.  

Azure에서 함수의 URL을 가져올 때 확장은 Azure 계정을 사용하여 함수를 시작하는 데 필요한 키를 자동으로 검색합니다. [함수 액세스 키에 대해 자세히 알아보기](security-concepts.md#function-access-keys) HTTP가 아닌 트리거된 함수를 시작하려면 관리자 키를 사용해야 합니다.

## <a name="republish-project-files"></a>프로젝트 파일 다시 게시

[지속적 배포](functions-continuous-deployment.md)를 설정하면 연결된 소스 위치에서 소스 파일을 업데이트할 때 Azure의 함수 앱이 업데이트됩니다. 연속 배포를 권장하지만 Visual Studio Code에서 프로젝트 파일 업데이트를 다시 게시할 수도 있습니다.

> [!IMPORTANT]
> 기존 함수 앱에 게시하면 Azure에서 해당 앱의 콘텐츠를 덮어씁니다.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="run-functions"></a>함수 실행

Azure Functions 확장을 사용하면 로컬 개발 컴퓨터의 프로젝트 또는 Azure 구독에서 개별 함수를 실행할 수 있습니다. 

HTTP 트리거 함수의 경우 확장은 HTTP 엔드포인트를 호출합니다. 다른 종류의 트리거의 경우에는 관리자 API를 호출하여 함수를 시작합니다. 함수로 전송되는 요청의 메시지 본문은 트리거의 유형에 따라 달라집니다. 트리거에서 테스트 데이터를 요구하는 경우 특정 JSON 형식으로 데이터를 입력하라는 메시지가 표시됩니다.

### <a name="run-functions-in-azure"></a>Azure에서 함수 실행

Visual Studio Code를 사용하여 Azure에서 함수를 실행하려면 다음을 수행합니다. 

1. 명령 팔레트에서 **Azure Functions: Execute function now** 를 입력하고 Azure 구독을 선택합니다. 

1. 목록에서 Azure의 함수 앱을 선택합니다. 함수 앱이 표시되지 않는 경우 올바른 구독에 로그인했는지 확인합니다. 

1. 목록에서 실행할 함수를 선택하고 **요청 본문 입력** 에 요청의 메시지 본문을 입력합니다. Enter를 눌러 이 요청 메시지를 함수로 보냅니다. **요청 본문 입력** 의 기본 텍스트는 본문 형식이어야 합니다. 함수 앱에 함수가 없으면 이 오류와 함께 알림 오류가 표시됩니다. 

1. 함수가 Azure에서 실행되고 응답을 반환하는 경우 Visual Studio Code에서 알림이 발생합니다.
 
Azure 구독의 함수 앱에서 실행할 함수를 마우스 오른쪽 단추로 클릭(Mac의 경우 Ctrl 클릭)하고 **지금 함수 실행...** 을 선택하여 **Azure: Functions** 영역에서 함수를 실행할 수도 있습니다.

Azure에서 함수를 실행할 때 확장은 Azure 계정을 사용하여 함수를 시작하는 데 필요한 키를 자동으로 검색합니다. [함수 액세스 키에 대해 자세히 알아보기](security-concepts.md#function-access-keys) HTTP가 아닌 트리거된 함수를 시작하려면 관리자 키를 사용해야 합니다.

### <a name="run-functions-locally"></a>로컬로 함수 실행

로컬 런타임은 Azure에서 함수 앱을 호스트하는 것과 동일한 런타임입니다. 로컬 설정은 [local.settings.json 파일](#local-settings-file)에서 읽습니다. Functions 프로젝트를 로컬로 실행하려면 [추가 요구 사항](#run-local-requirements)을 충족해야 합니다.

#### <a name="configure-the-project-to-run-locally"></a>로컬로 실행되도록 프로젝트 구성

Functions 런타임은 HTTP 및 웹후크가 아닌 모든 트리거 형식에 대해 내부적으로 Azure Storage 계정을 사용합니다. 따라서 **Values.AzureWebJobsStorage** 키를 유효한 Azure Storage 계정 연결 문자열로 설정해야 합니다.

이 섹션에서는 [Azure Storage Explorer](https://storageexplorer.com/)와 함께 [Visual Studio Code용 Azure Storage 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)을 사용하여 스토리지 연결 문자열에 연결하고 검색합니다.

스토리지 계정 연결 문자열을 설정하려면 다음을 수행합니다.

1. Visual Studio에서 **클라우드 탐색기** 를 열고 **스토리지 계정** > **스토리지 계정** 을 확장한 후 **속성** 을 선택하고 **기본 연결 문자열** 값을 복사합니다.

2. 사용자 프로젝트에서 local.settings.json 파일을 열고 **AzureWebJobsStorage** 키의 값을 복사한 연결 문자열로 설정합니다.

3. 이전 단계를 반복하여 사용자의 함수에 필요한 다른 연결에 대한 **값** 배열에 고유 키를 추가합니다.

자세한 내용은 [로컬 설정 파일](#local-settings-file)을 참조하세요.

#### <a name="debug-functions-locally"></a><a name="debugging-functions-locally"></a>로컬로 함수 디버그  

함수를 디버깅하려면 F5 키를 선택합니다. [Core Tools][Azure Functions Core Tools]를 아직 다운로드하지 않았다면 다운로드하라는 메시지가 표시됩니다. Core Tools를 설치하고 실행하는 경우 출력은 터미널에 표시됩니다. 이는 터미널에서 `func host start` Core Tools 명령을 실행하는 것과 동일하지만 추가 빌드 작업과 연결된 디버거가 있습니다.  

프로젝트가 실행 중일 때 확장의 **지금 함수 실행...** 기능을 사용하여 프로젝트가 Azure에 배포될 때와 같이 함수를 트리거할 수 있습니다. 디버그 모드에서 프로젝트를 실행하면 예상대로 Visual Studio Code에서 중단점이 적중됩니다. 

1. 명령 팔레트에서 **Azure Functions: Execute function now** 을 입력하고 **로컬 프로젝트** 를 선택합니다. 

1. 프로젝트에서 실행할 함수를 선택하고 **요청 본문 입력** 에 요청의 메시지 본문을 입력합니다. Enter를 눌러 이 요청 메시지를 함수로 보냅니다. **요청 본문 입력** 의 기본 텍스트는 본문 형식이어야 합니다. 함수 앱에 함수가 없으면 이 오류와 함께 알림 오류가 표시됩니다. 

1. 함수가 로컬로 실행되고 응답이 수신된 후 Visual Studio Code에서 알림이 발생합니다. 기능 실행에 대한 정보는 **터미널** 패널에 표시됩니다.

함수를 로컬로 실행하려면 키를 사용하지 않아도 됩니다. 

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

기본적으로 이러한 설정은 프로젝트가 Azure에 게시될 때 자동으로 마이그레이션되지 않습니다. 게시가 완료된 후 Azure에서 함수 앱에 local.settings.json의 설정을 게시하는 옵션이 제공됩니다. 자세한 내용은 [애플리케이션 설정 게시](#publish-application-settings)를 참조하세요.

**ConnectionStrings** 의 값은 절대 게시되지 않습니다.

코드에서 함수 애플리케이션 설정 값을 환경 변수로 읽을 수도 있습니다. 자세한 내용은 다음 언어별 참조 문서의 Environment 변수 섹션을 참조하세요.

* [미리 컴파일된 C#](functions-dotnet-class-library.md#environment-variables)
* [C# 스크립트(.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

## <a name="application-settings-in-azure"></a>Azure의 애플리케이션 설정

프로젝트의 local.settings.json 파일에 대한 설정은 Azure의 함수 앱에 있는 애플리케이션 설정과 동일해야 합니다. local.settings.json에 추가하는 모든 설정은 Azure의 함수 앱에도 추가해야 합니다. 이러한 설정은 프로젝트를 게시할 때 자동으로 업로드되지 않습니다. 마찬가지로 [포털](functions-how-to-use-azure-function-app-settings.md#settings)의 함수 앱에서 만든 모든 설정은 로컬 프로젝트에 다운로드해야 합니다.

### <a name="publish-application-settings"></a>애플리케이션 설정 게시

Azure의 함수 앱에 필요한 설정을 게시하는 가장 쉬운 방법은 프로젝트를 게시한 후 표시되는 **설정 업로드** 링크를 사용하는 것입니다.

![애플리케이션 설정 업로드](./media/functions-develop-vs-code/upload-app-settings.png)

명령 팔레트에서 **Azure Functions: Upload Local Setting** 명령을 사용하여 설정을 게시할 수도 있습니다. **Azure Functions: Add New Setting** 명령을 사용하여 Azure의 애플리케이션 설정에 개별 설정을 추가할 수 있습니다.

> [!TIP]
> local.settings.json을 파일을 게시하기 전에 저장해야 합니다.

로컬 파일이 암호화되어 있는 경우 암호 해독, 게시 및 암호화가 다시 수행됩니다. 두 위치에 충돌하는 값이 있는 설정이 있는 경우 계속하는 방법을 선택하라는 메시지가 표시됩니다.

구독, 함수 앱 및 **애플리케이션 설정** 을 확장하여 **Azure: Functions** 영역에서 기존 앱 설정을 봅니다.

![Visual Studio Code에서 함수 앱 설정 보기](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Azure에서 설정 다운로드

Azure에서 애플리케이션 설정을 만든 경우 **Azure Functions: Download Remote Settings** 명령을 사용하여 local.settings.json 파일에 다운로드할 수 있습니다.

업로드와 마찬가지로 로컬 파일이 암호화되어 있는 경우 암호 해독, 업데이트 및 암호화가 다시 수행됩니다. 두 위치에 충돌하는 값이 있는 설정이 있는 경우 계속하는 방법을 선택하라는 메시지가 표시됩니다.

## <a name="monitoring-functions"></a>함수 모니터링

[로컬로 함수를 실행](#run-functions-locally)하면 로그 데이터가 터미널 콘솔로 스트리밍됩니다. Functions 프로젝트가 Azure의 함수 앱에서 실행되는 경우 로그 데이터를 가져올 수도 있습니다. Azure에서 스트리밍 로그에 연결하여 거의 실시간으로 로그 데이터를 확인하거나 함수 앱의 동작 방식을 보다 완전히 파악하기 위해 Application Insights를 사용하도록 설정할 수 있습니다.

### <a name="streaming-logs"></a>스트리밍 로그

애플리케이션을 개발하는 동안 거의 실시간의 로깅 정보를 보는 것이 종종 유용합니다. 함수에 의해 생성되는 로그 파일의 스트리밍을 볼 수 있습니다. 이 출력은 HTTP로 트리거되는 함수에 대한 요청의 스트리밍 로그 예입니다.

![HTTP 트리거에 대한 스트리밍 로그 출력](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

자세한 내용은 [스트리밍 로그](functions-monitoring.md#streaming-logs)를 참조하세요.

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> 스트리밍 로그는 Functions 호스트의 단일 인스턴스만 지원합니다. 함수를 여러 인스턴스로 확장하는 경우 다른 인스턴스의 데이터가 로그 스트림에 표시되지 않습니다. Application Insights의 [라이브 메트릭 스트림](../azure-monitor/app/live-stream.md)은 여러 인스턴스를 지원합니다. 스트리밍 분석은 거의 실시간으로 [샘플링된 데이터](configure-monitoring.md#configure-sampling)를 기반으로 합니다.

### <a name="application-insights"></a>Application Insights

함수 앱을 Application Insights와 통합하여 함수 실행을 모니터링하는 것이 좋습니다. Azure Portal에서 함수 앱을 만들 때 이 통합은 기본적으로 발생합니다. Visual Studio 게시 중에 함수 앱을 만들 때 Application Insights를 직접 통합해야 합니다. 방법을 알아보려면 [Application Insights 통합 사용](configure-monitoring.md#enable-application-insights-integration)을 참조하세요.

Application Insights를 사용하여 모니터링하는 방법에 대한 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요.

## <a name="c-script-projects"></a>C\# 스크립트 프로젝트

기본적으로 모든 C# 프로젝트는 [C#으로 컴파일된 클래스 라이브러리 프로젝트](functions-dotnet-class-library.md)로 생성됩니다. 대신 C# 스크립트 프로젝트를 사용하려면 Azure Functions 확장 설정에서 C# 스크립트를 기본 언어로 선택해야 합니다.

1. **파일** > **기본 설정** > **설정** 을 선택합니다.

1. **사용자 설정** > **확장** > **Azure Functions** 로 이동합니다.

1. **Azure Function: Project Language** 에서 **C#Script** 를 선택합니다.

이 단계를 완료한 후 기본 Core Tools에 대한 호출에는 C# 스크립트(.csx) 프로젝트 파일을 생성하고 게시하는 `--csx` 옵션이 포함됩니다. 이 기본 언어를 지정하면 사용자가 만드는 모든 프로젝트는 기본적으로 C# 스크립트 프로젝트로 지정됩니다. 기본값이 설정되면 프로젝트 언어를 선택하라는 메시지가 표시되지 않습니다. 다른 언어로 프로젝트를 만들려면 이 설정을 변경하거나 사용자 settings.json 파일에서 제거해야 합니다. 이 설정을 제거하면 프로젝트를 만들 때 언어를 선택하라는 메시지가 다시 표시됩니다.

## <a name="command-palette-reference"></a>명령 팔레트 참조

Azure Functions 확장은 Azure에서 함수 앱과 상호 작용하기 위한 영역에서 유용한 그래픽 인터페이스를 제공합니다. 명령 팔레트(F1)에서도 같은 기능을 명령으로 사용할 수 있습니다. 다음 Azure Functions 명령을 사용할 수 있습니다.

|Azure Functions 명령  | Description  |
|---------|---------|
|**새 설정 추가**  |  Azure에서 새 애플리케이션 설정을 만듭니다. 자세한 내용은 [애플리케이션 설정 게시](#publish-application-settings)를 참조하세요. [이 설정을 로컬 설정으로 다운로드](#download-settings-from-azure)해야 할 수도 있습니다. |
| **배포 원본 구성** | Azure의 함수 앱을 로컬 Git 리포지토리에 연결합니다. 자세히 알아보려면 [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)를 참조하세요. |
| **GitHub 리포지토리에 연결** | 함수 앱을 GitHub 리포지토리에 연결합니다. |
| **함수 URL 복사** | Azure에서 실행되는 HTTP 트리거 함수의 원격 URL을 가져옵니다. 자세한 내용은 [배포된 함수의 URL 가져오기](#get-the-url-of-the-deployed-function)를 참조하세요. |
| **Azure에서 함수 앱 만들기** | Azure의 구독에서 새 함수 앱을 만듭니다. 자세한 내용은 [Azure에서 새 함수 앱에 게시](#publish-to-azure)하는 방법에 대한 섹션을 참조하세요.        |
| **설정 암호 해독** | **Azure Functions: Encrypt Settings** 에 의해 암호화된 [로컬 설정](#local-settings-file)을 해독합니다.  |
| **함수 앱 삭제** | Azure의 구독에서 함수 앱을 제거합니다. App Service 계획에 다른 앱이 없는 경우 해당 앱을 삭제하는 옵션도 제공됩니다. 스토리지 계정 및 리소스 그룹과 같은 다른 리소스는 삭제되지 않습니다. 모든 리소스를 제거하려면 대신 [리소스 그룹을 삭제](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources)해야 합니다. 로컬 프로젝트가 영향을 받지 않습니다. |
|**함수 삭제**  | Azure의 함수 앱에서 기존 함수를 제거합니다. 이 삭제는 로컬 프로젝트에 영향을 미치지 않으므로 대신 로컬로 함수를 제거한 다음 [프로젝트를 다시 게시](#republish-project-files)하는 것이 좋습니다. |
| **프록시 삭제** | Azure의 함수 앱에서 Azure Functions 프록시를 제거합니다. 프록시에 대한 자세한 내용은 [Azure Functions 프록시 사용](functions-proxies.md)을 참조하세요. |
| **설정 삭제** | Azure에서 함수 앱 설정을 삭제합니다. 이 삭제는 local.settings.json 파일의 설정에 영향을 주지 않습니다. |
| **리포지토리에서 연결 해제**  | Azure의 함수 앱과 소스 제어 리포지토리 간의 [지속적 배포](functions-continuous-deployment.md) 연결을 제거합니다. |
| **원격 설정 다운로드** | Azure에서 선택한 함수 앱의 설정을 local.settings.json 파일로 다운로드합니다. 로컬 파일이 암호화되면 암호 해독, 업데이트 및 암호화가 다시 수행됩니다. 두 위치에 충돌하는 값이 있는 설정이 있는 경우 계속하는 방법을 선택하라는 메시지가 표시됩니다. 이 명령을 실행하기 전에 local.settings.json 파일에 대한 변경 내용을 저장해야 합니다. |
| **설정 편집** | Azure의 기존 함수 앱 설정 값을 변경합니다. 이 명령은 local.settings.json 파일의 설정에 영향을 주지 않습니다.  |
| **암호화 설정** | [로컬 설정](#local-settings-file)에서 `Values` 배열의 개별 항목을 암호화합니다. 이 파일에서 `IsEncrypted`도 `true`로 설정되어 로컬 런타임이 설정을 사용하기 전에 암호 해독하도록 지정합니다. 로컬 설정을 암호화하여 중요한 정보 누출의 위험을 줄입니다. Azure에서 애플리케이션 설정은 항상 암호화된 상태로 저장됩니다. |
| **지금 함수 실행** | 관리 API를 사용하여 함수를 수동으로 시작합니다. 이 명령은 디버깅 중에 로컬로 테스트하거나 Azure에서 실행되는 함수에 대해 테스트하는 데 사용됩니다. Azure에서 함수를 트리거하는 경우 확장은 먼저 Azure에서 함수를 시작하는 원격 관리 API를 호출하는 데 사용하는 관리자 키를 자동으로 가져옵니다. API로 전송되는 메시지의 본문은 트리거의 유형에 따라 달라집니다. 타이머 트리거는 데이터를 전달할 필요가 없습니다. |
| **VS Code에서 사용할 프로젝트 초기화** | 필요한 Visual Studio Code 프로젝트 파일을 기존 Functions 프로젝트에 추가합니다. Core Tools를 사용하여 만든 프로젝트로 작업하려면 이 명령을 사용합니다. |
| **Azure Functions Core Tools 설치 또는 업데이트** | 로컬로 함수를 실행하는 데 사용되는 [Azure Functions Core Tools]를 설치하거나 업데이트합니다. |
| **재배포**  | 연결된 Git 리포지토리의 프로젝트 파일을 Azure의 특정 배포에 다시 배포할 수 있습니다. Visual Studio Code에서 로컬 업데이트를 다시 게시하려면 [프로젝트를 다시 게시](#republish-project-files)하세요. |
| **설정 이름 바꾸기** | Azure에서 기존 함수 앱 설정의 키 이름을 변경합니다. 이 명령은 local.settings.json 파일의 설정에 영향을 주지 않습니다. Azure에서 설정 이름을 바꾼 후에는 [해당 변경 사항을 로컬 프로젝트에 다운로드](#download-settings-from-azure)해야 합니다. |
| **다시 시작** | Azure에서 함수 앱을 다시 시작합니다. 업데이트를 배포하면 함수 앱도 다시 시작됩니다. |
| **Set AzureWebJobsStorage**| `AzureWebJobsStorage` 애플리케이션 설정의 값을 설정합니다. 이 설정은 Azure Functions에 필요합니다. Azure에서 함수 앱을 만들 때 설정됩니다. |
| **Start** | Azure에서 중지된 함수 앱을 시작합니다. |
| **스트리밍 로그 시작** | Azure에서 함수 앱에 대한 스트리밍 로그를 시작합니다. 로깅 정보를 거의 실시간으로 확인해야 하는 경우 Azure에서 원격 문제 해결 중에 스트리밍 로그를 사용합니다. 자세한 내용은 [스트리밍 로그](#streaming-logs)를 참조하세요. |
| **중지** | Azure에서 실행되는 함수 앱을 중지합니다. |
| **스트리밍 로그 중지** | Azure에서 함수 앱에 대한 스트리밍 로그를 중지합니다. |
| **슬롯 설정으로 전환** | 사용하도록 설정하면 지정된 배포 슬롯에 대한 애플리케이션 설정이 유지됩니다. |
| **Azure Functions Core Tools 제거** | Azure Functions Core Tools를 제거합니다. 이는 확장에 필요합니다. |
| **로컬 설정 업로드** | local.settings.json 파일에서 Azure의 선택한 함수 앱에 설정을 업로드합니다. 로컬 파일이 암호화되면 암호 해독, 업로드 및 암호화가 다시 수행됩니다. 두 위치에 충돌하는 값이 있는 설정이 있는 경우 계속하는 방법을 선택하라는 메시지가 표시됩니다. 이 명령을 실행하기 전에 local.settings.json 파일에 대한 변경 내용을 저장해야 합니다. |
| **GitHub에서 커밋 보기** | 함수 앱이 리포지토리에 연결되어 있을 때 특정 배포의 최신 커밋을 보여 줍니다. |
| **배포 로그 보기** | Azure에서 함수 앱에 대한 특정 배포에 대한 로그를 보여 줍니다. |

## <a name="next-steps"></a>다음 단계

Azure Functions Core Tools에 대한 자세한 내용은 [Azure Functions Core Tools 사용](functions-run-local.md)을 참조하세요.

.NET 클래스 라이브러리인 함수를 개발하는 방법에 대해 자세히 알아보려면 [Azure Functions C# 개발자 참조](functions-dotnet-class-library.md)를 참조하세요. 이 문서에서는 특성을 사용하여 Azure Functions에서 지원하는 다양한 유형의 바인딩을 선언하는 방법의 예제에 대한 링크를 제공합니다.

[Visual Studio Code용 Azure Functions 확장]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md