---
title: 코스모스 DB 마이그레이션 옵션
description: 이 문서에서는 온-프레미스 또는 클라우드 데이터를 Azure Cosmos DB로 마이그레이션하는 다양한 옵션에 대해 설명합니다.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 34698a215477abdd7d68c3dfe050657ecf049690
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984898"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>온-프레미스 또는 클라우드 데이터를 Azure Cosmos DB로 마이그레이션하는 옵션

다양한 데이터 원본의 데이터를 Azure Cosmos DB로 로드할 수 있습니다. 또한 Azure Cosmos DB는 여러 API를 지원하므로 대상은 기존 API 중 어느 것이든 될 수 있습니다. 다양한 소스에서 다른 Azure Cosmos DB API로의 마이그레이션 경로를 지원하기 위해 각 마이그레이션 경로에 대해 전문적인 처리를 제공하는 여러 솔루션이 있습니다. 이 문서에서는 사용 가능한 솔루션을 나열하고 해당 솔루션의 장점과 한계를 설명합니다.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>마이그레이션 도구 선택에 영향을 미치는 요인

다음 요소는 마이그레이션 도구의 선택을 결정합니다.
* **온라인 및 오프라인 마이그레이션**: 많은 마이그레이션 도구는 일회성 마이그레이션만 수행하는 경로를 제공합니다. 즉, 데이터베이스에 액세스하는 응용 프로그램에서 는 가동 중지 시간이 발생할 수 있습니다. 일부 마이그레이션 솔루션은 원본과 대상 간에 복제 파이프라인이 설정되어 있는 라이브 마이그레이션을 수행하는 방법을 제공합니다.

* **데이터 원본**: 기존 데이터는 오라클 DB2, Datastax 카산다, Azure SQL Server, PostgreSQL 등과 같은 다양한 데이터 원본에 있을 수 있습니다. 데이터는 기존 Azure Cosmos DB 계정에 있을 수도 있으며 마이그레이션의 의도는 데이터 모델을 변경하거나 다른 파티션 키가 있는 컨테이너에서 데이터를 다시 분할하는 것입니다.

* **Azure 코스모스 DB API**: Azure Cosmos DB의 SQL API의 경우 Azure Cosmos DB 팀에서 개발한 다양한 도구가 있습니다. 다른 모든 API에는 커뮤니티에서 개발하고 유지 관리하는 자체 특수 도구 집합이 있습니다. Azure Cosmos DB는 와이어 프로토콜 수준에서 이러한 API를 지원하므로 이러한 도구는 데이터를 Azure Cosmos DB로 마이그레이션하는 동안 있는 대로 작동해야 합니다. 그러나 이 개념은 Azure Cosmos DB에만 해당하므로 스로틀에 대한 사용자 지정 처리가 필요할 수 있습니다.

* **데이터 크기**: 대부분의 마이그레이션 도구는 작은 데이터 집합에 매우 적합합니다. 데이터 집합이 수백 기가바이트를 초과하면 마이그레이션 도구의 선택이 제한됩니다. 

* **예상 마이그레이션 기간**: 마이그레이션은 처리량을 적게 소비하거나 대상 Azure Cosmos DB 컨테이너에 프로비전된 전체 처리량을 소비하고 더 빠른 시간에 마이그레이션을 완료할 수 있는 느린 증분 속도로 수행되도록 구성할 수 있습니다.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
|**마이그레이션 유형**|**해결 방법**|**고려 사항**|
|---------|---------|---------|
|오프라인|[데이터 마이그레이션 도구](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;설정이 간편하고 여러 소스 지원 <br/>&bull;대용량 데이터 집합에는 적합하지 않습니다.|
|오프라인|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;설정이 간편하고 여러 소스 지원 <br/>&bull;Azure 코스모스 DB 대량 실행기 라이브러리를 사용합니다. <br/>&bull;대용량 데이터 세트에 적합 <br/>&bull;검사점 부족 - 마이그레이션 중에 문제가 발생하면 전체 마이그레이션 프로세스를 다시 시작해야 한다는 의미입니다.<br/>&bull;배달 못한 편지 대기열 부족 - 몇 개의 잘못된 파일이 전체 마이그레이션 프로세스를 중지할 수 있음을 의미합니다.|
|오프라인|[Azure 코스모스 DB 스파크 커넥터](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Azure 코스모스 DB 대량 실행기 라이브러리를 사용합니다. <br/>&bull;대용량 데이터 세트에 적합 <br/>&bull;사용자 지정 스파크 설정이 필요합니다. <br/>&bull;Spark는 스키마 불일치에 민감하며 마이그레이션 중에 문제가 될 수 있습니다. |
|오프라인|[코스모스 DB 대량 집행기 라이브러리와 사용자 정의 도구](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;마이그레이션 복원력을 높이는 체크 포인트, 데드 레터링 기능 제공 <br/>&bull;매우 큰 데이터 집합(10TB+)에 적합  <br/>&bull;앱 서비스로 실행되는 이 도구의 사용자 지정 설정이 필요합니다. |
|온라인|[코스모스 DB 기능 + 체인지피드 API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;설치가 용이함 <br/>&bull;소스가 Azure 코스모스 DB 컨테이너인 경우에만 작동합니다. <br/>&bull;대용량 데이터 집합에는 적합하지 않습니다. <br/>&bull;원본 컨테이너에서 삭제를 캡처하지 않음 |
|온라인|[변경 피드를 사용하는 사용자 지정 마이그레이션 서비스](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull;진행률 추적 제공 <br/>&bull;소스가 Azure 코스모스 DB 컨테이너인 경우에만 작동합니다. <br/>&bull;더 큰 데이터 집합에도 적합합니다. <br/>&bull;사용자가 변경 피드 프로세서를 호스트하기 위해 앱 서비스를 설정해야 합니다. <br/>&bull;원본 컨테이너에서 삭제를 캡처하지 않음|
|온라인|[스트리임 (것)들](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;오라클, DB2, SQL Server 와 같은 다양한 소스와 함께 작동 <br/>&bull;ETL 파이프라인을 쉽게 구축하고 모니터링을 위한 대시보드를 제공합니다. <br/>&bull;더 큰 데이터 집합 지원 <br/>&bull;이 도구는 타사 도구이므로 마켓플레이스에서 구입하여 사용자 환경에 설치해야 합니다.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure 코스모스 DB 몽고 API
|**마이그레이션 유형**|**해결 방법**|**고려 사항**|
|---------|---------|---------|
|오프라인|[데이터 마이그레이션 도구](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;설정이 간편하고 여러 소스 지원 <br/>&bull;대용량 데이터 집합에는 적합하지 않습니다.|
|오프라인|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;설정이 간편하고 여러 소스 지원 <br/>&bull;Azure 코스모스 DB 대량 실행기 라이브러리를 사용합니다. <br/>&bull;대용량 데이터 세트에 적합 <br/>&bull;검사점이 없다는 것은 마이그레이션 과정에서 문제가 발생하므로 전체 마이그레이션 프로세스를 다시 시작해야 합니다.<br/>&bull;배달 못한 편지 대기열이 없어도 몇 개의 잘못된 파일이 전체 마이그레이션 프로세스를 중지할 수 있습니다. <br/>&bull;특정 데이터 원본에 대한 읽기 처리량을 높이기 위해 사용자 지정 코드가 필요합니다.|
|오프라인|[기존 몽고 도구 (몽고 덤프, 몽고 복원, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;간편한 설정 및 통합 <br/>&bull;스로틀에 대한 사용자 정의 처리가 필요합니다.|
|온라인|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Azure 코스모스 DB 대량 실행기 라이브러리를 사용합니다. <br/>&bull;대규모 데이터 집합에 적합하며 라이브 변경 내용 복제를 처리합니다. <br/>&bull;다른 MongoDB 소스에서만 작동|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API
|**마이그레이션 유형**|**해결 방법**|**고려 사항**|
|---------|---------|---------|
|오프라인|[cqlsh COPY 명령](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;설치가 용이함 <br/>&bull;대용량 데이터 집합에는 적합하지 않습니다. <br/>&bull;소스가 카산드라 테이블인 경우에만 작동합니다.|
|오프라인|[스파크가 있는 테이블 복사](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Spark 기능을 사용하여 변환 및 구성을 병렬화할 수 있습니다. <br/>&bull;스로틀을 처리하기 위해 사용자 지정 재시도 정책이 있는 구성이 필요합니다.|
|온라인|[스트림 (오라클 DB/아파치 카산드라 출신)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;오라클, DB2, SQL Server 와 같은 다양한 소스와 함께 작동 <br/>&bull;ETL 파이프라인을 쉽게 구축하고 모니터링을 위한 대시보드를 제공합니다. <br/>&bull;더 큰 데이터 집합 지원 <br/>&bull;이 도구는 타사 도구이므로 마켓플레이스에서 구입하여 사용자 환경에 설치해야 합니다.|
|온라인|[블리츠 (오라클 DB/아파치 카산드라 출신)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;더 큰 데이터 집합 지원 <br/>&bull;이 도구는 타사 도구이므로 마켓플레이스에서 구입하여 사용자 환경에 설치해야 합니다.|

## <a name="other-apis"></a>기타 API
SQL API, Mongo API 및 Cassandra API 이외의 API의 경우 각 API의 기존 에코시스템에 의해 지원되는 다양한 도구가 있습니다. 

**테이블 API** 
* [데이터 마이그레이션 도구](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [아즈카피](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin API**
* [그래프 대량 실행기 라이브러리](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [그렘린 스파크](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>다음 단계

* [.NET](bulk-executor-dot-net.md) 및 [Java에서](bulk-executor-java.md)대량 실행기 라이브러리를 사용하는 샘플 응용 프로그램을 사용해 보시면 자세히 알아보십시오. 
* 대량 실행기 라이브러리는 Cosmos DB 스파크 커넥터에 통합되어 자세한 내용은 [Azure Cosmos DB 스파크 커넥터](spark-connector.md) 문서를 참조하십시오.  
* "일반 권고" 문제 유형 및 "큰(TB+) 마이그레이션" 문제 하위 유형에서 지원 티켓을 열어 Azure Cosmos DB 제품 팀에 문의하여 대규모 마이그레이션에 대한 추가 도움말을 참조하세요.
