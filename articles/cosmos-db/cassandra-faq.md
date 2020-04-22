---
title: 카산드라에 대한 Azure 코스모스 DB API에 대한 자주 묻는 질문.
description: 카산드라에 대한 Azure 코스모스 DB API에 대한 자주 묻는 질문에 대한 답변을 얻을 수 있습니다.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 416f0c5f995a101298e84c81317c7d39fb5d43f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727386"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-db-api-for-cassandra"></a>카산드라에 대한 Azure 코스모스 DB API에 대한 자주 묻는 질문

## <a name="what-are-some-key-differences-between-apache-cassandra-and-cassandra-api"></a>아파치 카산드라와 카산드라 API의 주요 차이점은 무엇입니까?

- 아파치 카산드라는 파티션 키의 크기에 대해 100MB 제한을 권장합니다. 카산드라 API는 파티션당 최대 10GB를 허용합니다.
- 아파치 카산드라는 영구 커밋을 비활성화 할 수 있습니다 - 즉, 커밋 로그에 쓰기를 건너 뛰고 Memtable (들)로 직접 이동합니다. 이로 인해 Memtables가 디스크의 SStables로 플러시되기 전에 노드가 다운되면 데이터가 손실될 수 있습니다. Cosmos DB는 항상 내구성커밋을 수행하므로 데이터 손실이 발생하지 않습니다.
- 아파치 카산드라는 워크로드에 많은 교체 및/또는 삭제가 포함된 경우 성능이 저하되는 것을 볼 수 있습니다. 그 이유는 최신 데이터를 가져오기 위해 읽기 워크로드를 건너뛰어야 하는 삭제 표시입니다. Cassandra API는 워크로드에 많은 대체 및/또는 삭제가 있을 때 읽기 성능이 저하되지 않습니다.
- 높은 대체 워크로드 시나리오 동안 디스크에서 SSTables를 병합하기 위해 압축을 실행해야 합니다(아파치 카산드라의 쓰기만 추가되므로 여러 업데이트가 주기적으로 병합해야 하는 개별 SSTable 항목으로 저장되므로 병합이 필요합니다). 이로 인해 압축 중에 읽기 성능이 저하될 수도 있습니다. 카산드라 API에서는 압축을 구현하지 않으므로 이 작업을 수행하지 않습니다.
- 아파치 카산드라를 사용하면 복제 계수를 1로 설정할 수 있습니다. 그러나 데이터가 있는 유일한 노드가 다운되면 가용성이 낮아집니다. 항상 복제 계수가 4(쿼럼 3)이기 때문에 Azure Cosmos DB Cassandra API에서는 문제가 되지 않습니다.
- 아파치 카산드라에서 노드를 추가/제거하려면 많은 수동 개입이 필요하지만 기존 노드는 토큰 범위 중 일부를 새 노드로 이동하는 동안 새 노드의 높은 CPU도 필요합니다. 이는 기존 노드를 해제할 때도 마찬가지입니다. 그러나 확장은 Azure Cosmos DB Cassandra API의 후드 아래에서 서비스/응용 프로그램에서 관찰되는 문제 없이 원활하게 수행됩니다.
- 아파치 카산드라와 같이 클러스터의 각 노드에 num_tokens 설정할 필요가 없습니다. 노드 및 토큰 범위는 코스모스 DB에 의해 완전히 관리됩니다.
- Azure Cosmos DB Cassandra API는 완전히 관리되므로 아파치 카산드라에서 사용되는 복구, 해체 등과 같은 nodetool 명령이 필요하지 않습니다.

## <a name="other-frequently-asked-questions"></a>기타 자주 묻는 질문

### <a name="what-is-the-protocol-version-supported-by-azure-cosmos-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Azure 코스모스 DB 카산드라 API에서 지원하는 프로토콜 버전은 무엇입니까? 다른 프로토콜을 지원할 계획이 있나요?

Azure 코스모스 DB 카산드라 API는 CQL 버전 3.x를 지원합니다. 그것은 CQL 호환성은 공개 [아파치 카산드라 GitHub 리포지토리를](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile)기반으로합니다. 다른 프로토콜 지원에 대한 피드백이 있는 경우 [사용자 의견 피드백](https://feedback.azure.com/forums/263030-azure-cosmos-db)을 통해 알려주시거나 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)으로 메일을 보내주세요.

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>테이블에 대한 처리량을 요구 사항으로 선택하는 이유는 무엇인가요?

Azure Cosmos DB는 테이블을 만드는 위치, 포털 또는 CQL에 따라 컨테이너에 대해 기본 처리량을 설정합니다.
Azure Cosmos DB는 작업에 대한 상한을 사용하여 성능 및 대기 시간을 보장합니다. 이 보장은 엔진이 테넌트의 작업에 대한 거버넌스를 적용할 수 있는 경우 가능합니다. 처리량을 설정하면 플랫폼에서 이 용량을 예약하고 작업 성공을 보장하므로 보장된 처리량 및 대기 시간을 가져올 수 있습니다.
[처리량을 탄력적으로 변경하여](manage-scale-cassandra.md) 응용 프로그램의 계절성을 활용할 수 있고 비용을 절감할 수 있습니다.

처리량 개념은 [Azure Cosmos DB의 요청 단위](request-units.md)에 설명되어 있습니다. 테이블에 대한 처리량은 기본 실제 파티션에 동일하게 분산됩니다.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>CQL을 통해 만들 때 테이블의 기본 RU/s는 얼마인가요? 변경해야 하는 경우 어떻게 해야 하나요?

Azure Cosmos DB는 처리량을 제공하는 기준으로 초당 요청 단위(RU/s)를 사용합니다. CQL을 통해 생성된 테이블의 경우 400RU입니다. 포털에서 이 RU를 변경할 수 있습니다.

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>처리량을 다 사용하면 어떻게 되나요?

Azure Cosmos DB는 작업에 대한 상한을 사용하여 성능 및 대기 시간을 보장합니다. 이 보장은 엔진이 테넌트의 작업에 대한 거버넌스를 적용할 수 있는 경우 가능합니다. 처리량을 설정하면 플랫폼에서 이 용량을 예약하고 작업 성공을 보장하므로 보장된 처리량 및 대기 시간을 가져올 수 있습니다.
이 용량을 초과하면 용량을 초과했다는 오버로드 오류 메시지가 표시됩니다.
0x1001 오버로드됨: “요청 빈도가 높아서” 요청을 처리할 수 없습니다. 이 시점에서 어떤 작업 및 해당 볼륨으로 인해 이 문제가 발생한 것인지 확인해야 합니다. 포털의 메트릭을 통해 프로비전된 용량을 초과하는 사용 용량을 파악할 수 있습니다. 그런 다음 모든 기본 파티션에서 용량이 거의 균등하게 사용되는지 확인해야 합니다. 한 파티션에서 처리량의 대부분을 사용할 경우 워크로드가 한쪽으로 기웁니다.

개별 파티션 또는 전체 파티션이 1시간, 하루, 일주일 동안 처리량을 얼마나 사용하는지 보여주는 메트릭을 사용할 수 있습니다. 자세한 내용은 [Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버깅](use-metrics.md)을 참조하세요.

진단 로그에 대한 내용은 [Azure Cosmos DB 진단 로깅](logging.md) 문서에 설명되어 있습니다.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>기본 키는 Azure Cosmos DB의 파티션 키 개념에 대응하나요?

예, 파티션 키는 엔터티를 올바른 위치에 배치하는 데 사용됩니다. Azure Cosmos DB에서는 물리적 파티션에 저장된 올바른 논리 파티션을 찾는 데 사용됩니다. 분할 개념은 [Azure Cosmos DB의 파티션 및 확장](partition-data.md) 문서에 잘 설명되어 있습니다. 여기서 핵심은 논리 파티션이 현재의 10GB 한도를 초과해서는 안 된다는 것입니다.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>파티션이 가득 찼음을 나타내는 "할당량 가득 참" 알림이 표시되면 어떻게 되나요?

Azure Cosmos DB는 SLA 기반 시스템으로, 대기 시간, 처리량, 가용성 및 일관성을 보장할 뿐만 아니라 무제한 확장을 제공합니다. 이 무제한 스토리지는 분할을 주요 개념으로 사용하는 데이터 수평 확장을 기반으로 합니다. 분할 개념은 [Azure Cosmos DB의 파티션 및 확장](partition-data.md) 문서에 잘 설명되어 있습니다.

논리 파티션당 엔터티 또는 항목 수에 대한 10GB 제한을 준수해야 합니다. 애플리케이션이 원활하게 확장할 수 있도록 하려면 단일 파티션에 대한 모든 정보를 저장 및 쿼리하여 핫 파티션을 만들지 *않는* 것이 좋습니다. 이 오류는 하나의 파티션 키에 너무 많은 데이터가 있어서(10&nbsp;GB 초과) 데이터가 한쪽으로 기울어진 경우에만 발생합니다. 스토리지 포털을 사용하여 데이터 분포를 확인할 수 있습니다. 이 오류를 해결하는 방법은 테이블을 다시 만들고, 보다 균등한 데이터 분포를 허용하는 세분화된 기본 파티션 키를 선택하는 것입니다.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Cassandra API를 수백만 또는 수십 억 개의 개별 파티션 키가 포함된 키 값 저장소로 사용할 수 있나요?

Azure Cosmos DB는 스토리지를 수평으로 확장하여 무제한 데이터를 저장할 수 있습니다. 이는 처리량과는 별개입니다. 예, 올바른 기본/파티션 키를 지정하여 항상 Cassandra API로 키/값을 저장하고 검색할 수 있습니다. 이러한 개별 키는 자체 논리 파티션을 가져와서 아무런 문제 없이 실제 파티션 위에 상주합니다.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Azure Cosmos DB의 Apache Cassandra API를 사용하여 테이블을 둘 이상 만들 수 있나요?

예, Apache Cassandra API를 사용하여 테이블을 둘 이상 만들 수 있습니다. 이러한 각 테이블은 처리량 및 스토리지에 대한 단위로 처리됩니다.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>둘 이상의 테이블을 연속으로 만들 수 있나요?

Azure Cosmos DB는 데이터와 제어 평면 작업 모두에 대한 리소스 관리 시스템입니다. 컬렉션, 테이블 등의 컨테이너는 지정된 처리량 용량에 맞게 프로비전되는 런타임 엔터티입니다. 연속적으로 이러한 컨테이너를 만드는 작업은 올바른 활동이 아니며 제한됩니다. 테이블을 즉시 삭제/생성하는 테스트가 있다면 테이블 사이에 공간을 충분히 확보하세요.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>만들 수 있는 테이블의 최대 수는 얼마인가요?

테이블 수에 물리적 제한이 없으며, 일반적인 10대 또는 100대에서 만들어야 하는 테이블 수가 많은 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 경우(총 정상 크기가 10TBTB가 넘는 경우)에서 전자 메일을 보냅니다.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>키스페이스를 최대 몇 개까지 만들 수 있나요?

어떤 이유로 키 스페이스가 많은 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 경우 메타데이터 컨테이너인 키스페이스 수에 물리적 제한이 없습니다.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>시작한 후에 일반 테이블에서 많은 데이터를 가져올 수 있나요?

예. 균일하게 분산된 파티션을 가정하면 더 많은 데이터를 푸시할 때 저장소 용량이 자동으로 관리되고 증가합니다. 따라서 노드 관리 및 프로비전 등의 작업을 수행하지 않고도 필요한 만큼의 데이터를 안심하고 가져올 수 있습니다. 그러나 즉각적인 데이터 증가를 많이 예상하는 경우 더 낮게 시작하고 즉시 늘리는 것보다 예상 처리량을 직접 [프로비저닝하는](set-throughput.md) 것이 더 합리적입니다.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Azure Cosmos DB 동작의 Apache Casssandra API를 구성하도록 yaml 파일 설정을 제공할 수 있나요?

Azure Cosmos DB의 Apache Cassandra API는 플랫폼 서비스입니다. 작업을 실행하기 위한 프로토콜 수준 호환성을 제공합니다. 관리, 모니터링 및 구성의 복잡성을 숨깁니다. 개발자/사용자인 경우 가용성, 삭제 표식, 키 캐시, 행 캐시, bloom 필터 및 다양한 기타 설정에 대해 걱정할 필요가 없습니다. Azure Cosmos DB Apache Cassandra API는 구성 및 관리 오버헤드 없이 요구하는 읽기 및 쓰기 성능을 제공하는 데 중점을 둡니다.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Azure Cosmos DB의 Apache Cassandra API가 노드 추가/클러스터 상태/노드 상태 명령을 지원하나요?

Apache Cassandra API는 처리량 및 스토리지에 대한 탄력성 요구에 맞는 용량 계획을 쉽게 만들 수 있는 플랫폼 서비스입니다. Azure Cosmos DB를 사용하여 필요한 처리량을 프로비전합니다. 그런 다음 노드를 추가/삭제 또는 관리하는 방법에 대한 걱정 없이 하루 종일 여러 번 확장하고 축소할 수 있습니다. 따라서 노드, 클러스터 관리 도구도 사용할 필요가 없습니다.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>키 공간을 생성하기 위한 단순/네트워크와 같은 다양한 구성 설정과 관련하여 어떤 상황이 발생하나요?

Azure Cosmos DB는 가용성 및 짧은 대기 시간을 이유로 기본적인 글로벌 배포를 제공합니다. 복제본 또는 다른 작업을 설정할 필요가 없습니다. 모든 쓰기는 성능 보장을 제공하면서 작성하는 모든 지역에서 항상 지속적으로 커밋됩니다.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>bloom 필터, 캐싱, 읽기 복구 변경 내용, gc_grace, 압축 memtable_flush_period 등과 같은 테이블 메타데이터에 대한 다양한 설정은 어떻게 되나요?

Azure Cosmos DB는 구성 설정을 다루거나 실수로 조작하지 않고도 읽기/쓰기 성능과 처리량을 제공합니다.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>TTL(Time to Live)이 Cassandra 테이블에 지원되나요?

예, TTL이 지원됩니다.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>다양한 도구를 사용하여 노드 상태, 복제본 상태, GC 및 OS 매개 변수를 사전에 모니터링할 수 있나요? 이제 무엇을 모니터링해야 하나요?

Azure Cosmos DB는 인프라를 관리하고 모니터링할 걱정 없이 생산성을 높이는 데 도움이 되는 플랫폼 서비스입니다. 포털 메트릭에서 사용 가능한 처리량을 통해 제한에 근접했는지 확인하고 처리량을 늘리거나 낮추면 됩니다.
[SLA](monitor-accounts.md)를 모니터링하세요.
[메트릭](use-metrics.md)을 사용하고 [진단 로그](logging.md)를 사용하세요.

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Azure Cosmos DB의 Apache Cassandra API와 함께 사용할 수 있는 클라이언트 SDK는 무엇인가요?

CQLv3을 사용하는 아파치 카산드라 SDK의 클라이언트 드라이버는 클라이언트 프로그램에 사용되었습니다. 사용하는 다른 드라이버가 있거나 문제가 있는 경우 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)로 메일을 보냅니다.

### <a name="is-composite-partition-key-supported"></a>복합 파티션 키가 지원되나요?

예, 일반 구문을 사용하여 복합 파티션 키를 만들 수 있습니다.

### <a name="can-i-use-sstableloader-for-data-loading"></a>데이터 로딩에 스테이블로더를 사용할 수 있습니까?

아니요, 스테이블로더는 지원되지 않습니다.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>온-프레미스 아파치 카산드라 클러스터를 Azure 코스모스 DB의 카산드라 API와 페어링할 수 있습니까?

현재 Azure Cosmos DB에는 작업의 오버헤드 없이 클라우드 환경에 최적화된 환경이 있습니다. 페어링이 필요한 경우 시나리오에 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 대한 설명과 함께 메일을 보냅니다. 우리는 온-프레미스/다른 클라우드 카산드라 클러스터를 Cosomos DB의 카산드라 API와 페어링할 수 있도록 돕기 위해 노력하고 있습니다.

### <a name="does-cassandra-api-provide-full-backups"></a>Cassandra API에서 전체 백업을 제공하나요?

Azure Cosmos DB는 모든 API에서 당일에 4시간 간격으로 수행된 두 개의 무료 전체 백업을 제공합니다. 따라서 백업 일정 및 기타 항목을 설정할 필요가 없습니다.
보존 및 빈도를 수정하려면 전자 메일을 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 보내거나 지원 서비스 케이스를 발생시십시오. 백업 기능에 대한 정보는 [Azure Cosmos DB로 자동 온라인 백업 및 복원](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) 문서에 나와 있습니다.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>지역에서 작동이 중지된 경우 Cassandra API 계정은 장애 조치(Failover)를 어떻게 처리하나요?

Azure Cosmos DB Cassandra API는 Azure Cosmos DB의 전역적으로 분산된 플랫폼을 활용합니다. 애플리케이션이 데이터 센터 가동 중지 시간을 견뎌낼 수 있도록 하려면 Azure Cosmos DB 포털 [다중 하위 지역 Azure Cosmos DB 계정을 사용하여 개발](high-availability.md)에서 계정에 대해 하위 지역을 하나 이상 사용하도록 설정합니다. 포털 [다중 하위 지역 Azure Cosmos DB 계정을 사용하여 개발](high-availability.md)을 사용하여 하위 지역의 우선 순위를 설정할 수 있습니다.

계정에 대해 원하는 개수의 지역을 추가하고 장애 조치(Failover) 우선 순위를 지정하여 장애 조치할 수 있는 위치를 제어할 수 있습니다. 데이터베이스를 사용하려면 애플리케이션도 제공해야 합니다. 이렇게 하면 고객에게 가동 중지 시간이 발생하지 않습니다.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Apache Cassandra API는 기본적으로 엔터티의 모든 특성을 인덱싱하나요?

아니요. 카산드라 API는 아파치 카산드라와 매우 유사한 방식으로 행동 보조 [인덱스를](cassandra-secondary-index.md)지원합니다. 기본적으로 모든 특성을 인덱싱하지는 않습니다.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>새로운 Cassandra API SDK를 에뮬레이터에서 로컬로 사용할 수 있나요?

예, 이 지원됩니다. 이 기능을 활성화하는 방법에 대한 자세한 내용은 여기에서 확인할 수 [있습니다.](local-emulator.md#cassandra-api)


### <a name="how-can-i-migrate-data-from-their-apache-cassandra-clusters-to-cosmos-db"></a>아파치 카산드라 클러스터에서 코스모스 DB로 데이터를 마이그레이션하려면 어떻게 해야 합니까?

마이그레이션 옵션에 대한 자세한 내용은 여기에서 확인할 수 [있습니다.](cassandra-import-data.md)


### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>일반 Cassandra API의 x 기능이 현재 작동하지 않는데 어디로 피드백을 보낼 수 있나요?

피드백은 [사용자 의견 피드백](https://feedback.azure.com/forums/263030-azure-cosmos-db)을 통해 보내주세요.

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>다음 단계

- [Azure 코스모스 DB 카산드라 API 계정을 탄력적으로 확장하여](manage-scale-cassandra.md)시작하십시오.
