<properties title="Troubleshooting and Q & A about Application Insights" pageTitle="Troubleshooting and Q & A about Application Insights" description="Tips and troubleshooting" metaKeywords="analytics monitoring" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# 문제 해결과 질문 및 답변 - Microsoft Azure 미리 보기의 Application Insights

+ [Visual Studio의 내 프로젝트에 Application Insights를 추가하는 옵션이 표시되지 않습니다.][Visual Studio의 내 프로젝트에 Application Insights를 추가하는 옵션이 표시되지 않습니다.]
+ [내 새 웹 프로젝트는 만들었는데 Application Insights를 추가하지 못했습니다.][내 새 웹 프로젝트는 만들었는데 Application Insights를 추가하지 못했습니다.]
+ [Application Insights를 추가하고 내 앱을 실행했는데 포털에 데이터가 표시되지 않습니다.][Application Insights를 추가하고 내 앱을 실행했는데 포털에 데이터가 표시되지 않습니다.]
+ [사용 현황 분석에 데이터가 표시되지 않습니다.][사용 현황 분석에 데이터가 표시되지 않습니다.]
+ [Microsoft Azure 미리 보기 시작 보드를 살펴보고 있습니다. Application Insights에서 내 데이터를 어떻게 찾나요?][Microsoft Azure 미리 보기 시작 보드를 살펴보고 있습니다. Application Insights에서 내 데이터를 어떻게 찾나요?]
+ [Microsoft Azure 미리 보기 홈 화면에서 해당 맵이 내 응용 프로그램 상태를 보여 주나요?][Microsoft Azure 미리 보기 홈 화면에서 해당 맵이 내 응용 프로그램 상태를 보여 주나요?]
+ [내 응용 프로그램에 Application Insights 추가를 사용하고 Application Insights 포털을 열 경우 스크린샷과 완전히 다르게 보입니다.][내 응용 프로그램에 Application Insights 추가를 사용하고 Application Insights 포털을 열 경우 스크린샷과 완전히 다르게 보입니다.]
+ [Application Insights를 사용하여 인트라넷 웹 서버를 모니터링할 수 있나요?][Application Insights를 사용하여 인트라넷 웹 서버를 모니터링할 수 있나요?]
+ [Windows Phone 또는 Windows 스토어의 데이터를 어떻게 가져올 수 있나요?][Windows Phone 또는 Windows 스토어의 데이터를 어떻게 가져올 수 있나요?]
+ [내 코드에 로그인한 이벤트와 페이지 보기를 어떻게 볼 수 있나요?][내 코드에 로그인한 이벤트와 페이지 보기를 어떻게 볼 수 있나요?]
+ [Application Insights가 두 가지 버전으로 제공되는 이유는 무엇인가요?][Application Insights가 두 가지 버전으로 제공되는 이유는 무엇인가요?]
+ [현재 Azure 버전의 Application Insights에는 무엇이 빠져 있나요?][현재 Azure 버전의 Application Insights에는 무엇이 빠져 있나요?]
+ [Visual Studio Online 버전의 Application Insights에 포함된 멋진 기능을 어떻게 다시 사용할 수 있나요?][Visual Studio Online 버전의 Application Insights에 포함된 멋진 기능을 어떻게 다시 사용할 수 있나요?]
+ [Application Insights에서 내 프로젝트를 어떻게 수정합니까?][Application Insights에서 내 프로젝트를 어떻게 수정합니까?]
+ [Application Insights의 결과를 어떻게 찾을 수 있나요?][Application Insights의 결과를 어떻게 찾을 수 있나요?]
+ [다음 단계][다음 단계]



## <a name="q01"></a> Visual Studio의 내 프로젝트에 Application Insights를 추가하는 옵션이 표시되지 않습니다.

+ [Visual Studio 업데이트 3][Visual Studio 업데이트 3]이 설치되어 있는지 확인하세요. 이 업데이트는 Application Insights 도구가 사전 설치되어 제공되므로 확장 관리자에서 Application Insights를 확인할 수 있습니다.
+ 현재 Microsoft Azure 미리 보기의 Application Insights는 C\# 또는 Visual Basic의 ASP.NET 웹 프로젝트에서만 사용할 수 있습니다.
+ 기존 프로젝트가 있는 경우 솔루션 탐색기로 이동하여 웹 프로젝트(다른 프로젝트 또는 솔루션이 아님)를 클릭하세요. 그러면 '프로젝트에 Application Insights 원격 분석 추가' 메뉴 항목이 표시됩니다.
+ 새 프로젝트를 만들고 있는 경우 Visual Studio에서 파일 \> 새 프로젝트를 열고 {Visual C\#|Visual Basic} \> 웹 \> ASP.NET 웹 응용 프로그램을 선택합니다. 그러면 프로젝트에 Application Insights를 추가하는 옵션이 표시됩니다.

## <a name="q02"></a> 내 새 웹 프로젝트는 만들었는데 Application Insights를 추가하지 못했습니다.

이런 상황은 Application Insights 포털과의 통신에 실패한 경우 또는 계정에 어떤 문제가 있는 경우에 발생할 수 있습니다.

+ 올바른 Azure 계정의 로그인 자격 증명을 제공했는지 확인하세요. 새 프로젝트 대화 상자에 표시되는 Microsoft Azure 자격 증명은 Visual Studio의 오른쪽 위에 표시되는 Visual Studio Online 자격 증명과 다를 수 있습니다.
+ 잠시 기다린 후 [기존 프로젝트에 Application Insights를 추가][기존 프로젝트에 Application Insights를 추가]합니다.
+ Microsoft Azure 계정 설정으로 이동하여 제한을 확인합니다. Application Insights 응용 프로그램을 수동으로 추가할 수 있는지 확인합니다.


## <a name="q03"></a> Application Insights를 추가하고 내 앱을 실행했는데 포털에 데이터가 표시되지 않습니다.

+ 데이터를 기다리고 있는 모든 블레이드를 닫은 후 열어야 합니다. 현재 버전에서는 블레이드의 콘텐츠에 대한 새로 고침이 자동으로 수행되지 않습니다.
+ Microsoft Azure 시작 보드에서 서비스 상태 맵을 살펴보세요. 어떤 경고 표시가 있는 경우 정상으로 돌아갈 때까지 기다린 후 Application Insights 응용 프로그램 블레이드를 닫고 다시 엽니다.
+ 웹 서버의 방화벽에서 나가는 트래픽을 위해 포트 443을 열어야 할 수도 있습니다.

## <a name="q04"></a> 사용 현황 분석에 데이터가 표시되지 않습니다.

+ 데이터는 웹 페이지의 스크립트에서 비롯됩니다. 기존 웹 프로젝트에 Application Insights를 추가한 경우 [스크립트를 직접 추가해야 합니다][기존 프로젝트에 Application Insights를 추가].


## <a name="q05"></a> Microsoft Azure 미리 보기 시작 보드를 살펴보고 있습니다. Application Insights에서 내 데이터를 어떻게 찾나요?

다음 중 한 방법으로 찾을 수 있습니다.

* "찾아보기, Application Insights, 프로젝트 이름"을 선택합니다. 선택할 프로젝트가 없는 경우 [Visual Studio에서 웹 프로젝트에 Application Insights를 추가][기존 프로젝트에 Application Insights를 추가]해야 합니다.

* Visual Studio 솔루션 탐색기에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 "Application Insights 포털 열기"를 선택합니다.

## <a name="q06"></a> Microsoft Azure 미리 보기 홈 화면에서 해당 맵이 내 응용 프로그램 상태를 보여 주나요?

아니요! Azure 서비스의 상태를 보여 줍니다. 웹 테스트 결과를 확인하려면 찾아보기 \> Application Insights \> (응용 프로그램)을 선택한 후 웹 테스트 결과를 살펴봅니다.


## <a name="q07"></a> 내 응용 프로그램에 Application Insights 추가를 사용하고 Application Insights 포털을 열 경우 스크린샷과 완전히 다르게 보입니다.

Visual Studio Online 버전에 연결되는 이전 버전의 Application Insights 도구를 사용하고 있을 가능성이 있습니다.

스크린샷의 도움말 페이지는 Visual Studio 업데이트 3에서 이미 전환되어 제공되는 Microsoft Azure 미리 보기용 Application Insights입니다.

## <a name="q08"></a> Application Insights를 사용하여 인트라넷 웹 서버를 모니터링할 수 있나요?

예, 서버가 공용 인터넷에 데이터를 보낼 수 있는 경우 상태 및 사용량을 모니터링할 수 있습니다.

하지만 서비스에 대해 웹 테스트를 실행하려는 경우 공용 인터넷에서 액세스할 수 있어야 합니다.


## <a name="q10"></a> 내 코드에 로그인한 이벤트와 페이지 보기를 어떻게 볼 수 있나요?

Microsoft Azure 버전에서는 아직 해당 기능을 지원하지 않습니다. 그러나 서비스 예정입니다. 당분간은 [이전 버전][이전 버전](영문)에서 이용할 수 있습니다.


## <a name="q11"></a> Application Insights가 두 가지 버전으로 제공되는 이유는 무엇인가요?

이전 포털은 Visual Studio Online의 구성 요소입니다. 해당 버전을 더 이상 크게 변경하지 않을 계획입니다. 이전 버전의 Visual Studio용 Application Insights 도구가 있는 경우 Visual Studio Online 포털에 연결할 수 있습니다.

Visual Studio 업데이트 3은 새로운 Application Insights 도구의 사전 설치된 버전과 함께 제공됩니다. 이 버전은 Microsoft Azure 미리 보기의 구성 요소인 새로운 Application Insights 포털에 연결할 수 있습니다. Microsoft는 현재 Application Insights를 이 새로운 환경으로 이식하고 있습니다. 작업이 아직 완료되지 않아 몇 가지 제한 사항이 있습니다.

## <a name="q12"></a> 현재 Azure 버전의 Application Insights에는 무엇이 빠져 있나요?

많은 사항이 현재 진행 중입니다.

그러나 현재 빠져 있는 주요 기능으로는 Windows Phone 및 Windows 스토어와 같은 장치 앱에 대한 지원, `trackEvent()` 및 `trackPageView()`와 같은 사용자 지정 원격 분석 보고서 기능이 있습니다.

## <a name="q13"></a> Visual Studio Online 버전의 Application Insights에 포함된 멋진 기능을 어떻게 다시 사용할 수 있나요?

1. Visual Studio의 확장 관리자로 이동합니다.
2. Application Insights 도구를 제거합니다.
3. [이전 버전의 도구 설치 관리자][이전 버전의 도구 설치 관리자](영문)를 실행하고 해당 [시작 가이드][이전 버전](영문)를 읽어보세요.

## <a name="q14"></a> Application Insights에서 내 프로젝트를 어떻게 수정합니까?

+ 프로젝트에 다음 파일이 추가됩니다.

 + ApplicationInsights.config
 + ai.js


+ 다음 NuGet 패키지가 설치됩니다.

 - *Application Insights API* - 핵심 API

 - *웹 응용 프로그램용 Application Insights API* - 서버에서 원격 분석을 보내는 데 사용

 - *JavaScript 응용 프로그램용 Application Insights API* - 클라이언트에서 원격 분석을 보내는 데 사용

    패키지에는 다음 어셈블리가 포함됩니다.

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ (새 프로젝트에만 해당 - [기존 프로젝트에 Application Insights를 추가][기존 프로젝트에 Application Insights를 추가]한 경우 이를 수동으로 수행해야 합니다.) 클라이언트 및 서버 코드에 코드 조각을 삽입하여 Application Insights 리소스 ID로 해당 코드를 초기화합니다. 예를 들어 MVC 앱에서 코드를 다음에 삽입합니다.

 - 마스터 페이지 Views/Shared/_Layout.cshtml

 - Web.config

 - packages.config

## <a name="q15"></a> Application Insights의 결과를 어떻게 찾을 수 있나요?
1. 다음과 같이 Microsoft Azure를 엽니다.
 - Visual Studio에서 웹 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Azure 미리 보기 포털 열기**를 선택합니다.
 - 또는 웹 브라우저에서 Microsoft Azure 미리 보기의 계정을 열 수 있습니다.

1. "찾아보기, Application Insights"를 선택한 후 프로젝트를 선택합니다.

## <a name="next"></a> 자세한 정보

* [Application Insights][Application Insights]
* [프로젝트에 Application Insights 추가][기존 프로젝트에 Application Insights를 추가]
* [현재 라이브 웹 서버 모니터링][현재 라이브 웹 서버 모니터링]
* [Application Insights의 메트릭 탐색][Application Insights의 메트릭 탐색]
* [진단 로그 검색][진단 로그 검색]
* [웹 테스트로 가용성 추적][웹 테스트로 가용성 추적]
* [이벤트 및 메트릭으로 사용량 추적][이벤트 및 메트릭으로 사용량 추적]
* [질문 및 답변과 문제 해결][질문 및 답변과 문제 해결]



<!--Link references-->

[Visual Studio의 내 프로젝트에 Application Insights를 추가하는 옵션이 표시되지 않습니다.]: #q01
[내 새 웹 프로젝트는 만들었는데 Application Insights를 추가하지 못했습니다.]: #q02
[Application Insights를 추가하고 내 앱을 실행했는데 포털에 데이터가 표시되지 않습니다.]: #q03
[사용 현황 분석에 데이터가 표시되지 않습니다.]: #q04
[Microsoft Azure 미리 보기 시작 보드를 살펴보고 있습니다. Application Insights에서 내 데이터를 어떻게 찾나요?]: #q05
[Microsoft Azure 미리 보기 홈 화면에서 해당 맵이 내 응용 프로그램 상태를 보여 주나요?]: #q06
[내 응용 프로그램에 Application Insights 추가를 사용하고 Application Insights 포털을 열 경우 스크린샷과 완전히 다르게 보입니다.]: #q07
[Application Insights를 사용하여 인트라넷 웹 서버를 모니터링할 수 있나요?]: #q08
[Windows Phone 또는 Windows 스토어의 데이터를 어떻게 가져올 수 있나요?]: #q09
[내 코드에 로그인한 이벤트와 페이지 보기를 어떻게 볼 수 있나요?]: #q10
[Application Insights가 두 가지 버전으로 제공되는 이유는 무엇인가요?]: #q11
[현재 Azure 버전의 Application Insights에는 무엇이 빠져 있나요?]: #q12
[Visual Studio Online 버전의 Application Insights에 포함된 멋진 기능을 어떻게 다시 사용할 수 있나요?]: #q13
[Application Insights에서 내 프로젝트를 어떻게 수정합니까?]: #q14
[Application Insights의 결과를 어떻게 찾을 수 있나요?]: #q15
[다음 단계]: #next
[Visual Studio 업데이트 3]: http://go.microsoft.com/fwlink/?LinkId=397827
[기존 프로젝트에 Application Insights를 추가]: ../app-insights-monitor-application-health-usage/
[이전 버전]: http://www.visualstudio.com/get-started/get-usage-data-vs
[이전 버전의 도구 설치 관리자]: http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a
[Application Insights]: ../app-insights-get-started/
[현재 라이브 웹 서버 모니터링]: ../app-insights-monitor-performance-live-website-now/
[Application Insights의 메트릭 탐색]: ../app-insights-explore-metrics/
[진단 로그 검색]: ../app-insights-search-diagnostic-logs/
[웹 테스트로 가용성 추적]: ../app-insights-monitor-web-app-availability/
[이벤트 및 메트릭으로 사용량 추적]: ../app-insights-track-usage-custom-events-metrics/
[질문 및 답변과 문제 해결]: ../app-insights-troubleshoot-faq/
