---
title: Azure Cosmos 컨테이너에 대한 전 세계적으로 분산된 트랜잭션 및 분석(비공개 미리 보기) 저장소
description: Azure Cosmos 컨테이너에 대한 트랜잭션 및 분석 저장소 및 구성 옵션에 대해 알아봅니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623390"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Azure Cosmos 컨테이너에 대한 전 세계적으로 분산된 트랜잭션 및 분석 저장소

Azure Cosmos 컨테이너는 트랜잭션 저장소 엔진과 업데이터 분석 저장소 엔진(개인 미리 보기)의 두 개의 저장소 엔진에 의해 내부적으로 지원됩니다. 두 스토리지 엔진은 더 빠른 업데이트를 위해 로그 구조화되고 쓰기에 최적화되어 있습니다. 그러나 각 인코딩은 다르게 인코딩됩니다.

* **트랜잭션 저장소 엔진** - 빠른 트랜잭션 읽기 및 쿼리를 위해 행 지향 형식으로 인코딩됩니다.

* **분석 저장소 엔진** - 빠른 분석 쿼리 및 검사를 위해 열 형식으로 인코딩됩니다.

![스토리지 엔진 및 Azure 코스모스 DB API 매핑](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

트랜잭션 저장소 엔진은 로컬 SSD에 의해 지원되는 반면 분석 저장소는 저렴한 오프 클러스터 SSD 스토리지에 저장됩니다. 다음 표는 트랜잭션 저장소와 분석 저장소 간의 주목할 만한 차이점을 캡처합니다.


|기능  |트랜잭션 저장소  |분석 스토리지 |
|---------|---------|---------|
|Azure 코스모스 컨테이너당 최대 저장소 |   제한 없음      |    제한 없음     |
|논리 파티션 키당 최대 스토리지   |   20GB      |   제한 없음      |
|스토리지 인코딩  |   내부 형식을 사용하여 행 지향적입니다.   |   아파치 마루 형식을 사용하여 열 지향. |
|스토리지 지역성 |   로컬/클러스터 내 SSD로 백업되는 복제된 스토리지입니다. |  저렴한 원격/오프 클러스터 SSD로 백업되는 복제 스토리지.       |
|내구성  |    99.99999 (7-9 s)     |  99.99999 (7-9 s)       |
|데이터에 액세스하는 API  |   SQL, 몽고DB, 카산드라, 그렘린, 테이블 등       | Apache Spark         |
|보존(실시간 또는 TTL)   |  Azure Cosmos 컨테이너에서 속성을 사용하여 구성된 `DefaultTimeToLive` 정책 기반입니다.       |   Azure Cosmos 컨테이너에서 속성을 사용하여 구성된 `ColumnStoreTimeToLive` 정책 기반입니다.      |
|GB당 가격    |   가격 [책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/) 보기     |   가격 [책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/) 보기        |
|스토리지 트랜잭션 가격    |  가격 [책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/) 보기         |   가격 [책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/) 보기        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>트랜잭션 및 분석 스토리지의 이점

### <a name="no-etl-operations"></a>ETL 운영 없음

기존의 분석 파이프라인은 각각 ETL(추출-변환-로드) 작업이 필요한 여러 단계로 복잡하며 계산 및 스토리지 계층을 오갈 수 있습니다. 이로 인해 복잡한 데이터 처리 아키텍처가 생성됩니다. 즉, 여러 단계의 스토리지 및 컴퓨팅에 대한 높은 비용과 다양한 스토리지 및 컴퓨팅 단계 간에 전송되는 방대한 양의 데이터로 인해 대기 시간이 많이 줄어듭니다.  

Azure Cosmos DB를 사용하여 ETL 작업을 수행하는 오버헤드는 없습니다. 각 Azure Cosmos 컨테이너는 트랜잭션 및 분석 저장소 엔진에 의해 지원되며 트랜잭션 및 분석 저장소 엔진 간의 데이터 전송은 데이터베이스 엔진 내에서 네트워크 홉 없이 수행됩니다. 기존의 분석 솔루션에 비해 지연 시간과 비용이 현저히 낮습니다. 또한 트랜잭션 워크로드와 분석 워크로드 모두에 대해 전 세계적으로 분산된 단일 스토리지 시스템을 사용할 수 있습니다.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>여러 버전 저장, 업데이트 및 분석 저장소 쿼리

분석 저장소는 완전히 업데이트 할 수 있으며 Azure Cosmos 컨테이너에서 발생한 모든 트랜잭션 업데이트의 전체 버전 기록을 포함합니다.

트랜잭션 저장소에 대한 모든 업데이트는 30초 이내에 분석 저장소에 표시될 수 있습니다. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>전 세계에 분산된 멀티 마스터 분석 스토리지

Azure Cosmos 계정이 단일 리전으로 범위가 지정되는 경우 컨테이너에 저장된 데이터(트랜잭션 및 분석 저장소)도 단일 리전으로 범위가 지정됩니다. 반면Azure Cosmos 계정이 전역적으로 분산된 경우 컨테이너에 저장된 데이터도 전역적으로 분산됩니다.

여러 쓰기 영역으로 구성된 Azure Cosmos 계정의 경우 컨테이너에 쓰기(트랜잭션 및 분석 저장소 모두에)는 항상 로컬 리전에서 수행되므로 빠릅니다.

단일 또는 다중 영역 Azure Cosmos 계정의 경우 단일 쓰기 영역(단일 마스터) 또는 다중 쓰기 영역(다중 마스터라고도 함)에 관계없이 트랜잭션 및 분석 읽기/쿼리는 지정된 리전에서 로컬로 수행됩니다.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>트랜잭션 워크로드와 분석 워크로드 간의 성능 격리

지정된 리전에서 트랜잭션 워크로드는 컨테이너의 트랜잭션/행 저장소에 대해 작동합니다. 반면에 분석 워크로드는 컨테이너의 분석/열 저장소에 대해 작동합니다. 두 스토리지 엔진은 독립적으로 작동하며 워크로드 간에 엄격한 성능 격리를 제공합니다.

트랜잭션 워크로드는 프로비저닝된 처리량(RUs)을 사용합니다. 트랜잭션 워크로드와 달리 분석 워크로드 처리량은 실제 사용량을 기반으로 합니다. 분석 워크로드는 온디맨드 리소스를 소비합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 TTL(Time to Live)](time-to-live.md)
