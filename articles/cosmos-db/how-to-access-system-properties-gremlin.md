---
title: Azure Cosmos DB 그래프를 통해 시스템 문서 속성 액세스
description: Gremlin API를 통해 Cosmos DB 시스템 문서 속성을 읽고 쓰는 방법을 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 4ed7e67ae0ef027b260d0e0f0407e4e05ed5a8f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898299"
---
# <a name="system-document-properties"></a>시스템 문서 속성

Azure Cosmos DB에는 모든 문서에 ```_ts```, ```_self```, ```_attachments```, ```_rid```및 ```_etag``` 와 같은 [시스템 속성이](https://docs.microsoft.com/rest/api/cosmos-db/databases) 있습니다. 또한 Gremlin 엔진이 모서리에 ```inVPartition``` 및 ```outVPartition``` 속성을 추가합니다. 기본적으로 이러한 속성은 통과에 사용할 수 없습니다. 그러나 Gremlin 통과에 특정 속성이나 모든 속성을 포함할 수 있습니다.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-Tag

이 속성은 낙관적 동시성 제어에 사용됩니다. 애플리케이션이 몇 개의 분리된 통과로 작업을 나누어야 하는 경우 eTag 속성을 사용하여 동시 쓰기에서 데이터 손실을 방지할 수 있습니다.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>TTL(Time to Live)

컬렉션에서 문서 만료가 사용되고 문서에 ```ttl``` 속성이 설정되어 있으면, 이 속성을 일반 꼭짓점 또는 모서리 속성으로 Gremlin 통과에 사용할 수 있습니다. ```ProjectionStrategy```는 TTL(Time to Live) 속성 노출을 사용하도록 설정하는 데 필요하지 않습니다.

아래 순회로 만들어진 꼭짓점은 **123초** 이내에 자동 삭제됩니다.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>다음 단계
* [Cosmos DB 낙관적 동시성](faq.md#how-does-the-sql-api-provide-concurrency)
* Azure Cosmos DB의 [TTL(Time to Live)](time-to-live.md)
