---
title: Azure Functions 바인딩 확장 등록
description: 사용자 환경에 따라 Azure Functions 바인딩 확장 등록 하는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 53eb5fc9389d913ecacec3729a06e47a1c2bf56b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65864542"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions 바인딩 확장 등록

Azure Functions 버전에서 2.x [바인딩](./functions-triggers-bindings.md) functions 런타임은에서 별도 패키지로 사용할 수 있습니다. .NET 함수 바인딩을 통해 NuGet 패키지에 액세스 하는 동안 확장 번들 구성 설정을 통해 모든 바인딩에 다른 함수 액세스를 허용 합니다.

바인딩 확장 관련 된 다음 항목을 고려 합니다.

- 바인딩 확장 함수에서 명시적으로 등록 되지 않습니다 경우는 제외 1.x [만들기를 C# Visual Studio 2019를 사용 하 여 클래스 라이브러리](#local-csharp)합니다.

- HTTP 및 타이머 트리거 기본적으로 지원 되 고 확장에 필요 하지 않습니다.

다음 표는 바인딩을 등록 하는 방법과 시기를 나타냅니다.

| 개발 환경 |등록<br/> (Functions 1.x)  |등록<br/> (Functions 2.x)  |
|-------------------------|------------------------------------|------------------------------------|
|Azure portal|자동|자동|
|비.NET 언어 또는 로컬 Azure 핵심 도구 개발|자동|[Azure Functions 핵심 도구 및 확장 번들을 사용 하 여](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|C#Visual Studio 2019를 사용 하 여 클래스 라이브러리|[NuGet 도구 사용](#c-class-library-with-visual-studio-2019)|[NuGet 도구 사용](#c-class-library-with-visual-studio-2019)|
|Visual Studio Code를 사용하는 C# 클래스 라이브러리|N/A|[.NET Core CLI 사용](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>Azure Functions 핵심 도구 및 확장 번들을 사용 하 여 로컬 개발

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2019"></a>C#Visual Studio 2019로 클래스 라이브러리

**Visual Studio 2019**를 사용 하 여 패키지 관리자 콘솔에서 패키지를 설치할 수 있습니다 합니다 [Install-package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) 명령을 다음 예와에서 같이:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

지정 된 바인딩에 사용 하는 패키지의 이름은 해당 바인딩에 대 한 참조 문서에서 제공 됩니다. 예를 들어 [Service Bus 바인딩 참조 문서의 패키지 섹션](functions-bindings-service-bus.md#packages---functions-1x)을 참조하세요.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열되어 있습니다. Functions 참조 1.x 또는 2.x에 해당하는 주요 버전은 바인딩에 대한 참조 문서에 지정되어 있습니다.

## <a name="c-class-library-with-visual-studio-code"></a>Visual Studio Code를 통한 C# 클래스 라이브러리

**Visual Studio Code**의 경우 다음 예제와 같이 .NET Core CLI에서 [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 명령을 사용하여 명령 프롬프트에서 패키지를 설치할 수 있습니다.

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

.NET Core CLI는 Azure Functions 2.x 개발에만 사용할 수 있습니다.

지정된 바인딩에 사용할 패키지의 이름은 해당 바인딩에 대한 참조 문서에 제공됩니다. 예를 들어 [Service Bus 바인딩 참조 문서의 패키지 섹션](functions-bindings-service-bus.md#packages---functions-1x)을 참조하세요.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열되어 있습니다. Functions 참조 1.x 또는 2.x에 해당하는 주요 버전은 바인딩에 대한 참조 문서에 지정되어 있습니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure 함수 트리거 및 바인딩 예제](./functions-bindings-example.md)

