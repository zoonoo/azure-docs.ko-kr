---
title: 구성 옵션-Java Azure Monitor Application Insights
description: Azure Monitor Application Insights Java에 대 한 구성 옵션
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 710347061f072fe66987d88852045986c00812c8
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377686"
---
# <a name="configuration-options-for-azure-monitor-application-insights-java"></a>Azure Monitor Application Insights Java에 대 한 구성 옵션

> [!WARNING]
> **3.0 미리 보기에서 업그레이드 하는 경우**
>
> 모든 구성 옵션은 json 구조가 완전히 변경 되었으므로 모든 구성 옵션을 신중 하 게 검토 해야 합니다.

## <a name="connection-string-and-role-name"></a>연결 문자열 및 역할 이름

시작 하는 데 필요한 가장 일반적인 설정은 연결 문자열 및 역할 이름입니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "role": {
    "name": "my cloud role name"
  }
}
```

연결 문자열이 필요 하며, 다른 응용 프로그램의 데이터를 동일한 Application Insights 리소스로 보낼 때마다 역할 이름이 중요 합니다.

아래에서 자세한 내용 및 추가 구성 옵션을 확인할 수 있습니다.

## <a name="configuration-file-path"></a>구성 파일 경로

기본적으로 Application Insights Java 3.0은 구성 파일의 이름을로 지정 하 `applicationinsights.json` 고와 동일한 디렉터리에 위치 하도록 `applicationinsights-agent-3.0.0.jar` 합니다.

다음 중 하나를 사용 하 여 사용자 고유의 구성 파일 경로를 지정할 수 있습니다.

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` 환경 변수 또는
* `applicationinsights.configuration.file` Java 시스템 속성

상대 경로를 지정 하면가 있는 디렉터리를 기준으로 확인 됩니다 `applicationinsights-agent-3.0.0.jar` .

## <a name="connection-string"></a>연결 문자열

이것은 필수입니다. Application Insights 리소스에서 연결 문자열을 찾을 수 있습니다.

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="연결 문자열 Application Insights":::


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
}
```

환경 변수를 사용 하 여 연결 문자열을 설정할 수도 있습니다 `APPLICATIONINSIGHTS_CONNECTION_STRING` .

연결 문자열을 설정 하지 않으면 Java 에이전트가 사용 되지 않습니다.

## <a name="cloud-role-name"></a>클라우드 역할 이름

클라우드 역할 이름은 응용 프로그램 맵의 구성 요소에 레이블을 적용 하는 데 사용 됩니다.

클라우드 역할 이름을 설정 하려면 다음을 수행 합니다.

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

클라우드 역할 이름이 설정 되지 않은 경우 Application Insights 리소스의 이름을 사용 하 여 응용 프로그램 맵의 구성 요소에 레이블을 지정할 수 있습니다.

환경 변수를 사용 하 여 클라우드 역할 이름을 설정할 수도 있습니다 `APPLICATIONINSIGHTS_ROLE_NAME` .

## <a name="cloud-role-instance"></a>클라우드 역할 인스턴스

클라우드 역할 인스턴스는 기본적으로 컴퓨터 이름으로 설정 됩니다.

클라우드 역할 인스턴스를 컴퓨터 이름이 아닌 다른 항목으로 설정 하려면 다음을 수행 합니다.

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

환경 변수를 사용 하 여 클라우드 역할 인스턴스를 설정할 수도 있습니다 `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

## <a name="sampling"></a>샘플링

샘플링은 비용을 줄여야 할 경우에 유용 합니다.
샘플링은 작업 ID (추적 ID 라고도 함)에서 함수로 수행 되므로 동일한 작업 ID가 항상 동일한 샘플링 결정을 발생 하도록 합니다. 이렇게 하면에서 샘플링 되는 분산 트랜잭션의 일부가 샘플링 되지 않습니다.

예를 들어 샘플링을 10%로 설정 하면 트랜잭션의 10%만 표시 되지만 이러한 10% 중 각각에는 완전 한 종단 간 트랜잭션 세부 정보가 포함 됩니다.

다음은 **모든 트랜잭션의 약 1/3** 을 캡처하기 위해 샘플링을 설정 하는 방법의 예입니다. 사용 사례에 맞는 샘플링 주기를 설정 했는지 확인 하세요.

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

환경 변수를 사용 하 여 샘플링 비율을 설정할 수도 있습니다 `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` .

> [!NOTE]
> 샘플링 비율의 경우 100/N(여기서 N은 정수)에 가까운 백분율을 선택합니다. 현재 샘플링은 다른 값을 지원하지 않습니다.

## <a name="jmx-metrics"></a>JMX 메트릭

몇 가지 추가 JMX 메트릭을 수집 하려면:

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` 이 JMX 메트릭에 할당 되는 메트릭 이름입니다 (모두 일 수 있음).

`objectName` 수집 하려는 JMX MBean의 [개체 이름](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) 입니다.

`attribute` 수집 하려는 JMX MBean 내의 특성 이름입니다.

숫자 및 부울 JMX 메트릭 값이 지원 됩니다. 부울 JMX 메트릭은 `0` false에 대해, true의 경우로 매핑됩니다 `1` .

[//]: # "참고: 여기 APPLICATIONINSIGHTS_JMX_METRICS 문서화 하지 않음"
[//]: # "env var에 포함 된 json은 복잡 하며 코드 없는 attach 시나리오용 으로만 설명 해야 합니다."

## <a name="custom-dimensions"></a>사용자 지정 차원

모든 원격 분석에 사용자 지정 차원을 추가 하려면 다음을 수행 합니다.

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` 시작 시 지정 된 환경 변수에서 값을 읽는 데 사용할 수 있습니다.

## <a name="telemetry-processors-preview"></a>원격 분석 프로세서 (미리 보기)

이 기능은 미리 보기 기능입니다.

이를 통해 요청, 종속성 및 추적 원격 분석에 적용 되는 규칙을 구성할 수 있습니다 (예:).
 * 중요 한 데이터 마스킹
 * 조건부로 사용자 지정 차원 추가
 * 집계 및 표시에 사용 되는 원격 분석 이름을 업데이트 합니다.

자세한 내용은 [원격 분석 프로세서](./java-standalone-telemetry-processors.md) 설명서를 확인 하세요.

## <a name="auto-collected-logging"></a>자동 수집 된 로깅

Log4j, Logback 및 java는 자동으로 계측 되며 이러한 로깅 프레임 워크를 통해 수행 된 로깅은 자동으로 수집 됩니다.

기본적으로 로깅은 수준 이상에서 수행 되는 경우에만 수집 됩니다 `INFO` .

이 컬렉션 수준을 변경 하려면 다음을 수행 합니다.

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

환경 변수를 사용 하 여 임계값을 설정할 수도 있습니다 `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` .

이러한 `level` 값은 파일에 지정할 수 있는 유효한 값 `applicationinsights.json` 이며, 여러 로깅 프레임 워크의 로깅 수준에 해당 하는 방법입니다.

| 수준             | Log4j  | Logback | 7월     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| 심각한             | 심각한  | 오류   | SEVERE  |
| 오류 (또는 심각한) | 오류  | 오류   | SEVERE  |
| 경고 (또는 경고) | 게   | 게    | 경고 |
| 정보              | 정보   | 정보    | 정보    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| 디버그 (또는 자세히)   | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| 추적 (또는 가장) | TRACE  | TRACE   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>자동 수집 된 마이크로 측정기 메트릭 (스프링 부트 발동기 메트릭 포함)

응용 프로그램에서 [마이크로 측정기](https://micrometer.io)를 사용 하는 경우 마이크로 측정기 글로벌 레지스트리로 전송 되는 메트릭은 자동으로 수집 됩니다.

또한 응용 프로그램에서 [스프링 부팅 발동기](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)를 사용 하는 경우 스프링 부트 발동기로 구성 된 메트릭도 자동으로 수집 됩니다.

마이크로 측정기 메트릭의 자동 수집을 사용 하지 않도록 설정 하려면 (스프링 부트 발동기 메트릭 포함):

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="heartbeat"></a>하트비트

기본적으로 Application Insights Java 3.0는 30 분 마다 한 번씩 하트 비트 메트릭을 보냅니다. 하트 비트 메트릭을 사용 하 여 경고를 트리거하는 경우이 하트 비트의 빈도를 늘릴 수 있습니다.

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> 하트 비트 데이터가 Application Insights 사용을 추적 하는 데에도 사용 되므로이 하트 비트의 빈도는 낮출 수 없습니다.

## <a name="http-proxy"></a>HTTP 프록시

응용 프로그램이 방화벽 뒤에 있고 Application Insights에 직접 연결할 수 없는 경우 ( [Application Insights에서 사용 하는 IP 주소](./ip-addresses.md)참조) HTTP 프록시를 사용 하도록 Java 3.0 Application Insights 구성할 수 있습니다.

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

[//]: # "참고 OpenTelemetry 지원 광고는 0.10.0를 지원 하기 전까지 0.9.0의 대규모 주요 변경 사항이 있습니다."

[//]: # "# # OpenTelemetry API 이전 1.0 릴리스 지원"

[//]: # "OpenTelemetry API가 아직 안정적이 지 않아 OpenTelemetry API의 이전 1.0 버전에 대 한 지원이 옵트인 (opt in) 됩니다."
[//]: # "그리고 각 버전의 에이전트는 OpenTelemetry API의 특정 1.0 버전만 지원 합니다."
[//]: # "(OpenTelemetry API 1.0을 해제 한 후에는이 제한이 적용 되지 않습니다.)"

[//]: # "' ' ' json"
[//]: # "{"
[//]: # "  \"미리 보기 \" : {"
[//]: # "    \"openTelemetryApiSupport \" : true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>자체 진단

"자체 진단"은 Application Insights Java 3.0의 내부 로깅을 나타냅니다.

Application Insights 자체와 관련 된 문제를 발견 하 고 진단 하는 데 도움이 될 수 있습니다.

기본적으로 Application Insights Java 3.0 `INFO` 은 `applicationinsights.log` 이 구성에 해당 하는 파일 및 콘솔 모두에 대 한 수준으로 로그를 기록 합니다.

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` 는, 또는 중 하나일 수 있습니다 `file` `console` `file+console` .

`level` ,,,, 또는 중 하나일 수 있습니다 `OFF` `ERROR` `WARN` `INFO` `DEBUG` `TRACE` .

`path` 절대 경로 또는 상대 경로일 수 있습니다. 상대 경로는가 있는 디렉터리를 기준으로 확인 됩니다 `applicationinsights-agent-3.0.0.jar` .

`maxSizeMb` 는 롤오버 되기 전에 로그 파일의 최대 크기입니다.

`maxHistory` 현재 로그 파일 외에도 유지 되는 로그 파일에 대해 롤업되는 수입니다.
