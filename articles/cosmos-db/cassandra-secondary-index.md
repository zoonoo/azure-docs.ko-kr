---
title: Azure Cosmos DB Cassandra API 계정의 인덱싱
description: Azure Azure Cosmos DB Cassandra API 계정에서 보조 인덱싱이 작동 하는 방식을 알아봅니다.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80758028"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API의 보조 인덱싱

Azure Cosmos DB의 Cassandra API는 기본 인덱싱 인프라를 활용 하 여 플랫폼에 내재 된 인덱싱 수준을 노출 합니다. 그러나 핵심 SQL API와 달리 Azure Cosmos DB의 Cassandra API는 기본적으로 모든 특성을 인덱싱하지 않습니다. 대신, Apache Cassandra와 동일한 방식으로 동작 하는 특정 특성에 대 한 인덱스를 만들기 위한 보조 인덱싱을 지원 합니다.  

일반적으로 분할 되지 않은 열에 대해서는 필터 쿼리를 실행 하지 않는 것이 좋습니다. 허용 필터링 구문을 명시적으로 사용 해야 합니다. 이렇게 하면 작업이 정상적으로 수행 되지 않을 수 있습니다. Azure Cosmos DB에서 결과를 검색 하기 위해 파티션 간에 팬 하므로 낮은 카디널리티 특성에 대해 이러한 쿼리를 실행할 수 있습니다.

자주 업데이트 되는 열에 인덱스를 만드는 것은 권장 되지 않습니다. 테이블을 정의할 때 인덱스를 만드는 것이 좋습니다. 이렇게 하면 데이터와 인덱스가 일관 된 상태를 유지 합니다. 기존 데이터에 새 인덱스를 만드는 경우 현재 테이블의 인덱스 진행률 변경을 추적할 수 없습니다. 이 작업에 대 한 진행률을 추적 해야 하는 경우 [지원 티켓]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 통해 진행률 변경을 요청 해야 합니다.


> [!NOTE]
> 보조 인덱스는 다음 개체에서 지원 되지 않습니다.
> - 고정 된 컬렉션 형식, decimal 및 variant 형식과 같은 데이터 형식입니다.
> - 정적 열
> - 클러스터링 키

## <a name="indexing-example"></a>인덱싱 예

먼저 CQL shell 프롬프트에서 다음 명령을 실행 하 여 sample keyspace 및 table을 만듭니다.

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

그런 다음, 다음 명령을 사용 하 여 샘플 사용자 데이터를 삽입 합니다.

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

다음 문을 실행 하려고 하면를 사용 하 라는 오류가 발생 합니다 `ALLOW FILTERING` . 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

이전 섹션에서 설명한 것 처럼 Cassandra API는 필터링 허용을 지원 하지만 권장 되지는 않습니다. 대신 다음 예제와 같이에 인덱스를 만들어야 합니다.

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
"Lastname" 필드에 인덱스를 만든 후에는 이제 이전 쿼리를 성공적으로 실행할 수 있습니다. Azure Cosmos DB에서 Cassandra API 인덱스 이름을 제공 하지 않아도 됩니다. 형식이 인 기본 인덱스가 `tablename_columnname_idx` 사용 됩니다. 예를 들어 ` t1_lastname_idx` 은 이전 테이블의 인덱스 이름입니다.

## <a name="dropping-the-index"></a>인덱스 삭제 
인덱스를 삭제할 인덱스 이름을 알고 있어야 합니다. 명령을 실행 `desc schema` 하 여 테이블에 대 한 설명을 가져옵니다. 이 명령의 출력에는 형식의 인덱스 이름이 포함 됩니다 `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)` . 그런 다음 인덱스 이름을 사용 하 여 다음 예제와 같이 인덱스를 삭제할 수 있습니다.

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>다음 단계
* 에서 [자동 인덱싱이](index-overview.md) 작동 하는 방식을 알아봅니다 Azure Cosmos DB
* [Azure Cosmos DB Cassandra API에서 지원하는 Apache Cassandra 기능](cassandra-support.md)
