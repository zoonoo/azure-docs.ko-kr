---
title: "Linux의 Web App에서 .NET Core 사용 | Microsoft Docs"
description: "Linux의 Web App에서 .NET Core를 사용합니다."
keywords: "azure app service, 웹앱, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
authors: michimune, rachelappel
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: aelnably;wesmc;mikono;rachelap
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: cbcc43e04e58894b6505fdf6c48c7792343049b9
ms.contentlocale: ko-kr
ms.lasthandoff: 07/26/2017

---

# <a name="use-net-core-in-an-azure-web-app-on-linux"></a>Linux의 Azure Web App에서 .NET Core 사용 #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

Linux의[Web App](https://docs.microsoft.com/azure/app-service-web/app-service-linux-intro)은 Linux 운영 체제를 사용하여 확장성이 높은 자동 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에는 Linux의 Azure Web App에서 [.NET Core](https://docs.microsoft.com/aspnet/core/) 앱을 만드는 방법을 보여 주는 단계별 지침이 나와 있습니다. 

![Linux에서 Web App][10]

Mac, Windows 또는 Linux 컴퓨터를 사용하여 아래 단계를 따르면 됩니다.

## <a name="prerequisites"></a>필수 조건 ##

이 자습서를 완료하려면 다음이 필요합니다. 

* [.NET Core SDK](https://www.microsoft.com/net/download/core)를 설치합니다.
* [Git](https://git-scm.com/downloads)를 설치합니다.

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-local-net-core-application"></a>로컬 .NET Core 응용 프로그램 만들기 ##

새 터미널 세션을 시작합니다. `hellodotnetcore`라는 디렉터리를 만들고 현재 디렉터리를 이 디렉터리로 변경합니다. 그런 후에 다음을 입력합니다. 

```
dotnet new web
``` 

  이 명령은 현재 디렉터리 아래에 3개의 파일(*hellodotnetcore.csproj*, *Program.cs* 및 *Startup.cs*)과 하나의 빈 폴더(*wwwroot/*)를 만듭니다. `.csproj` 파일의 내용은 다음과 같아야 합니다. 

```xml
  <!-- Empty lines are omitted. -->

  <Project Sdk="Microsoft.NET.Sdk.Web">
        <PropertyGroup>
        <TargetFramework>netcoreapp1.1</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
        <Folder Include="wwwroot\" />
        </ItemGroup>
        <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore" Version="1.1.2" />
        </ItemGroup>
  </Project>
```

이 앱은 웹 응용 프로그램이므로 ASP.NET Core 패키지에 대한 참조가 *hellodotnetcore.csproj* 파일에 자동으로 추가되었습니다. 패키지의 버전 번호는 선택한 프레임워크에 따라 설정됩니다. .NET Core 1.1이 사용되었으므로 이 예제에서는 ASP.NET Core 버전 1.1.2를 참조합니다.

## <a name="build-and-test-the-application-locally"></a>로컬에서 응용 프로그램 빌드 및 테스트 ##

다음과 같이 `dotnet restore` 명령 다음에 `dotnet run` 명령을 사용하여 .NET Core 앱을 빌드하고 실행할 수 있습니다.

```
dotnet restore
dotnet run
```


응용 프로그램을 시작하면 포트에서 들어오는 요청을 수신 대기 중임을 나타내는 메시지가 이 응용 프로그램에 표시됩니다. 

```bash
Hosting environment: Production
Content root path: C:\hellodotnetcore
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

브라우저에서 `http://localhost:5000/`으로 이동하여 테스트합니다. 모든 기능이 제대로 작동하는 경우 결과 텍스트로 "Hello World!"가 표시됩니다.

![브라우저를 통한 테스트][7]

## <a name="create-a-net-core-app-in-the-azure-portal"></a>Azure Portal에서 .NET Core 앱 만들기 ##

먼저 비어 있는 웹앱을 만들어야 합니다. [Azure Portal](https://portal.azure.com/)에 로그인하고 [Linux에 새 Web App을 만듭니다](https://portal.azure.com/#create/Microsoft.AppSvcLinux).

![웹앱 만들기][1]

**만들기** 페이지가 열리면 웹앱에 대한 세부 정보를 제공합니다.

![.NET Core 런타임 스택 선택][2]

다음 표를 지침으로 사용하여 **만들기** 페이지를 채운 다음 **확인**과 **만들기**를 선택하여 앱을 만듭니다.

| 설정      | 제안 값  | 설명                                        |
| ------------ | ---------------- | -------------------------------------------------- |
| 앱 이름 | hellodotnetcore  | 앱의 이름입니다. 이 이름은 고유해야 합니다. |
| 구독 | 기존 구독 선택 | Azure 구독입니다. |
| 리소스 그룹 | myResourceGroup |  웹앱을 포함할 Azure 리소스 그룹입니다. |
| 앱 서비스 계획 | 기존 App Service 계획 이름 |  App Service 계획입니다.  |
| 컨테이너 구성 | .NET Core 1.1 | 이 웹앱에 대한 컨테이너 형식, 즉 기본 제공, Docker 또는 개인 레지스트리입니다. |
| 이미지 원본  | 기본 제공  |  이미지의 원본입니다. |
| 런타임 스택  | .NET Core 1.1  | 런타임 스택 및 버전입니다.  |

## <a name="deploy-your-application-via-git"></a>Git를 통한 응용 프로그램 배포 ##

Git를 사용하여 .NET Core 응용 프로그램을 Linux의 Azure App Service Web App에 배포합니다.

새 Azure 웹앱에는 이미 Git 배포가 구성되어 있습니다. 웹앱 이름을 삽입한 후 다음 URL로 이동하여 Git 배포 URL을 찾을 수 있습니다.

```https://{your web app name}.scm.azurewebsites.net/api/scm/info```

Git URL은 웹앱 이름에 따라 다음과 같은 형식을 갖습니다.

```https://{your web app name}.scm.azurewebsites.net/{your web app name}.git```

다음 명령을 실행하여 Azure 웹앱에 로컬 응용 프로그램을 배포합니다. 
 
```bash
git init
git remote add azure <Git deployment URL from above>
git add *.csproj *.cs
git commit -m "Initial deployment commit"
git push azure master
```

응용 프로그램의 소스 파일을 Azure로 푸시할 때 응용 프로그램이 클라우드에 빌드되므로 *bin/* 또는 *obj/* 디렉터리 아래에 모든 파일을 푸시할 필요가 없습니다. 빌드 프로세스가 완료되면 이진 파일이 응용 프로그램 디렉터리 */home/site/wwwroot/*에 복사됩니다.

원격 배포 작업이 성공을 보고하는지 확인합니다. 패키지 확인 및 빌드 프로세스가 클라우드에서 실행되므로 푸시 작업에 시간이 걸릴 수 있습니다. 파일이 복사되었음을 포함하여 몇 가지 상태 메시지가 표시됩니다. 출력은 다음과 비슷해야 합니다.

```bash
/* some output has been removed for brevity */
remote: Copying file: 'System.Net.Websockets.dll' 
remote: Copying file: 'System.Runtime.CompilerServices.Unsafe.dll' 
remote: Copying file: 'System.Runtime.Serialization.Primitives.dll' 
remote: Copying file: 'System.Text.Encodings.Web.dll' 
remote: Copying file: 'hellodotnetcore.deps.json' 
remote: Copying file: 'hellodotnetcore.dll' 
remote: Omitting next output lines...
remote: Finished successfully.
remote: Running post deployment commands...
remote: Deployment successful.
To https://hellodotnetcore.scm.azurewebsites.net/
 * [new branch]           master -> master

```

배포가 완료되면 웹앱을 다시 시작하여 배포를 적용합니다. 이렇게 하려면 Azure Portal로 이동하여 웹앱의 **개요** 페이지로 이동합니다. 해당 페이지에서 **다시 시작** 단추를 선택합니다. 팝업 창이 표시되면 **예**를 선택하여 확인합니다. 다음과 같이 웹앱을 찾아볼 수 있습니다.

![Linux의 Azure App Service에 배포된 .NET Core 앱 검색][10]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계
* [Linux의 Azure App Service Web App에 대한 FAQ](./app-service-linux-faq.md)

[1]: ./media/app-service-linux-using-dotnetcore/top-level-create.png
[2]: ./media/app-service-linux-using-dotnetcore/dotnet-new-webapp.png
[7]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-local.png
[10]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-azure.png

