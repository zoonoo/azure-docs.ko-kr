<properties 
	pageTitle="Java용 Application Insights에 대한 릴리스 정보" 
	description="최신 업데이트입니다." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="awills"/>
 
# Application Insights SDK for Java에 대한 릴리스 정보

[Application Insights SDK for Java](app-insights-java-get-started.md)는 라이브 앱에 대한 원격 분석을 [Application Insights](http://azure.microsoft.com/services/application-insights/)에 보내며 사용법과 성능을 분석할 수 있습니다.

#### 응용 프로그램에 SDK를 설치하려면

[Java용 SDK 시작](app-insights-java-get-started.md)을 참조하세요.

#### 최신 SDK를 업그레이드하려면 

업그레이드 후에 ApplicationInsights.xml에 대해 작성된 모든 사용자을 다시 병합해야 합니다. 새 파일와 비교할 개체의 복사본을 수행 합니다.

*Maven 또는 Gradle를 사용하는 경우*

1. pom.xml 또는 build.gradle에서 특정 버전 번호를 지정한 경우 업데이트합니다.
2. 프로젝트의 종속성을 새로 고칩니다.

*그렇지 않은 경우*

* 최신 버전의 [Java용 Azure 라이브러리](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html)를 다운로드하고 이전 버전을 대체합니다. 
 
이전 버전과 새 ApplicationInsights.xml을 비교합니다. 대부분 변경 내용은 모듈을 추가하고 제거하기 때문입니다. 변경한 모든 사용자 지정을 복구합니다.

## 버전 1.0.1
- [Java 에이전트](app-insights-java-agent.md)는 다음에 대한 종속성 정보를 수집합니다.
	- HttpClient, OkHttp 및 RestTemplate(Spring)을 통해 수행한 HTTP 호출
	- Jedis 클라이언트를 통해 수행한 Redis 호출. 구성 가능한 임계값이 전달되면 SDK가 호출 인수도 가져옵니다.
	- Oracle DB 및 Apache Derby DB 클라이언트를 사용하여 수행한 JDBC 호출
	- 준비된 문에 대해 'executeBatch' 쿼리 형식 지원 – SDK에서 일괄 처리 수와 함께 문이 표시됩니다.
	- JDBC 클라이언트에 대해 해당 (MySql, PostgreSql)을 지원하는 쿼리 계획 제공 - 구성 가능한 임계값을 초과하는 경우에만 쿼리 계획을 가져옵니다.

## Version 1.0.0
- CollectD의 Application Insights 기록기 플러그인에 대한 지원을 추가합니다.
- 응용 프로그램 insight Java 에이전트에 대한 지원을 추가 합니다.
- HttpClient 버전 4.2 이상 지원에 관한 호환성 문제를 수정합니다.

## 버전 0.9.6
- Java SDK를 서블릿 v2.5 및 HttpClient pre-v4.3과 호환되도록 하기
- Java EE 인터셉터에 대한 지원 추가
- Logback 어펜더에서 중복 종속성 제거

## 버전 0.9.5  

- 오류를 분석하는 쿠키로 인한 사용자 지정 이벤트가 사용자/세션과 상관되는 문제를 수정합니다.  
- ApplicationInsights.xml 구성 파일의 위치를 확인하기 위한 논리를 개선합니다.
- 익명 사용자 및 세션 쿠키는 서버쪽에서 생성되지 않습니다. 웹앱의 사용자 및 세션 추적을 구현하려면, JavaScript SDK를 포함한 계측은 원래 목적을 실현하는 JavaScript SDK의 쿠키가 필요합니다. 이 변경 사항 은 오직 사용자-시작 세션만 계산되는 것처럼 사용자 및 세션 개수의 중요한 restatement를 발생시킨다는 것을 참고하세요.

## 버전 0.9.4

- 32비트 Windows 컴퓨터에서 성능 카운터를 수집할 수 있습니다.
- 새로운 ```trackDependency``` 메서드 API를 사용하여 수동으로 종속성을 추적할 수 있습니다.
- 보고된 항목에 ```SyntheticSource``` 속성을 추가하여 원격 분석 항목에 가상으로 태그를 지정할 수 있습니다.
 

<!---HONumber=Sept15_HO2-->