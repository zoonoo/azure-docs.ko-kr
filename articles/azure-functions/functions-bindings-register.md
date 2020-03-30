---
title: Azure 함수 바인딩 확장 등록
description: 사용자 환경에 따라 Azure Functions 바인딩 확장을 등록하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277519"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure 함수 바인딩 확장 등록

Azure Functions 버전 2.x에서 [바인딩은](./functions-triggers-bindings.md) 함수 런타임과 별도의 패키지로 사용할 수 있습니다. .NET 함수는 NuGet 패키지를 통해 바인딩에 액세스하지만 확장 번들은 구성 설정을 통해 다른 함수가 모든 바인딩에 액세스할 수 있도록 합니다.

바인딩 확장과 관련된 다음 항목을 고려하십시오.

- 바인딩 확장은 [Visual Studio를 사용하여 C# 클래스 라이브러리를 만들](#local-csharp)때를 제외하고 함수 1.x에 명시적으로 등록되지 않습니다.

- HTTP 및 타이머 트리거는 기본적으로 지원되며 확장이 필요하지 않습니다.

다음 표는 바인딩을 등록하는 시기와 방법을 나타냅니다.

| 개발 환경 |등록<br/> (Functions 1.x)  |등록<br/> (Functions 2.x)  |
|-------------------------|------------------------------------|------------------------------------|
|Azure portal|자동|자동|
|Non-.NET 언어 또는 로컬 Azure 코어 도구 개발|자동|[Azure 함수 핵심 도구 및 확장 번들 사용](#extension-bundles)|
|비주얼 스튜디오를 사용하는 C# 클래스 라이브러리|[NuGet 도구 사용](#vs)|[NuGet 도구 사용](#vs)|
|Visual Studio Code를 사용하는 C# 클래스 라이브러리|해당 없음|[.NET Core CLI 사용](#vs-code)|

## <a name="extension-bundles-for-local-development"></a><a name="extension-bundles"></a>로컬 개발을 위한 확장 번들

확장 번들은 함수 앱에 호환되는 Functions 바인딩 확장 집합을 추가할 수 있는 배포 기술입니다. 앱을 빌드할 때 미리 정의된 확장 집합이 추가됩니다. 번들에 정의된 확장 패키지는 서로 호환되며, 이는 패키지 간의 충돌을 방지하는 데 도움이 됩니다. 앱의 host.json 파일에서 확장 번들을 활성화합니다.  

버전 2.x 및 함수 런타임의 이후 버전과 함께 확장 번들을 사용할 수 있습니다. 로컬로 개발할 때 Azure [Functions 핵심 도구의](functions-run-local.md#v2)최신 버전을 사용하고 있는지 확인합니다.

Azure Functions 핵심 도구, Visual Studio 코드 및 원격으로 빌드할 때 로컬 개발에 확장 번들을 사용합니다.

확장 번들을 사용하지 않는 경우 바인딩 확장을 설치하기 전에 로컬 컴퓨터에 .NET Core 2.x SDK를 설치해야 합니다. 확장 번들은 로컬 개발에 대한 이 요구 사항을 제거합니다. 

확장 번들을 사용하려면 *host.json* 파일을 업데이트하여 다음에 `extensionBundle`대한 항목을 포함합니다.
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>비주얼\# 스튜디오가 있는 C 클래스 라이브러리

**Visual Studio에서는**다음 예제와 같이 [설치 패키지](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) 명령을 사용하여 패키지 관리자 콘솔에서 패키지를 설치할 수 있습니다.

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

지정된 바인딩에 사용되는 패키지의 이름은 해당 바인딩에 대한 참조 문서에 제공됩니다. 예를 들어 [Service Bus 바인딩 참조 문서의 패키지 섹션](functions-bindings-service-bus.md#functions-1x)을 참조하세요.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org.](https://nuget.org) 함수 런타임 1.x 또는 2.x에 해당하는 주 버전은 바인딩에 대한 참조 문서에 지정됩니다.

바인딩을 `Install-Package` 참조하는 데 사용하는 경우 [확장 번들을](#extension-bundles)사용할 필요가 없습니다. 이 방법은 Visual Studio에 빌드된 클래스 라이브러리에 만 해당합니다.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>비주얼 스튜디오 코드가 있는 C# 클래스 라이브러리

**Visual Studio 코드에서**.NET Core CLI에서 [dotnet add 패키지](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 명령을 사용하여 명령 프롬프트에서 C# 클래스 라이브러리 프로젝트에 대한 패키지를 설치합니다. 다음 예제에서는 바인딩을 추가하는 방법을 보여 줍니다.

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI는 Azure Functions 2.x 개발에만 사용할 수 있습니다.

필요한 `<BINDING_TYPE_NAME>` 바인딩이 포함된 패키지 이름으로 바꿉니다. 지원되는 바인딩 목록에서 원하는 바인딩 참조 문서를 찾을 수 [있습니다.](./functions-triggers-bindings.md#supported-bindings)

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org.](https://nuget.org) 함수 런타임 1.x 또는 2.x에 해당하는 주 버전은 바인딩에 대한 참조 문서에 지정됩니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Function 트리거 및 바인딩 예제](./functions-bindings-example.md)
