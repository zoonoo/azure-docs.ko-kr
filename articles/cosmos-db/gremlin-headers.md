---
title: Azure 코스모스 DB 그렘린 응답 헤더
description: 추가 문제 해결을 가능하게 하는 서버 응답 메타데이터에 대한 참조 문서
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755090"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure 코스모스 DB 그렘린 서버 응답 헤더
이 문서에서는 Cosmos DB Gremlin 서버가 요청 실행 시 호출자에게 반환하는 헤더에 대해 설명합니다. 이러한 헤더는 Cosmos DB 서비스에 기본적으로 통합되고 고객 지원을 간소화하는 애플리케이션을 구축하는 요청 성능 문제 해결에 유용합니다.

이러한 헤더에 대한 종속성을 고려하면 응용 프로그램의 이식성을 다른 Gremlin 구현으로 제한할 수 있습니다. 그 대가로 코스모스 DB 그렘린과의 긴밀한 통합이 가속화되고 있습니다. 이러한 헤더는 TinkerPop 표준이 아닙니다.

## <a name="headers"></a>headers

| 헤더 | Type | 샘플 값 | 포함된 경우 | 설명 |
| --- | --- | --- | --- | --- |
| **x-ms-요청 요금** | double | 11.3243 | Success and Failure | 부분 응답 메시지에 대해 [요청 단위(RU/s 또는 RU)에서](request-units.md) 사용되는 수집 또는 데이터베이스 처리량의 양입니다. 이 헤더는 청크가 여러 개인 요청에 대한 모든 연속에 존재합니다. 특정 응답 청크의 요금을 반영합니다. 단일 응답 청크로 구성된 요청에 대해서만 이 헤더는 총 통과 비용과 일치합니다. 그러나 대부분의 복잡한 순회에 대해 이 값은 부분 비용을 나타냅니다. |
| **x-ms-총 요청 요금** | double | 423.987 | Success and Failure | 전체 요청에 대해 [요청 단위(RU/s 또는 RU)에서](request-units.md) 사용되는 수집 또는 데이터베이스 처리량입니다. 이 헤더는 청크가 여러 개인 요청에 대한 모든 연속에 존재합니다. 요청 시작 이후 누적 요금을 나타냅니다. 마지막 청크에서 이 헤더의 값은 완전한 요청 요금을 나타냅니다. |
| **x-ms-서버 시간-ms** | double | 13.75 | Success and Failure | 이 헤더는 대기 시간 문제 해결을 위해 포함되어 있습니다. Cosmos DB Gremlin 서버가 부분 응답 메시지를 실행하고 생성하는 데 걸린 시간(밀리초)을 나타냅니다. 이 헤더의 값을 사용하고 이를 전체 요청 대기 시간 응용 프로그램과 비교하면 네트워크 대기 시간 오버헤드를 계산할 수 있습니다. |
| **x-ms-총 서버 시간-ms** | double | 130.512 | Success and Failure | 총 시간(밀리초)으로 코스모스 DB 그렘린 서버가 전체 통과를 실행하는 데 걸렸습니다. 이 헤더는 모든 부분 응답에 포함됩니다. 요청시작 이후누적 실행 시간을 나타냅니다. 마지막 응답은 총 실행 시간을 나타냅니다. 이 헤더는 클라이언트와 서버를 대기 시간의 원본으로 구분하는 데 유용합니다. 클라이언트의 통과 실행 시간을 이 헤더의 값과 비교할 수 있습니다. |
| **x-ms 상태 코드** | long | 200 | Success and Failure | 헤더는 요청 완료 또는 종료에 대한 내부 이유를 나타냅니다. 응용 프로그램은 이 헤더의 값을 보고 수정 작업을 하는 것이 좋습니다. |
| **x-ms-하위 상태 코드** | long | 1003 | 실패만 | 코스모스 DB는 통합 스토리지 계층 위에 구축된 다중 모델 데이터베이스입니다. 이 헤더에는 고가용성 스택의 하위 계층 내에서 오류가 발생하는 오류 이유에 대한 추가 인사이트가 포함되어 있습니다. Cosmos DB 고객 지원에 문의할 때 이 헤더를 저장하고 사용하는 것이 좋습니다. 이 헤더의 값은 코스모스 DB 엔지니어가 빠른 문제 해결에 유용합니다. |
| **x-ms-재시도 후 ms** | 문자열(시간 범위) | "00:00:03.9500000" | 실패만 | 이 헤더는 .NET [TimeSpan](https://docs.microsoft.com/dotnet/api/system.timespan) 형식의 문자열 표현입니다. 이 값은 프로비저닝된 처리량 고갈로 인해 실패한 요청에만 포함됩니다. 신청서는 지시된 기간 이후에 다시 통과를 다시 제출해야 합니다. |
| **x-ms-활동 ID** | 문자열(Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Success and Failure | 헤더에는 요청의 고유한 서버 측 식별자가 포함되어 있습니다. 각 요청에는 추적을 위해 서버에서 고유 식별자가 할당됩니다. 응용 프로그램은 고객이 고객 지원에 문의할 수 있는 요청에 대해 서버에서 반환하는 활동 식별자를 기록해야 합니다. 코스모스 DB 지원 담당자는 코스모스 DB 서비스 원격 분석에서 이러한 식별자에 의해 특정 요청을 찾을 수 있습니다. |

## <a name="status-codes"></a>상태 코드

서버에서 반환하는 가장 일반적인 상태 코드는 다음과 같습니다.

| 상태 | 설명 |
| --- | --- |
| **401** | 인증 `"Unauthorized: Invalid credentials provided"` 암호가 Cosmos DB 계정 키와 일치하지 않으면 오류 메시지가 반환됩니다. Azure 포털에서 Cosmos DB Gremlin 계정으로 이동하여 키가 올바른지 확인합니다.|
| **404** | 동일한 모서리 또는 정점을 동시에 삭제하고 업데이트하려고 시도하는 동시 작업입니다. `"Owner resource does not exist"` 오류 메시지는 지정된 데이터베이스 또는 컬렉션이 `/dbs/<database name>/colls/<collection or graph name>` 형식의 연결 매개 변수에서 올바르지 않다는 것을 의미합니다.|
| **408** | `"Server timeout"`는 통과에 **30초** 이상 걸렸으며 서버에서 취소했음을 나타냅니다. 모든 탐색 홉에서 정점이나 가장자리를 필터링하여 탐색 범위를 좁혀 빠르게 실행하도록 다각을 최적화합니다.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` 이 문제는 식별자가 있는 꼭짓점 또는 에지가 이미 그래프에 있는 경우에 발생합니다.| 
| **412** | 상태 코드는 오류 메시지로 `"PreconditionFailedException": One of the specified pre-condition is not met`보완됩니다. 이 오류는 가장자리 또는 정점을 읽고 수정 후 저장소에 다시 쓰는 것 사이의 낙관적 동시성 제어 위반을 나타냅니다. 이 오류가 발생하는 가장 일반적인 상황은 속성 `g.V('identifier').property('name','value')`수정(예: 예: )입니다. 그렘린 엔진은 정점을 읽고 수정한 다음 다시 작성합니다. 동일한 정점이나 가장자리를 작성하려고 병렬로 실행중인 다른 순회가 있는 경우 그 중 하나가 이 오류를 받게 됩니다. 응용 프로그램은 서버에 다시 트래버스를 제출해야 합니다.| 
| **429** | 요청이 제한되었으며 **x-ms-retry-after-ms** 값 이후에 다시 시도해야 합니다.| 
| **500** | `"NotFoundException: Entity with the specified id does not exist in the system."`이 포함된 오류 메시지는 데이터베이스 및/또는 컬렉션이 같은 이름으로 다시 생성되었음을 나타냅니다. 변경 내용이 전파되면 5분 내에 이 오류가 사라지고 다른 Cosmos DB 구성 요소에서 캐시가 무효화됩니다. 이 문제를 방지하려면 매번 고유한 데이터베이스 및 컬렉션 이름을 사용합니다.| 
| **1000** | 이 상태 코드는 서버가 메시지를 구문 분석했지만 실행할 수 없을 때 반환됩니다. 일반적으로 쿼리에 문제가 있음을 나타냅니다.| 
| **1001** | 이 코드는 서버가 통과 실행을 완료하지만 클라이언트에 대한 응답을 직렬화하지 못하면 반환됩니다. 이 오류는 통과가 복잡한 결과를 생성하거나 너무 크거나 TinkerPop 프로토콜 사양을 준수하지 않을 때 발생할 수 있습니다. 응용 프로그램은 이 오류가 발생할 때 통과를 단순화해야 합니다. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`통과가 허용된 메모리 제한을 초과하면 반환됩니다. 메모리 제한은 통과당 **2GB입니다.**| 
| **1004** | 이 상태 코드는 잘못된 그래프 요청을 나타냅니다. 요청이 역직렬화에 실패하거나 비값 형식이 값 유형으로 역직렬화되거나 지원되지 않는 그렘린 작업이 요청될 때 잘못 형성될 수 있습니다. 응용 프로그램은 성공하지 못하여 요청을 다시 시도해서는 안 됩니다. | 
| **1007** | 일반적으로 이 상태 코드는 `"Could not process request. Underlying connection has been closed."`오류 메시지와 함께 반환됩니다. 이 상황은 클라이언트 드라이버가 서버에서 닫히는 연결을 사용하려고 할 때 발생할 수 있습니다. 응용 프로그램은 다른 연결에서 통과를 다시 시도해야 합니다.
| **1008** | 코스모스 DB 그렘린 서버는 클러스터의 트래픽 균형을 조정하기 위해 연결을 종료할 수 있습니다. 클라이언트 드라이버는 이 상황을 처리하고 라이브 연결만 사용하여 서버에 요청을 보내야 합니다. 경우에 따라 클라이언트 드라이버가 연결이 종료된 것을 감지하지 못할 수 있습니다. 응용 프로그램에 오류가 `"Connection is too busy. Please retry after sometime or open more connections."` 발생하면 다른 연결에서 순회를 다시 시도해야 합니다.

## <a name="samples"></a>샘플

하나의 상태 특성을 읽는 Gremlin.Net 기반으로 하는 샘플 클라이언트 응용 프로그램:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Gremlin java 클라이언트에서 상태 특성을 읽는 방법을 보여 주는 예제입니다.

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>다음 단계
* [Azure 코스모스 DB에 대한 HTTP 상태 코드](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [일반적인 Azure 코스모스 DB REST 응답 헤더](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [TinkerPop 그래프 드라이버 공급자 요구 사항]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
