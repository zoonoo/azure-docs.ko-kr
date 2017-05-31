---
title: "Azure Cosmos DB 자습서: Apache TinkerPops Gremlin 콘솔에서 만들기, 쿼리하기 및 트래버스 | Microsoft Docs"
description: "Azure Cosmos DB 빠른 시작은 Azure Cosmos DB Graph API를 사용하여 꼭짓점, 에지 및 쿼리를 만듭니다."
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: terminal
ms.topic: hero-article
ms.date: 05/19/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: caf3b69b25ccd15322054a0bbf95fc2a5816e00a
ms.contentlocale: ko-kr
ms.lasthandoff: 05/20/2017


---
# <a name="azure-cosmos-db-create-query-and-traverse-a-graph-in-the-gremlin-console"></a>Azure Cosmos DB: Gremlin 콘솔에서 그래프 만들기, 쿼리 및 트래버스

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Cosmos DB 계정, 데이터베이스 및 그래프(컨테이너)를 만들고 [Apache TinkerPop](http://tinkerpop.apache.org)의 [Gremlin 콘솔](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console)을 사용하여 Graph API(미리 보기) 데이터를 사용하는 방법을 보여줍니다. 이 자습서에서는 꼭짓점 및 에지를 만들고 쿼리하며 꼭짓점 속성을 업데이트하고 꼭짓점을 쿼리하고 그래프를 트래버스하고 꼭짓점을 삭제합니다.

![Apache Gremlin 콘솔의 Azure Cosmos DB](./media/create-graph-gremlin-console/gremlin-console.png)

Gremlin 콘솔은 Groovy/Java 기반이며 Linux, Mac 및 Windows에서 실행됩니다. [Apache TinkerPop 사이트](https://www.apache.org/dyn/closer.lua/tinkerpop/3.2.4/apache-tinkerpop-gremlin-console-3.2.4-bin.zip)에서 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서 Azure Cosmos DB 계정을 만들려면 Azure 구독이 있어야 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

또한 [Gremlin 콘솔](http://tinkerpop.apache.org/)을 설치해야 합니다. 버전 3.2.4 이상을 사용합니다.

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>그래프 추가

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a id="ConnectAppService"></a>앱 서비스에 연결
1. Gremlin 콘솔을 시작하기 전에 *apache-tinkerpop-gremlin-console-3.2.4/conf* 디렉터리에서 *remote-secure.yaml* 구성 파일을 만들거나 수정합니다.
2. *호스트*, *포트*, *사용자 이름*, *암호*, *connectionPool* 및 *serializer* 구성을 입력합니다.

    설정|제안 값|설명
    ---|---|---
    호스트|***.graphs.azure.com|그래프 서비스 URI는 Azure Portal에서 검색할 수 있습니다.
    포트|443|443으로 설정
    사용자 이름|*사용자 이름*|리소스 양식은 `/dbs/<db>/colls/<coll>`입니다.
    암호|*기본 마스터 키*|Azure Cosmos DB에 대한 기본 마스터 키
    ConnectionPool|{enableSsl: true}|SSL에 대한 연결 풀 설정
    serializer|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|이 값으로 설정

3. 터미널에서 *bin/gremlin.bat* 또는 *bin/gremlin.sh*를 실행하여 [Gremlin 콘솔](http://tinkerpop.apache.org/docs/3.2.4/tutorials/getting-started/)을 시작합니다.
4. 터미널에서 *:remote connect tinkerpop.server conf/remote-secure.yaml*을 실행하여 앱 서비스에 연결합니다.

잘하셨습니다. 설정을 완료했으므로 콘솔 명령을 실행해 보겠습니다.

## <a name="create-vertices-and-edges"></a>꼭짓점 및 에지 만들기

*Thomas*, *Mary Kay*, *로빈* 및 *Ben*이라는 4명의 사용자 꼭짓점을 추가하여 시작해 보겠습니다.

입력(Thomas):

```
:> g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

출력:

```
==>[id:1eb91f79-94d7-4fd4-b026-18f707952f21,label:person,type:vertex,properties:[firstName:[[id:ec5fcfbe-040e-48c3-b961-31233c8b1801,value:Thomas]],lastName:[[id:86e5b580-0bca-4bc2-bc53-a46f92c1a182,value:Andersen]],age:[[id:2caeab3c-c66d-4098-b673-40a8101bb72a,value:44]]]]
```
입력(Mary Kay):

```
:> g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39)
```

출력:

```
==>[id:899a9d37-6701-48fc-b0a1-90950be7e0f4,label:person,type:vertex,properties:[firstName:[[id:c79c5599-8646-47d1-9a49-3456200518ce,value:Mary Kay]],lastName:[[id:c1362095-9dcc-479d-ab21-86c1b6d4ffc1,value:Andersen]],age:[[id:0b530408-bfae-4e8f-98ad-c160cd6e6a8f,value:39]]]]
```

입력(Robin):

```
:> g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield')
```

출력:

```
==>[id:953aefd9-5a54-4033-9b3a-d4dc3049f720,label:person,type:vertex,properties:[firstName:[[id:bbda02e0-8a96-4ca1-943e-621acbb26824,value:Robin]],lastName:[[id:f0291ad3-05a3-40ec-aabb-6538a7c331e3,value:Wakefield]]]]
```

입력(Ben):

```
:> g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller')
```

출력:

```
==>[id:81c891d9-beca-4c87-9009-13a826c9ed9a,label:person,type:vertex,properties:[firstName:[[id:3a3b53d3-888c-46da-bb54-1c42194b1e18,value:Ben]],lastName:[[id:48c6dd50-79c4-4585-ab71-3bf998061958,value:Miller]]]]
```

다음으로, 사용자 간의 관계에 에지를 추가해 보겠습니다.

입력(Thomas -> Mary Kay):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

출력:

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

입력(Thomas -> Robin):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

출력:

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

입력(Robin -> Ben):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

출력:

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>꼭짓점 업데이트

*45*세라는 나이로 *Thomas* 꼭짓점을 업데이트하겠습니다.

입력:
```
:> g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
출력:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>그래프 쿼리

이제 그래프에 대한 다양한 쿼리를 실행해 보겠습니다.

먼저 40세 이상인 사용자만을 반환하는 필터가 포함된 쿼리를 사용해 보겠습니다.

입력(필터 쿼리):

```
:> g.V().hasLabel('person').has('age', gt(40))
```

출력:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

다음으로, 40세 이상인 사용자의 이름을 프로젝트하겠습니다.

입력(필터 + 프로젝션 쿼리):

```
:> g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

출력:

```
==>Thomas
```

## <a name="traverse-your-graph"></a>그래프 트래버스

Thomas의 친구를 모두 반환하는 그래프를 트래버스하겠습니다.

입력(Thomas의 친구):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

출력: 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

다음으로 꼭짓점 다음 계층을 가져와 보겠습니다. Thomas의 친구의 친구를 모두 반환하는 그래프를 트래버스합니다.

입력(Thomas의 친구의 친구):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
출력:

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>꼭짓점 삭제

이제 그래프 데이터베이스에서 꼭짓점을 삭제하겠습니다.

입력(로빈 꼭짓점 삭제):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').drop()
```

## <a name="clear-your-graph"></a>그래프 정리

마지막으로 모든 꼭짓점 및 에지의 데이터베이스를 정리해 보겠습니다.

입력:

```
:> g.V().drop()
```

축하합니다. 이 Azure Cosmos DB: Graph API 자습서를 완료했습니다.

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 앱을 계속 사용하지 않으려면 Azure Portal에서 다음 단계에 따라 이 빠른 시작에서 만든 리소스를 모두 삭제합니다.  

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 이름을 클릭합니다. 
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Cosmos DB Azure 계정을 만들고, 데이터 탐색기를 사용하여 그래프를 만들고, 꼭짓점과 에지를 만들고, Gremlin 콘솔을 사용하여 그래프를 트래버스하는 방법을 알아보았습니다. 이제 Gremlin을 사용하여 더 복잡한 쿼리를 작성하고 강력한 그래프 순회 논리를 구현할 수 있습니다. 

> [!div class="nextstepaction"]
> [Gremlin을 사용하여 쿼리](tutorial-query-graph.md)
