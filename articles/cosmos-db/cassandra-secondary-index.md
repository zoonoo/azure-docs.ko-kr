---
title: Azure 코스모스 DB 카산드라 API 계정의 인덱싱
description: Azure Azure Cosmos DB Cassandra API 계정에서 보조 인덱싱이 어떻게 작동하는지 알아봅니다.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758028"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Azure 코스모스 DB 카산드라 API의 보조 인덱싱

Azure Cosmos DB의 Cassandra API는 기본 인덱싱 인프라를 활용하여 플랫폼에 내재된 인덱싱 강도를 노출합니다. 그러나 핵심 SQL API와 달리 Azure Cosmos DB의 Cassandra API는 기본적으로 모든 특성을 인덱싱하지 않습니다. 대신 보조 인덱싱을 지원하여 아파치 카산드라와 동일한 방식으로 작동되는 특정 속성에 대한 인덱스를 만듭니다.  

일반적으로 분할되지 않은 열에서 필터 쿼리를 실행하는 것은 권장되지 않습니다. 허용 필터링 구문을 명시적으로 사용해야 하므로 작업이 잘 수행되지 않을 수 있습니다. Azure Cosmos DB에서는 파티션 간에 팬아웃하여 결과를 검색하기 때문에 낮은 카디널리티 특성에 대해 이러한 쿼리를 실행할 수 있습니다.

자주 업데이트되는 열에 인덱스를 만드는 것은 권장되지 않습니다. 테이블을 정의할 때 인덱스를 만드는 것이 신중합니다. 이렇게 하면 데이터와 인덱스가 일관된 상태로 유지됩니다. 기존 데이터에 새 인덱스를 만드는 경우 현재 테이블에 대한 인덱스 진행률 변경 을 추적할 수 없습니다. 이 작업의 진행 률을 추적해야 하는 경우 [지원 티켓을]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)통해 진행률 변경을 요청해야 합니다.


> [!NOTE]
> 보조 인덱스는 다음 개체에서 지원되지 않습니다.
> - 고정된 컬렉션 유형, 소수점 및 변형 유형과 같은 데이터 형식입니다.
> - 정적 열
> - 클러스터링 키

## <a name="indexing-example"></a>인덱싱 예제

먼저 CQL 셸 프롬프트에서 다음 명령을 실행하여 샘플 키스페이스 및 테이블을 만듭니다.

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

그런 다음 다음 명령을 사용하여 샘플 사용자 데이터를 삽입합니다.

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

다음 문을 실행하려고 하면 다음을 사용하도록 `ALLOW FILTERING`요청하는 오류가 발생합니다. 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Cassandra API는 허용 필터링을 지원하지만 이전 섹션에서 설명한 대로 필터링을 허용하지 않는 것은 권장되지 않습니다. 대신 다음 예제와 같이 인덱스를 만들어야 합니다.

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
"성" 필드에 인덱스를 만든 후 이전 쿼리를 성공적으로 실행할 수 있습니다. Azure Cosmos DB의 Cassandra API를 사용하면 인덱스 이름을 제공할 필요가 없습니다. 형식이 `tablename_columnname_idx` 있는 기본 인덱스가 사용됩니다. 예를 들어 ` t1_lastname_idx` 이전 테이블의 인덱스 이름입니다.

## <a name="dropping-the-index"></a>인덱스 삭제 
인덱스를 삭제하려면 인덱스 이름이 무엇인지 알아야 합니다. `desc schema` 명령을 실행하여 테이블에 대한 설명을 가져옵니다. 이 명령의 출력에는 형식의 `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`인덱스 이름이 포함됩니다. 그런 다음 다음 예제와 같이 인덱스 이름을 사용하여 인덱스를 삭제할 수 있습니다.

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB에서 [자동 인덱싱의](index-overview.md) 작동 방식 알아보기
* [아파치 카산드라 는 Azure 코스모스 DB 카산드라 API에 의해 지원 기능](cassandra-support.md)
