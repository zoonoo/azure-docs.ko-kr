---
title: Azure Cosmos DB의 한도
description: 이 문서에는 Azure Cosmos DB에 대 한 제한을 설명합니다.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2019
ms.openlocfilehash: 28eb7c6a11f71fa87835bcfe78e635753965bac3
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561228"
---
# <a name="limits-in-azure-cosmos-db"></a>Azure Cosmos DB의 한도

이 문서에서는 Azure Cosmos DB 서비스에 대 한 개요를 제공합니다.

## <a name="storage-and-throughput"></a>저장소 및 처리량

구독에서 Azure Cosmos 계정을 만든 후 하 여 계정에 데이터를 관리할 수 있습니다 [데이터베이스, 컨테이너 및 항목을 만드는](databases-containers-items.md)합니다. 컨테이너 수준 또는 측면의 데이터베이스 수준에서 처리량을 프로 비전 할 수 [요청 단위 (RU/s 또는 Ru)](request-units.md)합니다. 다음 표에서 저장소 및 컨테이너/데이터베이스 당 처리량 한도 나열합니다.

| Resource | 기본 제한 |
| --- | --- |
| 컨테이너 당 최대 RUs ([전용된 처리량 프로 비전된 모드](databases-containers-items.md#azure-cosmos-containers)) | 기본적으로 1,000,000입니다. 증가 될 수 있습니다 [Azure 지원 티켓을 제출](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) 을 통해 문의 하거나 [Cosmos DB 요청](mailto:askcosmosdb@microsoft.com) |
| 데이터베이스당 최대 RUs ([공유 처리량 프로 비전된 모드](databases-containers-items.md#azure-cosmos-containers)) | 기본적으로 1,000,000입니다. 증가 될 수 있습니다 [Azure 지원 티켓을 제출](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) 을 통해 문의 하거나 [Cosmos DB 요청](mailto:askcosmosdb@microsoft.com) |
| 논리 파티션 키 당 최대 Ru | 10000 |
| 모든 항목 (논리) 파티션 키 당 최대 저장소| 10 GB |
| 고유한 논리 파티션 키의 최대 수 | Unlimited |
| 컨테이너 당 최대 저장소 | Unlimited |
| 데이터베이스당 최대 저장소 | Unlimited |

> [!NOTE]
> 저장소 또는 처리량도 증가 해야 하는 파티션 키가 있는 워크 로드 관리 모범 사례를 참조 하세요. [핫 파티션 키에 대 한 디자인](synthetic-partition-keys.md)
>

Cosmos 컨테이너 (또는 공유 처리량 데이터베이스)에 최소 400 Ru 처리량이 있어야 합니다. 컨테이너 증가 함에 따라 지원 되는 최소 처리량도 같은 요인에 따라 다릅니다.

* 최대 처리량 적이 컨테이너에서 프로 비전 합니다. 서비스는 10%의 프로 비전 된 최대 컨테이너의 낮추기 처리량을 지원 합니다. 예를 들어 10000 Ru 처리량이 증가 했습니다 후 가능한 가장 낮은 프로 비전 된 처리량 것 1000 Ru
* 컨테이너 당 100 Ru에서 측정 한 적이 공유 처리량 데이터베이스에서 만든 컨테이너의 총 수입니다. 예를 들어 공유 처리량 데이터베이스 내에 5 개의 컨테이너를 만든 경우 처리량에 있어야 합니다 500 대 이상의 Ru

컨테이너 또는 데이터베이스의 현재 및 최소 처리량은 Sdk 또는 Azure portal에서 검색할 수 있습니다. 자세한 내용은 [컨테이너 및 데이터베이스에 대 한 프로 비전 처리량](set-throughput.md)합니다. 요약 하자면, 최소 프로 비전 된 RU 제한이 다음과 같습니다. 

| Resource | 기본 제한 |
| --- | --- |
| 컨테이너 당 최소 RUs ([전용된 처리량 프로 비전된 모드](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| 데이터베이스당 최소 RUs ([공유 처리량 프로 비전된 모드](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| 공유 처리량 데이터베이스 내에서 컨테이너 당 최소 Ru | 100 |

Cosmos DB는 컨테이너 또는 Sdk 또는 포털을 통해 데이터베이스 당 처리량 (Ru)의 탄력적인 확장을 지원 합니다. 각 컨테이너는 최소값과 최대값 사이 100 번 10 확장 범위 내에서 동기적으로, 즉시 확장할 수 있습니다. 요청 된 처리량 값 범위를 벗어난 경우 크기 조정 비동기적으로 수행 됩니다. 비동기 확장 분 요청 된 처리량 및 컨테이너의 데이터 저장소 크기에 따라 완료 하는 데 시간이 걸릴 수 있습니다.  

## <a name="control-plane-operations"></a>제어 평면 작업

할 수 있습니다 [프로 비전 하 고 Azure Cosmos 계정 관리](how-to-manage-database-account.md) Azure portal, Azure PowerShell, Azure CLI 및 Azure Resource Manager 템플릿을 사용 합니다. 다음 표에 구독, 계정 및 작업 수가 별 한도가 있습니다.

| Resource | 기본 제한 |
| --- | --- |
| 구독 당 최대 데이터베이스 계정 | 기본적으로 50입니다. 증가 될 수 있습니다 [Azure 지원 티켓을 제출](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) 을 통해 문의 하거나 [Cosmos DB 요청](mailto:askcosmosdb@microsoft.com)|
| 지역별 장애 조치의 최대 수 | 기본적으로 1 시간당 합니다. 증가 될 수 있습니다 [Azure 지원 티켓을 제출](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) 을 통해 문의 하거나 [Cosmos DB 요청](mailto:askcosmosdb@microsoft.com)|

> [!NOTE]
> 지역별 장애 조치는 단일 지역 쓰기 계정에만 적용 됩니다. 다중 지역 쓰기 계정 필요 하지 않거나 쓰기 지역을 변경에 제한이 있습니다.

Cosmos DB는 자동으로 데이터의 백업을 정기적으로 수행 합니다. 백업 보존 간격 및 windows에 대 한 내용은 참조 하세요 [Azure Cosmos DB에서 온라인 백업 및 요청 시 데이터 복원](online-backup-and-restore.md)합니다.

## <a name="per-container-limits"></a>컨테이너 당 제한

Azure Cosmos 컨테이너를 사용 하는 API에 따라 두 컬렉션에 테이블을 나타내는 하거나 그래프 수 있습니다. 컨테이너 지원에 대 한 구성을 [고유 키 제약 조건이](unique-keys.md)를 [저장 프로시저, 트리거 및 Udf](stored-procedures-triggers-udfs.md), 및 [인덱싱 정책](how-to-manage-indexing-policy.md)합니다. 다음 표에서 컨테이너 내에서 구성에 특정 제한을 나열합니다. 

| Resource | 기본 제한 |
| --- | --- |
| 데이터베이스 또는 컨테이너 이름의 최대 길이 | 255 |
| 컨테이너 당 최대 저장된 프로시저 | 100 <sup>*</sup>|
| 컨테이너 당 최대 Udf | 25 <sup>*</sup>|
| 인덱싱 정책에 대 한 경로의 최대 수| 100 <sup>*</sup>|
| 컨테이너 당 고유 키의 최대 수|10 <sup>*</sup>|
| 고유 키 제약 조건당 경로의 최대 수|16 <sup>*</sup>|

<sup>*</sup> 이러한 컨테이너 당 제한 중 하나를 통해 문의 하거나 Azure 지원에 문의 하 여 늘릴 수 있습니다 [Cosmos DB 요청](mailto:askcosmosdb@microsoft.com)합니다.

## <a name="per-item-limits"></a>항목별 제한

사용할 API에 따라 Azure Cosmos 항목 행을 테이블 또는 노드 컬렉션의 문서 또는 그래프의 가장자리를 나타낼 수 있습니다. 다음 표에서 Cosmos DB에서 항목당 한도 보여 줍니다. 

| Resource | 기본 제한 |
| --- | --- |
| 항목의 최대 크기 | 2MB (JSON 표현의 길이 u t F-8) |
| 파티션 키 값의 최대 길이 | 2048 바이트 |
| Id 값의 최대 길이 | 1024 바이트 |
| 각 항목 속성의 최대 수 | 실제로 제한이 없습니다 |
| 최대 중첩 깊이 | 실제로 제한이 없습니다 |
| 속성 이름의 최대 길이 | 실제로 제한이 없습니다 |
| 속성 값의 최대 길이 | 실제로 제한이 없습니다 |
| 문자열 속성 값의 최대 길이 | 실제로 제한이 없습니다 |
| 숫자 속성 값의 최대 길이 | IEEE754 배정밀도 64 비트 |

파티션 키 및 id 값을 전체 길이 제한 제외 하 고 속성 및 중첩 깊이 번호와 같은 항목 페이로드에 대 한 제한은 없습니다 2mb 제한 크기입니다. RU 소비를 줄이기 위해 대규모 또는 복잡 한 항목 구조를 사용 하 여 컨테이너에 대 한 인덱싱 정책을 구성 해야 합니다. 참조 [Cosmos DB에서 항목을 모델링](how-to-model-partition-example.md) 실제 예 및 큰 항목을 관리 하는 패턴입니다.

## <a name="per-request-limits"></a>요청 제한

Cosmos DB는 지원 [CRUD 및 쿼리 작업](https://docs.microsoft.com/rest/api/cosmos-db/) 컨테이너, 항목 및 데이터베이스와 같은 리소스에 대 한 합니다.  

| Resource | 기본 제한 |
| --- | --- |
| 단일 작업 (예: 저장된 프로시저 실행 또는 단일 쿼리 페이지 검색)에 대 한 최대 실행 시간| 5초 |
| 최대 요청 크기 (저장된 프로시저, CRUD)| 2MB |
| 최대 응답 크기 (예를 들어, 페이지 매긴된 쿼리) | 4MB |

한 번 쿼리 실행 제한 시간 또는 응답 크기 제한에 도달 하는 같은 작업을 반환 결과 집합과 연속 토큰이 페이지 클라이언트 실행을 다시 시작 합니다. 단일 쿼리 실행 페이지/연속 사이의 기간에 제한이 있습니다.

Cosmos DB 권한 부여를 위해 HMAC를 사용합니다. 마스터 키를 사용할 수 있습니다 또는 [리소스 토큰](secure-access-to-data.md) 컨테이너 같은 리소스에 세분화 된 액세스 제어에 대 한 파티션 키 또는 항목입니다. 다음 표에서 Cosmos DB에서 권한 부여 토큰에 대 한 제한을 나열합니다.

| Resource | 기본 제한 |
| --- | --- |
| 최대 마스터 토큰 만료 시간 | 15분  |
| 최소 리소스 토큰 만료 시간 | 10 분  |
| 최대 리소스 토큰 만료 시간 | 기본적으로 24 시간입니다. 증가 될 수 있습니다 [Azure 지원 티켓을 제출](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) 을 통해 문의 하거나 [Cosmos DB 요청](mailto:askcosmosdb@microsoft.com)|
| 토큰 인증에 대 한 최대 클럭 오차| 15분 |

Cosmos DB는 쓰기 작업 동안 트리거의 실행을 지원 합니다. 서비스는 최대 하나의 사전 트리거 및 쓰기 작업당 하나의 사후 트리거를 지원합니다. 

## <a name="sql-query-limits"></a>SQL 쿼리 제한

Cosmos DB에서 지 원하는 항목을 사용 하 여 쿼리 [SQL](how-to-sql-query.md)합니다. 다음 표에서 절 이나 쿼리 길이 수를 기준으로 예를 들어 쿼리 문 제한 사항을 설명합니다.

| Resource | 기본 제한 |
| --- | --- |
| SQL 쿼리의 최대 길이| 256KB. <sup>*</sup>|
| 쿼리당 최대 조인| 5 <sup>*</sup>|
| 쿼리당 최대 ANDs| 2000 <sup>*</sup>|
| 쿼리당 최대 ORs| 2000 <sup>*</sup>|
| 쿼리당 최대 Udf| 10 <sup>*</sup>|
| 식에서 당 최대 인수| 6000 <sup>*</sup>|
| 다각형 당 최대 요소| 4096 <sup>*</sup>|

<sup>*</sup> 이러한 SQL 쿼리 제한 중 하나를 통해 문의 하거나 Azure 지원에 문의 하 여 늘릴 수 있습니다 [Cosmos DB 요청](mailto:askcosmosdb@microsoft.com)합니다.

## <a name="mongodb-api-specific-limits"></a>MongoDB API 관련 제한

Cosmos DB는 MongoDB에 대해 작성 된 응용 프로그램에 대 한 MongoDB 유선 프로토콜을 지원 합니다. 지원 되는 명령을 확인 하 고 프로토콜에서 버전 [지원 되는 MongoDB 기능 및 구문](mongodb-feature-support.md)합니다.

다음 표에서 MongoDB 기능 지원에 특정 제한을 나열합니다. MongoDB API는 SQL (코어) API에 대해 언급 된 기타 서비스 제한도 적용 됩니다.

| Resource | 기본 제한 |
| --- | --- |
| 최대 MongoDB 쿼리 메모리 크기 | 40MB |
| MongoDB 작업에 대 한 최대 실행 시간| 30초 |

## <a name="try-cosmos-db-free-limits"></a>Cosmos DB 무료 한도 시도 하세요.

다음 표에서 대 한 제한 된 [무료로 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 평가판입니다.

| Resource | 기본 제한 |
| --- | --- |
| 평가판 기간 | 30 일 (수 임의의 횟수 만큼 갱신) |
| (SQL, Gremlin Table API) 구독 당 최대 컨테이너 | 1 |
| 구독 (MongoDB API) 당 최대 컨테이너 | 3 |
| 컨테이너 당 최대 처리량 | 5,000 |
| 공유 처리량 데이터베이스당 최대 처리량 | 20000 |
| 계정당 최대 총 저장소 | 10 GB |

Try Cosmos DB는 미국 중부, 유럽 북부 및 동남 아시아 지역에서 글로벌 배포를 지원 합니다. Azure Cosmos DB 계정에 대 한 azure 지원 티켓을 만들 수 없습니다. 그러나 기존 지원 플랜을 사용 하 여 구독자에 대 한 지원이 제공 됩니다.

## <a name="next-steps"></a>다음 단계

Cosmos DB에 대해 자세히 알아보기 핵심 개념인 [전역 배포](distribute-data-globally.md) 하 고 [분할](partitioning-overview.md) 하 고 [프로 비전 된 처리량](request-units.md)합니다.

다음 요약 설명서를 통해 Azure Cosmos DB를 시작해 보세요.

* [Azure Cosmos DB SQL API 시작](create-sql-api-dotnet.md)
* [Azure Cosmos DB의 MongoDB API 시작](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API 시작](create-cassandra-dotnet.md)
* [Azure Cosmos DB Gremlin API 시작](create-graph-dotnet.md)
* [Azure Cosmos DB Table API 시작](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [무료로 Azure Cosmos DB 사용해 보기](https://azure.microsoft.com/try/cosmosdb/)
