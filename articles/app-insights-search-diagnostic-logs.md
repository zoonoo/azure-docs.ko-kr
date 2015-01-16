<properties title="Search diagnostic logs with Application Insights" pageTitle="진단 로그 검색" description="추적, NLog 또는 Log4Net을 사용하여 생성된 로그를 검색합니다." metaKeywords="analytics web test" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 
# Application Insights의 진단 검색

대부분의 일반적인 디버깅 방법 중 하나는 추적 로그를 내보내는 코드 줄을 삽입하는 것입니다. [Application Insights][start]에서는 웹 서버 로그를 캡처하고 검색 및 필터링하도록 도와줍니다. log4Net, NLog 또는 System.Diagnostics.Trace를 이미 사용하는 경우 어댑터를 통해 이러한 로그를 캡처할 수 있습니다. 또는 Application Insights SDK에 기본으로 제공되는 TrackTrace 및 TrackException 메서드를 사용할 수 있습니다.

또한 검색 결과에는 작성한 [사용자 지정 TrackEvent 호출][track]과 함께 [사용 현황][usage] 및 [성능][perf] 보고서를 작성하는 데 사용되는 일반 페이지 보기 및 요청 이벤트도 포함될 수 있습니다.


2. [로깅 프레임워크용 어댑터 설치 여부](#capture)
+ [진단 로그 호출 삽입](#pepper)
+ [예외](#exceptions)
+ [로그 데이터 보기](#view)
+ [로그 데이터 검색](#search)
+ [문제 해결](#questions)
+ [다음 단계](#next)



## <a name="capture"></a> 로깅 프레임워크용 어댑터 설치 여부

[프로젝트에 Application Insights를 아직 설치하지 않은 경우][start] 지금 수행합니다.

기본 제공 Application Insights SDK Track*() 호출을 사용하는 경우 어댑터가 필요 없습니다. 따라서 [다음 섹션으로 건너뛰세요](#pepper).

Log4Net, NLog 또는 System.Diagnostics.Trace로 생성된 로그를 검색하려면 해당 어댑터를 설치합니다.

1. Log4Net 또는 NLog를 사용하려는 경우 프로젝트에 설치합니다. 
2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.
3. 온라인 > 모두를 선택하고, **시험판 포함**을 선택하고, "Microsoft.ApplicationInsights"를 검색합니다.

    ![Get the prerelease version of the appropriate adapter](./media/appinsights/appinsights-36nuget.png)

4. 다음 패키지 중에서 적절한 패키지를 하나 선택합니다.
  + Microsoft.ApplicationInsights.TraceListener (to capture System.Diagnostics.Trace calls)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

NuGet 패키지는 필요한 어셈블리를 설치하고 web.config 또는 app.config를 수정합니다.

## <a name="pepper"></a>3. 진단 로그 호출 삽입

선택한 로깅 프레임워크를 사용하여 이벤트 로깅 호출을 삽입합니다. 

예를 들어 Application Insights SDK를 사용하는 경우 다음을 삽입할 수 있습니다.

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

또는 System.Diagnostics.Trace를 사용하는 경우

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Log4net 또는 NLog를 원할 경우

    logger.Warn("Slow response - database01");

디버그 모드에서 응용 프로그램을 실행하거나 웹 서버에 배포합니다.

### <a name="exceptions"></a>예외

로그에 예외를 보내려면

클라이언트의 JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

서버의 C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

서버의 VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

속성 및 측정 매개 변수는 선택적이지만 추가 정보를 필터링 및 추가하는 데 유용합니다. 예를 들어 여러 게임을 실행할 수 있는 앱이 있는 경우 특정 게임과 관련된 모든 예외 보고서를 찾을 수 있습니다. 각 사전에 원하는 만큼 항목을 추가할 수 있습니다.

## <a name="view"></a>4. 로그 데이터 보기


1. Application Insights에서 진단 검색을 엽니다.

    ![Open diagnostic search](./media/appinsights/appinsights-30openDiagnostics.png)
   
2. 보려는 이벤트 유형에 대한 필터를 설정합니다.

    ![Open diagnostic search](./media/appinsights/appinsights-331filterTrace.png)


이벤트 유형은 다음과 같습니다.

* **추적** - 웹 서버에서 캡처한 진단 로그를 검색합니다. 여기에는 log4Net, NLog, System.Diagnostic.Trace 및 ApplicationInsights TrackTrace 호출이 포함됩니다.
* **요청** - 페이지 요청, 데이터 요청, 이미지 등을 비롯하여 웹 앱의 서버 구성 요소에 수신된 HTTP 요청을 검색합니다. 표시되는 이벤트는 Application Insights SDK 서버에서 보낸 원격 분석으로, 요청 개수 보고서를 만드는 데 사용됩니다.
* **페이지 뷰** - 페이지 뷰 이벤트를 검색합니다. 이러한 이벤트는 웹 클라이언트에서 보내고 페이지 뷰 보고서를 만드는 데 사용됩니다. (여기에 아무 내용도 표시되지 않으면 [웹 클라이언트 모니터링][usage]을 설정합니다.)
* **사용자 지정 이벤트** - [사용 현황][track]을 모니터링하기 위해 TrackEvent() 및 TrackMetric()에 대한 호출을 삽입하는 경우 여기서 검색할 수 있습니다.

세부 정보를 볼 로그 이벤트를 선택합니다. 

![Open diagnostic search](./media/appinsights/appinsights-32detail.png)

일반 문자열(와일드카드 없이)을 사용하여 항목 내에서 필드 데이터를 필터링할 수 있습니다.

사용 가능한 필드는 로깅 프레임워크 및 호출에 사용한 매개 변수에 따라 달라집니다.


## <a name="search"></a>5. 데이터 검색

시간 범위를 설정하고 용어를 검색합니다. 짧은 기간을 검색할수록 더 빨라집니다. 

![Open diagnostic search](./media/appinsights/appinsights-311search.png)

문자열의 일부가 아닌 용어를 검색합니다. 용어는 '.' 및 '_'과 같은 문장 부호를 포함하는 영숫자 문자열입니다. 예를 들면 다음과 같습니다.

<table>
  <tr><th>용어</th><th>다음과 일치하지 않음</th><th>다음과는 일치</th></tr>
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
                        <p>지정된 날짜 범위의 필드에 "slow" 용어가 포함된 모든 이벤트를 찾습니다.</p>
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
                        <p>?는 검색 용어의 시작 부분에 사용할 수 없습니다.</p>
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
                        <p>두 용어를 모두 포함하는 이벤트를 찾습니다. "and"가 아닌 대문자 "AND"를 사용하세요.</p>
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
                        <p>둘 중 한 용어를 포함하는 이벤트를 찾습니다. "or"가 아닌 "OR"을 사용하세요.</p>
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
       <!-- -- fielded search feature not ready yet --
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
 -->
</table>


## <a name="questions"></a>질문과 대답

### <a name="emptykey"></a>"계측 키는 비워 둘 수 없습니다." 오류가 발생합니다.

Application Insights를 설치하지 않고 로깅 어댑터 Nuget 패키지를 설치한 것 같습니다.

솔루션 탐색기에서 `ApplicationInsights.config`를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. Azure에 로그인하고 Application Insights 리소스를 만들거나 기존 리소스를 다시 사용하도록 초대하는 대화 상자가 표시됩니다. 이 경우 문제가 해결된 것입니다.

### <a name="limits"></a>얼마나 많은 데이터가 보존되나요?

각 응용 프로그램에서 초당 최대 500개의 이벤트가 보존됩니다. 이벤트는 7일 동안 보존됩니다.

### <a name="cani"></a>수행할 수 있는 작업...

- 이벤트 및 예외에 대한 경고 설정
- 추가 분석을 위해 로그 내보내기
- 특정 속성 검색

아직은 제공되지 않지만 이러한 모든 기능이 백로그에 있습니다.

## <a name="add"></a>다음 단계

* [가용성 및 응답성 테스트 설정][availability]
* [문제 해결][qna]





[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




