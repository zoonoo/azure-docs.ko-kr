---
title: Azure Cosmos DB의 Gremlin API에 대 한 질문과 대답
description: Azure Cosmos DB의 Gremlin API에 대 한 질문과 대답을 확인 하세요.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 416cf4f027b6f1e72641324be39ba0304301db37
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614500"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Azure Cosmos DB의 Gremlin API에 대 한 질문과 대답

이 문서에서는 Azure Cosmos DB의 Gremlin API에 대 한 몇 가지 자주 묻는 질문에 대 한 답변을 설명 합니다.

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>Gremlin 쿼리의 효율성을 평가 하는 방법

**executionProfile()** 미리 보기 단계를 사용하여 쿼리 실행 계획에 대한 분석을 제공할 수 있습니다. 이 단계는 다음 예와 같이 Gremlin 쿼리 끝에 추가해야 합니다.

**쿼리 예**

```
g.V('mary').out('knows').executionProfile()
```

**예제 출력**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

위의 프로필 출력에는 꼭 짓 점 개체,에 지 개체 및 작업 데이터 집합의 크기를 가져오는 데 소요 된 시간이 표시 됩니다. 이는 Azure Cosmos DB 쿼리에 대한 표준 비용 측정과 관련이 있습니다.

## <a name="other-frequently-asked-questions"></a>기타 자주 묻는 질문

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>그래프 데이터베이스에서 쿼리 실행 시 RU/s는 어떻게 청구되나요?

모든 그래프 개체, 꼭짓점 및 모서리는 백 엔드에서 JSON 문서로 표시됩니다. 하나의 Gremlin 쿼리가 한 번에 하나 이상의 그래프 개체를 수정할 수 있으므로 쿼리와 연결된 비용은 쿼리에서 처리하는 개체인 모서리와 직접 관련이 있습니다. 이것은 Azure Cosmos DB가 모든 다른 API에 사용하는 것과 동일한 프로세스입니다. 자세한 내용은 [Azure Cosmos DB의 요청 단위](request-units.md)를 참조 하세요.

RU 청구는 결과 집합이 아닌 횡단의 유효 데이터 집합에 기반합니다. 예를 들어 쿼리가 결과로 단일 꼭짓점을 획득하려고 하지만 도중에 둘 이상의 다른 개체를 트래버스해야 한다면 비용은 한 개의 결과 꼭짓점을 컴퓨팅하기 위해 사용되는 모든 그래프 개체에 기반합니다.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Graph 데이터베이스가 Azure Cosmos DB Gremlin API에서 가질 수 있는 최대 크기는 얼마 인가요?

Azure Cosmos DB는 [수평 분할](partition-data.md)을 활용하여 스토리지 및 처리량 요구 사항 증가를 자동으로 해결합니다. 작업의 최대 처리량 및 저장소 용량은 지정 된 컨테이너와 연결 된 파티션 수에 따라 결정 됩니다. 그러나 Gremlin API 컨테이너에는 적절 한 규모의 성능 환경을 보장 하기 위한 특정 지침 집합이 있습니다. 분할에 대한 자세한 내용 및 모범 사례는 [Azure Cosmos DB에서 분할](partition-data.md) 문서를 참조하세요.

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>C#.NET 개발의 경우 Microsoft.Azure.Graphs 패키지 또는 Gremlin.NET를 사용해야 하나요?

Azure Cosmos DB Gremlin API는 서비스의 기본 커넥터로 오픈 소스 드라이버를 활용합니다. 따라서 권장되는 옵션은 [Apache Tinkerpop에서 지원하는 드라이버](https://tinkerpop.apache.org/)를 사용하는 것입니다.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Gremlin 드라이버를 사용하여 삽입 공격을 방지하려면 어떻게 하나요?

대부분의 기본 Apache Tinkerpop Gremlin 드라이버는 쿼리 실행을 위한 매개 변수 사전을 제공 하는 옵션을 허용 합니다. 다음은 [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) 및 [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js)에서 수행하는 방법의 예제입니다.

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>"Gremlin 쿼리 컴파일 오류: 메서드를 찾을 수 없습니다." 오류가 발생 하는 이유는 무엇 인가요?

Azure Cosmos DB Gremlin API는 Gremlin 노출 영역에 정의된 기능의 하위 집합을 구현합니다. 지원되는 단계 및 자세한 내용은 [Gremlin 지원](gremlin-support.md) 문서를 참조하세요.

가장 좋은 해결 방법은 모든 필수 Gremlin 단계가 Azure Cosmos DB에서 지원되므로 지원되는 기능을 사용하여 필요한 Gremlin 단계를 다시 작성하는 것입니다.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>"WebSocketException: 상태 코드 ' 101 '가 필요한 데 서버에서 상태 코드 ' 200 '을 반환 했습니다." 오류가 발생 하는 이유는 무엇 인가요?

이 오류는 잘못된 엔드포인트를 사용하는 경우 발생할 가능성이 높습니다. 이 오류를 생성하는 엔드포인트에는 다음과 같은 패턴이 있습니다.

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

그래프 데이터베이스에 대한 문서 엔드포인트입니다.  사용할 올바른 엔드포인트는 다음 형식의 Gremlin 엔드포인트입니다.

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>"RequestRateIsTooLarge" 오류가 발생 하는 이유는 무엇 인가요?

이 오류는 초당 할당된 요청 단위가 쿼리를 처리할 정도로 충분하지 않다는 것을 의미합니다. 이 오류는 일반적으로 모든 꼭짓점을 가져오는 쿼리를 실행할 때 표시됩니다.

```
// Query example:
g.V()
```

이 쿼리는 그래프에서 모든 꼭짓점을 가져오려고 합니다. 따라서 이 쿼리의 비용은 RU 측면에서 적어도 꼭짓점 개수와 동일하게 됩니다. 초당 RU는 이 쿼리를 해결할 수 있도록 조정되어야 합니다.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>내 Gremlin 드라이버 연결이 갑자가 끊어진 이유는 무엇인가요?

Gremlin 연결은 WebSocket 연결을 통해 이루어집니다. WebSocket 연결에 특정 TTL(Time to Live)이 없을 경우 Azure Cosmos DB Gremlin API는 30분의 비활성 후 유휴 연결을 종료합니다.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>기본 Gremlin 드라이버에서 흐름 API 호출을 사용할 수 없는 이유는 무엇 인가요?

흐름 API 호출은 Azure Cosmos DB Gremlin API에서 아직 지원되지 않습니다. Fluent API 호출은 Azure Cosmos DB Gremlin API에서 현재 지원되지 않는, 바이트코드 지원이라는 내부 서식 지정 기능이 필요합니다. 같은 이유로 인해 최신 Gremlin-JavaScript 드라이버도 현재 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB Gremlin 유선 프로토콜 지원](gremlin-support.md)
* [Gremlin 콘솔](create-graph-gremlin-console.md) 을 사용 하 여 Azure Cosmos DB graph 데이터베이스 만들기, 쿼리 및 트래버스
