---
title: Azure SQL 데이터베이스 하이퍼스케일 자주 묻는 질문
description: 일반적으로 하이퍼스케일 데이터베이스라고 하는 하이퍼스케일 서비스 계층의 Azure SQL 데이터베이스에 대해 고객이 하는 일반적인 질문에 대한 답변입니다.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 9f518df02b1923513fd014be53646a9a1be8465e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268627"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Azure SQL 데이터베이스 하이퍼스케일 자주 묻는 질문

이 문서에서는 이 FAQ의 나머지 부분에서 하이퍼스케일이라고 하는 Azure SQL Database 하이퍼스케일 서비스 계층의 데이터베이스를 고려하는 고객에게 자주 묻는 질문에 대한 답변을 제공합니다. 이 문서에서는 하이퍼스케일이 지원하는 시나리오와 하이퍼스케일과 호환되는 기능에 대해 설명합니다.

- 이 FAQ는 하이퍼스케일 서비스 계층에 대해 간략히 이해하고 있으며 특정 질문 및 문제에 대한 답변을 원하는 독자를 대상으로 합니다.
- 이 FAQ는 하이퍼스케일 데이터베이스를 사용하는 방법에 대한 가이드북이나 질문에 답하기 위한 것이 아닙니다. 하이퍼스케일에 대한 소개는 Azure SQL [Database 하이퍼스케일](sql-database-service-tier-hyperscale.md) 설명서를 참조하는 것이 좋습니다.

## <a name="general-questions"></a>일반적인 질문

### <a name="what-is-a-hyperscale-database"></a>하이퍼스케일 데이터베이스란?

하이퍼스케일 데이터베이스 하이퍼스케일 스케일 아웃 스토리지 기술에 기반하는 하이퍼스케일 서비스 계층에 있는 Azure SQL 데이터베이스입니다. 하이퍼스케일 데이터베이스는 최대 100TB의 데이터를 지원하며 높은 처리량과 성능을 제공할 뿐만 아니라 워크로드 요구 사항에 맞게 신속한 확장이 가능합니다. 확장은 다른 Azure SQL 데이터베이스와 마찬가지로 연결, 쿼리 처리 등 응용 프로그램에 투명합니다.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>하이퍼스케일을 지원하는 리소스 종류 및 구매 모델

하이퍼스케일 서비스 계층은 Azure SQL Database의 vCore 기반 구매 모델을 사용하는 단일 데이터베이스에만 사용할 수 있습니다.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>하이퍼스케일 서비스 계층과 범용 및 중요 비즈니스용 서비스 계층의 차이점

vCore 기반 서비스 계층은 다음 표에 설명된 대로 데이터베이스 가용성 및 저장소 유형, 성능 및 최대 크기에 따라 구분됩니다.

| | 리소스 유형 | 범용 |  하이퍼스케일 | 중요 비즈니스용 |
|:---:|:---:|:---:|:---:|:---:|
| **적합한 대상** |모두|예산 중심의 균형 잡힌 컴퓨팅 및 스토리지 옵션을 제공합니다.|대부분의 비즈니스 워크로드. 최대 100TB의 스토리지 크기를 자동 크기 조정하고, 빠른 수직 및 수평 컴퓨팅 크기 조정, 빠른 데이터베이스 복원.|트랜잭션 속도가 높고 IO 대기 시간이 낮은 OLTP 응용 프로그램입니다. 여러 동기적으로 업데이트된 복제본을 사용하여 장애 및 빠른 장애 조치(failover)에 대한 최고의 복원력을 제공합니다.|
|  **리소스 유형** ||단일 데이터베이스/탄력적 풀/관리되는 인스턴스 | 단일 데이터베이스 | 단일 데이터베이스/탄력적 풀/관리되는 인스턴스 |
| **컴퓨팅 크기**|단일 데이터베이스/탄력적 풀* | vCore 1~80개 | vCore 1~80개* | vCore 1~80개 |
| |관리되는 인스턴스 | vCore 8, 16, 24, 32, 40, 64, 80개 | 해당 없음 | vCore 8, 16, 24, 32, 40, 64, 80개 |
| **스토리지 유형** | 모두 |프리미엄 원격 스토리지(인스턴스별) | 로컬 SSD 캐시를 사용한 분리형 스토리지(인스턴스별) | 초고속 로컬 SSD 스토리지(인스턴스별) |
| **스토리지 크기** | 단일 데이터베이스/탄력적 풀*| 5GB~4TB | 최대 100TB | 5GB~4TB |
| | 관리되는 인스턴스  | 32GB~8TB | 해당 없음 | 32GB~4TB |
| **IOPS** | 단일 데이터베이스 | vCore당 500 IOPS(최대 7,000 IOPS) | 하이퍼스케일은 여러 수준에서 캐싱이 있는 다중 계층 아키텍처입니다. 효과적인 IOPS는 워크로드에 따라 달라집니다. | 5000 IOPS(최대 200,000 IOPS)|
| | 관리되는 인스턴스 | 파일 크기에 따라 다름 | 해당 없음 | 1375 IOPS/vCore |
|**가용성**|모두|복제본 1개, 확장 읽기 없음, 로컬 캐시 없음 | 여러 복제본, 최대 4개의 확장 축소 읽기, 부분 로컬 캐시 | 복제본 3개, 확장 중 읽기 1개, 영역 중복 HA, 전체 로컬 저장소 |
|**Backup**|모두|RA-GRS, 7-35일 보존(기본적으로 7일)| RA-GRS, 7일 보존, 일정한 시간 적시 복구(PITR) | RA-GRS, 7-35일 보존(기본적으로 7일) |

\*하이퍼스케일 서비스 계층에서는 탄력적 풀이 지원되지 않습니다.

### <a name="who-should-use-the-hyperscale-service-tier"></a>하이퍼스케일 서비스 계층을 사용해야 하는 경우

하이퍼스케일 서비스 계층은 대규모 온-프레미스 SQL Server 데이터베이스를 가지고 있고 클라우드로 이동하여 응용 프로그램을 현대화하려는 고객 또는 Azure SQL Database를 이미 사용하고 있고 크게 확장하려는 고객을 위한 것입니다. 데이터베이스 성장 가능성이 있습니다. 하이퍼스케일은 고성능과 고가용성을 모두 원하는 고객에게도 적합합니다. 하이퍼스케일을 통해 얻을 수 있는 사항:

- 최대 100TB의 데이터베이스 크기
- 데이터베이스 크기에 관계없이 빠른 데이터베이스 백업(백업은 저장소 스냅숏을 기반으로 합니다).
- 데이터베이스 크기에 관계없이 빠른 데이터베이스 복원(복원은 저장소 스냅숏에서 나온 것임)
- 데이터베이스 크기 및 vCore 수에 관계없이 로그 처리량 증가
- 읽기 오프로딩 및 핫 대기로 사용되는 하나 이상의 읽기 전용 복제본을 사용하여 배율 조정을 읽습니다.
- 일정 시간에 컴퓨팅 규모가 신속하게 확장되어, 강력한 기능으로 과도한 워크로드를 수용하고 일정 시간에 규모를 축소할 수 있습니다. 예를 들어 P6와 P11 간에 는 확장 및 축소와 비슷하지만 데이터 작업 크기가 아니기 때문에 훨씬 빠릅니다.

### <a name="what-regions-currently-support-hyperscale"></a>현재 하이퍼스케일이 지원되는 지역

하이퍼스케일 서비스 계층은 현재 [Azure SQL Database 하이퍼스케일 개요](sql-database-service-tier-hyperscale.md#regions)아래에 나열된 리전에서 사용할 수 있습니다.

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>논리 서버에 대해 여러 개의 하이퍼스케일 데이터베이스를 만들 수 있나요?

예. 논리 서버별 하이퍼스케일 데이터베이스 수에 대한 제한과 자세한 내용은 [논리 서버의 단일 및 풀링된 데이터베이스에 대한 SQL Database 리소스 제한](sql-database-resource-limits-logical-server.md)을 참조하세요.

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>하이퍼스케일 데이터베이스의 성능 특성은 무엇입니까?

하이퍼스케일 아키텍처는 대규모 데이터베이스 크기를 지원하면서 고성능 및 처리량을 제공합니다. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>하이퍼스케일 데이터베이스의 확장성은 어떻게 되나요?

하이퍼스케일은 워크로드 수요에 따라 빠른 확장성을 제공합니다.

- **강화/축소**

  하이퍼스케일을 사용하면 CPU 및 메모리와 같은 리소스 측면에서 기본 계산 크기를 확장한 다음 일정한 시간에 축소할 수 있습니다. 스토리지가 공유되기 때문에 강화와 축소는 데이터 작업의 크기가 아닙니다.  
- **규모 감축/확장**

  하이퍼스케일을 사용하면 읽기 요청을 제공하는 데 사용할 수 있는 하나 이상의 추가 계산 복제본을 프로비전할 수도 있습니다. 즉, 이러한 추가 계산 복제본을 읽기 전용 복제본으로 사용하여 기본 계산에서 읽기 워크로드를 오프로드할 수 있습니다. 읽기 전용 외에도 이러한 복제본은 주 복제본에서 장애 조치(failover)가 있는 경우에도 핫 대기 역할을 합니다.

  이러한 각 추가 계산 복제본의 프로비전은 일정한 시간에 수행할 수 있으며 온라인 작업입니다. 연결 문자열의 `ApplicationIntent` 인수를 로 설정하여 이러한 추가 읽기 전용 계산 `ReadOnly`복제본에 연결할 수 있습니다. 응용 프로그램 `ReadOnly` 의도가 있는 모든 연결은 추가 읽기 전용 계산 복제본 중 하나로 자동으로 라우팅됩니다.

## <a name="deep-dive-questions"></a>심층 다이빙 질문

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>하이퍼스케일 과 단일 데이터베이스를 단일 논리 서버로 혼합할 수 있습니까?

 예, 할 수 있습니다.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>하이퍼스케일에서 내 애플리케이션 프로그래밍 모델을 변경해야 하나요?

아니요, 애플리케이션 프로그래밍 모델은 그대로 유지됩니다. 평소와 같이 연결 문자열을 사용하고 다른 일반적인 방법으로 Hyperscale 데이터베이스와 상호 작용합니다.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>하이퍼스케일 데이터베이스의 기본값인 트랜잭션 격리 수준

기본 복제본에서 기본 트랜잭션 격리 수준은 RCSI(커밋된 스냅숏 격리 읽기)입니다. 읽기 배율 조정 보조 복제본에서 기본 격리 수준은 스냅숏입니다.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>온-프레미스 또는 IaaS SQL Server 라이선스를 하이퍼스케일로 가져올 수 있습니까?

예, [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 하이퍼스케일에 사용할 수 있습니다. 모든 SQL Server Standard 코어를 하이퍼스케일 vCore 하나에 매핑할 수 있습니다. 모든 SQL Server Enterprise 코어를 하이퍼스케일 vCore 4개에 매핑할 수 있습니다. 보조 복제본에 대해 SQL 라이선스가 필요하지 않습니다. Azure 하이브리드 혜택 가격은 읽기 확장(보조) 복제본에 자동으로 적용됩니다.

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>하이퍼스케일은 어떤 종류의 워크로드를 위해 설계되어 있습니까?

하이퍼스케일은 모든 SQL Server 워크로드를 지원하지만 주로 OLTP에 최적화되어 있습니다. 하이브리드(HTAP) 및 분석(데이터 마트) 워크로드도 가져올 수 있습니다.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>Azure SQL 데이터 웨어하우스와 Azure SQL 데이터베이스 하이퍼스케일 중에서 선택해 두는 방법은 무엇입니까?

현재 SQL Server를 데이터 웨어하우스로 사용하여 대화형 분석 쿼리를 실행하는 경우, 하이퍼스케일은 더 저렴한 비용으로 소규모 및 중간 규모의 데이터 웨어하우스(예: 몇 TB에서 최대 100TB)를 호스팅할 수 있고 SQL Server 데이터를 마이그레이션할 수 있으므로 훌륭한 옵션입니다. 최소한의 T-SQL 코드 변경으로 하이퍼스케일로 워크로드를 처리합니다.

복잡한 쿼리와 지속적인 수집 속도가 100MB/s보다 높은 대규모로 데이터 분석을 실행하거나 병렬 데이터 웨어하우스(PDW), Teradata 또는 기타 대규모 병렬 처리(MPP) 데이터 웨어하우스를 사용하는 경우 SQL 데이터 웨어하우스가 최선의 선택일 수 있습니다.
  
## <a name="hyperscale-compute-questions"></a>하이퍼스케일 컴퓨팅 질문

### <a name="can-i-pause-my-compute-at-any-time"></a>컴퓨팅을 아무 때나 일시 중지할 수 있나요?

그러나 사용량이 적은 시간에는 계산 및 복제본 수를 축소하여 사용량이 적은 시간 동안 비용을 줄일 수 있습니다.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>메모리 집약적 워크로드에 대해 추가 RAM을 사용하여 계산 복제본을 프로비전할 수 있습니까?

아니요. RAM을 더 확보하려면 더 큰 컴퓨팅 크기로 업그레이드해야 합니다. 자세한 내용은 [하이퍼스케일 스토리지 및 컴퓨팅 크기](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)를 참조하세요.

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>서로 다른 크기의 여러 계산 복제본을 프로비전할 수 있습니까?

아니요.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>지원되는 읽기 확장 복제본 수

하이퍼스케일 데이터베이스는 기본적으로 하나의 읽기 확장 복제본(주 복제본을 포함한 두 개의 복제본)으로 만들어집니다. [Azure 포털](https://portal.azure.com) 또는 [REST API를](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)사용하여 읽기 전용 복제본 수를 0에서 4 사이로 확장할 수 있습니다.

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>고가용성을 위해 추가 계산 복제본을 프로비전해야 합니까?

하이퍼스케일 데이터베이스에서는 데이터 복원력은 저장소 수준에서 제공됩니다. 복원력을 제공하기 위해 복제본이 하나만 있으면 됩니다. 컴퓨팅 복제본이 다운되면 데이터가 손실되지 않고 새로운 복제본이 자동으로 생성됩니다.

단, 복제본이 하나만 있으면 장애 조치(failover) 후 새 복제본에 로컬 캐시를 구축하는 데 약간의 시간이 걸릴 수 있습니다. 캐시 재생 성 단계에서 데이터베이스는 페이지 서버에서 직접 데이터를 가져오므로 저장소 대기 시간이 증가하고 쿼리 성능이 저하됩니다.

장애 조치 의 영향을 최소화하면서 고가용성이 필요한 미션 크리티컬 앱의 경우 주 계산 복제본을 포함하여 최소 2개의 계산 복제본을 프로비전해야 합니다. 이 값이 기본 구성입니다. 이렇게 하면 장애 조치 대상역할을 하는 핫 대기 복제본을 사용할 수 있습니다.

## <a name="data-size-and-storage-questions"></a>데이터 크기 및 스토리지 관련 질문

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>하이퍼스케일에서 지원되는 최대 데이터베이스 크기는 무엇입니까?

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>하이퍼스케일의 트랜잭션 로그 크기는 얼마인가요?

하이퍼스케일의 트랜잭션 로그는 사실상 무한합니다. 로그 처리량이 많은 시스템에서 로그 공간 부족을 걱정할 필요가 없습니다. 그러나 지속적으로 적극적으로 워크로드를 작성하기 위해 로그 생성 속도가 제한될 수 있습니다. 최대 지속 로그 생성 속도는 100MB/s입니다.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>데이터베이스가 `tempdb` 커질수록 확장이 수행됩니까?

`tempdb` 데이터베이스는 로컬 SSD 저장소에 있으며 프로비전하는 계산 크기에 비례하여 크기가 조정됩니다. 최대 `tempdb` 성능 이점을 제공하도록 최적화되어 있습니다. `tempdb`크기는 구성할 수 없으며 관리됩니다.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>데이터베이스 크기가 자동으로 증가합니까, 아니면 데이터 파일의 크기를 관리해야 합니까?

데이터베이스 크기는 데이터를 추가로 삽입/수집하면 자동으로 커집니다.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>하이퍼스케일이 지원하거나 시작하는 가장 작은 데이터베이스 크기는 무엇입니까?

40GB. 하이퍼스케일 데이터베이스는 시작 크기가 10GB로 만들어집니다. 그런 다음 10분마다 10GB씩 증가하여 40GB 크기에 도달할 때까지 증가하기 시작합니다. 이러한 10GB 척은 더 많은 IOPS및 더 높은 I/O 병렬 처리성을 제공하기 위해 다른 페이지 서버에 할당됩니다. 이러한 최적화로 인해 초기 데이터베이스 크기를 40GB보다 작게 선택하더라도 데이터베이스가 자동으로 40GB 이상으로 증가합니다.

### <a name="in-what-increments-does-my-database-size-grow"></a>데이터베이스가 증가하는 증분의 크기는 얼마인가요?

각 데이터 파일은 10GB씩 증가합니다. 여러 데이터 파일이 동시에 증가할 수 있습니다.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>하이퍼스케일 로컬 또는 리모컨의 스토리지입니다.

하이퍼스케일에서 데이터 파일은 Azure 표준 스토리지에 저장됩니다. 데이터는 계산 복제본에 가까운 페이지 서버의 로컬 SSD 저장소에 완전히 캐시됩니다. 또한 계산 복제본에는 원격 페이지 서버에서 데이터를 가져오는 빈도를 줄이기 위해 로컬 SSD 및 메모리에 데이터 캐시가 있습니다.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>하이퍼스케일로 파일이나 파일 그룹을 관리하거나 정의할 수 있나요?

아니요. 데이터 파일이 자동으로 추가됩니다. 추가 파일 그룹을 만드는 일반적인 이유는 하이퍼스케일 저장소 아키텍처에는 적용되지 않습니다.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>데이터베이스의 데이터 증가에 대해 하드 캡을 프로비전할 수 있나요?

아니요.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>하이퍼스케일로 데이터 파일을 배치하는 방법

데이터 파일은 데이터 파일당 하나의 페이지 서버로 페이지 서버에 의해 제어됩니다. 데이터 크기가 커지면 데이터 파일과 관련 페이지 서버가 추가됩니다.

### <a name="is-database-shrink-supported"></a>데이터베이스 축소가 지원되나요?

아니요.

### <a name="is-data-compression-supported"></a>데이터 압축이 지원됩니다.

예. 행, 페이지 및 열 저장소 압축을 포함합니다.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>거대한 테이블이 있는 경우 테이블 데이터가 여러 데이터 파일에 분산되나요?

예. 주어진 테이블과 연결된 데이터 페이지는 모두 동일한 파일 그룹에 속하는 여러 데이터 파일에 분산될 수 있습니다. SQL Server는 [비례 채우기 전략을](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) 사용하여 데이터 파일을 통해 데이터를 배포합니다.

## <a name="data-migration-questions"></a>데이터 마이그레이션 질문

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>기존 Azure SQL 데이터베이스를 하이퍼스케일 서비스 계층으로 이동할 수 있나요?

예. 기존 Azure SQL 데이터베이스를 하이퍼스케일로 이동할 수 있습니다. 단방향 마이그레이션입니다. 하이퍼스케일에서 또 다른 서비스 계층으로 데이터베이스를 이동할 수는 없습니다. 개념 증명(POC)의 경우 데이터베이스 복사본을 만들고 복사본을 Hyperscale으로 마이그레이션하는 것이 좋습니다.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>하이퍼스케일 데이터베이스를 다른 서비스 계층으로 이동할 수 있습니까?

아니요. 지금은 하이퍼스케일 데이터베이스를 다른 서비스 계층으로 이동할 수 없습니다.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>하이퍼스케일 서비스 계층으로 마이그레이션한 후에 손실되는 기능이 있나요?

예. 일부 Azure SQL Database 기능은 장기 백업 보존을 포함하되 이에 국한되지 않는 하이퍼스케일에서는 아직 지원되지 않습니다. 하이퍼스케일로 데이터베이스를 마이그레이션한 후에는 해당 기능이 작동을 중지합니다.  이러한 제한은 일시적일 것으로 예상됩니다.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>온-프레미스 SQL Server 데이터베이스 또는 클라우드 가상 컴퓨터에서 SQL Server 데이터베이스를 하이퍼스케일로 이동할 수 있습니까?

예. 모든 기존 마이그레이션 기술을 사용하여 트랜잭션 복제 및 기타 데이터 이동 기술(대량 복사, Azure 데이터 팩터리, Azure Databricks, SSIS)을 포함하여 하이퍼스케일로 마이그레이션할 수 있습니다. 많은 마이그레이션 시나리오를 지원하는 [Azure 데이터베이스 마이그레이션 서비스도](../dms/dms-overview.md)참조하십시오.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>온-프레미스 또는 가상 시스템 환경에서 하이퍼스케일로 마이그레이션하는 동안 가동 중지 시간은 어떻게 됩니까?

하이퍼스케일로 마이그레이션하는 가동 중지 시간은 데이터베이스를 다른 Azure SQL Database 서비스 계층으로 마이그레이션할 때의 가동 중지 시간과 동일합니다. [트랜잭션 복제](replication-to-sql-database.md#data-migration-scenario
)를 사용하면 크기가 최대 몇 TB에 이르는 데이터베이스의 마이그레이션 가동 중지 시간을 최소화할 수 있습니다. 매우 큰 데이터베이스(10TB 이상)의 경우 ADF, Spark 또는 기타 데이터 이동 기술을 사용하여 데이터를 마이그레이션하는 것을 고려할 수 있습니다.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>하이퍼스케일에 X 양의 데이터를 가져오는 데 걸리는 시간

하이퍼스케일은 100MB/s의 새/변경된 데이터를 사용할 수 있지만 Azure SQL 데이터베이스로 데이터를 이동하는 데 필요한 시간도 사용 가능한 네트워크 처리량, 원본 읽기 속도 및 대상 데이터베이스 서비스 수준 목표의 영향을 받습니다.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>Blob 저장소에서 데이터를 읽고 빠른 로드를 수행할 수 있습니까(예: SQL 데이터 웨어하우스의 Polybase).

클라이언트 응용 프로그램이 Azure Storage에서 데이터를 읽고 다른 Azure SQL 데이터베이스와 마찬가지로 하이퍼스케일 데이터베이스로 데이터 로드를 로드할 수 있습니다. Polybase는 현재 Azure SQL 데이터베이스에서 지원되지 않습니다. 빠른 로드를 제공하는 대신 Azure [Data Factory를](https://docs.microsoft.com/azure/data-factory/)사용하거나 [SQL용 스파크 커넥터를](sql-database-spark-connector.md)사용하여 [Azure Databricks에서](https://docs.microsoft.com/azure/azure-databricks/) 스파크 작업을 사용할 수 있습니다. SQL에 대한 Spark 커넥터는 대량 삽입을 지원합니다.

대량 삽입 또는 OPENROWSET: [Azure Blob 저장소의 데이터에 대한 대량 액세스 예제를](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location)사용하여 Azure Blob 저장소에서 데이터를 대량으로 읽을 수도 있습니다.

하이퍼스케일에서는 단순 복구 또는 대량 로깅 모델이 지원되지 않습니다. 고가용성 및 시점 복구를 제공하려면 전체 복구 모델이 필요합니다. 그러나 하이퍼스케일 로그 아키텍처는 다른 Azure SQL Database 서비스 계층에 비해 더 나은 데이터 수집 속도를 제공합니다.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>하이퍼스케일을 사용하면 많은 양의 데이터를 병렬로 수집하기 위해 여러 노드를 프로비전할 수 있습니까?

아니요. 하이퍼스케일은 대칭 다중 처리(SMP) 아키텍처이며 대규모 병렬 처리(MPP) 또는 다중 마스터 아키텍처가 아닙니다. 여러 개의 복제본을 만들어서 읽기 전용 워크로드 규모 확장할 수만 있습니다.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>하이퍼스케일로 마이그레이션할 때 지원되는 가장 오래된 SQL Server 버전은 무엇입니까?

SQL Server 2005입니다. 자세한 내용은 [단일 데이터베이스 또는 풀링된 데이터베이스로 마이그레이션](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database)을 참조하세요. 호환성 문제는 [데이터베이스 마이그레이션 호환성 문제 해결](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)을 참조하세요.

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>하이퍼스케일은 아마존 오로라, MySQL, PostgreSQL, 오라클, DB2 및 기타 데이터베이스 플랫폼과 같은 다른 데이터 소스에서 마이그레이션을 지원합니다.

예. [Azure 데이터베이스 마이그레이션 서비스는](../dms/dms-overview.md) 많은 마이그레이션 시나리오를 지원합니다.

## <a name="business-continuity-and-disaster-recovery-questions"></a>비즈니스 연속성 및 재해 복구 질문

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>하이퍼스케일 데이터베이스에 제공되는 SLA

[Azure SQL 데이터베이스에 대한 SLA를](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/)참조하십시오. 추가 보조 계산 복제본은 두 개 이상의 보조 계산 복제본이 있는 데이터베이스의 가용성을 최대 99.99%까지 향상시키게 됩니다.

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Azure SQL Database 서비스에서 데이터베이스 백업이 관리되나요?

예.

### <a name="how-often-are-the-database-backups-taken"></a>데이터베이스 백업이 얼마나 자주 수행되나요?

하이퍼스케일 데이터베이스에 대한 기존의 전체, 차동 및 로그 백업은 없습니다. 대신 데이터 파일의 일반 저장소 스냅숏이 있습니다. 생성된 로그는 구성된 보존 기간 동안 있는 그대로 유지되므로 보존 기간 내의 어느 시점까지 복원할 수 있습니다.

### <a name="does-hyperscale-support-point-in-time-restore"></a>하이퍼스케일 지원 시점 복원

예.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>하이퍼스케일에서 데이터베이스 복원을 위한 RPO(복구 지점 목표)/복구 시간 목표(RTO)란 무엇입니까?

RPO는 0분입니다. RTO 목표는 데이터베이스 크기에 관계없이 10분 미만입니다. 

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>데이터베이스 백업이 주 복제본 또는 보조 복제본의 계산 성능에 영향을 미칩니까?

아니요. 백업은 저장소 하위 시스템에서 관리되며 저장소 스냅숏을 활용합니다. 사용자 워크로드에 영향을 미치지 않습니다.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>하이퍼스케일 데이터베이스로 지리적 복원을 수행할 수 있습니까?

예. 지역 복원은 완전히 지원됩니다. 시점 복원과 달리 지역 복원에는 데이터 크기 작업이 필요합니다. 데이터 파일은 병렬로 복사되므로 이 작업의 기간은 주로 전체 데이터베이스 크기가 아니라 데이터베이스에서 가장 큰 파일의 크기에 따라 달라집니다. 원본 데이터베이스의 영역과 [쌍을 이루는](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) Azure 영역에서 데이터베이스를 복원하면 지리적 복원 시간이 크게 짧아집니다.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>하이퍼스케일 데이터베이스로 지역 복제를 설정할 수 있습니까?

지금은 없습니다.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>하이퍼스케일 데이터베이스 백업을 사용하여 온-프레미스 서버 또는 VM의 SQL Server에 복원할 수 있습니까?

아니요. 하이퍼스케일 데이터베이스의 저장소 형식은 릴리스된 SQL Server 버전과 다르며 백업을 제어하거나 액세스할 수 없습니다. 하이퍼스케일 데이터베이스에서 데이터를 빼려면 Azure 데이터 팩터리, Azure 데이터 브릭, SSIS 등과 같은 모든 데이터 이동 기술을 사용하여 데이터를 추출할 수 있습니다.

## <a name="cross-feature-questions"></a>상호 기능 간 질문

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>하이퍼스케일 서비스 계층으로 마이그레이션한 후에 손실되는 기능이 있나요?

예. 일부 Azure SQL Database 기능은 장기 백업 보존을 포함하되 이에 국한되지 않는 하이퍼스케일에서는 지원되지 않습니다. 하이퍼스케일로 데이터베이스를 마이그레이션한 후에는 해당 기능이 작동을 중지합니다.

### <a name="will-polybase-work-with-hyperscale"></a>폴리베이스는 하이퍼 스케일과 함께 작동합니다

아니요. Polybase Azure SQL 데이터베이스에서 지원 되지 않습니다.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>하이퍼스케일은 R과 파이썬에 대한 지원이 있습니까?

지금은 없습니다.

### <a name="are-compute-nodes-containerized"></a>계산 노드가 컨테이너화되어 있습니까?

아니요. 하이퍼스케일 프로세스는 컨테이너가 아닌 [서비스 패브릭](https://azure.microsoft.com/services/service-fabric/) 노드(VM)에서 실행됩니다.

## <a name="performance-questions"></a>성능 질문

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>하이퍼스케일 데이터베이스에서 푸시할 수 있는 쓰기 처리량

모든 하이퍼스케일 계산 크기에 대해 트랜잭션 로그 처리량 한도는 100MB/s로 설정됩니다. 이 속도를 달성하는 기능은 워크로드 유형, 클라이언트 구성 및 이 속도로 로그를 생성하기에 충분한 계산 용량을 갖는 등 여러 요인에 따라 달라집니다.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>가장 큰 컴퓨팅에서 얼마나 많은 IOPS를 얻을 수 있습니까?

IOPS 및 IO 대기 시간은 워크로드 패턴에 따라 달라집니다. 액세스중인 데이터가 계산 복제본에 캐시되면 로컬 SSD와 유사한 IO 성능이 표시됩니다.

### <a name="does-my-throughput-get-affected-by-backups"></a>처리량이 백업의 영향을 받나요?

아니요. 계산은 저장소 계층에서 분리됩니다. 이렇게 하면 백업의 성능 영향이 제거됩니다.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>추가 계산 복제본을 프로비전할 때 처리량이 영향을 받습니까?

저장소가 공유되고 기본 계산 복제본과 보조 계산 복제본 간에 직접적인 물리적 복제가 발생하지 않으므로 보조 복제본을 추가하여 주 복제본의 처리량은 직접적인 영향을 받지 않습니다. 그러나 보조 복제본의 읽기 성능 저하를 방지하기 위해 보조 복제본 및 페이지 서버에 로그가 적용될 수 있도록 주 복제본에 대한 워크로드를 지속적으로 적극적으로 작성할 수 있습니다.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>하이퍼스케일 데이터베이스에서 성능 문제를 진단하고 해결하려면 어떻게 해야 합니까?

대부분의 성능 문제, 특히 저장소 성능에 기반하지 않는 경우 일반적인 SQL Server 진단 및 문제 해결 단계가 적용됩니다. 하이퍼스케일별 스토리지 진단의 경우 [SQL 하이퍼스케일 성능 문제 해결 진단을](sql-database-hyperscale-performance-diagnostics.md)참조하십시오.

## <a name="scalability-questions"></a>확장성 질문

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>계산 복제본을 확장 및 축소하는 데 걸리는 시간

계산을 위또는 아래로 확장하는 데는 데이터 크기에 관계없이 5~10분이 소요됩니다.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>강화 및 축소 작업을 진행하는 동안 데이터베이스는 오프라인 상태인가요?

아니요. 강화 및 축소는 온라인 상태가 됩니다.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>크기 조정 작업을 진행하는 동안 연결이 끊어질 수 있나요?

확장 또는 축소로 인해 크기 조정 작업이 끝날 때 장애 조치(failover)가 발생할 때 기존 연결이 삭제됩니다. 보조 복제본을 추가해도 연결이 끊어지지 않습니다.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>계산 복제본의 확장 및 축소가 자동 또는 최종 사용자 트리거 작업인가요?

최종 사용자가 트리거합니다. 자동이 아닙니다.  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>계산이 확장됨에 `tempdb` 따라 데이터베이스 크기도 증가합니까?

예. 계산이 `tempdb` 증가함에 따라 데이터베이스가 자동으로 확장됩니다.  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>다중 마스터 시스템과 같은 여러 주 컴퓨팅 복제본을 프로비전할 수 있으며, 여기서 여러 주 컴퓨팅 헤드가 더 높은 수준의 동시성을 구동할 수 있습니다.

아니요. 기본 계산 복제본만 읽기/쓰기 요청을 허용합니다. 보조 계산 복제본은 읽기 전용 요청만 허용합니다.

## <a name="read-scale-out-questions"></a>스케일 아웃 질문 읽기

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>프로비전할 수 있는 보조 계산 복제본 수

기본적으로 하이퍼스케일 데이터베이스에 대해 하나의 보조 복제본을 만듭니다. 복제본 수를 조정하려면 [Azure 포털](https://portal.azure.com) 또는 [REST API를](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)사용하여 복제본 수를 조정할 수 있습니다.

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>이러한 보조 계산 복제본에 연결하려면 어떻게 해야 합니까?

연결 문자열의 `ApplicationIntent` 인수를 로 설정하여 이러한 추가 읽기 전용 계산 `ReadOnly`복제본에 연결할 수 있습니다. 표시된 `ReadOnly` 모든 연결은 추가 읽기 전용 계산 복제본 중 하나로 자동으로 라우팅됩니다.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>SSMS 또는 기타 클라이언트 도구를 사용하여 보조 계산 복제본에 성공적으로 연결했는지 확인하려면 어떻게 해야 합니까?

다음 T-SQL 쿼리를 실행할 `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`수 있습니다.
그 결과 `READ_ONLY` 읽기 전용 보조 복제본에 연결되어 `READ_WRITE` 있고 주 복제본에 연결되어 있는 경우 결과가 생성됩니다. 데이터베이스 컨텍스트는 `master` 데이터베이스가 아닌 하이퍼스케일 데이터베이스의 이름으로 설정해야 합니다.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>읽기 배율 조정 복제본에 대한 전용 끝점을 만들 수 있습니까?

아니요. 을 지정하여 확장 축소 복제본 읽기에만 `ApplicationIntent=ReadOnly`연결할 수 있습니다.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>시스템에서 읽기 워크로드에 대한 지능적인 부하 분산이 수행되나요?

아니요. 읽기 전용 의도가 있는 새 연결이 임의의 읽기 배율 조정 복제본으로 리디렉션됩니다.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>보조 계산 복제본을 주 복제본과 독립적으로 확장/축소할 수 있습니까?

아니요. 보조 계산 복제본은 고가용성 장애 조치 대상으로도 사용되므로 장애 조치 후 예상되는 성능을 제공하기 위해 기본 구성과 동일한 구성이 필요합니다.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>기본 계산 `tempdb` 및 추가 보조 계산 복제본에 대해 다른 크기 조정을 받을 수 있습니까?

아니요. `tempdb` 데이터베이스는 계산 크기 프로비저닝에 따라 구성되며 보조 계산 복제본은 기본 계산과 크기가 같습니다.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>보조 계산 복제본에 인덱스 및 뷰를 추가할 수 있습니까?

아니요. 하이퍼스케일 데이터베이스에는 공유 저장소가 있으므로 모든 계산 복제본에 동일한 테이블, 인덱스 및 보기가 표시됩니다. 보조 에서 읽기에 최적화 된 추가 인덱스를 원하는 경우 기본에 추가 해야 합니다.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>기본 계산 복제본과 보조 계산 복제본 사이에 지연이 얼마나 지연될 수 있습니까?

트랜잭션이 주 에서 기본에 커밋된 시점부터 보조 로그 생성 속도에 따라 달라지는 시간까지의 데이터 대기 시간은 현재 로그 생성 속도에 따라 달라집니다. 일반적인 데이터 대기 시간은 밀리초가 낮습니다.

## <a name="next-steps"></a>다음 단계

하이퍼스케일 서비스 계층에 대한 자세한 내용은 [하이퍼스케일 서비스 계층을](sql-database-service-tier-hyperscale.md)참조하십시오.
