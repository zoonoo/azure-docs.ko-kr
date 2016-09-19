<properties
	pageTitle="개발자 분석"
	description="Visual Studio, Application Insights 및 HockeyApp을 사용한 DevOps"
	authors="alancameronwills"
	services="application-insights"
    documentationCenter=""
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="05/18/2016"
	ms.author="awills"/>

# Application Insights 및 HockeyApp을 사용한 개발자 분석

*Application Insights는 미리 보기 상태입니다.*

여러 프로젝트를 통해 빠른 [DevOps](https://en.wikipedia.org/wiki/DevOps) 주기가 작동됩니다. 이러한 프로젝트는 응용 프로그램을 빌드 및 배포하고, 수행 방식 및 수행하는 사용자에 대한 피드백을 받고, 해당 지식을 토대로 추가 개발 주기를 계획합니다.

사용 현황 및 성능을 모니터링하려면 라이브 응용 프로그램에서 원격 분석을 얻고 사용자 자체의 의견을 받는 것이 중요합니다.

많은 시스템은 사용자의 브라우저에서 또는 휴대폰이나 기타 장치에서 앱으로 실행되는 클라이언트 소프트웨어, 웹 서비스, 백 엔드 프로세서 또는 데이터 저장소 등의 여러 구성 요소에서 빌드됩니다. 이러한 여러 다른 구성 요소의 원격 분석이 함께 취합되어야 합니다.

일부 릴리스는 지정된 테스터에게 제한적으로 배포되었습니다. 또한 Flighting(제한된 대상과 새 기능 테스트) 및 A | B 테스트(대체 UI의 병렬 테스트)를 구성했습니다.

여러 클라이언트 및 서버 구성 요소에 대해 배포를 관리하고 모니터링을 통합하는 태스크는 간단하지 않습니다. 이 프로세스는 응용 프로그램 아키텍처의 필수적인 부분으로, 반복적인 개발 주기 및 우수한 모니터링 도구가 없으면 이러한 종류의 시스템을 만들 수 없습니다.

이 문서에서는 devOps 주기의 모니터링 측면이 프로세스의 다른 부분에 어떻게 잘 맞는지를 살펴보겠습니다.

특정 예제에서는 확인하려는 경우 여러 클라이언트 및 서버 구성 요소가 있는 [흥미로운 사례 연구](http://aka.ms/mydrivingdocs)를 참조할 수 있습니다.

## DevOps 주기

Visual Studio 및 개발자 분석 도구는 잘 통합된 devOps 환경을 제공합니다. 예를 들어 다음은 웹 응용 프로그램(Java, Node.js 또는 ASP.NET)에 대한 일반적인 주기입니다.

![웹앱 DevOps 주기](./media/app-insights-developer-analytics/040.png)

* 개발자는 코드 리포지토리를 확인하거나 주요 분기에 병합합니다. 이 그림에서 리포지토리는 Git이지만 [Team Foundation 버전 제어](https://www.visualstudio.com/docs/tfvc/overview)와 같을 수 있습니다.
* 이러한 변경은 빌드 및 단위 테스트를 트리거합니다. 빌드 서비스는 [Visual Studio Team Services 또는 온-프레미스의 상대적 항목인 Team Foundation Server](https://www.visualstudio.com/docs/vsts-tfs-overview)에 있을 수 있습니다.
* 빌드 및 단위 테스트가 성공하면 [자동 배포가 트리거](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)될 수 있습니다. 웹앱 호스트는 자체 웹 서버 또는 Microsoft Azure일 수 있습니다.
* 라이브 앱의 원격 분석이 서버 및 [클라이언트 브라우저 둘 다에서](app-insights-javascript.md) [Application Insights](app-insights-overview.md)로 전송됩니다. 여기서 앱의 성능과 사용 패턴을 분석할 수 있습니다. 강력한 [검색 도구](app-insights-analytics.md)는 문제를 진단하는 데 도움이 됩니다. [경고](app-insights-alerts.md)는 문제가 발생하는 즉시 알림을 표시합니다.
* 다음 개발 주기는 라이브 원격 분석 결과를 분석하면 알 수 있습니다.

### 장치 및 데스크톱 앱

장치 및 데스크톱 앱의 경우, 하나 또는 두 개의 서버로만 업로드하는 것이 아니므로 주기의 배포 부분이 약간 다릅니다. 대신 빌드 및 단위 테스트가 정상적으로 수행되면 [HockeyApp으로의 업로드가 트리거](https://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs)될 수 있습니다. HockeyApp은 테스트 사용자 팀(또는 원할 경우 일반 대중)으로의 배포를 감독합니다.


![장치 DevOps 주기](./media/app-insights-developer-analytics/030.png)

또한 HockeyApp은 다음 형태로 성능 및 사용 현황 데이터를 수집합니다.

* 스크린샷을 포함하는 정확한 사용자 피드백
* 충돌 보고서
* 사용자가 코딩한 사용자 지정 원격 분석

즉, devOps 주기는 획득한 피드백을 고려해서 향후 개발 계획을 세울 때 완료됩니다.


## 개발자 분석 설정

응용 프로그램의 각 구성 요소에서(모바일, 웹 또는 데스크톱) 단계는 기본적으로 동일합니다. 많은 유형의 앱에 대해 Visual Studio는 이러한 단계 중 일부를 자동으로 수행합니다.

1. 앱에 적합한 SDK를 추가합니다. 장치 앱의 경우는 HockeyApp이고, 웹 서비스의 경우는 Application Insights입니다. 각각은 플랫폼마다 여러 변형 버전으로 사용됩니다. 데스크톱 앱의 경우 HockeyApp이 권장되지만 SDK를 사용할 수도 있습니다.
2. 사용한 SDK에 따라 앱을 Application Insights 또는 HockeyApp 포털에 등록합니다. 여기서 라이브 앱의 분석을 확인할 수 있습니다. SDK에서 원격 분석을 전송할 위치를 알 수 있도록 구성하는 계측 키 또는 ID를 앱으로 가져옵니다.
3. 원할 경우 로그 이벤트 또는 메트릭에 사용자 지정 코드를 추가하여 성능 또는 사용 현황의 진단이나 분석에 도움을 줄 수 있습니다. 많은 모니터링 기능이 기본적으로 제공되어 있으므로 첫 번째 주기에서는 필요하지 않습니다.
3. 장치 앱:
 * HockeyApp에 디버그 빌드를 업로드합니다. 여기에서 테스트 사용자의 팀에 배포할 수 있습니다. 후속 빌드를 업로드할 때마다 팀에 알림이 제공됩니다.
 * 연속 빌드 서비스를 설정할 때 플러그 인 단계를 통해 HockeyApp에 업로드하는 릴리스 정의를 만듭니다.

### HockeyApp 원격 분석의 분석 및 내보내기

[브리지를 설정](app-insights-hockeyapp-bridge-app.md)하여 Application Insights의 분석 및 연속 내보내기 기능을 사용하는 HockeyApp 사용자 지정 및 로그 원격 분석을 조사할 수 있습니다.



## 다음 단계
 
다음은 다양한 유형의 앱에 대한 자세한 지침입니다.

* [ASP.NET 웹앱](app-insights-asp-net.md)
* [Java 웹앱](app-insights-java-get-started.md)
* [Node.js 웹앱](https://github.com/Microsoft/ApplicationInsights-node.js)
* [iOS 앱](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Mac OS X 앱](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Android 앱](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [유니버설 Windows 앱](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Windows Phone 8 및 8.1 앱](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows Presentation Foundation 앱](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

<!---HONumber=AcomDC_0907_2016-->