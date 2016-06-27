<properties
	pageTitle="개발자 분석용 Visual Studio 확장에 대한 릴리스 정보"
	description="개발자 분석용 Visual Studio Tools의 최신 업데이트."
	services="application-insights"
    documentationCenter=""
	authors="acearun"
	manager="douge"/>
<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2016"
	ms.author="acearun"/>

# 릴리스 정보 - 개발자 분석 도구
##### Visual Studio에서 Application Insights 및 HockeyApp 분석
## Version 7.0.1
Application Insights는 이제 Visual Studio에서 ASP.NET Core RC2 프로젝트를 지원합니다. Application Insights를 새 프로젝트 대화 상자에서 새로운 ASP.NET Core RC2 프로젝트나 기존 프로젝트에 추가할 수 있습니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 “Application Insights 원격 분석 추가..."를 선택합니다.

![.NET Core 지원](./media/app-insights-release-notes-vsix/NetCoreSupport.PNG)

ASP.NET 5 RC1과 ASP.NET Core RC2 프로젝트는 진단 도구 창에서 새 지원이 제공됩니다. PC에서 로컬 디버깅을 하는 동안 ASP.NET 앱에서 요청 및 예외 등의 Application Insights 이벤트를 볼 수 있습니다. 각 이벤트에서 “검색"을 클릭하면 자세한 정보를 살펴볼 수 있습니다.

![진단 도구 지원](./media/app-insights-release-notes-vsix/DiagnosticTools.PNG)

다른 새 기능:

* 시간 범위와 세부 정보 필터를 선택하는 즉시 자동 적용되도록 하여 Application Insights Search 경험이 더욱 빠르고 직관적이 되었습니다.
* Application Insights Search에는 요청에서 코드로 이동 옵션이 생겼습니다.
* HockeyApp 로그인 환경도 개선했습니다.

## 버전 5.2
Visual Studio에 HockeyApp 시나리오가 도입되었음을 알려 드립니다. Microsoft에서 구현한 첫 번째 통합은 VS 내 유니버설 Windows 및 Windows Forms 앱의 베타 배포판입니다.

베타 배포판을 통해 이전 버전의 앱을 HockeyApp에 업로드하여 선택한 일부 그룹의 고객 또는 테스터에게 배포할 수 있습니다. 베타 배포판은 HockeyApp 크래시 수집 및 사용자 의견 기능과 함께 광범위한 릴리스에 앞서 앱에 대한 소중한 정보를 제공할 수 있습니다. 이 정보를 사용하여 문제가 커지기 전에(낮은 평점, 좋지 않은 의견 등) 문제를 해결할 수 있습니다.

VS 내에서 베타 배포판의 빌드를 업로드하는 것이 얼마나 간단한지 알아보세요.
### 범용 Windows 앱
이제 UWP 프로젝트 노드의 상황에 맞는 메뉴에 HockeyApp으로 빌드를 업로드하는 옵션이 포함되어 있습니다.

![유니버설 앱의 프로젝트 상황에 맞는 메뉴](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

항목을 선택하면 HockeyApp 업로드 대화 상자가 표시됩니다. 빌드를 업로드하려면 HockeyApp 계정이 필요합니다. 새 사용자인 경우 걱정하지 마세요. 계정을 만드는 과정은 간단합니다.

연결되면 대화 상자에 업로드 양식이 표시됩니다.

![유니버설 앱의 업로드 대화 상자](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

업로드할 콘텐츠(appxbundle 또는 appx)를 선택한 다음 마법사에서 릴리스 옵션을 선택합니다. 다음 페이지에서 선택적으로 릴리스 정보를 추가할 수 있습니다. ‘마침’을 선택하면 업로드가 시작됩니다.

업로드가 완료되면 확인 및 HockeyApp 포털의 앱 링크가 포함된 HockeyApp 알림이 표시됩니다.

![업로드 완료 알림](./media/app-insights-release-notes-vsix/UploadComplete.png)

이것으로 끝입니다. 방금 몇 번의 클릭만으로 베타 배포판에 대한 빌드를 업로드했습니다.

HockeyApp 포털에서 다양한 방법(사용자 초대, 크래시 보고서 및 의견 보기, 세부 정보 변경 등)으로 응용 프로그램을 관리할 수 있습니다.

![HockeyApp 포털](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

응용 프로그램 관리에 대한 자세한 내용은 [Hockey 앱 기술 자료](http://support.hockeyapp.net/kb/app-management-2)에서 확인할 수 있습니다.

### Windows Forms 앱
Windows Form 프로젝트 노드의 상황에 맞는 메뉴에 HockeyApp으로 빌드를 업로드하는 옵션이 포함되어 있습니다.

![Windows Forms 앱의 프로젝트 상황에 맞는 메뉴](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

유니버설 앱의 대화 상자와 유사한 HockeyApp 업로드 대화 상자가 표시됩니다.

![Windows Forms 앱의 업로드 대화 상자](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

이 마법사의 추가 필드에서 앱의 버전을 지정할 수 있습니다. 유니버설 앱의 경우 매니페스트에서 정보가 채워집니다. Win Forms에는 이와 동일한 기능이 없으므로 수동으로 지정해야 합니다.

나머지 흐름은 유니버설 앱과 유사합니다. 빌드 및 릴리스 옵션을 선택하고, 릴리스 정보를 추가하고, 업로드하고, HockeyApp 포털에서 관리할 수 있습니다.

그만큼 간단합니다. 사용해 보시고 의견을 알려 주시기 바랍니다.
## 버전 4.3
### 로컬 디버그 세션에서 원격 분석 검색
이 릴리스에서는 Visual Studio 디버그 세션에서 생성되는 Application Insights 원격 분석에 대한 검색 기능을 도입합니다. 이전에는 Application Insights로 앱을 등록한 경우에만 검색이 가능했습니다. 이 릴리스에서 앱은 로컬 원격 분석에 대해 검색하기 위해 설치된 Application Insights SDK만 필요합니다.

#### Application Insights SDK를 사용하는 ASP.NET 응용 프로그램이 있는 경우

- 응용 프로그램을 디버그합니다.
- 다음 방법 중 하나를 사용하여 Application Insights 검색을 엽니다.
	- 보기 메뉴-> 다른 창 -> Application Insights 검색
	- Application Insights 도구 모음 단추 클릭
	- 솔루션 탐색기에서 ApplicationInsights.config 확장 -> 디버그 세션 원격 분석 검색
- Application Insights에 등록하지 않은 경우 '디버그 세션 원격 분석' 모드에서 검색 창이 열립니다.
- 검색 아이콘을 클릭하여 로컬 원격 분석을 확인합니다.

![업로드 완료](./media/app-insights-release-notes-vsix/LocalSearch.png)



##버전 4.2
이 릴리스에서는 이벤트의 컨텍스트에서 데이터 검색을 더 쉽게 할 수 있는 기능, 더 많은 데이터 이벤트에서 코드로 점프할 수 있는 기능 및 더 간편하게 로깅 데이터를 Application Insights에 보내는 환경을 추가했습니다. 이 확장은 매달 업데이트됩니다. 피드백 또는 기능 요청이 있는 경우 aidevtools@microsoft.com에 보내세요.
###- 0-클릭 로깅 환경
이미 NLog, Log4Net 또는 System.Diagnostics 추적을 사용하는 경우 모든 추적을 AI에 이동시킬 필요가 없습니다. 현재 Application Insights 로깅 어댑터를 일반 구성 환경과 통합하는 중입니다. 이미 이러한 로깅 프레임워크 구성 중 하나가 있는 경우 다음과 같이 가져올 수 있습니다.
####Application Insights를 이미 추가한 경우
- 프로젝트 노드를 마우스 오른쪽 단추로 클릭->Application Insights->Application Insights 구성을 선택합니다. 구성 창에서 올바른 어댑터를 추가하기 위한 옵션을 확인해야 합니다.
- 또한 솔루션을 구성할 때 화면 오른쪽 위에 표시되는 팝업을 유의하고 구성을 클릭합니다. ![로깅 알림](./media/app-insights-release-notes-vsix/LoggingToast.png)

로깅 어댑터를 설치한 경우 응용 프로그램을 실행하고 다음과 같이 진단 도구 탭에 데이터가 표시되는지 확인합니다. ![추적](./media/app-insights-release-notes-vsix/Traces.png)
###- 사용자가 원격 분석 이벤트 속성을 내보낸 코드로 점프하거나 찾을 수 있음
새 릴리스에서는 사용자가 이벤트 세부 정보의 값을 클릭하면 현재 열려 있는 솔루션에서 일치하는 문자열을 검색할 수 있습니다. 아래와 같이 Visual Studio "결과 찾기" 목록에 결과가 표시됩니다. ![일치 항목 찾기](./media/app-insights-release-notes-vsix/FindMatch.png)
###- 로그인하지 않은 사용자를 위한 검색 창의 새 화면
프로덕션에서 해당 데이터를 검색하도록 사용자를 안내하는 검색 창 모양이 개선되었습니다. ![검색 창](./media/app-insights-release-notes-vsix/SearchWindow.png)
###- 사용자가 이벤트와 연결된 모든 원격 분석 이벤트를 볼 수 있음
이벤트 세부 정보 옆에 관련된 모든 데이터를 보기 위해 미리 정의된 쿼리가 포함된 새 탭이 사용자가 보는 원격 분석 이벤트에 추가되었습니다. 예를 들어 요청에 작업 ID라는 필드가 있고 이 요청에 연결된 모든 이벤트에 동일한 작업 ID가 있으므로 요청을 처리하는 동안 예외가 발생한 경우 요청과 동일한 작업 ID를 가져와서 해당 요청을 좀 더 쉽게 찾을 수 있습니다. 따라서 이제 요청을 확인하는 사용자가 "이 작업에 대한 모든 원격 분석"을 클릭하면 새 검색 결과가 포함된 새 탭이 열립니다. ![관련 항목](./media/app-insights-release-notes-vsix/RelatedItems.png)
### - 검색에 기록 앞으로/뒤로 추가
이제 사용자가 검색 결과의 앞뒤로 이동할 수 있습니다. ![뒤로 이동](./media/app-insights-release-notes-vsix/GoBAck.png)

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

*Connect();* 2015에서 장치에 대한 모바일 DevOps 환경이 HockeyApp이라고 [발표](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/)했습니다. HockeyApp을 사용하면 테스터에 베타 빌드를 배포하고 앱에서 모든 충돌을 수집하고 분석하며 고객으로부터 직접 의견을 수집할 수 있습니다. HockeyApp은 iOS, Android, Windows 또는 Xamarin, Cordova, Unity와 같은 플랫폼 간 솔루션이든 모바일 응용 프로그램을 빌드하는 모든 플랫폼을 지원합니다.

향후 Application Insights 확장 릴리스에서는 HockeyApp과 Visual Studio 간에 보다 통합된 환경을 사용할 수 있도록 새로운 기능이 도입됩니다. 이제 NuGet 참조를 추가하기만 하면 HockeyApp을 시작할 수 있습니다. 자세한 내용은 [설명서](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)를 참조하세요.

<!---HONumber=AcomDC_0615_2016-->