---
title: C# ASP.NET Core 웹앱 만들기 - Azure App Service | Microsoft Docs
description: 기본 C# ASP.NET Core 웹앱을 배포하여 Azure App Service에서 웹앱을 실행하는 방법을 알아봅니다.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 82a1dc293a019e4a48760ccbce830d067f2d620d
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240930"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Azure에서 ASP.NET Core 웹앱 만들기

> [!NOTE]
> 이 문서에서는 Windows의 App Service에 앱을 배포합니다. _Linux_ 의 App Service에 배포하려면 [Linux의 App Service에서 .NET Core 웹앱 만들기](./containers/quickstart-dotnetcore.md)를 참조하세요.
>

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다.

이 빠른 시작에서는 첫 번째 ASP.NET Core 웹앱을 Azure App Service에 배포하는 방법을 보여줍니다. 빠른 시작을 마치고 나면 배포된 웹 애플리케이션으로 App Service 계획 및 App Service 앱으로 구성된 리소스 그룹이 만들어집니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 **ASP.NET 및 웹 개발** 워크로드가 포함된 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>를 설치합니다.

Visual Studio 2019를 이미 설치한 경우:

- **도움말** > **업데이트 확인**을 차례로 선택하여 Visual Studio에서 최신 업데이트를 설치합니다.
- **도구** > **도구 및 기능 가져오기**를 차례로 선택하여 워크로드를 추가합니다.

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core 웹앱 만들기

다음 단계에 따라 ASP.NET Core 웹앱을 만듭니다.

1. Visual Studio를 연 다음, **새 프로젝트 만들기**를 선택합니다.

1. **새 프로젝트 만들기**에서 C#용 **ASP.NET Core 웹 응용 프로그램**을 찾아서 선택하고, **다음**을 선택합니다.

1. **새 프로젝트 구성**에서 애플리케이션 이름을 _myFirstAzureWebApp_으로 지정한 다음, **만들기**를 선택합니다.

   ![웹앱 프로젝트 구성](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. 이 빠른 시작에서는 **웹 응용 프로그램** 템플릿을 선택합니다. 인증이 **인증 없음**으로 설정되어 있고 다른 옵션이 선택되어 있지 없음을 확인합니다. **만들기**를 선택합니다.

   ![이 자습서에서 ASP.NET Core Razor Pages 선택](./media/app-service-web-get-started-dotnet/aspnet-razor-pages-app.png)

    모든 종류의 ASP.NET Core 웹앱을 Azure에 배포할 수 있습니다.

1. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작**을 차례로 선택하여 웹앱을 로컬로 실행합니다.

   ![로컬에서 앱 실행](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-your-web-app"></a>웹앱 게시

1. **솔루션 탐색기**에서 마우스 오른쪽 단추로 **myFirstAzureWebApp** 프로젝트를 클릭하고, **게시**를 선택합니다.

1. **App Service**를 선택한 다음, **게시**를 선택합니다.

   ![프로젝트 개요 페이지에서 게시](./media/app-service-web-get-started-dotnet/publish-app-vs2019.png)

1. **App Service 새로 만들기**의 옵션은 Azure에 이미 로그인했는지 여부와 Azure 계정에 연결된 Visual Studio 계정이 있는지 여부에 따라 달라집니다. **계정 추가** 또는 **로그인** 중 하나를 선택하여 Azure 구독에 로그인합니다. 이미 로그인한 경우 원하는 계정을 선택합니다.

   > [!NOTE]
   > 이미 로그인한 경우 아직 **만들기**를 선택하지 마십시오.
   >

   ![Azure에 로그인](./media/app-service-web-get-started-dotnet/sign-in-azure-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. **리소스 그룹**에 대해 **새로 만들기**를 선택합니다.

1. **새 리소스 그룹 이름**에서 *myResourceGroup*을 입력하고, **확인**을 선택합니다.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. **호스팅 계획**에 대해 **새로 만들기**를 선택합니다.

1. **호스팅 계획 구성** 대화 상자에서 다음 표의 값을 입력한 다음, **확인**을 선택합니다.

   | 설정 | 제안 값 | 설명 |
   |-|-|-|
   |App Service 계획| myAppServicePlan | App Service 계획의 이름입니다. |
   | 위치 | 서유럽 | 웹앱이 호스팅된 데이터 센터입니다. |
   | 크기 | 무료 | [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)은 호스팅 기능을 결정합니다. |

   ![App Service 플랜 만들기](./media/app-service-web-get-started-dotnet/app-service-plan-vs2019.png)

1. **이름**에서 유효한 문자(`a-z`, `A-Z`, `0-9` 및 `-`)만 포함된 고유한 앱 이름을 입력합니다. 자동으로 생성된 고유한 이름을 적용할 수 있습니다. 웹앱의 URL은 `http://<app_name>.azurewebsites.net`이며, 여기서 `<app_name>`은 앱 이름입니다.

   ![앱 이름 구성](./media/app-service-web-get-started-dotnet/web-app-name-vs2019.png)

1. **만들기**를 선택하여 Azure 리소스를 만듭니다.

마법사가 완료되면 Azure에 ASP.NET Core 웹앱을 게시한 다음 기본 브라우저에서 앱을 시작합니다.

![Azure에서 게시된 ASP.NET 웹앱](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**App Service 새로 만들기** 페이지에서 지정한 앱 이름은 `http://<app_name>.azurewebsites.net` 형식의 URL 접두사로 사용됩니다.

**축하합니다.** ASP.NET Core 웹앱이 Azure App Service에서 실시간으로 실행되고 있습니다.

## <a name="update-the-app-and-redeploy"></a>앱 업데이트 및 재배포

1. **솔루션 탐색기**의 프로젝트 아래에서 **페이지** > **Index.cshtml**을 차례로 엽니다.

1. 두 개의 `<div>` 태그를 다음 코드로 바꿉니다.

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Azure에 다시 배포하려면 **솔루션 탐색기**에서 **myFirstAzureWebApp** 프로젝트를 마우스 오른쪽 버튼으로 클릭하고 **게시**를 선택합니다.

1. **게시** 요약 페이지에서 **게시**를 선택합니다.

   ![Visual Studio 게시 요약 페이지](./media/app-service-web-get-started-dotnet/publish-summary-page-vs2019.png)

게시가 완료되면 Visual Studio가 웹앱의 URL로 브라우저를 시작합니다.

![Azure에서 업데이트된 ASP.NET 웹앱](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>Azure 앱 관리

1. 웹앱을 관리하려면 <a href="https://portal.azure.com" target="_blank">Azure Portal</a>로 이동합니다.

1. 왼쪽 메뉴에서 **App Services**를 선택한 다음, Azure 앱의 이름을 선택합니다.

   ![Azure 앱에 대한 포털 탐색](./media/app-service-web-get-started-dotnet/access-portal-vs2019.png)

   웹앱의 개요 페이지가 표시됩니다. 여기서 찾아보기, 중지, 시작, 다시 시작 및 삭제와 같은 기본 관리를 수행할 수 있습니다.

   ![Azure Portal의 App Service](./media/app-service-web-get-started-dotnet/web-app-general-vs2019.png)

   왼쪽 메뉴로 앱 구성을 위한 여러가지 페이지를 볼 수 있습니다.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [SQL Database를 사용한 ASP.NET Core](app-service-web-tutorial-dotnetcore-sqldb.md)
