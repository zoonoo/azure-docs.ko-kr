---
title: Application Insights Profiler를 사용하여 ASP.NET Core Azure Linux 웹앱 프로파일링 | Microsoft Docs
description: Application Insights Profiler에 대한 개념 및 이를 사용하는 방법에 대해 간략히 설명하는 단계별 자습서입니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 63a7ceacffe1ee33227d3a8272dda7de7b3b1135
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Application Insights Profiler를 사용하여 ASP.NET Core Azure Linux 웹앱 프로파일링

이 기능은 현재 미리 보기로 제공됩니다.

[Application Insights](app-insights-overview.md)를 사용할 때 라이브 웹 응용 프로그램의 각 메서드에서 얼마나 많은 시간이 소요되는지 알아봅니다. Profiler는 이제 Linux의 App Services에서 호스팅되는 ASP.NET Core 웹앱에서 사용할 수 있습니다. 이 가이드에서는 ASP.NET Core Linux 웹앱에 대한 프로파일러 추적을 수집하는 방법에 대한 단계별 지침을 제공합니다.

이 연습을 완료하면 앱에서 아래 스크린샷과 비슷한 프로파일러 추적을 수집합니다. 다음 예에서는 대부분의 시간이 대기하는 데 소요되었으므로 프로파일러 추적에서 특정 웹 요청이 느리다는 것을 나타냅니다. 앱의 속도가 저하된 코드의 핫 경로 앞에는 화염 아이콘이 표시됩니다. 이 예에서는 `HomeController`의 `About` 메서드에서 `Thread.Sleep`을 호출했기 때문에 웹앱의 속도가 저하되었음을 보여 줍니다.

![Profiler 추적](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="pre-requisites"></a>필수 조건
아래의 지침은 모든 Windows, Linux 및 Mac 개발 환경에 적용됩니다.

* [.NET Core SDK 2.1.2 이상](https://www.microsoft.com/net/download/windows/build)을 설치합니다.
* [시작 - Git 설치](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)의 지침에 따라 Git을 설치합니다.

## <a name="setup-project-locally"></a>로컬로 프로젝트 설정

1. 컴퓨터에서 명령 프롬프트를 엽니다. 아래의 지침은 모든 Windows, Linux 및 Mac 개발 환경에서 작동합니다.

2. ASP.NET Core MVC 웹 응용 프로그램을 만듭니다.
    ```
    dotnet new mvc -n LinuxProfilerTest
    ```
3. 명령 프롬프트에서 디렉터리를 프로젝트 루트 폴더로 변경합니다.

4. 프로파일러 추적을 수집하기 위해 Nuget 패키지를 추가합니다.
    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```
5. HomeController.cs에서 몇 초 동안 임의로 지연하는 코드 줄을 추가합니다.

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```
6. 변경 내용을 로컬 리포지토리에 저장하고 커밋합니다.

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-azure-app-service-for-hosting-your-project"></a>프로젝트를 호스팅하기 위한 Azure App Service 만들기
1. App Services Linux 환경을 만듭니다.

    ![Linux App Services 만들기](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. 배포 자격 증명을 만듭니다. 나중에 앱을 배포할 때 필요하므로 암호를 적어 둡니다.

    ![배포 자격 증명 만들기](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. 배포 옵션을 선택합니다. Azure Portal의 지침에 따라 웹앱에 로컬 Git 리포지토리를 설정합니다. Git 리포지토리가 자동으로 만들어집니다.

    ![Git 리포지토리 설정](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

[여기](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)서 더 많은 배포 옵션을 사용할 수 있습니다.

## <a name="deploy-your-project"></a>프로젝트 배포

1. 명령 프롬프트에서 프로젝트 루트 폴더로 이동합니다. App Services에서 가리키도록 Git 원격 리포지토리를 추가합니다.

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```
    * "배포 자격 증명 만들기" 단계의 '사용자 이름'을 사용합니다.
    * '앱 서비스 만들기' 단계의 '앱 이름'을 사용합니다.

2. 변경 내용을 Azure로 푸시하여 프로젝트를 배포합니다.

    ```
    git push azure master
    ```
다음과 비슷한 출력이 표시됩니다.

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>웹앱을 모니터링하는 Application Insights 추가
1. [Application Insights 리소스를 만듭니다.](./app-insights-create-new-resource.md)
2. Application Insights 리소스의 iKey를 복사하고 앱 서비스에서 다음 설정을 지정합니다

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![앱 설정 지정](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    앱 설정을 변경하면 사이트가 자동으로 다시 시작됩니다. 새 설정이 적용되면 프로파일러가 즉시 2분 동안 실행되기 시작합니다. 따라서 매시간 2분 동안 실행됩니다.

3. 웹 사이트에 일부 트래픽을 생성합니다. 사이트의 ```About``` 페이지를 몇 번 새로 고칠 수 있습니다.

4. 이벤트를 Application Insights에 집계할 수 있도록 2-5분 정도 기다립니다.

5. Azure Portal에서 Application Insights 성능 창으로 이동합니다. 오른쪽 아래 모서리에 프로파일러 추적이 표시됩니다.

    ![추적 보기](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>알려진 문제

### <a name="enable-button-in-profiler-configuration-pane-does-not-work"></a>프로파일러 구성 창에서 단추를 사용해도 작동하지 않습니다.
**App Services Linux를 사용하여 앱을 호스트하는 경우 App Insights 포털의 성능 창에서 프로파일러를 다시 사용하지 않아도 됩니다. 프로젝트에서 NuGet 패키지를 포함하고 앱 설정에서 App Insights iKey를 설정하면 충분히 프로파일러를 사용할 수 있습니다**.

[Windows용 App Insights Profiler](./app-insights-profiler.md) 사용 워크플로를 수행하여 프로파일러 구성 창에서 **사용**을 클릭하는 경우 단추가 Linux 환경에서 프로파일러 에이전트의 Windows 버전을 설치하려고 하므로 오류가 발생하게 됩니다.

사용 환경에서 이 문제를 해결하려고 노력하고 있습니다.

![프로파일러가 Linux App Services에서 작동하기 위해 성능 창에서 다시 프로파일러를 설정하지 않아도 됩니다.](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>다음 단계
App Services에 의해 호스트되는 사용자 지정 컨테이너를 사용하는 경우 [컨테이너화된 ASP.NET Core 응용 프로그램에 Service Profiler 설정](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp)의 지침을 따라 App Insights Profiler를 사용하도록 설정합니다.

문제 또는 제안 사항이 있으면 github 리포지토리([ApplicationInsights-Profiler-AspNetCore: 문제](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues))에 보고해 주세요.
