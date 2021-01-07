---
title: Azure Cosmos DB Cassandra API 분할
description: Azure Cosmos DB 분할에 대해 알아봅니다 Cassandra API
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ba615d3e41393afe007238a0fe1e694732ad123e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087641"
---
# <a name="partitioning-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API 분할
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

이 문서에서는 Azure Cosmos DB Cassandra API에서 분할이 작동 하는 방식을 설명 합니다. 

Cassandra API는 분할을 사용 하 여 응용 프로그램의 성능 요구에 맞게 keyspace의 개별 테이블 크기를 조정 합니다. 파티션은 테이블의 각 레코드와 연결 된 파티션 키의 값에 따라 형성 됩니다. 파티션의 모든 레코드는 동일한 파티션 키 값을 갖습니다. Azure Cosmos DB는 테이블의 확장성 및 성능 요구를 효율적으로 충족 하기 위해 물리적 리소스에서 파티션 배치를 투명 하 고 자동으로 관리 합니다. 응용 프로그램의 처리량 및 저장소 요구 사항이 증가 하면 더 많은 수의 물리적 컴퓨터에서 데이터를 이동 하 고 분산 Azure Cosmos DB.

개발자 관점에서 분할은 네이티브 [Apache Cassandra](https://cassandra.apache.org/)와 동일한 방식으로 Cassandra API Azure Cosmos DB와 동일한 방식으로 작동 합니다. 그러나 내부적으로는 약간의 차이가 있습니다. 


## <a name="differences-between-apache-cassandra-and-azure-cosmos-db"></a>Apache Cassandra와 Azure Cosmos DB 간의 차이점

Azure Cosmos DB에서 파티션이 저장 되는 각 컴퓨터는 [실제 파티션이](partitioning-overview.md#physical-partitions)라고도 합니다. 실제 파티션은 가상 머신과 유사 합니다. 전용 계산 단위 또는 물리적 리소스 집합입니다. 이 계산 단위에 저장 된 각 파티션은 Azure Cosmos DB [논리 파티션](partitioning-overview.md#logical-partitions) 이라고 합니다. Apache Cassandra에 대해 잘 알고 있는 경우 Cassandra의 일반 파티션과 동일한 방식으로 논리 파티션을 생각해 볼 수 있습니다. 

Apache Cassandra은 파티션에 저장할 수 있는 데이터 크기에 대해 100의 제한을 권장 합니다. Azure Cosmos DB에 대 한 Cassandra API는 논리적 파티션당 최대 20gb, 실제 파티션당 최대 30GB의 데이터를 허용 합니다. Azure Cosmos DB Apache Cassandra와 달리 실제 파티션에서 사용 가능한 계산 용량은 [요청 단위](request-units.md)라는 단일 메트릭을 사용 하 여 표현 됩니다 .이 메트릭을 사용 하면 코어, 메모리 또는 IOPS가 아닌 초당 요청 (읽기 또는 쓰기)의 측면에서 워크 로드를 고려할 수 있습니다. 이렇게 하면 각 요청에 대 한 비용을 이해 하 고 나면 용량을 보다 효율적으로 계획할 수 있습니다. 각 실제 파티션에는 사용할 수 있는 계산의 최대 1만 RUs가 있을 수 있습니다. 확장성 옵션에 대 한 자세한 내용은 Cassandra API에서 [탄력적 확장](manage-scale-cassandra.md) 에 대 한 문서를 참조 하세요. 

Azure Cosmos DB에서 각 실제 파티션은 복제본 집합이 라고도 하는 복제본 집합으로 구성 되며 파티션 당 복제본이 4 개 이상 포함 됩니다. 이는 복제 인수를 1로 설정 하는 것이 가능한 Apache Cassandra와는 대조적입니다. 그러나 데이터가 있는 유일한 노드가 중단 되 면 가용성이 낮아집니다. Cassandra API의 복제 인수는 항상 4 (쿼럼을 3)입니다. Azure Cosmos DB는 자동으로 복제본 집합을 관리 하지만 Apache Cassandra의 다양 한 도구를 사용 하 여 유지 해야 합니다. 

Apache Cassandra에는 파티션 키의 해시로 토큰 개념이 있습니다. 토큰은 murmur3 64 바이트 해시를 기반으로 하며, 값은-2 ^ 63에서-2 ^ 63-1 사이입니다. 이 범위는 일반적으로 Apache Cassandra에서 "토큰 링" 이라고 합니다. 토큰 링은 토큰 범위에 배포 되 고 이러한 범위는 네이티브 Apache Cassandra 클러스터에 있는 노드 간에 분할 됩니다. Azure Cosmos DB에 대 한 분할은 다른 해시 알고리즘을 사용 하 고 내부 토큰 링이 더 크다는 점을 제외 하 고 비슷한 방식으로 구현 됩니다. 그러나 외부에서는 Apache Cassandra와 같은 토큰 범위 (예:-2 ^ 63 ~-2 ^ 63-1)를 제공 합니다.


## <a name="primary-key"></a>기본 키

Cassandra API의 모든 테이블에는가 정의 되어 있어야 합니다 `primary key` . 기본 키에 대 한 구문은 다음과 같습니다.

```shell
column_name cql_type_definition PRIMARY KEY
```

여러 사용자에 대 한 메시지를 저장 하는 사용자 테이블을 만들려고 한다고 가정 합니다.

```shell
CREATE TABLE uprofile.user ( 
   id UUID PRIMARY KEY, 
   user text,  
   message text);
```

이 디자인에서는 `id` 필드를 기본 키로 정의 했습니다. 기본 키는 테이블의 레코드에 대 한 식별자로 함수를 사용 하며 Azure Cosmos DB에서 파티션 키로도 사용 됩니다. 기본 키가 앞에서 설명한 방식으로 정의 된 경우 각 파티션에는 단일 레코드만 있습니다. 이렇게 하면 데이터베이스에 데이터를 쓸 때 완벽 하 게 수평이 고 확장 가능한 배포가 발생 하며 키-값 조회 사용 사례에 적합 합니다. 읽기 성능을 최대화 하기 위해 테이블에서 데이터를 읽을 때마다 응용 프로그램에서 기본 키를 제공 해야 합니다. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning.png" alt-text="영역" border="false":::


## <a name="compound-primary-key"></a>복합 기본 키

Apache Cassandra의 개념도  `compound keys` 있습니다. 복합은 `primary key` 두 개 이상의 열로 구성 되며, 첫 번째 열은이 `partition key` 고, 추가 열은 `clustering keys` 입니다. 에 대 한 구문은 `compound primary key` 다음과 같습니다.

```shell
PRIMARY KEY (partition_key_column_name, clustering_column_name [, ...])
```

위의 디자인을 변경 하 고 지정 된 사용자에 대 한 메시지를 효율적으로 검색할 수 있도록 하려는 경우를 가정해 보겠습니다.

```shell
CREATE TABLE uprofile.user (
   user text,  
   id int, 
   message text, 
   PRIMARY KEY (user, id));
```

이 디자인에서는 이제를 `user` 파티션 키로,를 클러스터링 키로 정의 `id` 합니다. 원하는 수의 클러스터링 키를 정의할 수 있지만 클러스터링 키에 대 한 각 값 (또는 값의 조합)은 같은 파티션에 여러 레코드를 추가 하기 위해 고유 해야 합니다. 예를 들면 다음과 같습니다.

```shell
insert into uprofile.user (user, id, message) values ('theo', 1, 'hello');
insert into uprofile.user (user, id, message) values ('theo', 2, 'hello again');
```

반환 된 데이터는 Apache Cassandra에서 예상 대로 클러스터링 키를 기준으로 정렬 됩니다.

:::image type="content" source="./media/cassandra-partitioning/select-from-pk.png" alt-text="영역":::

이러한 방식으로 모델링 된 데이터를 사용 하면 각 파티션에 여러 레코드를 할당 하 고 사용자별로 그룹화 할 수 있습니다. 따라서 `partition key` `user` 지정 된 사용자에 대 한 모든 메시지를 가져오기 위해 (이 경우)에서 효율적으로 라우팅되는 쿼리를 실행할 수 있습니다. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning2.png" alt-text="영역" border="false":::


## <a name="composite-partition-key"></a>복합 파티션 키

복합 파티션 키는 복합 파티션 키로 여러 열을 지정할 수 있다는 점을 제외 하 고 기본적으로 복합 키와 동일한 방식으로 작동 합니다. 복합 파티션 키 구문은 다음과 같습니다.

```shell
PRIMARY KEY (
   (partition_key_column_name[, ...]), 
    clustering_column_name [, ...]);
```
예를 들어 및의 고유 조합이 `firstname` `lastname` 파티션 키를 형성 하 고가 클러스터링 키인 다음을 사용할 수 있습니다 `id` .

```shell
CREATE TABLE uprofile.user ( 
   firstname text, 
   lastname text,
   id int,  
   message text, 
   PRIMARY KEY ((firstname, lastname), id) );
```

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 분할 및 수평 확장](partitioning-overview.md)에 대해 알아봅니다.
* [Azure Cosmos DB에서 프로 비전 된 처리량](request-units.md)에 대해 알아봅니다.
* [Azure Cosmos DB의 글로벌 배포](distribute-data-globally.md)에 대해 알아봅니다.
