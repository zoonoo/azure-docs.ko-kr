---
title: 어디서 나 Java 응용 프로그램 모니터링 Azure Monitor Application Insights
description: 앱을 계측 하지 않고 모든 환경에서 실행 중인 Java 응용 프로그램에 대 한 응용 프로그램 성능 모니터링을 코드 없는. 분산 추적 및 응용 프로그램 맵을 사용 하 여 문제 d의 근본 원인을 찾습니다.
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: ca3094197deb7c74ba9b51422a78ee0f5d3687d2
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374289"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>구성 옵션-Azure Monitor Application Insights 용 Java 독립 실행형 에이전트



## <a name="connection-string-and-role-name"></a>연결 문자열 및 역할 이름

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

연결 문자열이 필요 하며, 다른 응용 프로그램의 데이터를 동일한 Application Insights 리소스로 보낼 때마다 역할 이름이 중요 합니다.

자세한 내용은 아래에서 자세한 내용 및 추가 구성 옵션을 확인할 수 있습니다.

## <a name="configuration-file-path"></a>구성 파일 경로

기본적으로 Java 3.0 미리 보기 Application Insights 구성 파일의 이름을로 지정 하 `ApplicationInsights.json` 고와 동일한 디렉터리에 배치 합니다 `applicationinsights-agent-3.0.0-PREVIEW.5.jar` .

다음 중 하나를 사용 하 여 사용자 고유의 구성 파일 경로를 지정할 수 있습니다.

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`환경 변수 또는
* `applicationinsights.configurationFile`Java 시스템 속성

상대 경로를 지정 하면가 있는 디렉터리를 기준으로 확인 됩니다 `applicationinsights-agent-3.0.0-PREVIEW.5.jar` .

## <a name="connection-string"></a>연결 문자열

이것은 필수입니다. Application Insights 리소스에서 연결 문자열을 찾을 수 있습니다.

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="연결 문자열 Application Insights":::

환경 변수를 사용 하 여 연결 문자열을 설정할 수도 있습니다 `APPLICATIONINSIGHTS_CONNECTION_STRING` .

## <a name="cloud-role-name"></a>클라우드 역할 이름

클라우드 역할 이름은 응용 프로그램 맵의 구성 요소에 레이블을 적용 하는 데 사용 됩니다.

클라우드 역할 이름을 설정 하려면 다음을 수행 합니다.

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
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
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

환경 변수를 사용 하 여 클라우드 역할 인스턴스를 설정할 수도 있습니다 `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

## <a name="application-log-capture"></a>응용 프로그램 로그 캡처

Application Insights Java 3.0 미리 보기는 Log4j, Logback 및 java를 통해 응용 프로그램 로깅을 자동으로 캡처합니다.

기본적으로 수준 이상에서 수행 되는 모든 로깅을 캡처합니다 `WARN` .

이 임계값을 변경 하려면 다음을 수행 합니다.

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

이러한 `threshold` 값은 파일에 지정할 수 있는 유효한 값 `ApplicationInsights.json` 이며, 여러 로깅 프레임 워크에서 로깅 수준에 해당 하는 방법입니다.

| `threshold`  | Log4j  | Logback | 7월     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| 심각한        | 심각한  | 오류   | SEVERE  |
| 오류/심각 | 오류  | 오류   | SEVERE  |
| 경고/경고 | 게   | 게    | 경고 |
| 정보         | INFO   | INFO    | INFO    |
| CONFIG       | DEBUG  | DEBUG   | CONFIG  |
| 디버그/자세히   | DEBUG  | DEBUG   | FINE    |
| FINER        | DEBUG  | DEBUG   | FINER   |
| 추적/가장 | TRACE  | TRACE   | FINEST  |
| ALL          | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>JMX 메트릭

JMX 메트릭이 있다면 다음과 같이 캡처할 수 있습니다.

```json
{
  "instrumentationSettings": {
    "preview": {
        "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>마이크로 측정기 (스프링 부트 발동기의 메트릭 포함)

응용 프로그램에서 [마이크로 측정기](https://micrometer.io)를 사용 하는 경우 Application Insights 3.0 (Preview부터 시작)는 이제 마이크로 측정기 글로벌 레지스트리로 전송 된 메트릭을 캡처합니다.

응용 프로그램에서 [스프링 부팅 발동기](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)Application Insights를 사용 하는 경우 3.0 (Preview부터 시작)는 이제 스프링 부트 작동기 (마이크로 측정기를 사용 하지만 마이크로 측정기 글로벌 레지스트리는 사용 하지 않음)로 구성 된 메트릭을 캡처합니다.

이러한 기능을 사용 하지 않도록 설정 하려면 다음을 수행 합니다.

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>하트비트

기본적으로 Application Insights Java 3.0 미리 보기는 30 분 마다 한 번씩 하트 비트 메트릭을 보냅니다. 하트 비트 메트릭을 사용 하 여 경고를 트리거하는 경우이 하트 비트의 빈도를 늘릴 수 있습니다.

```json
{
  "instrumentationSettings": {
    "preview": {
        "heartbeat": {
            "intervalSeconds": 60
        }
    }
  }
}
```

> [!NOTE]
> 하트 비트 데이터가 Application Insights 사용을 추적 하는 데에도 사용 되므로이 하트 비트의 빈도는 낮출 수 없습니다.

## <a name="sampling"></a>샘플링

샘플링은 비용을 줄여야 할 경우에 유용 합니다.
샘플링은 작업 ID (추적 ID 라고도 함)에서 함수로 수행 되므로 동일한 작업 ID가 항상 동일한 샘플링 결정을 발생 하도록 합니다. 이렇게 하면에서 샘플링 되는 분산 트랜잭션의 일부가 샘플링 되지 않습니다.

예를 들어 샘플링을 10%로 설정 하면 트랜잭션의 10%만 표시 되지만 이러한 10% 중 각각에는 완전 한 종단 간 트랜잭션 세부 정보가 포함 됩니다.

다음은 샘플링을 **모든 트랜잭션의 10%** 로 설정 하는 방법의 예입니다. 사용 사례에 맞는 샘플링 비율을 설정 했는지 확인 하세요.

```json
{
  "instrumentationSettings": {
    "preview": {
        "sampling": {
            "fixedRate": {
                "percentage": 10
            }
          }
        }
    }
}
```

## <a name="http-proxy"></a>HTTP 프록시

응용 프로그램이 방화벽 뒤에 있고 Application Insights에 직접 연결할 수 없는 경우 ( [Application Insights에서 사용 하는 IP 주소](./ip-addresses.md)참조), HTTP 프록시를 사용 하도록 Application Insights Java 3.0 미리 보기를 구성할 수 있습니다.

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>자체 진단

"자체 진단"은 Application Insights Java 3.0 미리 보기의 내부 로깅을 나타냅니다.

Application Insights 자체와 관련 된 문제를 발견 하 고 진단 하는 데 도움이 될 수 있습니다.

기본적으로 `warn` 이 구성에 해당 하는 수준으로 콘솔에 기록 합니다.

```json
{
  "instrumentationSettings": {
    "preview": {
        "selfDiagnostics": {
            "destination": "console",
            "level": "WARN"
        }
    }
  }
}
```

유효한 수준은 `OFF` , `ERROR` ,, `WARN` , `INFO` `DEBUG` 및 `TRACE` 입니다.

콘솔에 로그인 하는 대신 파일에 로그인 하려면 다음을 수행 합니다.

```json
{
  "instrumentationSettings": {
    "preview": {
        "selfDiagnostics": {
            "destination": "file",
            "directory": "/var/log/applicationinsights",
            "level": "WARN",
            "maxSizeMB": 10
        }    
    }
  }
}
```

파일 로깅을 사용 하는 경우 파일에 도달 하면 `maxSizeMB` 현재 로그 파일 뿐 아니라 가장 최근에 완료 된 로그 파일만 유지 하 게 됩니다.
