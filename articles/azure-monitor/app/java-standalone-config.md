---
title: 어디서나 Java 응용 프로그램 모니터링 - Azure 모니터 응용 프로그램 인사이트
description: 앱을 계측하지 않고 모든 환경에서 실행되는 Java 응용 프로그램에 대한 코드리스 애플리케이션 성능 모니터링. 분산 추적 및 응용 프로그램 맵을 사용하는 문제의 근본 원인을 찾습니다.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d930d349a2ab1efbd7a61904874bf6bdb411889
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641889"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>구성 옵션 - Azure 모니터 응용 프로그램 인사이트를 위한 Java 독립 실행형 에이전트



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

연결 문자열이 필요하며 역할 이름은 다른 응용 프로그램에서 동일한 Application Insights 리소스로 데이터를 보낼 때마다 중요합니다.

자세한 내용은 아래에서 자세한 내용을 확인할 수 있습니다.

## <a name="configuration-file-path"></a>구성 파일 경로

기본적으로 응용 프로그램 인사이트 Java 3.0 미리 보기는 구성 파일의 이름을 `ApplicationInsights.json`지정하고 `applicationinsights-agent-3.0.0-PREVIEW.jar`와 동일한 디렉토리에 위치할 것으로 예상합니다.

다음 을 사용하여 고유한 구성 파일 경로를 지정할 수 있습니다.

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`환경 변수 또는
* `applicationinsights.configurationFile`자바 시스템 속성

상대 경로를 지정하면 위치디렉터리기준으로 `applicationinsights-agent-3.0.0-PREVIEW.jar` 해결됩니다.

## <a name="connection-string"></a>연결 문자열

이것은 필수입니다. 응용 프로그램 인사이트 리소스에서 연결 문자열을 찾을 수 있습니다.

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="응용 프로그램 인사이트 연결 문자열":::

환경 변수를 `APPLICATIONINSIGHTS_CONNECTION_STRING`사용하여 연결 문자열을 설정할 수도 있습니다.

## <a name="cloud-role-name"></a>클라우드 역할 이름

클라우드 역할 이름은 응용 프로그램 맵의 구성 요소에 레이블을 지정하는 데 사용됩니다.

클라우드 역할 이름을 설정하려면 다음을 수행하십시오.

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

클라우드 역할 이름이 설정되지 않은 경우 응용 프로그램 인사이트 리소스의 이름이 응용 프로그램 맵의 구성 요소에 레이블을 지정하는 데 사용됩니다.

환경 변수를 `APPLICATIONINSIGHTS_ROLE_NAME`사용하여 클라우드 역할 이름을 설정할 수도 있습니다.

## <a name="cloud-role-instance"></a>클라우드 역할 인스턴스

클라우드 역할 인스턴스는 기본적으로 컴퓨터 이름으로 설정됩니다.

클라우드 역할 인스턴스를 컴퓨터 이름이 아닌 다른 것으로 설정하려면 다음을 수행하십시오.

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

환경 변수를 `APPLICATIONINSIGHTS_ROLE_INSTANCE`사용하여 클라우드 역할 인스턴스를 설정할 수도 있습니다.

## <a name="application-log-capture"></a>애플리케이션 로그 캡처

애플리케이션 인사이트 Java 3.0 미리 보기는 Log4j, Logback 및 java.util.logging을 통해 응용 프로그램 로깅을 자동으로 캡처합니다.

기본적으로 레벨 이상으로 수행되는 `WARN` 모든 로깅을 캡처합니다.

이 임계값을 변경하려면 다음을 수행하십시오.

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

다음은 `ApplicationInsights.json` 파일에 `threshold` 지정할 수 있는 유효한 값이며 다양한 로깅 프레임워크에서 로깅 수준에 해당하는 방법입니다.

| `threshold`  | Log4j  | Logback | 7월     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| 치명적인        | 치명적인  | 오류   | SEVERE  |
| 오류/심각 | 오류  | 오류   | SEVERE  |
| 경고/경고 | 경고   | 경고    | WARNING |
| INFO         | INFO   | INFO    | INFO    |
| CONFIG       | DEBUG  | DEBUG   | CONFIG  |
| 디버그/미세   | DEBUG  | DEBUG   | FINE    |
| FINER        | DEBUG  | DEBUG   | FINER   |
| 트레이스/최고급 | TRACE  | TRACE   | FINEST  |
| ALL          | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>JMX 메트릭

캡처에 관심이 있는 JMX 메트릭이 있는 경우:

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

## <a name="micrometer"></a>마이크로미터

기본적으로 응용 프로그램에서 [Micrometer](https://micrometer.io), 응용 프로그램 인사이트 3.0(Preview.2로 시작)을 사용하는 경우 이제 마이크로미터 전역 레지스트리에 자신을 추가하고 마이크로미터 메트릭을 캡처합니다.

이 기능을 사용하지 않도록 설정하려면 다음을 수행하십시오.

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

## <a name="heartbeat"></a>Heartbeat

기본적으로 애플리케이션 인사이트 Java 3.0 미리 보기는 15분에 한 번씩 하트비트 메트릭을 보냅니다. 하트비트 메트릭을 사용하여 경고를 트리거하는 경우 이 하트비트의 빈도를 늘릴 수 있습니다.

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
> 하트비트 데이터는 애플리케이션 인사이트 사용량을 추적하는 데도 사용되기 때문에 이 하트비트의 빈도를 줄일 수 없습니다.

## <a name="sampling"></a>샘플링

샘플링은 비용을 절감해야 하는 경우에 유용합니다.
샘플링은 작업 ID(추적 ID라고도 함)에 대한 함수로 수행되므로 동일한 작업 ID가 항상 동일한 샘플링 결정을 내림에 따라 결정됩니다. 이렇게 하면 분산 트랜잭션의 일부가 샘플링되는 동안 샘플링되지 않습니다.

예를 들어 샘플링을 10%로 설정하면 트랜잭션의 10%만 표시되지만 이러한 10%의 각 트랜잭션에는 전체 종단 간 트랜잭션 세부 정보가 있습니다.

다음은 샘플링을 **모든 트랜잭션의 10%로** 설정하는 방법의 예입니다 .

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

응용 프로그램이 방화벽 뒤에 있고 응용 프로그램 인사이트에 직접 연결할 수 없는 [경우(응용 프로그램 인사이트에서 사용되는 IP 주소](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)참조) HTTP 프록시를 사용하도록 응용 프로그램 인사이트 Java 3.0 미리 보기를 구성할 수 있습니다.

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

## <a name="self-diagnostics"></a>자가 진단

"자체 진단"은 응용 프로그램 인사이트 Java 3.0 미리 보기에서 내부 로깅을 말합니다.

이는 응용 프로그램 인사이트 자체로 문제를 발견하고 진단하는 데 유용할 수 있습니다.

기본적으로 이 구성에 해당하는 수준이 `warn`있는 콘솔로 기록됩니다.

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

유효한 수준은 `OFF` `ERROR`" `WARN` `INFO`, `DEBUG` `TRACE`

콘솔에 로그온하는 대신 파일에 로그온하려면 다음을 수행하십시오.

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

파일 로깅을 사용하는 경우 `maxSizeMB`파일이 적중되면 롤오버되어 현재 로그 파일 외에 가장 최근에 완료된 로그 파일만 유지됩니다.
