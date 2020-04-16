---
title: Azure 코스모스 DB 서비스 할당량
description: Azure Cosmos DB 서비스 할당량 및 다른 리소스 유형에 대한 기본 제한입니다.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: b24d7db679bb9cb9dacd5e1db8e6410b883548cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415702"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure 코스모스 DB 서비스 할당량

이 문서에서는 Azure Cosmos DB의 다른 리소스에 제공되는 기본 할당량에 대한 개요를 제공합니다.

## <a name="storage-and-throughput"></a>스토리지 및 처리량

구독에서 Azure Cosmos 계정을 만든 후 [데이터베이스, 컨테이너 및 항목을 만들어](databases-containers-items.md)계정의 데이터를 관리할 수 있습니다. [요청 단위(RU/s 또는 RU)의](request-units.md)관점에서 컨테이너 수준 또는 데이터베이스 수준에서 처리량을 프로비전할 수 있습니다. 다음 표에는 컨테이너/데이터베이스당 저장소 및 처리량에 대한 제한이 나열되어 있습니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 컨테이너당 최대[R(전용 처리량 프로비저닝 모드)](databases-containers-items.md#azure-cosmos-containers) | 기본적으로 1,000,000. [Azure 지원 티켓을 제출하여](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) 늘릴 수 있습니다. |
| 데이터베이스당 최대[R(공유 처리량 프로비저닝 모드)](databases-containers-items.md#azure-cosmos-containers) | 기본적으로 1,000,000. [Azure 지원 티켓을 제출하여](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) 늘릴 수 있습니다. |
| (논리) 파티션 키당 최대 루 | 10000 |
| 논리파티션 키당 모든 항목에 대한 최대 스토리지| 20GB |
| 고유(논리) 파티션 키의 최대 개수 | 제한 없음 |
| 컨테이너당 최대 저장 용량 | 제한 없음 |
| 데이터베이스당 최대 스토리지 | 제한 없음 |
| 계정당 최대 첨부 파일 크기(첨부 파일 기능이 감가상각됨) | 2GB |
| 1GB당 최소 루 | 10 RU / s |

> [!NOTE]
> 저장소 또는 처리량에 대한 더 높은 제한이 필요한 파티션 키가 있는 워크로드를 관리하는 모범 사례에 대해 알아보려면 [가상 파티션 키 만들기를](synthetic-partition-keys.md)참조하십시오.
>

Cosmos 컨테이너(또는 공유 처리량 데이터베이스)의 최소 처리량은 400R이상이어야 합니다. 컨테이너가 증가함에 따라 지원되는 최소 처리량도 다음 요소에 따라 달라집니다.

* 컨테이너에서 설정할 수 있는 최소 처리량은 컨테이너에서 프로비전된 최대 처리량에 따라 다릅니다. 예를 들어 처리량이 10000R로 증가한 경우 프로비저닝된 처리량중 가장 낮은 처리량은 1000R입니다.
* 공유 처리량 데이터베이스의 최소 처리량은 컨테이너당 100R로 측정된 공유 처리량 데이터베이스에서 만든 총 컨테이너 수에 따라 달라집니다. 예를 들어 공유 처리량 데이터베이스 내에서 5개의 컨테이너를 만든 경우 처리량은 500개 이상의 RE여야 합니다.

컨테이너 또는 데이터베이스의 현재 및 최소 처리량은 Azure 포털 또는 SDK에서 검색할 수 있습니다. 자세한 내용은 [컨테이너 및 데이터베이스에 대한 프로비저닝 처리량을](set-throughput.md)참조하십시오. 

> [!NOTE]
> 경우에 따라 처리량을 10% 미만으로 낮출 수 있습니다. API를 사용하여 컨테이너당 정확한 최소 RUs를 가져옵니다.
>

요약하면, 다음은 프로비저닝된 최소 RU 한도입니다. 

| 리소스 | 기본 제한 |
| --- | --- |
| 컨테이너당 최소[US(전용 처리량 프로비저닝 모드)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| 데이터베이스당 최소[R(공유 처리량 프로비저닝 모드)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| 공유 처리량 데이터베이스 내의 컨테이너당 최소 US | 100 |

Cosmos DB는 SDK 또는 포털을 통해 컨테이너 또는 데이터베이스당 처리량(R)의 탄력적 확장을 지원합니다. 각 컨테이너는 최소값과 최대값 사이에서 10~100배의 스케일 범위 내에서 동기식으로 즉시 확장할 수 있습니다. 요청된 처리량 값이 범위를 벗어나면 크기 조정이 비동기적으로 수행됩니다. 비동기 크기 조정은 컨테이너의 요청된 처리량 및 데이터 저장소 크기에 따라 완료하는 데 몇 분에서 몇 시간이 걸릴 수 있습니다.  

## <a name="control-plane-operations"></a>제어 평면 작업

Azure 포털, Azure PowerShell, Azure CLI 및 Azure 리소스 관리자 템플릿을 사용하여 [Azure Cosmos 계정을 프로비전하고 관리할](how-to-manage-database-account.md) 수 있습니다. 다음 표에는 구독, 계정 및 작업 수에 따른 한도가 나열되어 있습니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 구독당 최대 데이터베이스 계정 | 기본적으로 50. [Azure 지원 티켓을 제출하여](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) 늘릴 수 있습니다.|
| 최대 지역 장애 조치 수 | 기본적으로 1/시간. [Azure 지원 티켓을 제출하여](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) 늘릴 수 있습니다.|

> [!NOTE]
> 지역 장애 조치(장애)는 단일 리전 기록 계정에만 적용됩니다. 다중 지역 쓰기 계정은 쓰기 영역 을 변경하거나 변경할 필요가 없습니다.

코스모스 DB는 정기적으로 데이터를 자동으로 백업합니다. 백업 보존 간격 및 창에 대한 자세한 내용은 [Azure Cosmos DB의 온라인 백업 및 주문형 데이터 복원을](online-backup-and-restore.md)참조하십시오.

## <a name="per-account-limits"></a>계정당 한도

| 리소스 | 기본 제한 |
| --- | --- |
| 최대 데이터베이스 수 | 제한 없음 |
| 공유 처리량이 있는 데이터베이스당 최대 컨테이너 수 |25 |
| 전용 처리량이 있는 데이터베이스 또는 계정당 최대 컨테이너 수  |무제한 |
| 최대 지역 수 | 제한 없음(모든 Azure 지역) |

## <a name="per-container-limits"></a>컨테이너당 한도

사용하는 API에 따라 Azure Cosmos 컨테이너는 컬렉션, 테이블 또는 그래프를 나타낼 수 있습니다. 컨테이너는 고유한 [키 제약 조건,](unique-keys.md) [저장 프로시저, 트리거 및 DFOR](stored-procedures-triggers-udfs.md)및 [인덱싱 정책에](how-to-manage-indexing-policy.md)대한 구성을 지원합니다. 다음 표에는 컨테이너 내의 구성과 관련된 제한이 나열되어 있습니다. 

| 리소스 | 기본 제한 |
| --- | --- |
| 데이터베이스 또는 컨테이너 이름의 최대 길이 | 255 |
| 컨테이너당 최대 저장 프로시저 | 100<sup>*</sup>|
| 컨테이너당 최대 UdF | 25<sup>*</sup>|
| 인덱싱 정책의 최대 경로 수| 100<sup>*</sup>|
| 컨테이너당 최대 고유 키 수|10<sup>*</sup>|
| 고유 키 제약 조건당 최대 경로 수|16<sup>*</sup>|

<sup>*</sup>Azure 지원에 문의하여 이러한 컨테이너별 제한을 늘릴 수 있습니다.

## <a name="per-item-limits"></a>항목당 제한

사용하는 API에 따라 Azure Cosmos 항목은 컬렉션의 문서, 테이블의 행 또는 그래프의 노드 또는 가장자리를 나타낼 수 있습니다. 다음 표에서는 코스모스 DB의 항목당 제한을 보여 주습니다. 

| 리소스 | 기본 제한 |
| --- | --- |
| 항목의 최대 크기 | 2 MB (JSON 표현의 UTF-8 길이) |
| 파티션 키 값의 최대 길이 | 2048바이트 |
| ID 값의 최대 길이 | 1023바이트 |
| 항목당 최대 속성 수 | 실질적인 제한 없음 |
| 최대 중첩 깊이 | 실질적인 제한 없음 |
| 속성 이름의 최대 길이 | 실질적인 제한 없음 |
| 속성 값의 최대 길이 | 실질적인 제한 없음 |
| 문자열 속성 값의 최대 길이 | 실질적인 제한 없음 |
| 숫자 속성 값의 최대 길이 | IEEE754 이중 정밀도 64비트 |

파티션 키 및 ID 값에 대한 길이 제한 및 2MB의 전체 크기 제한을 제외하고 는 속성 수 및 중첩 깊이와 같은 항목 페이로드에 제한이 없습니다. RU 소비를 줄이기 위해 크거나 복잡한 항목 구조가 있는 컨테이너에 대한 인덱싱 정책을 구성해야 할 수 있습니다. 실제 예제는 [Cosmos DB의 항목 모델링및](how-to-model-partition-example.md) 큰 항목을 관리하는 패턴을 참조하십시오.

## <a name="per-request-limits"></a>요청당 한도

Azure Cosmos DB는 컨테이너, 항목 및 데이터베이스와 같은 리소스에 대해 [CRUD 및 쿼리 작업을](https://docs.microsoft.com/rest/api/cosmos-db/) 지원합니다. 또한 컨테이너에서 동일한 파티션 키가 있는 여러 항목에 대한 [트랜잭션 일괄 처리 요청을](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) 지원합니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 단일 작업에 대한 최대 실행 시간(예: 저장 프로시저 실행 또는 단일 쿼리 페이지 검색)| 5초 |
| 최대 요청 크기(예: 저장 프로시저, CRUD)| 2MB |
| 최대 응답 크기(예: 페이지가 있는 쿼리) | 4MB |
| 트랜잭션 일괄 처리의 최대 작업 수 | 100 |

쿼리와 같은 작업이 실행 시간 제한 또는 응답 크기 제한에 도달하면 결과 페이지와 연속 토큰을 클라이언트에 반환하여 실행을 다시 시작합니다. 단일 쿼리가 페이지/연속에 걸쳐 실행할 수 있는 기간에는 실질적인 제한이 없습니다.

코스모스 DB는 승인을 위해 HMAC를 사용합니다. 마스터 키 또는 [리소스 토큰을](secure-access-to-data.md) 사용하여 컨테이너, 파티션 키 또는 항목과 같은 리소스에 대한 세분화된 액세스 제어를 수행할 수 있습니다. 다음 표에는 Cosmos DB의 권한 부여 토큰에 대한 제한이 나열되어 있습니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 최대 마스터 토큰 만료 시간 | 15분  |
| 최소 리소스 토큰 만료 시간 | 10분  |
| 최대 리소스 토큰 만료 시간 | 기본적으로 24 시간. [Azure 지원 티켓을 제출하여](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) 늘릴 수 있습니다.|
| 토큰 권한 부여를 위한 최대 클럭 기울이기| 15분 |

코스모스 DB는 쓰기 중에 트리거 실행을 지원합니다. 이 서비스는 쓰기 작업당 최대 하나의 사전 트리거와 하나의 사후 트리거를 지원합니다. 

## <a name="autopilot-mode-limits"></a>오토파일럿 모드 제한

자동 [조종 모드의](provision-throughput-autopilot.md#autopilot-limits) 처리량 및 스토리지 제한은 자동 조종 장치 문서를 참조하십시오.

## <a name="sql-query-limits"></a>SQL 쿼리 제한

코스모스 DB는 [SQL을](how-to-sql-query.md)사용하여 쿼리 항목을 지원합니다. 다음 표에서는 절 수 또는 쿼리 길이와 같은 쿼리 문의 제한에 대해 설명합니다.

| 리소스 | 기본 제한 |
| --- | --- |
| SQL 쿼리의 최대 길이| 256 KB |
| 쿼리당 최대 JOI| 5<sup>*</sup>|
| 쿼리당 최대 UdF| 10<sup>*</sup>|
| 다각형당 최대 포인트| 4096 |
| 컨테이너당 최대 포함 경로| 500 |
| 컨테이너당 최대 제외 경로| 500 |
| 복합 인덱스의 최대 속성| 8 |

<sup>*</sup>Azure 지원에 문의하여 이러한 SQL 쿼리 제한을 늘릴 수 있습니다.

## <a name="mongodb-api-specific-limits"></a>몽고DB API 별 제한

코스모스 DB는 몽고DB를 상대로 작성된 애플리케이션에 대해 몽고DB 와이어 프로토콜을 지원합니다. [지원되는 MongoDB 기능 및 구문에서](mongodb-feature-support.md)지원되는 명령 및 프로토콜 버전을 찾을 수 있습니다.

다음 표에는 MongoDB 기능 지원과 관련된 제한사항이 나열되어 있습니다. SQL(코어) API에 대해 언급된 다른 서비스 제한도 MongoDB API에도 적용됩니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 최대 MongoDB 쿼리 메모리 크기(이 제한은 3.2 서버 버전에만 해당) | 40MB |
| MongoDB 작업에 대한 최대 실행 시간| 30초 |
| 서버 측 연결 클로저를 위한 유휴 연결 시간 시간* | 30분 |

\*[Azure LoadBalancer의 기본 시간 초과가 4분이므로](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)클라이언트 응용 프로그램은 드라이버 설정에서 유휴 연결 시간 초과를 2-3분으로 설정하는 것이 좋습니다.  이 시간 아웃은 클라이언트 컴퓨터와 Azure Cosmos DB 간의 중간 로드 밸런서에 의해 유휴 연결이 닫히지 않도록 합니다.

## <a name="try-cosmos-db-free-limits"></a>코스모스 DB 프리 리미티드 체험하기

다음 표에는 무료 평가판에 대한 Azure Cosmos DB 시도에 대한 제한이 [나열되어 있습니다.](https://azure.microsoft.com/try/cosmosdb/)

| 리소스 | 기본 제한 |
| --- | --- |
| 평가판 기간 | 30일(수회 갱신) |
| 구독당 최대 컨테이너(SQL, 그렘린, 테이블 API) | 1 |
| 구독당 최대 컨테이너(MongoDB API) | 3 |
| 컨테이너당 최대 처리량 | 5,000 |
| 공유 처리량 데이터베이스당 최대 처리량 | 20000 |
| 계정당 최대 총 스토리지 | 10 GB |

Try Cosmos DB는 미국 중부, 북유럽 및 동남아시아 지역에서만 글로벌 배포를 지원합니다. Azure Cosmos DB 계정에 대해 Azure 지원 티켓을 만들 수 없습니다. 그러나 기존 지원 플랜을 가진 구독자에게는 지원이 제공됩니다.

## <a name="free-tier-account-limits"></a>프리 티어 계정 한도
다음 표에는 Azure [Cosmos DB 프리 티어 계정에](optimize-dev-test.md#azure-cosmos-db-free-tier) 대한 제한이 나열되어 있습니다.

| 리소스 | 기본 제한 |
| --- | --- |
| Azure 구독당 프리 티어 계정 수 | 1 |
| 프리 티어 할인 기간 | 계정의 수명입니다. 계정 생성 중에 옵트인해야 합니다. |
| 최대 RU/s 무료 | 400RU/s |
| 최대 스토리지 무료 | 5GB |
| 공유 처리량 데이터베이스의 최대 수 | 5 |
| 공유 처리량 데이터베이스의 최대 컨테이너 수 | 25 <br>프리 티어 계정에서 최대 25개의 컨테이너가 있는 공유 처리량 데이터베이스의 최소 RU/s는 400RU/s입니다. |

  위의 것 외에도 [계정당 한도는](#per-account-limits) 프리 티어 계정에도 적용됩니다.

## <a name="next-steps"></a>다음 단계

코스모스 DB의 핵심 개념 [글로벌 배포](distribute-data-globally.md) 및 [분할](partitioning-overview.md) 및 [프로비저닝 처리량에](request-units.md)대해 자세히 읽어보십시오.

다음 요약 설명서를 통해 Azure Cosmos DB를 시작해 보세요.

* [Azure Cosmos DB SQL API 시작](create-sql-api-dotnet.md)
* [Azure Cosmos DB의 MongoDB API 시작](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API 시작](create-cassandra-dotnet.md)
* [Azure Cosmos DB Gremlin API 시작](create-graph-dotnet.md)
* [Azure Cosmos DB Table API 시작](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [무료로 Azure Cosmos DB 사용해 보기](https://azure.microsoft.com/try/cosmosdb/)
