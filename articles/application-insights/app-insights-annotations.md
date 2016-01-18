<properties
    pageTitle="Application Insights에 대한 배포 주석 | Microsoft Azure"
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
	ms.date="01/05/2016"
    ms.author="awills"/>

# Application Insights의 릴리스 주석

[메트릭 탐색기](app-insights-metrics-explorer.md) 차트의 릴리스 주석은 새 빌드를 배포한 위치를 표시합니다. 릴리스 주석으로 변경 내용이 응용 프로그램의 성능에 영향을 주는지 여부를 쉽게 확인할 수 있습니다. 릴리스 주석은 [Visual Studio Team Services 빌드 시스템](https://www.visualstudio.com/ko-KR/get-started/build/build-your-app-vs)에서 자동으로 만들 수 있습니다.

![서버 응답 시간과 상관 관계가 표시된 주석 예제](./media/app-insights-annotations/00.png)

릴리스 주석은 클라우드 기반 빌드 기능 및 Visual Studio Team Services 릴리스 서비스입니다.

## 주석 확장 설치(한 번)

릴리스 주석을 만들려면 Visual Studio 마켓플레이스에서 사용 가능한 여러 Team Service 확장 중 하나를 설치해야 합니다.

1. [Visual Studio Team Services](https://www.visualstudio.com/ko-KR/get-started/setup/sign-up-for-visual-studio-online) 프로젝트에 로그인합니다.
2. Visual Studio 마켓플레이스를 열고 Application Insights 주석 확장을 찾아 Team Services 계정에 추가합니다.

![Team Services 웹 페이지의 오른쪽 위에서 마켓플레이스를 엽니다. Application Insights 주석을 검색하여 계정에 설치합니다.](./media/app-insights-annotations/10.png)

Visual Studio Team Services 계정에 대해 이 작업을 한 번만 수행하면 됩니다. 이제 계정의 모든 프로젝트에 대해 릴리스 주석을 구성할 수 있습니다.



## 릴리스 템플릿에 주석 작업 추가

릴리스 주석을 만들려는 각 릴리스 템플릿에 대해 이 작업을 수행해야 합니다.

Visual Studio Team Services에서 배포를 관리하는 릴리스 템플릿을 열거나 만듭니다.

작업을 추가하고 메뉴에서 Application Insights 릴리스 주석 작업을 선택합니다.

![Team Services 웹 페이지의 오른쪽 위에서 마켓플레이스를 엽니다. Application Insights 주석을 검색하여 계정에 설치합니다.](./media/app-insights-annotations/40.png)

이 단계를 완료하려면 응용 프로그램을 모니터링하는 데 사용하는 Application Insights 리소스의 일부 세부 정보가 필요합니다.

Application Insights에서 세부 정보를 가져오는 동안 Team Services 창은 계속 열어 둡니다.

## Application Insights에서 API 키 복사

별도의 브라우저 창에서:

1. [Microsoft Azure 포털](https://portal.azure.com)에 로그인하고 응용 프로그램을 모니터링하는 Application Insights 리소스를 엽니다. (또는 아직 만들지 않은 경우 [지금 만듭니다](app-insights-overview.md).)
2. **Essentials** 드롭다운 목록을 열고 구독 ID, 리소스 그룹 및 리소스의 이름을 릴리스 주석 작업에 복사합니다. ![](./media/app-insights-annotations/50.png)
2. **설정**, **API 키**를 열고 새 키를 만듭니다. 이를 복사합니다. ![](./media/app-insights-annotations/30.png)

마지막으로 릴리스 정의를 **저장**합니다.

## 배포 표식

이제 릴리스 템플릿을 사용하여 새 릴리스를 배포할 때마다 주석이 Application Insights로 전송됩니다. 주석은 메트릭 탐색기의 차트에 표시됩니다.

<!---HONumber=AcomDC_0107_2016-->