---
title: 2.x에서 업그레이드-Azure Monitor Application Insights Java
description: Azure Monitor Application Insights Java 2.x에서 업그레이드
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 6e1c7e15ff77fd75ff2fb70a6741ea2dd9a4cab8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040246"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Application Insights Java 2.x SDK에서 업그레이드

응용 프로그램에서 Java 2.x SDK Application Insights 이미 사용 하 고 있는 경우이를 제거할 필요가 없습니다.
Java 3.0 에이전트는이를 검색 하 고, 2.x SDK를 통해 전송 하는 모든 사용자 지정 원격 분석을 캡처하고 상관 관계를 지정 하 고, 2. x SDK에서 수행 되는 자동 컬렉션을 무시 하 여 중복 된 원격 분석을 방지 합니다.

Application Insights 2.x 에이전트를 사용 하는 경우 2.x `-javaagent:` 에이전트를 가리키는 JVM 인수를 제거 해야 합니다.

이 문서의 나머지 부분에서는 2.x에서 3.0로 업그레이드 하는 경우에 발생할 수 있는 제한 사항 및 변경 내용 뿐만 아니라 도움이 될 수 있는 몇 가지 해결 방법을 설명 합니다.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers 및 TelemetryProcessors

3.0 에이전트를 사용 하는 경우 2.x SDK TelemetryInitializers 및 TelemetryProcessors가 실행 되지 않습니다.
[사용자 지정 차원을](./java-standalone-config.md#custom-dimensions) 구성 하거나 [원격 분석 프로세서](./java-standalone-telemetry-processors.md)를 구성 하 여 이전에 필요한 사용 사례를 3.0에서 해결할 수 있습니다.

## <a name="multiple-applications-in-a-single-jvm"></a>단일 JVM의 여러 응용 프로그램

현재 3.0는 실행 중인 프로세스 마다 단일 [연결 문자열과 역할 이름만](./java-standalone-config.md#connection-string-and-role-name) 지원 합니다. 특히 다른 연결 문자열 또는 다른 역할 이름을 사용 하 여 동일한 tomcat 배포에 여러 tomcat 웹 앱을 사용할 수 없습니다.

## <a name="operation-names"></a>작업 이름

3.0의 작업 이름은 일반적으로 Application Insights 포털 U/X에서 보다 효율적인 집계 보기를 제공 하도록 변경 되었습니다.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="3.0의 작업 이름":::

그러나 일부 응용 프로그램의 경우 이전 작업 이름에서 제공 된 U/X의 집계 보기를 사용 하는 것이 좋습니다 .이 경우 3.0의 [원격 분석 프로세서](./java-standalone-telemetry-processors.md) (미리 보기) 기능을 사용 하 여 이전 동작을 복제할 수 있습니다.

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>작업 이름 앞에 http 메서드 (, 등)를 접두사로 사용 `GET` `POST` 합니다.

2.x SDK에서 작업 이름의 앞에는 http 메서드 (, 등)가 붙습니다 `GET` `POST` . 예를 들면

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="Http 메서드가 접두사로 추가 된 작업 이름":::

아래 코드 조각은를 결합 하 여 이전 동작을 복제 하는 3 개의 원격 분석 프로세서를 구성 합니다.
원격 분석 프로세서는 다음 작업을 순서 대로 수행 합니다.

1. 첫 번째 원격 분석 프로세서는 범위 프로세서 (유형 `span` )로, 및에 적용 됩니다 `requests` `dependencies` .

   이라는 특성을 포함 하 `http.method` 고로 시작 하는 범위 이름을 가진 모든 범위와 일치 `/` 합니다.

   그런 다음 해당 범위 이름을 이라는 특성으로 추출 `tempName` 합니다.

2. 두 번째 원격 분석 프로세서는 범위 프로세서 이기도 합니다.

   이라는 특성이 있는 모든 범위와 일치 하 게 됩니다 `tempName` .

   그런 다음 두 특성을 공백으로 구분 하 여 범위 이름을 업데이트 `http.method` 합니다 `tempName` .

3. 마지막 원격 분석 프로세서는 특성 프로세서 (유형)입니다 `attribute` .이는 특성을 가진 모든 원격 분석 (현재 및)에 적용 됨을 의미 `requests` `dependencies` `traces` 합니다.

   이라는 특성이 있는 모든 원격 분석을 일치 시킵니다 `tempName` .

   그런 다음 `tempName` 사용자 지정 차원으로 보고 되지 않도록 라는 특성을 삭제 합니다.

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

### <a name="set-the-operation-name-to-the-full-path"></a>작업 이름을 전체 경로로 설정 합니다.

또한 2.x SDK에서 일부 경우에는 작업 이름에 전체 경로가 포함 됩니다 (예:).

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="전체 경로가 포함 된 작업 이름":::

아래 코드 조각은를 결합 하 여 이전 동작을 복제 하는 4 개의 원격 분석 프로세서를 구성 합니다.
원격 분석 프로세서는 다음 작업을 순서 대로 수행 합니다.

1. 첫 번째 원격 분석 프로세서는 범위 프로세서 (유형 `span` )로, 및에 적용 됩니다 `requests` `dependencies` .

   이라는 특성이 있는 모든 범위와 일치 하 게 됩니다 `http.url` .

   그런 다음 특성 값을 사용 하 여 범위 이름을 업데이트 합니다 `http.url` .

   이는 끝에 해당 하는 것을 제외 하 고는이의 끝에 해당 합니다 `http.url` `http://host:port/path` `/path` .

2. 두 번째 원격 분석 프로세서는 범위 프로세서 이기도 합니다.

   이라는 특성이 있는 모든 범위와 일치 `http.url` 합니다 (즉, 첫 번째 프로세서가 일치 하는 모든 범위).

   그런 다음 범위 이름의 경로 부분을 이라는 특성에 추출 합니다 `tempName` .

3. 세 번째 원격 분석 프로세서는 범위 프로세서 이기도 합니다.

   이라는 특성이 있는 모든 범위와 일치 하 게 됩니다 `tempPath` .

   그런 다음 특성에서 범위 이름을 업데이트 합니다 `tempPath` .

4. 마지막 원격 분석 프로세서는 특성 프로세서 (유형)입니다 `attribute` .이는 특성을 가진 모든 원격 분석 (현재 및)에 적용 됨을 의미 `requests` `dependencies` `traces` 합니다.

   이라는 특성이 있는 모든 원격 분석을 일치 시킵니다 `tempPath` .

   그런 다음 `tempPath` 사용자 지정 차원으로 보고 되지 않도록 라는 특성을 삭제 합니다.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
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

## <a name="dependency-names"></a>종속성 이름

3.0의 종속성 이름도 변경 되었으며 일반적으로 Application Insights 포털 U/X에서 보다 효율적인 집계 보기를 제공 합니다.

일부 응용 프로그램의 경우 이전 종속성 이름으로 제공 된 U/X에서 집계 보기를 사용 하는 것이 좋습니다 .이 경우 위의 유사한 기법을 사용 하 여 이전 동작을 복제할 수 있습니다.

## <a name="operation-name-on-dependencies"></a>종속성에 대 한 작업 이름

이전에는 2.x SDK에서 요청 원격 분석의 작업 이름도 종속성 원격 분석에서 설정 했습니다.
Application Insights Java 3.0은 더 이상 종속성 원격 분석에서 작업 이름을 채우지 않습니다.
종속성 원격 분석의 부모인 요청에 대 한 작업 이름을 확인 하려는 경우 종속성 테이블에서 요청 테이블로 조인 하는 로그 (Kusto) 쿼리를 작성할 수 있습니다. 예를 들면

```
let start = datetime('...');
let end = datetime('...');
dependencies
| where timestamp between (start .. end)
| project timestamp, type, name, operation_Id
| join (requests
    | where timestamp between (start .. end)
    | project operation_Name, operation_Id)
    on $left.operation_Id == $right.operation_Id
| summarize count() by operation_Name, type, name
```

## <a name="2x-sdk-logging-appenders"></a>2.x SDK 로깅 어 펜더

3.0 에이전트는 로깅 어 펜더을 구성할 필요 없이 [로깅을 자동으로 수집](./java-standalone-config.md#auto-collected-logging) 합니다.
2.x SDK 로깅 어 펜더을 사용 하는 경우 3.0 에이전트에 의해 표시 되지 않으므로 제거할 수 있습니다.

## <a name="2x-sdk-spring-boot-starter"></a>2.x SDK 스프링 부팅 스타터

3.0 스프링 부팅 스타터는 없습니다.
3.0 에이전트 설정 및 구성은 스프링 부팅을 사용 하는지 여부에 관계 없이 동일한 [간단한 단계](./java-in-process-agent.md#quickstart) 를 따릅니다.

2.x SDK 스프링 부팅 스타터에서 업그레이드 하는 경우 클라우드 역할 이름이 더 이상 기본값이 아닙니다 `spring.application.name` .
Json 구성 또는 환경 변수를 통해 3.0에서 클라우드 역할 이름을 설정 하려면 [3.0 구성 문서](./java-standalone-config.md#cloud-role-name) 를 참조 하세요.
