<properties title="Search diagnostic logs with Application Insights" pageTitle="Search diagnostic logs" description="Search logs generated with Trace, NLog, or Log4Net." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Application Insights로 진단 로그 검색

System.Diagnostics.Trace, NLog 및 Log4Net에서 진단 데이터를 캡처하고 검색할 수 있습니다. Application Insights는 응용 프로그램 상태 모니터링 기능을 보완하면서 하나 이상의 소스에서 로깅된 이벤트를 수집하고 조사하는 데 효율적이면서 쉽게 사용할 수 있는 도구를 제공합니다.

모니터링하는 웹 응용 프로그램은 온-프레미스로 또는 가상 컴퓨터에 호스트하거나 Microsoft Azure 웹 사이트일 수 있습니다.

1.  [로깅 어댑터 추가][로깅 어댑터 추가]
+ [진단 컬렉션 구성][진단 컬렉션 구성]
+ [로그 문 삽입, 구성 및 배포][로그 문 삽입, 구성 및 배포]
+ [로그 데이터 보기][로그 데이터 보기]
+ [데이터 검색][데이터 검색]
+ [다음 단계][다음 단계]



## <a name="add"></a> 1. 로깅 어댑터 추가

1. 아직 그렇게 하지 않은 경우 Visual Studio에서 [웹 서비스 프로젝트에 Application Insights를 추가][웹 서비스 프로젝트에 Application Insights를 추가]하세요.

    프로젝트에 로깅을 추가한 후 Application Insights를 추가하면 로깅 어댑터가 이미 설정 및 구성되어 있으므로 [프로젝트를 다시 배포][로그 문 삽입, 구성 및 배포]하고 [데이터를 보기][로그 데이터 보기]만 하면 됩니다.

2.  솔루션 탐색기의 프로젝트에 대한 상황에 맞는 메뉴에서 **NuGet 패키지 관리**를 선택합니다.
3.  온라인 \> 모두를 선택하고, **시험판 포함**을 선택하고, "Microsoft.ApplicationInsights"를 검색합니다.

    ![적절한 어댑터의 시험판 버전 가져오기][적절한 어댑터의 시험판 버전 가져오기]


4. 다음 중 적절한 패키지의 시험판 버전을 선택합니다.
  + Microsoft.ApplicationInsights.TraceListener
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

## <a name="configure"></a> 2. 진단 컬렉션 구성

### System.Diagnostics.Trace의 경우

Web.config에서 `<configuration>` 섹션에 다음 코드를 삽입합니다.

    <system.diagnostics>
     <trace autoflush="true" indentsize="0">
      <listeners>
       <add name="myAppInsightsListener"  
          type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, 
         Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
     </trace>
    </system.diagnostics> 

### NLog의 경우

Nlog.config에서 `<extensions>`, `<targets>` 및 `<rules>` 섹션에 다음 코드 조각을 병합합니다. 필요한 경우 해당 섹션을 만듭니다.

    <extensions> 
     <add  assembly="Microsoft.ApplicationInsights.NLogTarget" /> 
    </extensions> 
    
    <targets>
     <target xsi:type="ApplicationInsightsTarget" name="aiTarget"/>
    </targets>
    
    <rules>
     <logger name="*" minlevel="Trace" writeTo="aiTarget"/>
    </rules>
    
### Log4Net의 경우

Web.config에서 `<configsections>` 및 `<log4net>` 섹션에 다음 코드 조각을 병합합니다.

    <configSections>
      <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
    </configSections>
    
    <log4net>
     <root>
      <level value="ALL" /> <appender-ref ref="aiAppender" />
     </root>
     <appender name="aiAppender" type="Microsoft.ApplicationInsights.Log4NetAppender.ApplicationInsightsAppender, Microsoft.ApplicationInsights.Log4NetAppender">
      <layout type="log4net.Layout.PatternLayout">
       <conversionPattern value="%date [%thread] %-5level %logger - %message%newline" />
      </layout>
     </appender>
    </log4net>


## <a name="deploy"></a> 3. 로그 문 삽입, 구성 및 배포

선택한 로깅 프레임워크를 사용하여 이벤트 로깅 호출을 삽입합니다. 예를 들어 Log4Net을 사용할 경우 호출은 다음과 같을 수 있습니다.

    log.Warn("Slow response - database01");

로깅된 이벤트는 개발 및 작동 모드 모두에서 Application Insights에 전송됩니다.

## <a name="view"></a> 4. 로그 데이터 보기

Application Insights에서 진단 검색을 엽니다.

![진단 검색 열기][진단 검색 열기]

세부 정보를 볼 로그 이벤트를 선택합니다.

![진단 검색 열기][1]

사용 가능한 필드는 로깅 프레임워크 및 호출에 사용한 매개 변수에 따라 달라집니다.

일반 문자열(와일드카드 없이)을 사용하여 항목 내에서 필드 데이터를 필터링할 수 있습니다.


## <a name="search"></a> 5. 데이터 검색

시간 범위를 설정하고 용어를 검색합니다. 짧은 기간을 검색할수록 더 빨라집니다.

![진단 검색 열기][2]

문자열의 일부가 아닌 용어를 검색합니다. 용어는 '.' 및 '\_'과 같은 문장 부호를 포함하는 영숫자 문자열입니다. 예를 들면 다음과 같습니다.

<table>
  <tr><th>용어</th><th>해당 용어가 검색되지 않는 문자열</th><th>해당 용어가 검색되는 문자열</th></tr>
  <tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
  <tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
  <tr><td>New Delay</td><td>w d</td><td>new<br/>delay<br/>n* AND d*</td></tr>
</table>

다음은 사용할 수 있는 검색 식입니다.

<table>
                    <tr>
                      <th>
                        <p>샘플 쿼리</p>
                      </th>
                      <th>
                        <p>결과</p>
                      </th>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">slow</span>
                        </p>
                      </td>
                      <td>
                        <p>지정된 날짜 범위의 필드에 &quot;slow&quot; 용어가 포함된 모든 이벤트를 찾습니다.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">database??</span>
                        </p>
                      </td>
                      <td>
                        <p>database01, databaseAB 등과 일치합니다.</p>
                        <p>? 기호는 검색 용어의 시작 부분에 사용할 수 없습니다.</p>
                      </td>
                    </tr>
                     <tr>
                      <td>
                        <p>
                          <span class="code">database*</span>
                        </p>
                      </td>
                      <td>
                        <p>database, database01, databaseNNNN과 일치합니다.</p>
                        <p>*는 검색 용어의 시작 부분에 사용할 수 없습니다.</p>
                      </td>
                    </tr>
                   <tr>
                      <td>
                        <p>
                          <span class="code">apple AND banana</span>
                        </p>
                      </td>
                      <td>
                        <p>두 용어를 모두 포함하는 이벤트를 찾습니다. &quot;and&quot;가 아닌 대문자 &quot;AND&quot;를 사용하세요.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">apple OR banana</span>
                        </p>
                        <p>
                          <span class="code">apple banana</span>
                        </p>
                      </td>
                      <td>
                        <p>둘 중 한 용어를 포함하는 이벤트를 찾습니다. &quot;or&quot;가 아닌 &quot;OR&quot;을 사용하세요.</p>
                        <p>짧은 형식</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">apple NOT banana</span>
                        </p>
                        <p>
                          <span class="code">apple -banana</span>
                        </p>
                      </td>
                      <td>
                        <p>한 용어를 포함하지만 다른 용어는 포함하지 않는 이벤트를 찾습니다.</p>
                        <p>짧은 형식</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>app* AND banana NOT (grape OR pear)</p>
                        <p>
                          <span class="code">app* AND banana -(grape pear)</span>
                        </p>
                      </td>
                      <td>
                        <p>논리적 연산자 및 괄호 사용</p>
                        <p>더 짧은 형식</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">message:slow</span>
                        </p>
                        <p>
                          <span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span>
                        </p>
                        <p>
                          <span class="code">properties.logEventInfo.level:Error</span>
                        </p>
                      </td>
                      <td>
                        <p>지정된 필드와 일치합니다. 기본적으로 모든 필드가 검색됩니다. 사용 가능한 필드를 보려면 세부 정보를 볼 이벤트를 선택합니다.</p>
                      </td>
                    </tr>
</table>

## <a name="add"></a> 다음 단계

* [프로젝트에 Application Insights 추가][웹 서비스 프로젝트에 Application Insights를 추가]
* [가용성 및 응답성 테스트 설정][가용성 및 응답성 테스트 설정]
* [문제 해결][문제 해결]


## 자세한 정보

* [Application Insights][Application Insights]
* [프로젝트에 Application Insights 추가][웹 서비스 프로젝트에 Application Insights를 추가]
* [현재 라이브 웹 서버 모니터링][현재 라이브 웹 서버 모니터링]
* [Application Insights의 메트릭 탐색][Application Insights의 메트릭 탐색]
* [진단 로그 검색][진단 로그 검색]
* [웹 테스트로 가용성 추적][가용성 및 응답성 테스트 설정]
* [이벤트 및 메트릭으로 사용량 추적][이벤트 및 메트릭으로 사용량 추적]
* [질문 및 답변과 문제 해결][문제 해결]


<!--Link references-->

[로깅 어댑터 추가]: #add
[진단 컬렉션 구성]: #configure
[로그 문 삽입, 구성 및 배포]: #deploy
[로그 데이터 보기]: #view
[데이터 검색]: #search
[다음 단계]: #next
[웹 서비스 프로젝트에 Application Insights를 추가]: ../app-insights-monitor-application-health-usage/
[적절한 어댑터의 시험판 버전 가져오기]: ./media/appinsights/appinsights-36nuget.png
[진단 검색 열기]: ./media/appinsights/appinsights-30openDiagnostics.png
[1]: ./media/appinsights/appinsights-32detail.png
[2]: ./media/appinsights/appinsights-31search.png
[가용성 및 응답성 테스트 설정]: ../app-insights-monitor-web-app-availability/
[문제 해결]: ../app-insights-troubleshoot-faq/
[Application Insights]: ../app-insights-get-started/
[현재 라이브 웹 서버 모니터링]: ../app-insights-monitor-performance-live-website-now/
[Application Insights의 메트릭 탐색]: ../app-insights-explore-metrics/
[진단 로그 검색]: ../app-insights-search-diagnostic-logs/
[이벤트 및 메트릭으로 사용량 추적]: ../app-insights-track-usage-custom-events-metrics/
