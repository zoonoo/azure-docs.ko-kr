---
title: Azure Functions 바인딩 확장 등록
description: 사용자 환경에 따라 Azure Functions 바인딩 확장 등록 하는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 88ffd6ec24ed19dd3b1e57277884c8759cdac1f9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480328"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions 바인딩 확장 등록

Azure Functions 버전에서 2.x [바인딩](./functions-triggers-bindings.md) functions 런타임은에서 별도 패키지로 사용할 수 있습니다. .NET 함수 바인딩을 통해 NuGet 패키지에 액세스 하는 동안 확장 번들 구성 설정을 통해 모든 바인딩에 다른 함수 액세스를 허용 합니다.

바인딩 확장 관련 된 다음 항목을 고려 합니다.

- 바인딩 확장 함수에서 명시적으로 등록 되지 않습니다 경우는 제외 1.x [만들기를 C# Visual Studio를 사용 하 여 클래스 라이브러리](#local-csharp)합니다.

- HTTP 및 타이머 트리거 기본적으로 지원 되 고 확장에 필요 하지 않습니다.

다음 표는 바인딩을 등록 하는 방법과 시기를 나타냅니다.

| 개발 환경 |등록<br/> (Functions 1.x)  |등록<br/> (Functions 2.x)  |
|-------------------------|------------------------------------|------------------------------------|
|Azure portal|자동|자동|
|비.NET 언어 또는 로컬 Azure 핵심 도구 개발|자동|[Azure Functions 핵심 도구 및 확장 번들을 사용 하 여](#extension-bundles)|
|C#Visual Studio 2019를 사용 하 여 클래스 라이브러리|[NuGet 도구 사용](#c-class-library-with-visual-studio-2019)|[NuGet 도구 사용](#c-class-library-with-visual-studio-2019)|
|Visual Studio Code를 사용하는 C# 클래스 라이브러리|N/A|[.NET Core CLI 사용](#c-class-library-with-visual-studio-code)|

## <a name="extension-bundles"></a>로컬 개발에 대 한 확장 번들

확장 번들은 로컬 개발 기술 호환 일련의 함수 앱 프로젝트에 바인딩 확장 하는 함수를 추가할 수 있는 버전 2.x 런타임입니다. 이러한 확장 패키지는 Azure에 배포할 때 배포 패키지에 다음 포함 됩니다. 번들의 설정을 통해 사용할 수 있는 Microsoft에서 게시 하는 모든 바인딩을 통해 합니다 *host.json* 파일입니다. 번들에 정의 된 확장 패키지가 패키지 간의 충돌을 방지 하는 데 유용한 서로 호환 됩니다. 때 개발 로컬로 확인의 최신 버전을 사용 하 고 있는지 [Azure Functions 핵심 도구](functions-run-local.md#v2)합니다.

Azure Functions 핵심 도구 또는 Visual Studio Code를 사용 하 여 모든 로컬 개발에 대 한 확장 번들을 사용 합니다.

.NET Core를 설치 해야 확장 번들을 사용 하지 않는 경우 로컬 컴퓨터의 모든 바인딩 확장을 설치 하기 전에 SDK 2.x입니다. 번들에는 로컬 개발에 대 한이 요구 사항을 제거 합니다. 

확장 번들을 사용 하려면 업데이트 된 *host.json* 파일에 대 한 다음 항목을 포함 하도록 `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

다음 속성을 사용할 `extensionBundle`:

| 자산 | 설명 |
| -------- | ----------- |
| **`id`** | Microsoft Azure Functions 확장 번들에 대 한 네임 스페이스입니다. |
| **`version`** | 설치 하는 번들의 버전입니다. 함수 런타임에서 항상 버전 범위 또는 간격에 의해 정의 된 최대 허용 버전을 선택 합니다. 위의 버전 값을 최대 1.0.0 제외한 2.0.0에서 모든 번들 버전 수 있습니다. 자세한 내용은 참조는 [버전 범위를 지정 하기 위한 간격 표기법](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards)합니다. |

번들 변경에서 패키지로 번들 버전 증가 합니다. 주 버전 변경이 번들의 패키지는 일반적으로 Functions 런타임의 주 버전에서 변경 주 버전 씩 증가 하는 경우 발생 합니다.  

이 기본 번들으로 설치 된 확장의 현재 집합 열거 [extensions.json 파일](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)합니다.

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio-2019"></a>C\# Visual Studio 2019로 클래스 라이브러리

**Visual Studio 2019**를 사용 하 여 패키지 관리자 콘솔에서 패키지를 설치할 수 있습니다 합니다 [Install-package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) 명령을 다음 예와에서 같이:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

지정 된 바인딩에 사용 하는 패키지의 이름은 해당 바인딩에 대 한 참조 문서에서 제공 됩니다. 예를 들어 [Service Bus 바인딩 참조 문서의 패키지 섹션](functions-bindings-service-bus.md#packages---functions-1x)을 참조하세요.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열되어 있습니다. Functions 참조 1.x 또는 2.x에 해당하는 주요 버전은 바인딩에 대한 참조 문서에 지정되어 있습니다.

## <a name="c-class-library-with-visual-studio-code"></a>Visual Studio Code를 통한 C# 클래스 라이브러리

> [!NOTE]
> 사용 하는 것이 좋습니다 [확장 번들](#extension-bundles) 함수 바인딩 확장 패키지의 호환 가능한 집합을 자동으로 설치 해야 합니다.

**Visual Studio Code**, 패키지를 설치를 C# 사용 하 여 명령 프롬프트에서 클래스 라이브러리 프로젝트를 [dotnet 패키지를 추가](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 다음 예제에서와 같이.NET Core CLI 명령:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

.NET Core CLI는 Azure Functions 2.x 개발에만 사용할 수 있습니다.

지정된 바인딩에 사용할 패키지의 이름은 해당 바인딩에 대한 참조 문서에 제공됩니다. 예를 들어 [Service Bus 바인딩 참조 문서의 패키지 섹션](functions-bindings-service-bus.md#packages---functions-1x)을 참조하세요.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열되어 있습니다. Functions 참조 1.x 또는 2.x에 해당하는 주요 버전은 바인딩에 대한 참조 문서에 지정되어 있습니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure 함수 트리거 및 바인딩 예제](./functions-bindings-example.md)

