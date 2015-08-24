<properties 
	pageTitle="Application Insights에 대한 문제 해결 및 질문" 
	description="Visual Studio Application Insights에서 무엇인가 명확하지 않거나 작동하지 않나요? 여기를 참조하세요." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="awills"/>
 
# ASP.NET용 Application Insights 문제 해결 및 질문

## Application Insights와 같이 사용할 수 있나요...?

[플랫폼 참조][platforms]

## 무료입니까?

* 예, 무료 [가격 책정 계층](app-insights-pricing.md)을 선택하는 경우. 대부분의 기능 및 많은 데이터 할당량을 받습니다. 
* Microsoft Azure에 등록하려면 사용자의 신용 카드 데이터를 제공해야 하지만 다른 유료 Azure 서비스를 사용하거나 명시적으로 유료 계층으로 업그레이드하지 않는 한 요금이 부과되지 않습니다.
* 사용자의 앱을 무료 계층에 대해 월간 할당량보다 더 많이 보내면 기록이 중지됩니다. 이 경우 지불 시작을 선택하거나 월말에 할당량이 다시 설정될 때까지 기다릴 수 있습니다.
* 기본 사용량 및 세션 데이터에는 할당량이 적용되지 않습니다.
* 무료로 프리미엄 기능을 받는 무료 30일 평가판도 있습니다.
* 각 응용 프로그램 리소스에는 별도 할당량이 있으며 해당 가격 책정 계층을 다른 리소스와 독립적으로 설정합니다.

#### 지불을 할 경우 받는 혜택은 무엇입니까?

* 더 큰 [월간 데이터 할당량](http://azure.microsoft.com/pricing/details/application-insights/).
* 월간 할당량을 초과해도 데이터를 계속 수집하고 '초과분'에 대해 요금을 지불하는 옵션입니다. 사용자의 데이터가 할당량을 초과하면 Mb당 요금이 청구됩니다.
* [연속 내보내기](app-insights-export-telemetry.md).

## SDK 추가

#### <a name="q01"></a>Visual Studio의 내 프로젝트에 Application Insights를 추가하는 옵션이 표시되지 않습니다.

+ [Visual Studio 2013 업데이트 3 이후](http://go.microsoft.com/fwlink/?LinkId=397827)가 설치되어 있는지 확인하십시오. Application Insights 도구와 함께 사전설치되어 제공됩니다.
+ 도구는 모든 유형의 응용 프로그램을 지원하지 않지만 수동으로 프로젝트에 Application Insights SDK를 추가할 수 있습니다. [이 절차][windows]를 사용합니다. 


#### <a name="q02"></a>새 웹 프로젝트는 만들었는데 Application Insights를 추가하지 못했습니다.

이 경우에 발생할 수 있습니다.

* Application Insights 포털과의 통신이 실패했거나 또는
* 계정에 일부 문제가 있습니다.
* [새 리소스를 만들려고 하던 구독 또는 그룹에 대한 읽기 액세스](app-insights-resources-roles-access-control.md) 권한만이 있습니다.

해결책:

+ 올바른 Azure 계정의 로그인 자격 증명을 제공했는지 확인하세요. 이전 버전의 도구에서, 새 프로젝트 대화 상자에 표시되는 Microsoft Azure 자격 증명은 Visual Studio의 오른쪽 위에 표시되는 Visual Studio Online 자격 증명과 다를 수 있습니다.
+ 브라우저에서 [Azure 포털](https://portal.azure.com)에 대한 액세스 권한이 있는지 확인합니다. 설정을 열고 제한이 있는지 확인합니다.
+ [Application Insights를 기존 프로젝트에 추가][start]\: 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 "Application Insights 추가"를 선택합니다.
+ 여전히 작동하지 않는 경우 [수동 절차](app-insights-start-monitoring-app-health-usage.md)에 따라 포털에서 리소스에 추가한 다음 SDK를 프로젝트에 추가합니다. 

#### <a name="emptykey"></a>"계측 키는 비워 둘 수 없습니다." 오류가 발생합니다.

Application Insights를 설치하는 동안 문제가 발생했거나 로깅 어댑터에 문제가 있을 수 있습니다.

솔루션 탐색기에서 `ApplicationInsights.config`를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. Azure에 로그인하고 Application Insights 리소스를 만들거나 기존 리소스를 다시 사용하도록 초대하는 대화 상자가 표시됩니다.


#### <a name="q14"></a>Application Insights에서 내 프로젝트를 어떻게 수정하나요?

세부 정보는 프로젝트의 유형에 따라 달라집니다. 웹 응용 프로그램의 경우:


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

+ (새 프로젝트에만 해당 - [기존 프로젝트에 Application Insights를 추가][start]한 경우 이를 수동으로 수행해야 합니다.) 클라이언트 및 서버 코드에 코드 조각을 삽입하여 Application Insights 리소스 ID로 해당 코드를 초기화합니다. 예를 들어 MVC 앱에서 코드를 마스터 페이지 Views/Shared/\_Layout.cshtml에 삽입합니다.

####<a name="NuGetBuild"></a> 개발 컴퓨터에 모든 것이 잘 빌드되었지만 빌드 서버에 "NuGet 패키지가 누락됩니다"가 표시됩니다.

[NuGet 패키지 복원](http://docs.nuget.org/Consume/Package-Restore) 및 [자동 패키지 복원](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore)을 참조하십시오.

####<a name="FailUpdate"></a> NuGet 패키지를 0.17 또는 최신 버전으로 업데이트한 후에 빌드를 시도할 때 "프로젝트가 컴퓨터에서 누락된 NuGet 패키지를 참조합니다"가 표시됩니다.

0\.17 또는 최신 NuGet 패키지를 업데이트한 후에 위의 오류가 표시되면 proj 파일을 편집하고 남아있는 BCL 대상을 제거해야 합니다.

다음을 수행합니다.

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 프로젝트 언로드를 선택합니다.
2. 다시 프로젝트 단추를 클릭하고 *yourProject.csproj* 편집을 선택합니다. 
3. 프로젝트 파일의 아래쪽으로 이동하 고 ```<Import Project="..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets" Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" />와 유사한 BCL 대상을 제거합니다.
	  
	  <Target Name="EnsureBclBuildImported" BeforeTargets="BeforeBuild" Condition="'$(BclBuildImported)' == ''">
	  
	    <Error Condition="!Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=317567." HelpKeyword="BCLBUILD2001" />
	    
	    <Error Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="The build restored NuGet packages. Build the project again to include these packages in the build. For more information, see http://go.microsoft.com/fwlink/?LinkID=317568." HelpKeyword="BCLBUILD2002" />
	    
	</Target> ```
4. 파일을 저장합니다.
5. 다시 프로젝트 단추를 클릭하고 *yourProject.csproj* 다시 로드를 선택합니다.

## 이전 SDK 버전에서 업그레이드하려면 어떻게 해야 합니까?

사용자의 응용 프로그램 유형에 적합한 SDK는 [릴리스 정보](app-insights-release-notes.md)를 참조하세요.


## 데이터 없음

#### <a name="q03"></a>Application Insights를 추가하고 앱을 실행했는데 포털에 데이터가 표시되지 않습니다.

+ 개요 페이지에서 진단 검색을 열려면 검색 타일을 클릭합니다. 데이터가 여기에 먼저 나타납니다.
+ 새로고침 단추를 클릭합니다. 블레이드 자체는 주기적으로 새로 고쳐지지만 수동으로 새로 고칠 수도 있습니다. 시간 범위가 커지면 새로 고침 간격이 길어집니다.
+ Microsoft Azure 시작 보드에서 서비스 상태 맵을 살펴보세요. 어떤 경고 표시가 있는 경우 정상으로 돌아갈 때까지 기다린 후 Application Insights 응용 프로그램 블레이드를 닫고 다시 엽니다.
+ 또한 [상태 블로그](http://blogs.msdn.com/b/applicationinsights-status/)를 확인합니다.
+ 방화벽에서 dc.services.visualstudio.com 및 f5.services.visualstudio.com으로 나가는 트래픽에 대해 TCP 포트 80 및 443을 열어야 할 수 있습니다.
+ 회사 네트워크를 벗어나 보내기 위해 프록시를 사용해야 하는 경우, Web.config에서 [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx)를 설정하십시오.
+ Windows Server 2008: 다음 업데이트를 설치했는지 확인하십시오. [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

#### <a name="q04"></a> 웹 사이트에 대한 사용 현황 분석에 데이터가 표시되지 않습니다.

+ 데이터는 웹 페이지의 스크립트에서 비롯됩니다. 기존 웹 프로젝트에 Application Insights를 추가한 경우 [스크립트를 직접 추가해야 합니다][start].
+ Internet Explorer가 호환성 모드에서 사이트를 표시하고 있는지 확인합니다.
+ 데이터가 dc.services.visualstudio.com로 보내지고 있는지 확인하려면 (네트워크 일부 브라우저에서 F12, 그런 다음 네트워크를 선택합니다) 브라우저의 디버그 기능을 사용합니다.

#### <a name="q08"></a>Application Insights를 사용하여 인트라넷 웹 서버를 모니터링할 수 있나요?

예, 서버가 공용 인터넷에 데이터를 보낼 수 있는 경우 상태 및 사용량을 모니터링할 수 있습니다.

하지만 서비스에 대해 웹 테스트를 실행하려는 경우 포트 80상의 공용 인터넷에서 액세스할 수 있어야 합니다.

#### 공용 인터넷에 액세스할 수 없는 인트라넷 웹 서버를 모니터링할 수 있습니까?

Dc.services.visualstudio.com에 https POST 호출을 릴레이할 수 있는 프록시를 정렬해야 합니다.

#### 데이터를 보는 데 중지되었습니다.

* [상태 블로그](http://blogs.msdn.com/b/applicationinsights-status/)를 참조하세요.
* 데이터 요소의 월간 할당량에 도달했습니까? 설정/할당량 및 가격을 열어 찾아봅니다. 그렇다면 계획을 업그레이드하거나 추가 용량에 대한 비용을 지불할 수 있습니다. [가격 체계](http://azure.microsoft.com/pricing/details/application-insights/)를 참고하십시오.



## 포털

#### <a name="q05"></a>Microsoft Azure 미리 보기 시작 보드를 살펴보고 있습니다. Application Insights에서 내 데이터를 어떻게 찾나요?

다음 중 한 방법으로 찾을 수 있습니다.

* "찾아보기, Application Insights, 프로젝트 이름"을 선택합니다. 선택할 프로젝트가 없는 경우 [Visual Studio에서 웹 프로젝트에 Application Insights를 추가][start]해야 합니다.

* Visual Studio 솔루션 탐색기에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 "Application Insights 포털 열기"를 선택합니다.


#### <a name="update"></a>내 프로젝트에서 데이터를 보내는 Azure 리소스를 변경하려면 어떻게 해야 하나요?

솔루션 탐색기에서 `ApplicationInsights.config`를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. Azure에서 기존 또는 새 리소스로 데이터를 보낼 수 있습니다. 업데이트 마법사는 서버 SDK에서 데이터를 전송하는 위치를 결정하는 ApplicationInsights.config의 계측 키를 변경합니다. "모두 업데이트"를 선택 취소하지 않은 한, 웹 페이지에 표시되는 키도 변경됩니다.


#### <a name="q06"></a>Microsoft Azure 미리 보기 홈 화면에서 해당 맵이 내 응용 프로그램의 상태를 보여 주나요?

아니요! Azure 서비스의 상태를 보여 줍니다. 웹 테스트 결과를 확인하려면 찾아보기 > Application Insights > (응용 프로그램)을 선택한 후 웹 테스트 결과를 살펴봅니다.


#### <a name="q07"></a>내 응용 프로그램에 Application Insights 추가를 사용하고 Application Insights 포털을 열 경우 스크린샷과 완전히 다르게 보입니다.

Visual Studio Online 버전에 연결되는 [이전 버전의 Application Insights SDK](http://msdn.microsoft.com/library/dn793604.aspx)를 사용하고 있을 가능성이 있습니다.

스크린샷의 도움말 페이지는 Visual Studio 2013 업데이트 3 또는 이후에서 이미 전환되어 제공되는 [Microsoft Azure 미리 보기용 Application Insights][start]입니다.

#### <a name="data"></a>데이터가 포털에 얼마나 오래 보존되나요? 안전한가요?

[데이터 보존 및 개인 정보][data]를 살펴보십시오.

## 로깅

#### <a name="post"></a>진단 검색에서 POST 데이터를 어떻게 확인하나요?

POST 데이터를 자동으로 기록 하지 않지만 TrackTrace 호출을 사용할 수 있습니다. 메시지 매개 변수에 데이터를 배치합니다. 이것은 필터링할 수 없지만 문자열 속성에 대한 제한보다 긴 제한이 있습니다.

## 보안

#### 내 데이터는 포털에서 안전하나요? 얼마나 오래 유지되나요?

[데이터 보존 및 개인 정보][data]를 참조하세요.


## <a name="q17"></a>Application Insights의 모든 기능을 사용하도록 어떻게 설정하나요?

<table border="1">
<tr><th>표시 내용</th><th>시작 방법</th><th>원하는 이유</th></tr>
<tr><td>가용성 차트</td><td><a href="../app-insights-monitor-web-app-availability/">웹 테스트</a></td><td>웹 앱이 작동 중인지 확인</td></tr>
<tr><td>서버 앱 성능: 응답시간. ...
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">프로젝트에 Application Insights 추가</a><br/>또는 <br/><a href="../app-insights-monitor-performance-live-website-now/">서버에 AI 상태 모니터 설치</a>(또는 자체 코드를 <a href="../app-insights-api-custom-events-metrics/#track-dependency">종속성 추적</a>에 작성)</td><td>성능 문제 검색</td></tr>
<tr><td>종속성 원격 분석</td><td><a href="../app-insights-monitor-performance-live-website-now/">서버에 AI 상태 모니터 설치</a></td><td>데이터베이스 또는 다른 외부 구성 요소의 문제 진단</td></tr>
<tr><td>예외에서 스택 추적 가져오기</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">TrackException 호출을 코드에 삽입합니다</a> (하지만 일부는 자동으로 보고 됩니다)</td><td>예외 감지 및 진단</td></tr>
<tr><td>로그 추적 검색</td><td><a href="../app-insights-search-diagnostic-logs/">로깅 어댑터 추가</a></td><td>예외, 성능 문제 진단</td></tr>
<tr><td>클라이언트 사용 기본 사항: 페이지 보기, 세션,...</td><td><a href="../app-insights-start-monitoring-app-health-usage/#webclient">웹 페이지의 JavaScript 이니셜라이저</a></td><td>사용 현황 분석</td></tr>
<tr><td>클라이언트 사용자 지정 메트릭</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">웹 페이지에서 추적 호출</a></td><td>사용자 환경 향상</td></tr>
<tr><td>서버 사용자 지정 메트릭</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">서버 코드에서 추적 호출</a></td><td>비즈니스 인텔리전스</td></tr>
</table>

웹 서비스를 Azure VM에서 실행하는 경우 이 위치에서도 [진단을 가져올][azurediagnostic] 수 있습니다.

## 자동화

Application Insights 리소스를 만드는 [PowerShell script 스크립트를 작성](app-insights-powershell-script-create-resource.md)할 수 있습니다.


<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-get-started.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=August15_HO7-->