---
title: 모든 환경에서 Java 응용 프로그램 모니터링 - Azure 모니터 응용 프로그램 인사이트
description: 앱을 계측하지 않고 모든 환경에서 실행되는 Java 응용 프로그램에 대한 응용 프로그램 성능 모니터링. 분산 추적 및 응용 프로그램 맵.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 5706d5a74bd6850a237f7418b1a86a8e9c7762e1
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604570"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Java 코드리스 응용 프로그램 모니터링 Azure 모니터 응용 프로그램 인사이트 - 공개 미리 보기

Java 코드리스 응용 프로그램 모니터링은 단순성입니다 - 코드 변경이 없으며 Java 에이전트는 몇 가지 구성 변경을 통해서만 활성화할 수 있습니다.

 Java 에이전트는 모든 환경에서 작동하며 모든 Java 응용 프로그램을 모니터링할 수 있습니다. 즉, VM, 온-프레미스, AKS, Windows, Linux에서 Java 앱을 실행하든 관계없이 Java 3.0 에이전트가 앱을 모니터링합니다.

3.0 에이전트가 요청, 종속성 및 로그를 모두 자체적으로 자동 수집하므로 응용 프로그램 인사이트 Java SDK를 응용 프로그램에 추가할 필요가 없습니다.

응용 프로그램에서 사용자 지정 원격 분석을 계속 보낼 수 있습니다. 3.0 에이전트는 자동 수집된 모든 원격 분석을 추적하고 상호 연관시다.

## <a name="quickstart"></a>빠른 시작

**1. 에이전트 다운로드**

[애플리케이션 인사이트 에이전트 다운로드-3.0.0-PREVIEW.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.3/applicationinsights-agent-3.0.0-PREVIEW.3.jar)

**2. JVM을 에이전트에 가리킵니다.**

응용 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.3.jar` 프로그램의 JVM args에 추가

일반적인 JVM 아르그에는 `-Xmx512m` 및 `-XX:+UseG1GC`. 따라서 이 것들을 추가할 위치를 알고 있다면 이 것을 추가할 위치를 이미 알고 있습니다.

응용 프로그램의 JVM args 구성에 대한 추가 도움말은 [3.0 미리 보기: JVM args 업데이트 팁참조.](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Tips-for-updating-your-JVM-args)

**3. 에이전트를 응용 프로그램 인사이트 리소스로 가리킵니다.**

아직 Application Insights 리소스가 없는 경우 [리소스 만들기 가이드의](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)단계에 따라 새 리소스를 만들 수 있습니다.

환경 변수를 설정하여 에이전트를 Application Insights 리소스로 가리킵니다.

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

또는 :라는 `ApplicationInsights.json`구성 파일을 만들고 다음 내용과 함께 `applicationinsights-agent-3.0.0-PREVIEW.3.jar`와 동일한 디렉토리에 배치합니다.

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

응용 프로그램 인사이트 리소스에서 연결 문자열을 찾을 수 있습니다.

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="응용 프로그램 인사이트 연결 문자열":::

**4. 그게 다야!**

이제 응용 프로그램을 시작하고 Azure 포털의 Application Insights 리소스로 이동하여 모니터링 데이터를 확인합니다.

> [!NOTE]
> 모니터링 데이터가 포털에 표시되려면 몇 분 정도 걸릴 수 있습니다.


## <a name="configuration-options"></a>구성 옵션

파일에서 `ApplicationInsights.json` 다음을 추가로 구성할 수 있습니다.

* 클라우드 역할 이름
* 클라우드 역할 인스턴스
* 애플리케이션 로그 캡처
* JMX 메트릭
* 마이크로미터
* Heartbeat
* 샘플링
* HTTP 프록시
* 자가 진단

자세한 내용은 [3.0 공개 미리 보기: 구성 옵션을](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Configuration-Options)참조하십시오.

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>자동 수집된 요청, 종속성, 로그 및 메트릭

### <a name="requests"></a>요청

* JMS 소비자
* 카프카 소비자
* 네티/웹플럭스
* 서블릿
* 스프링 스케줄링

### <a name="dependencies-with-distributed-trace-propagation"></a>분산 추적 전파가 있는 종속성

* 아파치 Http클라이언트 및 HttpAsyncClient
* gRPC
* 자바.net.HttpURL연결
* JMS
* Kafka
* 네티 클라이언트
* 오크스 (주)

### <a name="other-dependencies"></a>기타 종속성

* Cassandra
* JDBC
* 몽고DB (비동기 및 동기화)
* 레디스 (양상추와 제디스)

### <a name="logs"></a>로그

* 자바 유틸리티.로깅
* Log4j
* SLF4J/로그백

### <a name="metrics"></a>메트릭

* 마이크로미터
* JMX 메트릭

## <a name="sending-custom-telemetry-from-your-application"></a>응용 프로그램에서 사용자 지정 원격 분석 보내기

3.0+의 목표는 표준 API를 사용하여 사용자 지정 원격 분석을 보낼 수 있도록 하는 것입니다.

우리는 마이크로 미터, Open Telemetry API 및 인기있는 로깅 프레임 워크를 지원합니다. 응용 프로그램 인사이트 Java 3.0은 자동으로 원격 분석을 캡처하고 자동 수집된 모든 원격 분석과 함께 원격 분석을 상호 연결합니다.

따라서 현재 응용 프로그램 인사이트 3.0을 갖춘 SDK를 릴리스할 계획이 없습니다.

응용 프로그램 인사이트 Java 3.0은 이미 응용 프로그램 인사이트 Java SDK 2.x로 전송되는 원격 분석을 듣고 있습니다. 이 기능은 기존 2.x 사용자에 대 한 업그레이드 이야기의 중요 한 부분 이며 OpenTelemetry API GA 때까지 사용자 지정 원격 분석 지원에 중요 한 간격을 채웁니다.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>응용 프로그램 인사이트 Java SDK 2.x를 사용하여 사용자 지정 원격 분석 보내기

응용 `applicationinsights-core-2.6.0.jar` 프로그램에 추가 (모든 2.x 버전은 응용 프로그램 인사이트 Java 3.0에서 지원되지만 선택 항목이있는 경우 최신 버전을 사용하는 것이 좋습니다).

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

원격 분석 클라이언트 만들기:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

사용자 지정 원격 분석을 전송하는 데 사용합니다.

### <a name="events"></a>이벤트

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>메트릭

[마이크로미터를](https://micrometer.io)통해 미터법 원격 분석을 보낼 수 있습니다.

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

또는 응용 프로그램 인사이트 Java SDK 2.x를 사용할 수도 있습니다.

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>종속성

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>로그
즐겨 찾는 로깅 프레임워크를 통해 사용자 지정 로그 원격 분석을 보낼 수 있습니다.

또는 응용 프로그램 인사이트 Java SDK 2.x를 사용할 수도 있습니다.

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>예외
즐겨 찾는 로깅 프레임워크를 통해 사용자 지정 예외 원격 분석을 보낼 수 있습니다.

또는 응용 프로그램 인사이트 Java SDK 2.x를 사용할 수도 있습니다.

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>애플리케이션 인사이트 Java SDK 2.x에서 업그레이드

응용 프로그램에서 이미 응용 프로그램 인사이트 Java SDK 2.x를 사용하고 있는 경우 제거할 필요가 없습니다. Java 3.0 에이전트는 자바 SDK 2.x를 통해 보내는 모든 사용자 지정 원격 분석을 캡처하고 상호 연관시키는 동시에 Java SDK 2.x에서 수행되는 자동 수집을 억제하여 중복 캡처를 방지합니다.

> [!NOTE]
> 참고: Java SDK 2.x 원격 측정초기라이저 및 원격 측정 프로세서는 3.0 에이전트를 사용할 때 실행되지 않습니다.
