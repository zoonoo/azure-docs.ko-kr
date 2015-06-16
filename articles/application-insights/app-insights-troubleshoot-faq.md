<properties 
	pageTitle="Application Insights에 대한 문제 해결 및 질문과 대답" 
	description="명확하지 않은 부분이 있거나 작동이 잘 안 되는 경우 여기를 참조하세요." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-02-03" 
	ms.author="awills"/>
 
# 문제 해결과 질문 및 답변 - Microsoft Azure 미리 보기의 Application Insights

+ [Visual Studio의 내 프로젝트에 Application Insights를 추가하는 옵션이 표시되지 않습니다.](#q01)
+ [내 새 웹 프로젝트는 만들었는데 Application Insights를 추가하지 못했습니다.](#q02)
+ [Application Insights를 추가하고 내 앱을 실행했는데 포털에 데이터가 표시되지 않습니다.](#q03)
+ [사용 현황 분석에 데이터가 표시되지 않습니다.](#q04)
+ [Microsoft Azure 미리 보기 시작 보드를 살펴보고 있습니다. Application Insights에서 내 데이터를 어떻게 찾나요?](#q05)
+ [내 데이터가 표시되는 Azure 리소스를 변경하려면 어떻게 해야 하나요?](#update)
+ ["계측 키는 비워 둘 수 없습니다." 오류가 발생합니다.](#emptykey)
+ [Microsoft Azure 미리 보기 홈 화면에서 해당 맵이 내 응용 프로그램 상태를 보여 주나요?](#q06)
+ [내 응용 프로그램에 Application Insights 추가를 사용하고 Application Insights 포털을 열 경우 스크린샷과 완전히 다르게 보입니다.](#q07)
+ [Application Insights를 사용하여 인트라넷 웹 서버를 모니터링할 수 있나요?](#q08)
+ [Windows Phone 또는 Windows 스토어의 데이터를 어떻게 가져올 수 있나요?](#q09)
+ [내 코드에 로그인한 이벤트와 페이지 보기를 어떻게 볼 수 있나요?](#q10)
+ [Application Insights가 두 가지 버전으로 제공되는 이유는 무엇인가요?](#q11)
+ [Visual Studio Online 버전의 Application Insights에 포함된 기능을 어떻게 다시 사용할 수 있나요?](#q13)
+ [Application Insights에서 내 프로젝트를 어떻게 수정하나요?](#q14)
+ [Application Insights의 결과를 어떻게 찾을 수 있나요?](#q15)
+ [방화벽 내에서 어떤 포트를 열어야 하나요?](#q16)
+ [Application Insights의 모든 기능을 사용하도록 설정했나요?](#q17)
+ [자세한 정보](#next)



## <a name="q01"></a>Visual Studio의 내 프로젝트에 Application Insights를 추가하는 옵션이 표시되지 않습니다.

+ [Visual Studio 업데이트 3](http://go.microsoft.com/fwlink/?LinkId=397827)이 설치되어 있는지 확인하세요. 이 업데이트는 Application Insights 도구가 사전 설치되어 제공되므로 확장 관리자에서 Application Insights를 확인할 수 있습니다.
+ 현재 Microsoft Azure 미리 보기의 Application Insights는 C# 또는 Visual Basic의 ASP.NET 웹 프로젝트에서만 사용할 수 있습니다.
+ 기존 프로젝트가 있는 경우 솔루션 탐색기로 이동하여 웹 프로젝트(다른 프로젝트 또는 솔루션이 아님)를 클릭하세요. 그러면 '프로젝트에 Application Insights 원격 분석 추가' 메뉴 항목이 표시됩니다.
+ 새 프로젝트를 만들고 있는 경우 Visual Studio에서 파일 > 새 프로젝트를 열고 {Visual C#|Visual Basic} > 웹 > ASP.NET 웹 응용 프로그램을 선택합니다. 그러면 프로젝트에 Application Insights를 추가하는 옵션이 표시됩니다.

## <a name="q02"></a>내 새 웹 프로젝트는 만들었는데 Application Insights를 추가하지 못했습니다.

이런 상황은 Application Insights 포털과의 통신에 실패한 경우 또는 계정에 어떤 문제가 있는 경우에 발생할 수 있습니다.

+ 올바른 Azure 계정의 로그인 자격 증명을 제공했는지 확인하세요. 새 프로젝트 대화 상자에 표시되는 Microsoft Azure 자격 증명은 Visual Studio의 오른쪽 위에 표시되는 Visual Studio Online 자격 증명과 다를 수 있습니다.
+ 잠시 기다린 후 [기존 프로젝트에 Application Insights를 추가][start]합니다.
+ Microsoft Azure 계정 설정으로 이동하여 제한을 확인합니다. Application Insights 응용 프로그램을 수동으로 추가할 수 있는지 확인합니다.


## <a name="q03"></a>Application Insights를 추가하고 내 앱을 실행했는데 포털에 데이터가 표시되지 않습니다.

+ 데이터를 기다리고 있는 모든 블레이드를 닫은 후 열어야 합니다. 현재 버전에서는 블레이드의 콘텐츠에 대한 새로 고침이 자동으로 수행되지 않습니다.
+ Microsoft Azure 시작 보드에서 서비스 상태 맵을 살펴보세요. 어떤 경고 표시가 있는 경우 정상으로 돌아갈 때까지 기다린 후 Application Insights 응용 프로그램 블레이드를 닫고 다시 엽니다.
+ 방화벽에서 dc.services.visualstudio.com 및 f5.services.visualstudio.com으로 나가는 트래픽에 대해 TCP 포트 80 및 443을 열어야 할 수 있습니다.

## <a name="q04"></a>사용 현황 분석에 데이터가 표시되지 않습니다.

+ 데이터는 웹 페이지의 스크립트에서 비롯됩니다. 기존 웹 프로젝트에 Application Insights를 추가한 경우 [스크립트를 직접 추가해야 합니다][start].


## <a name="q05"></a>Microsoft Azure 미리 보기 시작 보드를 살펴보고 있습니다. Application Insights에서 내 데이터를 어떻게 찾나요?

다음 중 한 방법으로 찾을 수 있습니다.

* "찾아보기, Application Insights, 프로젝트 이름"을 선택합니다. 선택할 프로젝트가 없는 경우 [Visual Studio에서 웹 프로젝트에 Application Insights를 추가][start]해야 합니다.

* Visual Studio 솔루션 탐색기에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 "Application Insights 포털 열기"를 선택합니다.

## <a name="update"></a>내 프로젝트에서 데이터를 전송하는 Azure 리소스를 변경하려면 어떻게 해야 하나요?

솔루션 탐색기에서 `ApplicationInsights.config`를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. Azure에서 기존 또는 새 리소스로 데이터를 보낼 수 있습니다. 업데이트 마법사는 서버 SDK에서 데이터를 전송하는 위치를 결정하는 ApplicationInsights.config의 계측 키를 변경합니다. "모두 업데이트"를 선택 취소하지 않은 한, 웹 페이지에 표시되는 키도 변경됩니다.

## <a name="emptykey"></a>"계측 키는 비워 둘 수 없습니다." 오류가 발생합니다.

Application Insights를 설치하는 동안 문제가 발생했거나 로깅 어댑터에 문제가 있을 수 있습니다.

솔루션 탐색기에서 `ApplicationInsights.config`를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. Azure에 로그인하고 Application Insights 리소스를 만들거나 기존 리소스를 다시 사용하도록 초대하는 대화 상자가 표시됩니다.

## <a name="q06"></a>Microsoft Azure 미리 보기 홈 화면에서 해당 맵이 내 응용 프로그램 상태를 보여 주나요?

아니요! Azure 서비스의 상태를 보여 줍니다. 웹 테스트 결과를 확인하려면 찾아보기 > Application Insights > (응용 프로그램)을 선택한 후 웹 테스트 결과를 살펴봅니다. 


## <a name="q07"></a>내 응용 프로그램에 Application Insights 추가를 사용하고 Application Insights 포털을 열 경우 스크린샷과 완전히 다르게 보입니다.

Visual Studio Online 버전에 연결되는 [이전 버전의 Application Insights 도구](http://msdn.microsoft.com/library/dn793604.aspx)를 사용하고 있을 가능성이 있습니다.

스크린샷의 도움말 페이지는 Visual Studio 업데이트 3에서 이미 전환되어 제공되는 [Microsoft Azure 미리 보기용 Application Insights][start]입니다. 

## <a name="q08"></a>Application Insights를 사용하여 인트라넷 웹 서버를 모니터링할 수 있나요?

예, 서버가 공용 인터넷에 데이터를 보낼 수 있는 경우 상태 및 사용량을 모니터링할 수 있습니다.

하지만 서비스에 대해 웹 테스트를 실행하려는 경우 공용 인터넷에서 액세스할 수 있어야 합니다.

## <a name="q09"></a>Windows Phone 또는 Windows 스토어의 데이터를 어떻게 가져올 수 있나요?

이 Microsoft Azure 버전에서는 아직 지원되지 않습니다. [Visual Studio Online 버전][older]을 사용하세요.


## <a name="q10"></a>내 코드에 로그인한 이벤트와 페이지 보기를 어떻게 볼 수 있나요?

Microsoft Azure 버전에서는 아직 해당 기능을 지원하지 않습니다. 그러나 서비스 예정입니다. 당분간은 [이전 버전][older]에서 이용할 수 있습니다.


## <a name="q11"></a>Application Insights가 두 가지 버전으로 제공되는 이유는 무엇인가요?

이전 포털은 Visual Studio Online의 구성 요소입니다. 해당 버전을 더 이상 크게 변경하지 않을 계획입니다. 이전 버전의 Visual Studio용 Application Insights 도구가 있는 경우 Visual Studio Online 포털에 연결할 수 있습니다.

Visual Studio 업데이트 3은 새로운 Application Insights 도구의 사전 설치된 버전과 함께 제공됩니다. 이 버전은 Microsoft Azure 미리 보기의 구성 요소인 새로운 Application Insights 포털에 연결할 수 있습니다. Microsoft는 현재 Application Insights를 이 새로운 환경으로 이식하고 있습니다. 이 작업은 아직 완료되지 않았습니다.

## <a name="q13"></a>Visual Studio Online 버전의 Application Insights에 포함된 멋진 기능을 어떻게 다시 사용할 수 있나요?

1. Visual Studio의 확장 관리자로 이동합니다. 
2. Application Insights 도구를 제거합니다.
3. [이전 버전의 도구 설치 관리자](http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a)를 실행하고 해당 [시작 가이드][older]를 읽어보세요.

## <a name="q14"></a>Application Insights에서 내 프로젝트를 어떻게 수정하나요?

세부 정보는 프로젝트의 유형에 따라 달라집니다. 웹 응용 프로그램의 경우:+


+ 프로젝트에 다음 파일이 추가됩니다.

 + ApplicationInsights.config 
 + ai.js


+ 다음 NuGet 패키지가 설치됩니다.

 -  *Application Insights API* - 핵심 API

 -  *웹 응용 프로그램용 Application Insights API* - 서버에서 원격 분석을 보내는 데 사용

 -  *JavaScript 응용 프로그램용 Application Insights API* - 클라이언트에서 원격 분석을 보내는 데 사용

    패키지에는 다음 어셈블리가 포함됩니다.

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ 항목 삽입 위치:

 - Web.config

 - packages.config

+ (새 프로젝트에만 해당 - [기존 프로젝트에 Application Insights를 추가][start]한 경우 이를 수동으로 수행해야 합니다.) 클라이언트 및 서버 코드에 코드 조각을 삽입하여 Application Insights 리소스 ID로 해당 코드를 초기화합니다. 예를 들어 MVC 앱에서 코드를 마스터 페이지 Views/Shared/_Layout.cshtml에 삽입합니다.


## <a name="q15"></a>Application Insights의 결과를 어떻게 찾을 수 있나요?
1. 다음과 같이 Microsoft Azure를 엽니다.
 - Visual Studio에서 웹 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Azure 미리 보기 포털 열기**를 선택합니다.
 - 또는 웹 브라우저에서 Microsoft Azure 미리 보기의 계정을 열 수 있습니다.

2. "찾아보기, Application Insights"를 선택한 후 프로젝트를 선택합니다.

## <a name="q16"></a>내 서버 또는 개발 컴퓨터와 공용 인터넷 사이에 방화벽이 있습니다. Application Insights를 사용하도록 허용해야 트래픽 종류

성능 및 사용 현황 데이터는 dc.services.visualstudio.com 및 f5.services.visualstudio.com에서 TCP 포트 80 및 443으로 전송됩니다.

웹 가용성 테스트는 포트 80에서 웹 서버에 대한 들어오는 액세스 권한에 따라 좌우됩니다.

## <a name="q17"></a> Application Insights의 모든 기능을 사용하도록 설정했나요?

<table border="1">
<tr><th>표시되는 내용</th><th>기능을 가져오는 방법</th><th>해당 기능을 원하는 이유</th></tr>
<tr><td>가용성 차트</td><td><a href="../app-insights-monitor-web-app-availability/">웹 테스트</a></td><td>웹 앱이 작동 중인지 확인</td></tr>
<tr><td>서버 앱 성능: 응답 시간, ...
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">프로젝트에 Application Insights 추가</a><br/>또는 <br/><a href="../app-insights-monitor-performance-live-website-now/">서버에 AI 상태 모니터 설치</a></td><td>성능 문제 검색</td></tr>
<!-- ####future#### <tr><td>종속성 원격 분석</td><td><a href="../app-insights-monitor-performance-live-website-now/">서버에 AI 상태 모니터 설치</a></td><td>데이터베이스 또는 다른 외부 구성 요소와 관련한 문제 진단</td></tr> -->
<!-- #####74.1#### <tr><td>서버 전체: CPU, 메모리, ...</td><td><a href="../app-insights-monitor-performance-live-website-now/">서버에 AI 상태 모니터 설치</a></td><td>용량 문제 진단</td></tr> --> 
<tr><td>검색 로그 추적</td><td><a href="../app-insights-search-diagnostic-logs/">로깅 어댑터 추가</a></td><td>예외, 성능 문제 진단</td></tr>
<tr><td>클라이언트 사용 현황 기본 사항: 페이지 뷰, 복귀, ...</td><td><a href="../app-insights-start-monitoring-app-health-usage/#webclient">웹 페이지의 JavaScript 이니셜라이저</a></td><td>사용 현황 분석</td></tr>
<tr><td>클라이언트 사용자 지정 메트릭</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">웹 페이지의 호출 추적</a></td><td>사용자 환경 개선</td></tr>
<tr><td>서버 사용자 지정 메트릭</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">서버 코드의 호출 추적</a></td><td>비즈니스 인텔리전스</td></tr>
</table>

웹 서비스를 Azure VM에서 실행하는 경우 이 위치에서도 [진단을 가져올 수 있습니다][azurediagnostic].



[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]




[azurediagnostic]: ../insights-how-to-use-diagnostics/

[older]: http://www.visualstudio.com/get-started/get-usage-data-vs


<!--HONumber=46--> 
 