---
title: 2.x에서 업그레이드-Azure Monitor Application Insights Java
description: Azure Monitor Application Insights Java 2.x에서 업그레이드
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: d1d09c09afbabd40a32cbb80f1901112c37ac3da
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355135"
---
# <a name="upgrading-from-application-insights-java-sdk-2x"></a>Application Insights Java SDK 2.x에서 업그레이드

응용 프로그램에서 Java SDK 2.x Application Insights 이미 사용 중인 경우에는 제거할 필요가 없습니다.
Java 3.0 에이전트는이를 검색 하 여 java SDK 2.x를 통해 전송 하는 모든 사용자 지정 원격 분석을 캡처하고 상관 관계를 지정 하는 동시에 Java SDK 2.x에서 수행 되는 자동 컬렉션을 무시 하 고 중복 된 원격 분석을 방지 합니다.

Application Insights 2.x 에이전트를 사용 하는 경우 2.x `-javaagent:` 에이전트를 가리키는 JVM 인수를 제거 해야 합니다.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers 및 TelemetryProcessors

3.0 에이전트를 사용 하는 경우 Java SDK 2.x TelemetryInitializers 및 TelemetryProcessors가 실행 되지 않습니다.
[사용자 지정 차원을](./java-standalone-config.md#custom-dimensions) 구성 하거나 [원격 분석 프로세서](./java-standalone-telemetry-processors.md)를 구성 하 여 이전에 필요한 사용 사례를 3.0에서 해결할 수 있습니다.

## <a name="multiple-applications-in-a-single-jvm"></a>단일 JVM의 여러 응용 프로그램

현재 3.0는 실행 중인 프로세스 마다 단일 [연결 문자열과 역할 이름만](./java-standalone-config.md#connection-string-and-role-name) 지원 합니다. 특히 다른 연결 문자열 또는 다른 역할 이름을 사용 하 여 동일한 tomcat 배포에 여러 tomcat 웹 앱을 사용할 수 없습니다.

## <a name="http-request-telemetry-names"></a>HTTP 요청 원격 분석 이름

3.0의 HTTP 요청 원격 분석 이름은 일반적으로 Application Insights 포털 U/X에서 보다 효율적인 집계 보기를 제공 하도록 변경 되었습니다.

그러나 일부 응용 프로그램의 경우 이전 원격 분석 이름에서 제공 된 U/X의 집계 보기를 사용 하는 것이 좋습니다 .이 경우 3.0의 원격 분석 프로세서 미리 보기 기능을 사용 하 여 이전 이름으로 돌아갈 수 있습니다.

### <a name="to-prefix-the-telemetry-name-with-the-http-method-get-post-etc"></a>원격 분석 이름을 http 메서드 (, 등)와 접두사로 사용 하려면 `GET` `POST` 다음을 수행 합니다.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="to-set-the-telemetry-name-to-the-full-url-path"></a>원격 분석 이름을 전체 URL 경로로 설정 하려면

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```
