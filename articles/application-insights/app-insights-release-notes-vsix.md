<properties 
	pageTitle="Application Insights에 대한 Visual Studio 확장 릴리스 정보" 
	description="Application Insights에 대한 Visual Studio Tools의 최신 업데이트입니다." 
	services="application-insights" 
    documentationCenter=""
	authors="dimazaid" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="dimazaid"/>
 
# Application Insights Tools for Visual Studio v 4.1에 대한 릴리스 정보

##버전 4.1
이 릴리스는 기존 버전에서 향상된 기능과 다양한 새로운 기능을 함께 제공합니다. 이 릴리스를 다운로드하려면 컴퓨터에 업데이트 1을 설치해야 합니다.

### 예외에서 소스 코드의 메서드로 이동
이제 Application Insights 검색 창의 프로덕션 앱에서 사용자에게 예외가 보일 때 예외가 발생하는 해당 코드의 메서드로 바로 이동할 수 있습니다. 올바른 프로젝트를 로드하기만 하면 나머지는 알아서 처리됩니다. (검색 창에 대한 자세한 내용은 아래의 4.0 릴리스 정보 참조)

#### 작동 원리

솔루션이 열려 있지 않은 경우 AI 검색은 솔루션을 열지 않으면서도 사용할 수 있습니다. 이 경우 스택 추적 영역에서 정보 메시지를 볼 수 있으며 스택 추적의 많은 항목이 회색으로 표시됩니다.


파일 정보를 사용할 수 있는 경우 일부 항목은 연결할 수 있지만 솔루션 정보 항목이 여전히 표시됩니다.

하이퍼링크를 클릭하면 코드에서 선택한 메서드가 있는 곳으로 이동하고, 버전 번호에 따라 다를 수 있지만 이후 릴리스에서 코드의 올바른 버전으로 이동하는 기능이 제공됩니다.

![예외 클릭](./media/app-insights-release-notes-vsix/jumptocode.png)

###솔루션 탐색기의 검색 환경에 대한 새 진입점 

![솔루션 탐색기의 진입점](./media/app-insights-release-notes-vsix/searchentry.png)


###게시 완료 시 팝업 알림
프로젝트를 온라인에 게시하면 팝업이 표시되므로 프로덕션에서 Application Insights 데이터를 볼 수 있습니다.

![팝업](./media/app-insights-release-notes-vsix/publishtoast.png)

## 버전 4.0

###Visual Studio에서 Application Insights 데이터 검색
Application Insights 포털에서 검색하는 것과 마찬가지로 이벤트 형식, 속성 값 및 텍스트를 필터링하고 검색하여 개별 이벤트를 검사할 수 있습니다.

![검색 창](./media/app-insights-release-notes-vsix/search.png)

###진단 도구 창의 로컬 상자에서 가져온 데이터를 참조하세요.

원격 분석은 Visual Studio 진단 허브에 다른 디버깅 데이터와 함께 표시됩니다. ASP.NET 4.5만 이 기능을 지원합니다. ASP.NET 5에 대한 지원은 출시 예정인 이후 릴리스에서 제공됩니다.

![진단 허브 창](./media/app-insights-release-notes-vsix/diagtools.png)

###Azure에 로그인하지 않고도 프로젝트에 SDK 추가

프로젝트 상황에 맞는 메뉴 또는 새 프로젝트 대화 상자에서 프로젝트에 Application Insights 패키지를 추가하기 위해 더 이상 Azure에 로그인할 필요가 없습니다. 로그인하면 이전과 마찬가지로 SDK가 설치되고 포털에 원격 분석을 보내도록 구성됩니다. 로그인하지 않으면 SDK가 프로젝트에 추가되고 진단 허브에 대한 원격 분석을 생성하므로 나중에 원할 때 구성할 수 있습니다.

![새 프로젝트 대화 상자](./media/app-insights-release-notes-vsix/newproject.png)

###장치 지원

Connect(); 2015에서 장치에 대한 모바일 DevOps 환경이 HockeyApp이라고 [발표했습니다](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/). HockeyApp을 사용하면 테스터에 베타 빌드를 배포하고 앱에서 모든 충돌을 수집하고 분석하며 고객으로부터 직접 의견을 수집할 수 있습니다. HockeyApp은 iOS, Android, Windows 또는 Xamarin, Cordova, Unity와 같은 플랫폼 간 솔루션이든 모바일 응용 프로그램을 빌드하는 모든 플랫폼을 지원합니다.

향후 Application Insights 확장 릴리스에서는 HockeyApp과 Visual Studio 간에 보다 통합된 환경을 사용할 수 있도록 새로운 기능이 도입됩니다. 이제 NuGet 참조를 추가하기만 하면 HockeyApp을 시작할 수 있습니다. 자세한 내용은 [설명서](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)를 참조하세요.

 

<!---HONumber=AcomDC_0121_2016-->