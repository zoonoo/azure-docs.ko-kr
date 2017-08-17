---
title: "Azure Application Insights에서 Java 웹앱에 대한 성능 모니터링 | Microsoft Docs"
description: "Application Insights로 Java 웹 사이트의 확장된 성능 및 사용량 모니터링"
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: a481c7c62383c92a5dfab0e3f2b1b4f4f0e5ddf5
ms.contentlocale: ko-kr
ms.lasthandoff: 01/25/2017

---
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>종속성, 예외 및 Java 웹앱에서의 실행 시간을 모니터링합니다.


[Application Insights로 Java 웹앱을 계측][java]한 경우, Java Agent를 사용하여 코드의 변경 없이 보다 심층적인 정보를 얻을 수 있습니다.

* **종속성:** 응용 프로그램이 다음을 포함한 다른 구성 요소에 수행하는 호출에 대한 데이터:
  * **REST 호출** .
  * **Redis** 호출. 호출 시간이 10초보다 길면, 에이전트도 호출 인수를 가져옵니다.
  * **[JDBC 호출](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** - MySQL, SQL Server, PostgreSQL, SQLite, Oracle DB 또는 Apache Derby DB "executeBatch"호출이 지원됩니다. MySQL 및 PostgreSQL의 경우 호출 시간이 10초보다 길면 에이전트가 쿼리 계획을 보고합니다.
* **예외 포착:** 코드에서 처리하는 예외에 대한 데이터입니다.
* **메서드 실행 시간:** 특정 메서드를 실행하는 데 걸리는 시간에 대한 데이터입니다.

Java 에이전트를 사용하려면 사용자의 서버에 설치합니다. [Application Insights Java SDK][java]를 사용하여 웹앱을 계측해야 합니다. 

## <a name="install-the-application-insights-agent-for-java"></a>Java용 Application Insights 에이전트 설치
1. Java 서버를 실행 중인 컴퓨터에서 [에이전트를 다운로드](https://aka.ms/aijavasdk)합니다.
2. 응용 프로그램 서버 시작 스크립트를 편집하여 다음 JVM을 추가합니다.
   
    `javaagent:`*에이전트 JAR 파일에 대한 전체 경로*
   
    예를 들어 Linux 컴퓨터의 Tomcat에서:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. 응용 프로그램 서버를 다시 시작합니다.

## <a name="configure-the-agent"></a>에이전트 구성
`AI-Agent.xml` 파일을 만들어 에이전트 JAR 파일과 동일한 폴더에 배치합니다.

xml 파일의 내용을 설정합니다. 다음 예제를 편집하여 원하는 기능을 포함 또는 생략합니다.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

개별 메서드에 대한 메서드 타이밍과 예외를 보고할 수 있도록 설정해야 합니다.

기본적으로 `reportExecutionTime`은 true이고 `reportCaughtExceptions`는 false입니다.

## <a name="view-the-data"></a>데이터 보기
Application Insights 리소스에서 원격 종속성과 메서드 실행 시간의 합계는 [성능 타일 아래][metrics]에 나타납니다.

종속성의 개별 인스턴스, 예외 및 메서드 보고서를 찾으려면 [검색][diagnostic]을 엽니다.

[종속성 문제 진단 - 자세한 내용](app-insights-asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>질문이 있으십니까? 문제가 있습니까?
* 데이터가 없나요? [방화벽 예외 설정](app-insights-ip-addresses.md)
* [Java 문제 해결](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md

