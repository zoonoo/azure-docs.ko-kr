---
title: Java 웹 앱 성능 모니터링 - Azure 응용 프로그램 인사이트
description: Application Insights로 Java 웹 사이트의 확장된 성능 및 사용량 모니터링
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: b047a8dd8c67679a5cc8a45e8be82f9ab5227aa4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537545"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Java 웹 앱에서 종속성, 적발된 예외 및 메서드 실행 시간 모니터링


[Application Insights로 Java 웹앱을 계측][java]한 경우, Java Agent를 사용하여 코드의 변경 없이 보다 심층적인 정보를 얻을 수 있습니다.

* **종속성:** 애플리케이션이 다음을 포함한 다른 구성 요소에 수행하는 호출에 대한 데이터:
  * 아파치 HttpClient, OkHttp를 통해 만든 나가는 `java.net.HttpURLConnection` HTTP **호출,** 캡처됩니다.
  * 제디스 클라이언트를 통해 이루어진 **Redis 호출이** 캡처됩니다.
  * **JDBC 쿼리** - MySQL 및 PostgreSQL의 경우 호출이 10초 이상 걸리는 경우 에이전트는 쿼리 계획을 보고합니다.

* **응용 프로그램 로깅:** HTTP 요청 및 기타 원격 분석과 응용 프로그램 로그 캡처 및 상관 관계
  * **Log4j 1.2**
  * **Log4j2**
  * **로그백**

* **더 나은 작업 이름 지정:** (포털에서 요청 집계에 사용)
  * **봄** - `@RequestMapping`를 기반으로 합니다.
  * **JAX-RS** - `@Path`기반 . 

Java 에이전트를 사용하려면 사용자의 서버에 설치합니다. [Application Insights Java SDK][java]를 사용하여 웹앱을 계측해야 합니다. 

## <a name="install-the-application-insights-agent-for-java"></a>Java용 Application Insights 에이전트 설치
1. Java 서버를 실행 중인 컴퓨터에서 [에이전트를 다운로드](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)합니다. Application Insights Java SDK 코어 및 웹 패키지와 동일한 버전의 Java Agent 다운로드를 확인하세요.
2. 응용 프로그램 서버 시작 스크립트를 편집하고 다음 JVM 인수를 추가합니다.
   
    `-javaagent:<full path to the agent JAR file>`
   
    예를 들어 Linux 컴퓨터의 Tomcat에서:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. 애플리케이션 서버를 다시 시작합니다.

## <a name="configure-the-agent"></a>에이전트 구성
`AI-Agent.xml` 파일을 만들어 에이전트 JAR 파일과 동일한 폴더에 배치합니다.

xml 파일의 내용을 설정합니다. 다음 예제를 편집하여 원하는 기능을 포함 또는 생략합니다.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>추가 구성(스프링 부팅)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Azure 앱 서비스의 경우 다음을 수행합니다.

* 설정 &gt; 애플리케이션 설정 선택
* 앱 설정 아래에서 새로운 키 값 쌍을 추가합니다.

키: `JAVA_OPTS` 값:`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Java 에이전트의 최신 버전은 여기에서 릴리스를 [확인하십시오.](https://github.com/Microsoft/ApplicationInsights-Java/releases
) 

에이전트는 D:/home/site/wwwroot/디렉터리에서 끝나지 있도록 프로젝트의 리소스로 패키징되어야 합니다. **개발 도구** > **고급 도구** > **디버그 콘솔로** 이동하여 사이트 디렉터리 내용을 검사하여 에이전트가 올바른 앱 서비스 디렉터리에 있는지 확인할 수 있습니다.    

* 설정을 저장하고 앱을 다시 시작합니다. 이러한 단계는 Windows에서 실행 중인 앱 서비스에만 적용됩니다.

> [!NOTE]
> AI-Agent.xml 및 에이전트 jar 파일은 동일한 폴더에 있어야 합니다. 종종 프로젝트의 `/resources` 폴더에 함께 배치됩니다.  

#### <a name="enable-w3c-distributed-tracing"></a>W3C 분산 추적 사용

AI-Agent.xml에 다음을 추가합니다.

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> 이전 버전과의 호환성 모드는 기본적으로 사용하도록 설정되어 있으며 enableW3CBackCompat 매개 변수는 선택 사항이므로, 해제하려는 경우에만 사용해야 합니다. 

이상적으로는 모든 서비스 W3C 프로토콜을 지원하는 최신 버전의 SDK로 업데이트된 경우입니다. 가능한 한 빨리 W3C를 지원하는 최신 버전의 SDK로 전환하는 것이 좋습니다.

**[수신](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) 및 발신(에이전트) 구성이 둘 다 정확히 동일**한지 확인합니다.

## <a name="view-the-data"></a>데이터 보기
Application Insights 리소스에서 집계된 원격 종속성 및 메서드 실행 시간은 [성능 타일 아래에][metrics]나타납니다.

종속성의 개별 인스턴스, 예외 및 메서드 보고서를 찾으려면 [검색][diagnostic]을 엽니다.

[종속성 문제 진단 - 자세한 내용](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>질문이 있으십니까? 문제가 있습니까?
* 데이터가 없나요? [방화벽 예외 설정](../../azure-monitor/app/ip-addresses.md)
* [Java 문제 해결](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
