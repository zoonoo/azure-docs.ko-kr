---
title: Azure Functions 바인딩 확장 등록
description: 사용자 환경에 따라 Azure Functions 바인딩 확장을 등록 하는 방법에 대해 알아봅니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 93ced443a73d5499d8b305770c3c866c26d540f0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086473"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions 바인딩 확장 등록

Azure Functions 버전 2.x에서 [바인딩은](./functions-triggers-bindings.md) 함수 런타임과 별도의 패키지로 사용할 수 있습니다. .NET 함수는 NuGet 패키지를 통해 바인딩에 액세스 하지만, 확장 번들을 사용 하면 다른 함수에서 구성 설정을 통해 모든 바인딩에 액세스할 수 있습니다.

바인딩 확장과 관련 된 다음 항목을 고려 합니다.

- 바인딩 확장 [은 Visual Studio를 사용 하 여 클래스 라이브러리 C# 를 만드는](#local-csharp)경우를 제외 하 고는 함수 1. x에 명시적으로 등록 되지 않습니다.

- HTTP 및 타이머 트리거는 기본적으로 지원 되며 확장이 필요 하지 않습니다.

다음 표는 바인딩을 등록 하는 시기와 방법을 나타냅니다.

| 개발 환경 |등록<br/> (Functions 1.x)  |등록<br/> (Functions 2.x)  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|자동|자동|
|Non-.NET 언어 또는 로컬 Azure 핵심 도구 개발|자동|[Azure Functions Core Tools 및 확장 번들 사용](#extension-bundles)|
|C#Visual Studio를 사용 하는 클래스 라이브러리|[NuGet 도구 사용](#vs)|[NuGet 도구 사용](#vs)|
|Visual Studio Code를 사용하는 C# 클래스 라이브러리|해당 사항 없음|[.NET Core CLI 사용](#vs-code)|

## <a name="extension-bundles"></a>로컬 개발용 확장 번들

확장 번들은 함수 앱 프로젝트에 호환 되는 함수 바인딩 확장 집합을 추가할 수 있는 버전 2.x 런타임에 대 한 로컬 개발 기술입니다. 이러한 확장 패키지는 Azure에 배포할 때 배포 패키지에 포함 됩니다. 번들을 사용 하면 Microsoft에서 게시 한 모든 바인딩을 *호스트 json* 파일의 설정을 통해 사용할 수 있습니다. 번들에 정의 된 확장 패키지는 서로 호환 되므로 패키지 간의 충돌을 방지할 수 있습니다. 로컬로 개발 하는 경우 최신 버전의 [Azure Functions Core Tools](functions-run-local.md#v2)을 사용 하 고 있는지 확인 합니다.

Azure Functions Core Tools 또는 Visual Studio Code를 사용 하 여 모든 로컬 개발에 확장 번들을 사용 합니다.

확장 번들을 사용 하지 않는 경우 바인딩 확장을 설치 하기 전에 로컬 컴퓨터에 .NET Core 2.x SDK를 설치 해야 합니다. 번들은 로컬 개발을 위한 이러한 요구 사항을 제거 합니다. 

확장 번들을 사용 하려면에 대 한 `extensionBundle`다음 항목을 포함 하도록 *호스트나 json* 파일을 업데이트 합니다.

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

에서 `extensionBundle`사용할 수 있는 속성은 다음과 같습니다.

| 속성 | 설명 |
| -------- | ----------- |
| **`id`** | Microsoft Azure 함수 확장 번들에 대 한 네임 스페이스입니다. |
| **`version`** | 설치할 번들의 버전입니다. 함수 런타임은 항상 버전 범위 또는 간격으로 정의 된 허용 가능한 최대 버전을 선택 합니다. 위의 version 값은 1.0.0의 모든 번들 버전을 2.0.0 포함 하는 것을 허용 하지 않습니다. 자세한 내용은 [버전 범위 지정을 위한 간격 표기법](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards)을 참조 하십시오. |

번들 버전은 번들 변경에서 패키지로 증가 합니다. 주 버전 변경은 번들의 패키지가 주 버전에 따라 증가 하는 경우에 발생 합니다 .이는 일반적으로 함수 런타임의 주 버전 변경 내용과 일치 합니다.  

기본 번들로 설치 된 확장의 현재 집합은이 [확장명 json 파일](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)에 열거 됩니다.

<a name="local-csharp"></a>

## <a name="vs"></a>Visual\# Studio를 사용 하는 C 클래스 라이브러리

다음 예제와 같이 **Visual Studio**에서 패키지 관리자 콘솔을 사용 하 여 패키지 [를 설치할 수](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) 있습니다.

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

지정 된 바인딩에 사용 되는 패키지의 이름은 해당 바인딩에 대 한 참조 문서에서 제공 됩니다. 예를 들어 [Service Bus 바인딩 참조 문서의 패키지 섹션](functions-bindings-service-bus.md#packages---functions-1x)을 참조하세요.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열되어 있습니다. Functions 참조 1.x 또는 2.x에 해당하는 주요 버전은 바인딩에 대한 참조 문서에 지정되어 있습니다.

를 사용 `Install-Package` 하 여 바인딩을 참조 하는 경우에는 [확장 번들](#extension-bundles)을 사용할 필요가 없습니다. 이 방법은 Visual Studio에서 빌드된 클래스 라이브러리에만 적용 됩니다.

## <a name="vs-code"></a>C# Visual Studio Code를 포함 하는 클래스 라이브러리

> [!NOTE]
> [확장 번들](#extension-bundles) 을 사용 하 여 함수에서 호환 되는 바인딩 확장 패키지 집합을 자동으로 설치 하는 것이 좋습니다.

**Visual Studio Code**에서 .NET Core CLI의 C# [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 명령을 사용 하 여 명령 프롬프트에서 클래스 라이브러리 프로젝트에 대 한 패키지를 설치 합니다. 다음 예제에서는 바인딩을 추가 하는 방법을 보여 줍니다.

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI는 Azure Functions 2.x 개발에만 사용할 수 있습니다.

원하는 `<BINDING_TYPE_NAME>` 바인딩에 대 한 참조 문서에 제공 된 패키지의 이름으로 대체 합니다. [지원 되](./functions-triggers-bindings.md#supported-bindings)는 바인딩 목록에서 원하는 바인딩 참조 문서를 찾을 수 있습니다.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열되어 있습니다. Functions 참조 1.x 또는 2.x에 해당하는 주요 버전은 바인딩에 대한 참조 문서에 지정되어 있습니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure 함수 트리거 및 바인딩 예제](./functions-bindings-example.md)
