<properties 
	pageTitle="Java 웹 프로젝트에서 Application Insights 시작하기" 
	description="Application Insights로 Java 웹 사이트의 성능 및 사용량 모니터링" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/26/2015" 
	ms.author="awills"/>
 
#Java 웹 프로젝트에서 Application Insights 시작하기

*Application Insights는 미리 보기 상태입니다.*

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

Visual Studio Application Insights를 프로젝트에 추가하여 성능 문제 및 예외 사항을 감지 및 진단할 수 있습니다.


![샘플 데이터](./media/app-insights-java-track-http-requests/5-results.png)

또한 [웹 테스트][availability]를 설정하여 응용 프로그램의 가용성을 모니터링하고 [웹 페이지에 코드를][track] 삽입하여 사용 패턴을 이해할 수 있습니다.

필요한 사항:

* Oracle JRE 1.6 이상 또는 Zulu JRE 1.6 이상
* [Microsoft Azure](http://azure.microsoft.com/) 구독. \([무료 평가판](http://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있음.\)


##1\. Application Insights 계측 키 가져오기

1. [Microsoft Azure 포털](https://portal.azure.com)에 로그인
2. 새 Application Insights 리소스 만들기

    ![\+를 클릭하고 Application Insights 선택](./media/app-insights-java-get-started/01-create.png)
3. Java 웹 응용 프로그램에 대한 응용 프로그램 종류를 설정합니다.

    ![이름을 채우고 Java 웹 앱을 선택하여 만들기 클릭](./media/app-insights-java-get-started/02-create.png)
4. 새 리소스의 계측 키를 찾습니다. 코드 프로젝트에 곧바로 붙여넣어야 합니다.

    ![새 리소스 개요에서 속성을 클릭하고 계측 키 복사](./media/app-insights-java-get-started/03-key.png)

##2\. 프로젝트에 Java용 Aplication Insights SDK 추가

*프로젝트에 적합한 방법을 선택합니다.*

####Eclipse에서 동적 웹 프로젝트를 만드는 경우...

[Java 플러그인용 Application Insights SDK][eclipse]를 사용합니다.

####Maven을 사용하는 경우...

빌드에 Maven을 사용하도록 프로젝트가 이미 설정된 경우 pom.xml 파일에 다음 코드 조각을 병합합니다.

그런 다음 프로젝트 종속성을 새로 고쳐 다운로드한 이진을 가져옵니다.

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[0.9,)</version>
      </dependency>
    </dependencies>


* *빌드 또는 체크섬 유효성 검사 오류가 있나요? 다음 특정 버전을 사용해 봅니다.* `<version>0.9.3</version>`

####Gradle을 사용하는 경우...

빌드에 Gradle을 사용하도록 프로젝트가 이미 설정된 경우 다음 코드 조각을 build.gradle 파일에 병합합니다.

그런 다음 프로젝트 종속성을 새로 고쳐 다운로드한 이진을 가져옵니다.

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '0.9.+'
      // or applicationinsights-core for bare API
    }

* *빌드 또는 체크섬 유효성 검사 오류가 있나요? 다음 특정 버전을 사용해 봅니다.* `version:'0.9.3'`

####기타...

수동으로 SDK 추가:

1. [Java용 Azure 라이브러리](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html) 다운로드
2. Zip 파일에서 다음과 같은 이진 파일을 추출하고 프로젝트에 추가:
 * applicationinsights-core
 * applicationinsights-web
 * commons-codec
 * commons-io
 * commons-lang
 * commons-logging
 * guava
 * httpclient
 * httpcore
 * jsr305


*`-core` 및 `-web` 구성 요소 사이의 관계란 무엇인가요?*

`applicationinsights-core`은\(는\) 자동 원격 분석 없이 완전한 API를 제공합니다. `applicationinsights-web`은\(는\) HTTP 요청 수 및 응답 시간을 추적하는 메트릭을 제공합니다.


##3\. Application Insights xml 파일 추가

ApplicationInsights.xml을 프로젝트의 리소스 폴더에 추가합니다. 다음 XML을 복사합니다.

Azure 포털에서 가져온 계측 키를 대체합니다.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* 계측 키는 원격 분석의 모든 항목과 함께 전송되며 리소스에서 표시하도록 Application Insights에 알려줍니다.
* HTTP 요청 구성 요소는 선택 사항입니다. 자동으로 포털에 요청 및 응답 시간에 대한 원격 분석을 보냅니다.
* 이벤트 상관 관계는 HTTP 요청 구성 요소에 추가됩니다. 이는 서버가 수신하는 요청마다 식별자를 할당하며 'Operation.Id' 속성으로 원격 분석의 모든 항목에 이를 속성으로 추가합니다. [진단 검색][diagnostic]에서 필터를 설정하여 각 요청과 연결된 원격 분석의 상관 관계를 지정할 수 있습니다.

##4\. HTTP 필터 추가

마지막 구성 단계는 HTTP 요청 구성 요소가 각 웹 요청을 로그하도록 허용합니다. \(완전한 API를 원하는 경우에는 요청되지 않습니다.\)

프로젝트에서 web.xml 파일을 찾아 열고, 응용 프로그램 필터가 구성된 웹 앱 노드 아래 다음 코드 조각을 병합합니다.

가장 정확한 결과를 얻으려면 필터를 다른 모든 필터 전에 매핑해야 합니다.

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

####MVC 3.1 이상을 사용하는 경우

Application Insights 패키지를 포함하도록 이러한 요소 편집:

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>

####Struts 2를 사용하는 경우

Struts 구성 파일에 이 항목 추가\(일반적으로 struts.xml 또는 struts-default.xml이라고 함\):

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

\(기본 스택에 정의된 인터셉터가 있는 경우 해당 인터셉터를 스택에 추가하면 됩니다.\)

##5\. Application Insights에서 원격 분석 보기

응용 프로그램을 실행합니다.

Microsoft Azure에서 Application Insights 리소스로 돌아갑니다.

HTTP 요청 데이터가 개요 블레이드에 표시됩니다. \(없는 경우 몇 초 정도 기다린 다음 새로고침을 클릭합니다.\)

![샘플 데이터](./media/app-insights-java-track-http-requests/5-results.png)
 

차트를 클릭하면 더 자세한 메트릭을 볼 수 있습니다.

![](./media/app-insights-java-track-http-requests/6-barchart.png)

 

또한 요청 속성 검토 시 요청 및 예외 사항과 관련된 원격 분석 이벤트를 볼 수 있습니다.
 
![](./media/app-insights-java-track-http-requests/7-instance.png)



[메트릭에 대해 자세히 알아봅니다.][metrics]

####스마트 주소 이름 계산

Application Insights는 MVC 응용 프로그램에 대한 HTTP 요청 형식을 다음과 같이 가정합니다. `VERB controller/action`


예를들어, `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` 및 `GET Home/Product/sdf96vws`은\(는\) `GET Home/Product`\(으\)로 그룹화됩니다.

이를 통해 요청 수와 같은 의미 있는 집계 및 요청에 대한 평균 실행 시간을 사용할 수 있습니다.

##5\. 성능 카운터

서버 타일을 클릭하면 다양한 성능 카운터가 표시됩니다.


![](./media/app-insights-java-get-started/11-perf-counters.png)

###성능 카운터 수집 사용자 지정

성능 카운터의 표준 집합 수집을 사용하지 않으려면 ApplicationInsights.xml 파일의 루트 노드 아래에 다음 조각을 추가합니다.

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

###추가 성능 카운터 수집

추가 성능 카운터가 수집되도록 지정할 수 있습니다.

####JMX 카운터\(Java 가상 컴퓨터를 통해 노출됨\)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*	`displayName` - Application Insights 포털에서 표시되는 이름입니다.
*	`objectName` – JMX 개체 이름입니다.
*	`attribute` - 가져올 JMX 개체 이름의 특성입니다.
*	`type` \(선택 사항\) - JMX 개체 특성의 유형:
 *	기본값: int 또는 long과 같은 단순 유형입니다.
 *	`composite`: 성능 카운터 데이터는 'Attribute.Data' 형식입니다.
 *	`tabular`: 성능 카운터 데이터는 표 행 형식입니다.



####Windows\(64비트\) 성능 카운터 

각 [Windows 성능 카운터](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)는 한 범주의 구성원입니다\(필드가 클래스의 구성원인 것과 동일한 방식\). 범주는 전역일 수 있으며, 번호 또는 이름이 지정된 인스턴스를 가질 수도 있습니다.

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*	displayName - Application Insights 포털에서 표시되는 이름입니다.
*	categoryName – 이 성능 카운터와 관련된 성능 카운터 범주\(성능 개체\)입니다.
*	counterName – 성능 카운터의 이름입니다.
*	instanceName – 성능 카운터 범주 인스턴스입니다. 또는 범주가 단일 인스턴스를 포함하는 경우 빈 문자열\(""\)의 이름입니다. categoryName이 프로세스이며 수입하려는 성능 카운터는 앱이 실행 중인 현재 JVM 프로세스에서 오는 경우, `"__SELF__"`을\(를\) 지정합니다.

성능 카운터에서가 [메트릭 탐색기][metrics]에서 사용자 지정 메트릭으로 보입니다.

![](./media/app-insights-java-get-started/12-custom-perfs.png)


##6\. 로그 추적 캡처

Application Insights를 사용하여 Log4J, Logback 또는 다른 로깅 프레임 워크에서 분리 및 분할할 수 있습니다. HTTP 요청 및 기타 원격 분석과 로그를 연관지을 수 있습니다. [방법을 알아봅니다][javalogs].

##7\. 사용자 고유의 원격 분석 전송

이제 SDK를 설치한 했으므로 API를 사용하여 사용자 고유의 원격 분석을 전송할 수 있습니다.

* [사용자 지정 이벤트 및 메트릭을 추적][track]하여 사용자가 응용 프로그램으로 수행하는 것을 알아볼 수 있습니다.
* [이벤트 및 로그를 검색][diagnostic]하여 문제를 진단할 수 있습니다.


또한 응용 프로그램과 관련하여 Application Insights의 더 많은 기능을 가져올 수 있습니다.

* [웹 클라이언트 원격 분석을 추가][usage]하여 페이지 보기 및 기본 사용자 메트릭을 모니터링할 수 있습니다.
* [웹 테스트를 설정][availability]하여 응용 프로그램이 라이브 상태로 유지되며 응답하는지 확인할 수 있습니다.


##질문이 있으십니까? 문제가 있습니까?

[Java 문제 해결](app-insights-java-troubleshoot.md)



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md


<!--HONumber=54-->