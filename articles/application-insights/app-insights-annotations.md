<properties
    pageTitle="Application Insights에 대한 릴리스 주석 | Microsoft Azure"
    description="Application Insights에서 배포 또는 빌드 표식을 메트릭 탐색기 차트에 추가합니다."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
	ms.date="06/28/2016"
    ms.author="awills"/>

# Application Insights의 릴리스 주석

[메트릭 탐색기](app-insights-metrics-explorer.md) 차트의 릴리스 주석은 새 빌드를 배포한 위치를 표시합니다. 릴리스 주석으로 변경 내용이 응용 프로그램의 성능에 영향을 주는지 여부를 쉽게 확인할 수 있습니다. [Visual Studio Team Services 빌드 시스템](https://www.visualstudio.com/ko-KR/get-started/build/build-your-app-vs)에서 자동으로 만들 수 있고 사용자가 [PowerShell에서 직접 만들 수도](#create-annotations-from-powershell) 있습니다.

![서버 응답 시간과 상관 관계가 표시된 주석 예제](./media/app-insights-annotations/00.png)

릴리스 주석은 클라우드 기반 빌드 기능 및 Visual Studio Team Services 릴리스 서비스입니다.

## 주석 확장 설치(한 번)

릴리스 주석을 만들려면 Visual Studio 마켓플레이스에서 사용 가능한 여러 Team Service 확장 중 하나를 설치해야 합니다.

1. [Visual Studio Team Services](https://www.visualstudio.com/ko-KR/get-started/setup/sign-up-for-visual-studio-online) 프로젝트에 로그인합니다.
2. Visual Studio 마켓플레이스의 [릴리스 주석 확장을 가져와서](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) 팀 서비스 계정에 추가합니다.

![Team Services 웹 페이지의 오른쪽 위에서 마켓플레이스를 엽니다. Visual Team Services를 선택하고 빌드 및 릴리스에서 자세히 보기를 선택합니다.](./media/app-insights-annotations/10.png)

Visual Studio Team Services 계정에 대해 이 작업을 한 번만 수행하면 됩니다. 이제 계정의 모든 프로젝트에 대해 릴리스 주석을 구성할 수 있습니다.

## Application Insights에서 API 키 가져오기

릴리스 주석을 만들려는 각 릴리스 템플릿에 대해 이 작업을 수행해야 합니다.


1. [Microsoft Azure 포털](https://portal.azure.com)에 로그인하고 응용 프로그램을 모니터링하는 Application Insights 리소스를 엽니다. (또는 아직 만들지 않은 경우 [지금 만듭니다](app-insights-overview.md).)
2. **API 액세스**를 차례로 열어 **Application Insights ID**를 복사합니다.

    ![portal.azure.com에서 Application Insights 리소스를 열고 설정을 선택합니다. API 액세스를 엽니다. 응용 프로그램 ID를 복사합니다.](./media/app-insights-annotations/20.png)

2. 별도의 브라우저 창에서, Visual Studio Team Services에서 배포를 관리하는 릴리스 템플릿을 열거나 만듭니다.

    작업을 추가하고 메뉴에서 Application Insights 릴리스 주석 작업을 선택합니다.

    API 액세스 블레이드에서 복사한 **응용 프로그램 ID**를 붙여넣습니다.

    ![Visual Studio Team Services에서 릴리스를 열고 릴리스 정의를 선택하고 편집을 선택합니다. 작업 추가를 클릭하고 Application Insights 릴리스 주석을 선택합니다. Application Insights ID를 붙여넣습니다.](./media/app-insights-annotations/30.png)

3. **APIKey** 필드를 변수 `$(ApiKey)`로 설정합니다.

4. API 액세스 블레이드로 돌아가 새 API 키를 만들어 복사합니다.

    ![Azure 창의 API 액세스 블레이드에서 API 키 만들기를 클릭합니다. 설명을 입력하고 주석 쓰기를 선택하고 키 생성을 클릭합니다. 새 키를 복사합니다.](./media/app-insights-annotations/40.png)

4. 릴리스 템플릿의 구성 탭을 엽니다.

    `ApiKey`에 대한 변수 정의를 만듭니다.

    ApiKey 변수 정의에 API 키를 붙여넣습니다.

    ![Team Services 창에서 구성 탭을 선택하고 변수 추가를 클릭합니다. ApiKey에 대한 이름을 설정하고 값에 방금 생성한 키를 붙여넣습니다.](./media/app-insights-annotations/50.png)


5. 마지막으로 릴리스 정의를 **저장**합니다.

## PowerShell에서 주석 만들기

VS Team 시스템을 사용하지 않고 원하는 모든 프로세스에서 주석을 만들 수도 있습니다.

[GitHub에서 Powershell 스크립트](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)를 가져옵니다.

다음과 같이 사용합니다.

    .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }

Application Insights 리소스에서 `applicationId` 및 `apiKey`를 가져옵니다. 설정, API 액세스를 차례로 열고 응용 프로그램 ID를 복사합니다. 그런 다음 API 키 만들기를 클릭하고 해당 키를 복사합니다.

## 릴리스 주석

이제 릴리스 템플릿을 사용하여 새 릴리스를 배포할 때마다 주석이 Application Insights로 전송됩니다. 주석은 메트릭 탐색기의 차트에 표시됩니다.

주석 마커를 클릭하면 요청자, 소스 제어 분기, 릴리스 정의, 환경 등 릴리스에 대한 세부 정보가 열립니다.


![릴리스 주석 마커를 클릭합니다.](./media/app-insights-annotations/60.png)

<!---HONumber=AcomDC_0713_2016-->