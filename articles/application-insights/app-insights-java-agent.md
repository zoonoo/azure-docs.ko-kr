<properties 
	pageTitle="종속성, 예외 및 Java 웹앱에서의 실행 시간을 모니터링 합니다." 
	description="Application Insights를 사용하여 Java 웹사이트에 대한 모니터링을 확장합니다." 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="awills"/>
 
# 종속성, 예외 및 Java 웹앱에서의 실행 시간을 모니터링 합니다.

*Application Insights는 미리 보기 상태입니다.*

[Application Insights로 Java 웹앱을 계측][java]한 경우, Java Agent를 사용하여 코드의 변경 없이 더 깊은 insight를 얻습니다.

* **원격 종속성:** 사용자의 응용 프로그램이 [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)드라이버같은 MySQL, SQL Server, PostgreSQL, SQLite 등을 통해 만든 호출에 관한 데이터 입니다
* **예외 포착:**코드에서 처리하는 예외에 대한 데이터입니다.
* **메서드 실행 시간:**특정 메서드를 실행하는 데 걸리는 시간에 대한 데이터입니다.

Java 에이전트를 사용하여 사용자의 서버에 설치합니다. [Java용 Aplication Insights SDK][java]를 사용하여 웹앱을 계측해야 합니다.

## Java 용 Application Insights 에이전트 설치

1. Java 서버를 실행 중인 컴퓨터에서[에이전트 다운로드](http://go.microsoft.com/fwlink/?LinkId=618633).
2. 응용 프로그램 서버 시작 스크립트를 편집하고 다음 JVM을 추가합니다.

    `javaagent:`* 에이전트 JAR 파일 * 전체 경로

    예를 들어 Linux 컴퓨터의 Tomcat에서:

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. 응용 프로그램 서버를 다시 시작 합니다.

## 에이전트를 구성하려면

`AI-Agent.xml`라는 파일을 만들고 JAR 파일 에이전트와 같은 폴더에 배치합니다.

Xml 파일의 콘텐츠를 설정 합니다. 원하는 기능을 생략하거나 다음 예제를 포함하여 편집합니다.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true"/>

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
              signature="(Ljava/lang/String:I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

개별 메서드에 대한 메서드 타이밍과 예외를 보고할 수 있도록 설정해야 합니다.

기본적으로`reportExecutionTime`가 true 이면`reportCaughtExceptions`은 false입니다.

## 데이터 보기

Application Insights 리소스에서 원격 종속성과 메서드 실행 시간의 합계는[성능 타일에서][metrics] 나타납니다.

종속성의 개별 인스턴스, 예외 및 메서드 보고서를 찾으려면, [검색][diagnostic]엽니다.

## 질문이 있으십니까? 문제가 있습니까?

[Java 문제 해결](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO3-->