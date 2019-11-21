---
title: Register Azure Functions binding extensions
description: Learn to register an Azure Functions binding extension based on your environment.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 599becae0225bea623c383ead49cd9abcea6fff2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231104"
---
# <a name="register-azure-functions-binding-extensions"></a>Register Azure Functions binding extensions

In Azure Functions version 2.x, [bindings](./functions-triggers-bindings.md) are available as separate packages from the functions runtime. While .NET functions access bindings through NuGet packages, extension bundles allow other functions access to all bindings through a configuration setting.

Consider the following items related to binding extensions:

- Binding extensions aren't explicitly registered in Functions 1.x except when [creating a C# class library using Visual Studio](#local-csharp).

- HTTP and timer triggers are supported by default and don't require an extension.

The following table indicates when and how you register bindings.

| 개발 환경 |등록<br/> (Functions 1.x)  |등록<br/> (Functions 2.x)  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|자동|자동|
|Non-.NET languages or local Azure Core Tools development|자동|[Use Azure Functions Core Tools and extension bundles](#extension-bundles)|
|C# class library using Visual Studio|[NuGet 도구 사용](#vs)|[NuGet 도구 사용](#vs)|
|Visual Studio Code를 사용하는 C# 클래스 라이브러리|N/A|[.NET Core CLI 사용](#vs-code)|

## <a name="extension-bundles"></a>Extension bundles for local development

Extension bundles is a deployment technology that lets you add a compatible set of Functions binding extensions to your function app. A predefined set of extensions are added when you build your app. Extension packages defined in a bundle are compatible with each other, which helps you avoid conflicts between packages. You enable extension bundles in the app's host.json file.  

You can use extension bundles with version 2.x and later versions of the Functions runtime. When developing locally, make sure you are using the latest version of [Azure Functions Core Tools](functions-run-local.md#v2).

Use extension bundles for local development using Azure Functions Core Tools, Visual Studio Code, and when you build remotely.

If you don't use extension bundles, you must install the .NET Core 2.x SDK on your local computer before you install any binding extensions. Extension bundles removes this requirement for local development. 

To use extension bundles, update the *host.json* file to include the following entry for `extensionBundle`:
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="vs"></a> C\# class library with Visual Studio

In **Visual Studio**, you can install packages from the Package Manager Console using the [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) command, as shown in the following example:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

The name of the package used for a given binding is provided in the reference article for that binding. 예를 들어 [Service Bus 바인딩 참조 문서의 패키지 섹션](functions-bindings-service-bus.md#packages---functions-1x)을 참조하세요.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. Valid versions are listed on the individual package pages at [NuGet.org](https://nuget.org). The major versions that correspond to Functions runtime 1.x or 2.x are specified in the reference article for the binding.

If you use `Install-Package` to reference a binding, you don't need to use [extension bundles](#extension-bundles). This approach is specific for class libraries built in Visual Studio.

## <a name="vs-code"></a> C# class library with Visual Studio Code

> [!NOTE]
> We recommend using [extension bundles](#extension-bundles) to have Functions automatically install a compatible set of binding extension packages. 

In **Visual Studio Code**, install packages for a C# class library project from the command prompt using the [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) command in the .NET Core CLI. The following example demonstrates how you add a  binding:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI는 Azure Functions 2.x 개발에만 사용할 수 있습니다.

Replace `<BINDING_TYPE_NAME>` with the name of the package that contains the binding you need. You can find the desired binding reference article in the [list of supported bindings](./functions-triggers-bindings.md#supported-bindings).

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. Valid versions are listed on the individual package pages at [NuGet.org](https://nuget.org). The major versions that correspond to Functions runtime 1.x or 2.x are specified in the reference article for the binding.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Function trigger and binding example](./functions-bindings-example.md)
