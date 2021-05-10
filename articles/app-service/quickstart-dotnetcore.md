---
title: '빠른 시작: ASP.NET 웹앱 배포'
description: 첫 번째 ASP.NET 앱을 배포하여 Azure App Service에서 웹앱을 실행하는 방법을 알아봅니다.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 482bf6d29fbc1e982ee4d17099d82915ff3a0241
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762464"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET Core 3.1 or .NET 5.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET Core 3.1      | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET 5.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>빠른 시작: ASP.NET 웹앱 배포

이 빠른 시작에서는 첫 번째 ASP.NET 웹앱을 만들고 [Azure App Service](overview.md)에 배포하는 방법을 알아봅니다. App Service는 다양한 버전의 .NET 앱을 지원하며 확장성이 뛰어난 자체 패치 웹 호스팅 서비스를 제공합니다. ASP.NET 웹앱은 크로스 플랫폼이며 Linux 또는 Windows에서 호스트할 수 있습니다. 이 빠른 시작을 마치고 나면 App Service 호스팅 계획 및 웹 애플리케이션이 배포된 App Service로 구성된 리소스 그룹이 하나 생깁니다.

> [!TIP]
> .NET Core 3.1은 .NET의 현재 LTS(장기 지원) 릴리스입니다. 자세한 내용은 [.NET 지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

:::zone target="docs" pivot="development-environment-vs"

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet).
- **ASP.NET 및 웹 개발** 워크로드가 설치된 <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019</a>입니다.

    Visual Studio 2019를 이미 설치한 경우:

    - **도움말** > **업데이트 확인** 을 차례로 선택하여 Visual Studio에서 최신 업데이트를 설치합니다.
    - **도구** > **도구 및 기능 가져오기** 를 차례로 선택하여 워크로드를 추가합니다.

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>
- <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure 도구</a> 확장

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> 최신 .NET Core 3.1 SDK를 설치합니다. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> 최신 .NET 5.0 SDK를 설치합니다. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> .NET Framework 4.8 개발자 팩을 설치합니다. </a>

> [!NOTE]
> Visual Studio Code는 크로스 플랫폼이지만 .NET Framework는 아닙니다. Visual Studio Code를 사용하여 .NET Framework 앱을 개발하는 경우 Windows 머신을 사용하여 빌드 종속성을 충족하는 것이 좋습니다.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet).
- <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>
- .NET SDK(런타임 및 CLI 포함)

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> 최신 .NET Core 3.1 SDK를 설치합니다. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> 최신 .NET 5.0 SDK를 설치합니다. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> 최신 .NET 5.0 SDK </a> 및 <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> .NET Framework 4.8 개발자 팩을 설치합니다. </a>

> [!NOTE]
> [.NET CLI](/dotnet/core/tools)는 크로스 플랫폼이지만 .NET Framework는 아닙니다. .NET CLI를 사용하여 .NET Framework 앱을 개발하는 경우 Windows 머신을 사용하여 빌드 종속성을 충족하는 것이 좋습니다.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>ASP.NET 웹앱 만들기

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Visual Studio를 연 다음, **새 프로젝트 만들기** 를 선택합니다.
1. **새 프로젝트 만들기** 에서 **ASP.NET Web Core 앱** 을 찾아서 선택한 후 **다음** 을 선택합니다.
1. **새 프로젝트 구성** 에서 애플리케이션 이름을 _MyFirstAzureWebApp_ 으로 지정한 후 **다음** 을 선택합니다.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="ASP.NET Core 3.1 웹앱 구성" border="true":::

1. **.NET Core 3.1(장기 지원)** 을 선택합니다.
1. **인증 유형** 이 **없음** 으로 설정되어 있는지 확인합니다. **만들기** 를 선택합니다.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio - .NET Core 3.1 및 인증 유형으로 없음을 선택합니다." border="true":::

1. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작** 을 차례로 선택하여 웹앱을 로컬로 실행합니다.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio - .NET Core 3.1 로컬에서 찾아보기" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Visual Studio를 연 다음, **새 프로젝트 만들기** 를 선택합니다.
1. **새 프로젝트 만들기** 에서 **ASP.NET Web Core 앱** 을 찾아서 선택한 후 **다음** 을 선택합니다.
1. **새 프로젝트 구성** 에서 애플리케이션 이름을 _MyFirstAzureWebApp_ 으로 지정한 후 **다음** 을 선택합니다.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio - ASP.NET 5.0 웹앱 구성" border="true":::

1. **.NET Core 5.0(현재)** 을 선택합니다.
1. **인증 유형** 이 **없음** 으로 설정되어 있는지 확인합니다. **만들기** 를 선택합니다.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio - .NET Core 5.0 선택 시 추가 정보" border="true":::

1. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작** 을 차례로 선택하여 웹앱을 로컬로 실행합니다.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio - 로컬에서 실행되는 ASP.NET Core 5.0" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

1. Visual Studio를 연 다음, **새 프로젝트 만들기** 를 선택합니다.
1. **새 프로젝트 만들기** 에서 **ASP.NET 웹 애플리케이션(.NET Framework)** 을 찾아서 선택한 후, **다음** 을 선택합니다.
1. **새 프로젝트 구성** 에서 애플리케이션 이름을 _MyFirstAzureWebApp_ 으로 지정한 다음, **만들기** 를 선택합니다.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio - ASP.NET Framework 4.8 웹앱 구성" border="true":::

1. **MVC** 템플릿을 선택합니다.
1. **인증** 이 **인증 없음** 으로 설정되었는지 확인합니다. **만들기** 를 선택합니다.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio - MVC 템플릿 선택" border="true":::

1. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작** 을 차례로 선택하여 웹앱을 로컬로 실행합니다.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio - 로컬에서 실행되는 ASP.NET Framework 4.8" lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

_MyFirstAzureWebApp_ 이라는 새 폴더를 만들고 Visual Studio Code에서 엽니다. <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">터미널</a> 을 열고 [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) 명령을 사용하여 새 .NET 웹앱을 만듭니다.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> `--target-framework-override` 플래그는 프로젝트의 TFM(대상 프레임워크 모니커)을 대체하는 자유 형식 텍스트이며 지원 템플릿이 존재하거나 컴파일된다는 보장은 없습니다. .NET Framework 앱은 Windows에서만 빌드하고 실행할 수 있습니다.

---

Visual Studio Code의 **터미널** 에서 [`dotnet run`](/dotnet/core/tools/dotnet-run) 명령을 사용하여 애플리케이션을 로컬에서 실행합니다.

```dotnetcli
dotnet run
```

웹 브라우저를 열고 `https://localhost:5001`의 앱으로 이동합니다.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3.1 웹앱 템플릿이 페이지에 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - .NET Core 3.1을 브라우저에서 로컬로 실행" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5.0 웹앱 템플릿이 페이지에 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - .NET 5.0을 브라우저에서 로컬로 실행" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4.8 웹앱 템플릿이 페이지에 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code - .NET 4.8을 브라우저에서 로컬로 실행" lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

머신에서 터미널 창을 열고 작업 디렉터리로 이동합니다. [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) 명령을 사용하여 새 .NET 웹앱을 만든 후 새로 만든 앱으로 디렉터리를 변경합니다.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> `--target-framework-override` 플래그는 프로젝트의 TFM(대상 프레임워크 모니커)을 대체하는 자유 형식 텍스트이며 지원 템플릿이 존재하거나 컴파일된다는 보장은 없습니다. .NET Framework 앱은 Windows에서만 빌드할 수 있습니다.

---

동일한 터미널 세션에서 [`dotnet run`](/dotnet/core/tools/dotnet-run) 명령을 사용하여 애플리케이션을 로컬로 실행합니다.

```dotnetcli
dotnet run
```

웹 브라우저를 열고 `https://localhost:5001`의 앱으로 이동합니다.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3.1 웹앱 템플릿이 페이지에 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - 로컬 브라우저의 ASP.NET Core 3.1" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5.0 웹앱 템플릿이 페이지에 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - 로컬 브라우저의 ASP.NET Core 5.0" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4.8 웹앱 템플릿이 페이지에 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code - 로컬 브라우저의 ASP.NET Framework 4.8" lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>웹앱 게시

웹앱을 게시하려면 먼저 앱을 게시할 수 있는 새 App Service를 만들고 구성해야 합니다.

App Service를 설정하는 과정에서 다음 항목을 만듭니다.

- 서비스에 필요한 모든 Azure 리소스를 포함할 새 [리소스 그룹](../azure-resource-manager/management/overview.md#terminology)
- 앱을 호스팅하는 웹 서버 팜의 위치, 크기 및 기능을 지정하는 새 [호스팅 계획](overview-hosting-plans.md)

다음 단계에 따라 App Service를 만들고 웹앱을 게시합니다.

:::zone target="docs" pivot="development-environment-vs"

1. **솔루션 탐색기** 에서 마우스 오른쪽 단추로 **MyFirstAzureWebApp** 프로젝트를 클릭하고 **게시** 를 선택합니다.
1. **게시** 에서 **Azure** 를 선택한 후 **다음** 을 선택합니다.

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio - 웹앱 게시 및 대상 Azure" border="true":::

1. 옵션은 Azure에 이미 로그인했는지 여부와 Azure 계정에 연결된 Visual Studio 계정이 있는지 여부에 따라 달라집니다. **계정 추가** 또는 **로그인** 중 하나를 선택하여 Azure 구독에 로그인합니다. 이미 로그인한 경우 원하는 계정을 선택합니다.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio - Azure에 로그인 선택 대화 상자":::

1. **특정 대상** 을 **Azure App Service(Linux)** 또는 **Azure App Service(Windows)** 중 하나로 선택합니다.

    > [!IMPORTANT]
    > ASP.NET Framework 4.8을 대상으로 하는 경우 **Azure App Service(Windows)** 를 사용합니다.

1. **App Service 인스턴스** 오른쪽에서 **+** 를 선택합니다.

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio - 새 App Service 앱 대화 상자":::

1. **구독** 의 경우 나열된 구독을 수락하거나 드롭다운 목록에서 새 구독을 선택합니다.
1. **리소스 그룹** 에 대해 **새로 만들기** 를 선택합니다. **새 리소스 그룹 이름** 에서 *myResourceGroup* 을 입력하고, **확인** 을 선택합니다.
1. **호스팅 계획** 에서 **새로 만들기** 를 선택합니다.
1. **호스팅 계획: 새로 만들기** 대화 상자에서 다음 표에 지정된 값을 입력합니다.

    | 설정          | 제안 값          | Description                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **호스팅 계획** | *MyFirstAzureWebAppPlan* | App Service 플랜의 이름입니다.                                         |
    | **위치**     | *서유럽*            | 웹앱이 호스팅된 데이터 센터입니다.                           |
    | **크기**         | *Free*                   | [가격 책정 계층][app-service-pricing-tier]은 호스팅 기능을 결정합니다. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="새 호스팅 계획 만들기":::

1. **이름** 에서 유효한 문자(`a-z`, `A-Z`, `0-9` 및 `-`)만 포함된 고유한 앱 이름을 입력합니다. 자동으로 생성된 고유한 이름을 적용할 수 있습니다. 웹앱의 URL은 `http://<app-name>.azurewebsites.net`이며, 여기서 `<app-name>`은 앱 이름입니다.
1. **만들기** 를 선택하여 Azure 리소스를 만듭니다.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio - 앱 리소스 만들기 대화 상자":::

   마법사가 완료되면 Azure 리소스가 생성되고 게시할 준비가 완료됩니다.

1. **마침** 을 선택하여 마법사를 닫습니다.
1. **게시** 페이지에서 **게시** 를 선택합니다. Visual Studio는 앱을 Azure에 빌드, 패키지 및 게시한 다음, 기본 브라우저에서 앱을 시작합니다.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    ASP.NET Core 3.1 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure의 ASP.NET Core 3.1 웹앱":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    ASP.NET Core 5.0 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure의 ASP.NET Core 5.0 웹앱":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio - Azure의 ASP.NET Framework 4.8 웹앱":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Visual Studio Azure 도구 확장을 사용하여 웹앱을 배포하려면:

1. Visual Studio Code에서 [**명령 팔레트**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)를 엽니다(<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd>).
1. "Azure App Service: 웹앱에 배포"를 검색하여 선택합니다.
1. 프롬프트에 다음과 같이 응답합니다.

    - *MyFirstAzureWebApp* 을 배포할 폴더로 선택합니다.
    - 메시지가 나타나면 **구성 추가** 를 선택합니다.
    - 메시지가 나타나면 기존 Azure 계정에 로그인합니다.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code - Azure에 로그인" border="true":::

    - **구독** 을 선택합니다.
    - **새 웹앱 만들기...고급** 을 선택합니다.
    - **전역적으로 고유한 이름 입력** 에는 모든 Azure에서 고유한 이름을 사용합니다(*유효한 문자: `a-z`, `0-9` 및 `-`* ). 회사 이름과 앱 식별자를 조합하여 사용하는 것이 좋습니다.
    - **새 리소스 그룹 만들기** 를 선택하고, `myResourceGroup`와 같은 이름을 지정합니다.
    - **런타임 스택 선택** 메시지가 나타나면:
      - *.NET Core 3.1* 의 경우 **.NET Core 3.1(LTS)** 을 선택합니다.
      - *.NET 5.0* 의 경우 **.NET 5** 를 선택합니다.
      - *.NET Framework 4.8* 의 경우 **ASP.NET V4.8** 을 선택합니다.
    - 운영 체제(Windows 또는 Linux)를 선택합니다.
        - *.NET Framework 4.8* 의 경우 Windows가 암시적으로 선택됩니다.
    - **새 App Service 계획 만들기** 를 선택하고, 이름을 제공하고, **F1 체험** [가격 책정 계층][app-service-pricing-tier]을 선택합니다.
    - Application Insights 리소스에 대해 **지금 건너뛰기** 를 선택합니다.
    - 가까운 위치를 선택합니다.

1. 게시가 완료되면 알림에서 **웹 사이트 찾아보기** 를 선택하고 메시지가 나타나면 **열기** 를 선택합니다.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    ASP.NET Core 3.1 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure의 ASP.NET Core 3.1 웹앱":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    ASP.NET Core 5.0 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure의 ASP.NET Core 5.0 웹앱":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code - Azure의 ASP.NET Framework 4.8 웹앱":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

[`az webapp up`](/cli/azure/webapp#az_webapp_up) 명령을 사용하여 로컬 *MyFirstAzureWebApp* 디렉터리에 코드를 배포합니다.

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- `az` 명령이 인식되지 않으면 [사전 요구 사항](#prerequisites)의 설명대로 Azure CLI가 설치되어 있는지 확인합니다.
- `<app-name>`을 모든 Azure에서 고유한 이름으로 바꿉니다(*유효한 문자는 `a-z`, `0-9` 및 `-`* ). 좋은 패턴은 회사 이름과 앱 식별자의 조합을 사용하는 것입니다.
- `--sku F1` 인수는 **무료** [가격 책정 계층][app-service-pricing-tier]에 웹앱을 만듭니다. 이 인수를 생략하여 더 빠른 프리미엄 계층을 사용합니다. 이 경우 시간당 비용이 발생합니다.
- `<os>`를 `linux` 또는 `windows`로 바꿉니다. *ASP.NET Framework 4.8* 을 대상으로 하는 경우 `windows`를 사용해야 합니다.
- 선택적으로 인수 `--location <location-name>`을 포함할 수 있습니다. 여기서 `<location-name>`은 사용 가능한 Azure 지역입니다. Azure 계정에 허용되는 지역 목록은 [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations) 명령을 실행하여 검색할 수 있습니다.

이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 실행되는 동안 리소스 그룹, App Service 계획 및 호스팅 앱 만들기, 로깅 구성, ZIP 배포 수행에 대한 메시지가 제공됩니다. 그런 다음, 앱의 URL이 포함된 메시지가 출력됩니다.

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

웹 브라우저를 열고 URL로 이동합니다.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3.1 웹앱이 페이지에 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI - Azure의 ASP.NET Core 3.1 웹앱":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5.0 웹앱이 페이지에 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI - Azure의 ASP.NET Core 5.0 웹앱":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI - Azure의 ASP.NET Framework 4.8 웹앱":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>앱 업데이트 및 재배포

웹앱을 업데이트하고 다시 배포하려면 다음 단계를 수행합니다.

:::zone target="docs" pivot="development-environment-vs"

1. **솔루션 탐색기** 의 프로젝트 아래에서 *Index.cshtml* 을 엽니다.
1. 첫 번째 `<div>` 요소를 다음 코드로 바꿉니다.

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   변경 내용을 저장합니다.

1. Azure에 다시 배포하려면 **솔루션 탐색기** 에서 **MyFirstAzureWebApp** 프로젝트를 마우스 오른쪽 버튼으로 클릭하고 **게시** 를 선택합니다.
1. **게시** 요약 페이지에서 **게시** 를 선택합니다.

    게시가 완료되면 Visual Studio가 웹앱의 URL로 브라우저를 시작합니다.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    업데이트된 ASP.NET Core 3.1 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure의 업데이트된 ASP.NET Core 3.1 웹앱":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    업데이트된 ASP.NET Core 5.0 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure의 업데이트된 ASP.NET Core 5.0 웹앱":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    업데이트된 ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio - Azure의 업데이트된 ASP.NET Framework 4.8 웹앱":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. *Index.cshtml* 이 열립니다.
1. 첫 번째 `<div>` 요소를 다음 코드로 바꿉니다.

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   변경 내용을 저장합니다.

1. Visual Studio Code **사이드바** 를 열고 **Azure** 아이콘을 선택하여 옵션을 확장합니다.
1. **APP SERVICE** 노드에서 구독을 확장하고 **MyFirstAzureWebApp** 을 마우스 오른쪽 단추로 클릭합니다.
1. **웹 앱에 배포...** 를 선택합니다.
1. 메시지가 나타나면 **배포** 를 선택합니다.
1. 게시가 완료되면 알림에서 **웹 사이트 찾아보기** 를 선택하고 메시지가 나타나면 **열기** 를 선택합니다.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    업데이트된 ASP.NET Core 3.1 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure의 업데이트된 ASP.NET Core 3.1 웹앱":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    업데이트된 ASP.NET Core 5.0 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure의 업데이트된 ASP.NET Core 5.0 웹앱":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    업데이트된 ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code - Azure의 업데이트된 ASP.NET Framework 4.8 웹앱":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

로컬 디렉터리에서 *Index.cshtml* 파일을 엽니다. 첫 번째 `<div>` 요소를 바꿉니다.

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

변경 내용을 저장한 다음, `az webapp up` 명령을 사용하여 앱을 다시 배포합니다.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3.1은 크로스 플랫폼이며, 이전 배포에 따라 `<os>`를 `linux` 또는 `windows`로 바꿉니다.

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5.0은 크로스 플랫폼이며, 이전 배포에 따라 `<os>`를 `linux` 또는 `windows`로 바꿉니다.

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4.8에는 프레임워크 종속성이 있으며 Windows에서 호스트해야 합니다.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Linux에서 .NET 앱을 호스트하는 데 관심이 있는 경우 [ASP.NET Framework에서 ASP.NET Core](/aspnet/core/migration/proper-to-2x)로 마이그레이션하는 것이 좋습니다.

---

이 명령은 앱 이름, 리소스 그룹 및 App Service 계획을 포함하여 *.azure/config* 파일에서 로컬로 캐시된 값을 사용합니다.

배포가 완료되면 **앱으로 이동** 단계에서 열린 브라우저 창으로 다시 전환하고 새로 고침을 누릅니다.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

업데이트된 ASP.NET Core 3.1 웹앱이 페이지에 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI - Azure의 업데이트된 ASP.NET Core 3.1 web app":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

업데이트된 ASP.NET Core 5.0 웹앱이 페이지에 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI - Azure의 업데이트된 ASP.NET Core 5.0 web app":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

업데이트된 ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI - Azure의 업데이트된 ASP.NET Framework 4.8 웹앱":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>Azure 앱 관리

웹앱을 관리하려면 [Azure Portal](https://portal.azure.com)로 이동하고, **App Services** 를 검색하여 선택합니다.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure Portal - App Services 선택 옵션":::

**App Service** 페이지에서 웹앱의 이름을 선택합니다.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure Portal - 예제 웹앱이 선택된 App Services 페이지":::

웹앱의 **개요** 페이지에는 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 옵션이 포함되어 있습니다. 왼쪽 메뉴는 앱을 구성하기 위한 추가 페이지를 제공합니다.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure Portal - App Service 개요 페이지":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ASP.NET 웹앱을 만들고 Azure App Service에 배포했습니다.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

다음 문서로 넘어가서 .NET Core 앱을 만들고 SQL Database에 연결하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자습서: SQL 데이터베이스를 사용하는 ASP.NET Core 앱](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core 3.1 앱 구성](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

다음 문서로 넘어가서 .NET Core 앱을 만들고 SQL Database에 연결하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자습서: SQL 데이터베이스를 사용하는 ASP.NET Core 앱](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core 5.0 앱 구성](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

다음 문서로 넘어가서 .NET Framework 앱을 만들고 SQL Database에 연결하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자습서: SQL 데이터베이스를 사용하는 ASP.NET 앱](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [ASP.NET Framework 앱 구성](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
