---
title: Azure Cosmos DB용 Azure Synapse Link에 대한 질문과 대답
description: 청구, 분석 저장소, 보안, 분석 저장소의 TTL(Time to Live) 등의 영역에서 Azure Cosmos DB용 Synapse Link에 대한 질문과 대답을 살펴봅니다.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 34bc8e3775c2334b0cdbb22c8cad8f8d1dd5c732
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568607"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Azure Cosmos DB용 Azure Synapse Link에 대한 질문과 대답

Azure Cosmos DB용 Azure Synapse Link는 Azure Cosmos DB와 Azure Synapse Analytics를 긴밀하게 통합합니다. 이를 통해 고객은 트랜잭션 워크로드에서 성능을 완벽하게 분리하고 ETL 파이프라인 없이 운영 데이터를 거의 실시간으로 분석할 수 있습니다. 이 문서는 Azure Cosmos DB용 Synapse Link에 대한 일반적인 질문과 대답을 제공합니다.

## <a name="general-faq"></a>일반 FAQ

### <a name="is-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Synapse Link가 모든 Azure Cosmos DB API에 대해 지원되나요?

공개 미리 보기 릴리스에서 Synapse 링크는 Azure Cosmos DB SQL (Core) API 및 MongoDB 용 Azure Cosmos DB API에 대해 지원 됩니다. 

### <a name="is-synapse-link-supported-for-multi-region-azure-cosmos-accounts"></a>다중 지역 Azure Cosmos 계정에 대해 Synapse Link가 지원되나요?

예, 다중 지역 Azure Cosmos 계정의 경우 분석 저장소에 저장된 데이터도 전역적으로 배포됩니다. 단일 쓰기 영역 또는 다중 쓰기 영역에 관계없이 Azure Synapse Analytics에서 수행된 분석 쿼리는 가장 가까운 로컬 지역에서 제공될 수 있습니다.

분석 저장소를 지원하는 다중 지역 Azure Cosmos 계정을 구성할 때는 계정 생성 시 필요한 모든 지역을 추가하는 것이 좋습니다.

### <a name="can-i-choose-to-enable-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>다중 지역 계정 설정 시 모든 지역이 아닌 특정 지역에서만 Synapse Link를 사용하도록 선택할 수 있나요?

미리 보기 릴리스에서 다중 지역 계정에 대해 Synapse Link를 사용하도록 설정하면 모든 지역에 분석 저장소가 생성됩니다. 기본 데이터는 트랜잭션 저장소의 처리량 및 트랜잭션 일관성을 위해 최적화됩니다.

### <a name="is-backup-and-restore-supported-for-synapse-link-enabled-accounts"></a>Synapse Link 사용 계정에 대해 백업 및 복원이 지원 되나요?

미리 보기에서 Synapse Link 사용 데이터베이스 계정의 경우 컨테이너의 백업 및 복원은 지원 되지 않습니다. 백업 및 복원 기능이 필요한 프로덕션 워크 로드가 있는 경우 해당 데이터베이스 계정에 대해 Synapse 링크를 사용 하도록 설정 하지 않는 것이 좋습니다. 

### <a name="can-i-disable-the-synapse-link-feature-for-my-azure-cosmos-account"></a>내 Azure Cosmos 계정에 대해 Synapse Link 기능을 사용하지 않도록 설정할 수 있나요?

현재, 계정 수준에서 Synapse Link 기능을 사용하도록 설정한 후에는 사용하지 않도록 설정할 수 없습니다.  기능을 해제하려면 계정을 삭제하고 새 Azure Cosmos 계정을 다시 만들어야 합니다.

Synapse Link 기능을 계정 수준에서 사용하도록 설정되어 있지만 분석 저장소를 사용하도록 설정된 컨테이너가 없으면 청구에 영향을 주지 **않습니다**.

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 분석 저장소

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>기존 컨테이너에서 분석 저장소를 사용하도록 설정할 수 있나요?

현재는 새 컨테이너(신규 및 기존 계정 모두)에만 분석 저장소를 사용할 수 있습니다.

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-containers-after-enabling-it-during-container-creation"></a>컨테이너를 만드는 동안 분석 저장소를 사용하도록 설정한 후 Azure Cosmos 컨테이너에서 사용하지 않도록 설정할 수 있나요?

현재, 컨테이너를 만드는 동안 분석 저장소를 사용하도록 설정했으면 Azure Cosmos 컨테이너에서 사용하지 않도록 설정할 수 없습니다.

### <a name="is-analytical-store-supported-for-azure-cosmos-containers-with-autoscale-provisioned-throughput"></a>처리량이 자동 크기 조정 프로비저닝된 Azure Cosmos 컨테이너에 대해 분석 저장소가 지원되나요?

예, 처리량이 자동 크기 조정 프로비저닝된 컨테이너에 대해 분석 저장소를 사용하도록 설정할 수 있습니다.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Azure Cosmos DB 트랜잭션 저장소에 프로비저닝된 RU에 영향을 주나요?

Azure Cosmos DB는 트랜잭션 워크로드와 분석 워크로드 간의 성능 격리를 보장합니다. 컨테이너에서 분석 저장소를 사용하도록 설정해도 Azure Cosmos DB 트랜잭션 저장소에 프로비저닝된 RU에는 영향을 주지 않습니다. 분석 저장소의 트랜잭션(읽기 및 쓰기) 및 스토리지 비용은 별도로 청구됩니다. 자세한 내용은 [Azure Cosmos DB 분석 저장소의 가격 책정](analytical-store-introduction.md#analytical-store-pricing)을 참조하세요.

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>트랜잭션 저장소에 대한 삭제 및 업데이트 작업이 분석 저장소에 반영되나요?

예, 트랜잭션 저장소의 데이터를 삭제하고 업데이트하면 분석 저장소에 반영됩니다. 기록 데이터를 포함하도록 컨테이너에서 TTL(Time to Live)을 구성 할 수 있습니다. 그러면 분석 TTL 기준을 충족하는 항목의 모든 버전이 분석 저장소에 유지됩니다. 자세한 내용은 [분석 TTL 개요](analytical-store-introduction.md#analytical-ttl)를 참조하세요.

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Azure Synapse Analytics 이외의 분석 엔진에서 분석 저장소에 연결할 수 있나요?

Azure Synapse Analytics에서 제공하는 다양한 런타임을 사용하는 경우에만 분석 저장소에 액세스하고 쿼리를 실행할 수 있습니다. 분석 저장소는 다음을 사용하여 쿼리하고 분석할 수 있습니다.

* Scala, Python, SparkSQL 및 C#을 완벽하게 지원하는 Synapse Spark. Synapse Spark는 데이터 엔지니어링 및 과학 시나리오의 중심입니다.
* T-SQL 언어 및 익숙한 BI 도구(예 : Power BI Premium 등) 지원이 포함된 SQL 서버리스

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>프로비저닝된 Synapse SQL에서 분석 저장소에 연결할 수 있나요?

현재는 프로비저닝된 Synapse SQL에서 분석 저장소에 연결할 수 없습니다.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Synapse의 쿼리 집계 결과를 분석 저장소에 다시 쓸 수 있나요?

분석 저장소는 Azure Cosmos 컨테이너의 읽기 전용 저장소입니다. 따라서 집계 결과를 분석 저장소에 직접 다시 쓸 수는 없습니다. 하지만 다른 컨테이너의 Azure Cosmos DB 트랜잭션 저장소에 쓸 수 있으며, 그러면 나중에 서비스 계층으로 활용할 수 있습니다.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>트랜잭션 저장소에서 분석 저장소로 자동 동기화 복제는 비동기식인가요? 동기식인가요? 대기 시간은 얼마나 되나요?

자동 동기화 대기 시간은 일반적으로 2 분 이내입니다. 컨테이너가 많은 공유 처리량 데이터베이스의 경우 개별 컨테이너의 자동 동기화 대기 시간이 더 높아질 수 있으며 최대 5 분이 걸릴 수 있습니다. 이 대기 시간이 시나리오에 얼마나 적합 한지 자세히 알아보세요. 이를 위해 [Azure Cosmos DB 팀](mailto:cosmosdbsynapselink@microsoft.com)에 연락 하세요.

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>트랜잭션 저장소의 항목이 분석 저장소로 자동 전파되지 못하는 시나리오가 있나요?

컨테이너의 특정 항목이 [분석을 위해 잘 정의된 스키마](analytical-store-introduction.md#analytical-schema)를 위반하면 해당 항목은 분석 저장소에 포함되지 않습니다. 분석을 위해 잘 정의된 스키마로 인해 차단된 시나리오가 있으면 [Azure Cosmos DB 팀](mailto:cosmosdbsynapselink@microsoft.com)에 이메일로 도움을 요청하세요.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>분석 저장소의 데이터를 트랜잭션 저장소와 다르게 분할할 수 있나요?

분석 저장소의 데이터는 트랜잭션 저장소에서 분할된 데이터의 수평 분할을 기반으로 분할됩니다. 현재는 분석 저장소에 대해 다른 분할 전략을 선택할 수 없습니다.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>분석 저장소에서 트랜잭션 데이터가 열 형식으로 변환되는 방식을 사용자 지정하거나 재정의할 수 있나요?

현재는 데이터 항목이 트랜잭션 저장소에서 분석 저장소로 자동 전파될 때 데이터 항목을 변환할 수 없습니다. 이러한 제한으로 인해 차단되는 시나리오가 있으면 [Azure Cosmos DB 팀](mailto:cosmosdbsynapselink@microsoft.com)에 이메일을 보내주세요.

## <a name="analytical-time-to-live-ttl"></a>분석 TTL(Time-to-Live)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>분석 데이터에 대한 TTL이 컨테이너와 항목 수준 모두에서 지원되나요?

현재 분석 데이터에 대한 TTL은 컨테이너 수준에서만 구성할 수 있으며 항목 수준에서 분석 TTL을 설정하는 것은 지원되지 않습니다.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Azure Cosmos DB 컨테이너에서 컨테이너 수준 분석 TTL을 설정한 후 나중에 다른 값으로 변경할 수 있나요?

예, 분석 TTL을 유효한 값으로 업데이트할 수 있습니다. 분석 TTL에 대한 자세한 내용은 [분석 TTL](analytical-store-introduction.md#analytical-ttl) 문서를 참조하세요.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>트랜잭션 저장소에서 항목 TTL이 만료된 후에 분석 저장소에서 항목을 업데이트하거나 삭제할 수 있나요?

모든 트랜잭션 업데이트 및 삭제는 분석 저장소로 복사되지만 트랜잭션 저장소에서 항목이 제거된 경우에는 분석 저장소에서 업데이트할 수 없습니다. 자세히 알아보려면 [분석 TTL](analytical-store-introduction.md#analytical-ttl) 문서를 참조하세요.

## <a name="billing"></a>결제

### <a name="what-is-the-billing-model-of-synapse-link-for-azure-cosmos-db"></a>Azure Cosmos DB용 Synapse Link의 청구 모델은 무엇인가요?

[Azure Cosmos DB 분석 저장소](analytical-store-introduction.md)는 2020년 8월 30일까지 분석 저장소에 대한 비용 없이 공개 미리 보기로 제공됩니다. Synapse Spark 및 Synapse SQL은 [Synapse 서비스 사용](https://azure.microsoft.com/pricing/details/synapse-analytics/)을 통해 청구됩니다.

## <a name="security"></a>보안

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>분석 저장소로 인증하는 방법에는 무엇이 있나요?

분석 저장소를 사용한 인증은 트랜잭션 저장소와 동일합니다. 지정 된 데이터베이스의 경우 기본 키 또는 읽기 전용 키를 사용 하 여 인증할 수 있습니다. Synapse Studio에서 연결된 서비스를 활용하여 Spark 노트북에 Azure Cosmos DB 키 붙여넣기를 방지할 수 있습니다. 연결된 서비스에 대한 액세스는 작업 영역에 액세스할 수 있는 모든 사람이 사용할 수 있습니다.

## <a name="synapse-run-times"></a>Synapse 런타임

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 분석 저장소에 액세스하도록 현재 지원되는 Synapse 런타임에는 무엇이 있나요?

|Synapse 런타임 |현재 지원 |
|---------|---------|
|Synapse Spark 풀 | 읽기, 쓰기(트랜잭션 저장소를 통해), 테이블, 임시 뷰 |
|서버를 사용 하지 않는 SQL server Synapse    | 읽기, 보기 |
|프로비저닝된 Synapse SQL   |  사용할 수 없음 |

### <a name="do-my-synapse-spark-tables-sync-with-my-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>Synapse Spark 테이블이 Azure Data Lake와 동일한 방식으로 Synapse SQL 서버 리스 테이블과 동기화 하나요?

현재 이 기능은 사용할 수 없습니다.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>분석 저장소에서 Spark 구조적 스트리밍을 수행할 수 있나요?

현재 Azure Cosmos DB에 대한 Spark 구조적 스트리밍 지원은 트랜잭션 저장소의 변경 피드 기능을 사용하여 구현되며 아직 분석 저장소에서는 지원되지 않습니다.

## <a name="synapse-studio"></a>Synapse Studio

### <a name="in-the-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Synapse Studio에서 분석 저장소를 사용하도록 설정된 Azure Cosmos DB 컨테이너에 연결되어 있는지 어떻게 알 수 있나요?

분석 저장소를 사용하도록 설정된 Azure Cosmos DB 컨테이너에는 다음 아이콘이 있습니다.

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="분석 저장소를 사용하도록 설정된 Azure Cosmos DB 컨테이너- 아이콘":::

트랜잭션 저장소 컨테이너는 다음 아이콘으로 표시됩니다.

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="분석 저장소를 사용하도록 설정된 Azure Cosmos DB 컨테이너- 아이콘":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-synapse-studio"></a>Synapse Studio에서 Azure Cosmos DB 자격 증명을 어떻게 전달하나요?

현재 Azure Cosmos DB 자격 증명은 Azure Cosmos DB 데이터베이스에 액세스할 수 있는 사용자가 연결된 서비스를 만드는 동안 전달됩니다. 이 저장소에 대한 액세스는 작업 영역에 액세스할 수 있는 다른 사용자가 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Synapse Link의 이점](synapse-link.md#synapse-link-benefits) 알아보기

* [Synapse Link와 Azure Cosmos DB 간의 통합](synapse-link.md#synapse-link-integration)에 대해 알아봅니다.
