---
title: Azure SQL Database 서버 리소스 제한
description: 이 문서에서는 단일 데이터베이스 및 탄력적 풀에 대한 Azure SQL Database 서버 리소스 한도를 간략하게 설명합니다. 또한 이러한 리소스 한도에 도달하거나 초과할 때 발생하는 사항에 대한 정보도 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 04/18/2019
ms.openlocfilehash: b358e69df1df579e91a9098c120c7e6b4e3f2ead
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687491"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Azure SQL Database 서버의 SQL Database 리소스 한도

이 문서에서는 단일 데이터베이스 및 탄력적 풀을 관리하는 SQL Database 서버의 SQL Database 리소스 한도를 간략하게 설명합니다. 또한 이러한 리소스 한도에 도달하거나 초과할 때 발생하는 사항에 대한 정보도 제공합니다.

> [!NOTE]
> 관리되는 인스턴스 한도는 [관리되는 인스턴스의 SQL Database 리소스 한도](sql-database-managed-instance-resource-limits.md)를 참조하세요.

## <a name="maximum-resource-limits"></a>최대 리소스 한도

| 리소스 | 제한 |
| :--- | :--- |
| 서버당 데이터베이스 | 5,000 |
| 모든 지역에서 구독당 서버의 기본 수 | 20 |
| 모든 지역에서 구독당 서버의 최대 수 | 200 |  
| 서버당 DTU/eDTU 할당량 | 54,000 |  
| 서버/인스턴스당 vCore 할당량 | 540 |
| 서버당 최대 풀 | DTU 또는 vCore의 수로 제한됩니다. 예를 들어 각 풀에 DTU가 1000개인 경우 서버는 54개 풀을 지원할 수 있습니다.|
|||

> [!NOTE]
> 기본 양보다 더 많은 DTU/eDTU 할당량, vCore 할당량 또는 더 많은 서버를 얻으려면 문제 유형 "할당량"을 사용하여 구독을 위한 Azure Portal에서 새 지원 요청을 제출할 수 있습니다. 서버당 DTU/eDTU 할당량 및 데이터베이스 제한은 서버당 탄력적 풀의 수를 제한합니다.
> [!IMPORTANT]
> 데이터베이스 수가 SQL Database 서버당 한도에 근접하면 다음이 발생할 수 있습니다.
> - 마스터 데이터베이스에 대해 쿼리를 실행할 때 대기 시간이 증가합니다.  여기에는 sys.resource_stats와 같은 리소스 사용률 통계 보기가 포함됩니다.
> - 관리 작업을 수행하고 서버의 데이터베이스 열거와 관련된 포털 뷰 포인트를 렌더링할 때 대기 시간이 증가합니다.

### <a name="storage-size"></a>스토리지 크기
- 단일 데이터베이스의 경우 rources에 대 한 [DTU 기반 리소스](sql-database-dtu-resource-limits-single-databases.md) 제한 또는 [vcore 기반 리소스 제한](sql-database-vcore-resource-limits-single-databases.md) 을 참조 하세요.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>데이터베이스 리소스 한계에 도달하면 어떻게 되나요?

### <a name="compute-dtus-and-edtus--vcores"></a>Compute(DTU 및 eDTU / vCore)

데이터베이스 계산 사용률 (Dtu 및 Edtu 또는 vCores로 측정)이 높으면 쿼리 대기 시간이 늘어나고 시간이 초과 될 수도 있습니다. 이러한 조건에서 쿼리는 서비스에 의해 큐에 대기 될 수 있으며 리소스가 사용 가능 해질 때 실행 되는 리소스를 제공 합니다.
높은 컴퓨팅 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 컴퓨팅 크기를 늘려 데이터베이스에 더 많은 컴퓨팅 리소스를 제공합니다. [단일 데이터베이스 리소스 확장](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하세요.
- 쿼리를 최적화하여 각 쿼리당 리소스 사용률을 줄입니다. 자세한 내용은 [쿼리 튜닝/힌트](sql-database-performance-guidance.md#query-tuning-and-hinting)를 참조하세요.

### <a name="storage"></a>Storage

사용된 데이터베이스 공간이 최대 크기 제한에 도달하면 데이터 크기 증가를 가져오는 데이터베이스 삽입 및 업데이트가 실패하고 클라이언트에 [오류 메시지](sql-database-develop-error-messages.md)가 표시됩니다. 데이터베이스 선택 및 삭제는 계속 성공적으로 수행됩니다.

높은 공간 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 최대 크기를 늘리거나 더 많은 스토리지를 추가합니다. [단일 데이터베이스 리소스 확장](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하세요.
- 데이터베이스가 탄력적 풀에 속하는 경우 데이터베이스를 풀 외부로 이동하여 스토리지 공간이 다른 데이터베이스와 공유되지 않도록 할 수 있습니다.
- 데이터베이스를 축소하여 사용하지 않는 공간을 회수합니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

### <a name="sessions-and-workers-requests"></a>세션 및 작업자(요청)

세션 및 작업자의 최대 수는 서비스 계층 및 컴퓨팅 크기(DTU 및 eDTU)에 따라 결정됩니다. 세션 또는 작업자 제한에 도달하면 새 요청이 거부되고 클라이언트에 오류 메시지가 표시됩니다. 사용할 수 있는 연결의 수는 애플리케이션에서 쉽게 제어할 수 있지만, 동시 작업자 수는 예측하고 제어하기가 어렵습니다. 좀 더 오래 실행되는 쿼리로 인해 데이터베이스 리소스 제한에 도달하고 작업자가 대기하게 되는 최대 부하 기간이 특히 그렇습니다.

높은 세션 또는 작업자 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 서비스 계층 또는 컴퓨팅 크기를 늘립니다. [단일 데이터베이스 리소스 확장](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하세요.
- 컴퓨팅 리소스에 대한 경합 때문에 작업자 사용률이 증가할 경우 쿼리를 최적화하여 각 쿼리의 리소스 사용률을 줄입니다. 자세한 내용은 [쿼리 튜닝/힌트](sql-database-performance-guidance.md#query-tuning-and-hinting)를 참조하세요.

## <a name="transaction-log-rate-governance"></a>트랜잭션 로그 요금 거 버 넌 스 
트랜잭션 로그 속도 거 버 넌 스는 대량 삽입, SELECT INTO 및 인덱스 빌드와 같은 워크 로드에 대 한 높은 수집 속도를 제한 하는 데 사용 되는 Azure SQL Database의 프로세스입니다. 이러한 한도는 1 초 수준에서 로그 레코드 생성 속도까지 추적 되 고 적용 되며, 데이터 파일에 대해 실행할 수 있는 IOs 수에 관계 없이 처리량이 제한 됩니다.  현재 트랜잭션 로그 생성 속도는 하드웨어에 종속 된 지점까지 선형적으로 확장 되며 vCore 구매 모델을 사용 하 여 최대 로그 속도는 96 m b/초까지 허용 됩니다. 

> [!NOTE]
> 실제 실제 Io 트랜잭션 로그 파일은 제어 되거나 제한 되지 않습니다. 

로그 속도는 다양 한 시나리오에서 달성 하 고 유지할 수 있도록 설정 되며, 전체 시스템은 사용자 부하에 대 한 영향이 최소화 된 기능을 유지할 수 있습니다. 로그 전송률 관리는 트랜잭션 로그 백업이 게시 된 복구 가능성 Sla 내에 유지 되도록 합니다.  이 거 버 넌 스는 또한 보조 복제본의 과도 한 백로그를 방지 합니다.

로그 레코드가 생성 되 면 각 작업이 평가 되 고, 최대 원하는 로그 전송률 (초당 MB/s)을 유지 하기 위해 지연 되어야 하는지 여부가 평가 됩니다. 로그 레코드가 저장소에 플러시될 때 지연 시간이 추가 되지 않고 로그 속도가 자동으로 생성 되는 동안 로그 속도가 적용 됩니다.

런타임에 적용 되는 실제 로그 생성 요금은 피드백 메커니즘의 영향을 받을 수 있으므로 시스템이 안정화 될 수 있도록 허용 가능한 로그 비율을 일시적으로 줄일 수 있습니다. 로그 파일 공간 관리를 사용 하 여 로그 공간 부족 상태를 방지 하 고 가용성 그룹 복제 메커니즘을 통해 전체 시스템 제한을 일시적으로 낮출 수 있습니다. 

로그 전송률 관리자 트래픽 셰이핑은 다음 대기 유형 ( [sys. dm _db_stststst_ststst_ststst_ststnstn_stn_st](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)

| 대기 유형 | 참고 사항 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 데이터베이스 제한 |
| POOL_LOG_RATE_GOVERNOR | 풀 제한 |
| INSTANCE_LOG_RATE_GOVERNOR | 인스턴스 수준 제한 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 피드백 컨트롤, 가용성 그룹 프리미엄/중요 비즈니스용의 물리적 복제를 유지 하지 않음 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 로그 공간 부족 상태를 방지 하기 위해 사용자 의견 제어, 속도 제한 |
|||

원하는 확장성을 hampering 로그 전송률 제한이 발생할 경우 다음 옵션을 고려 하십시오.
- 최대 96 m b/초 로그 속도를 얻기 위해 더 큰 계층으로 확장 합니다. 
- 데이터를 로드 하는 경우, 즉 ETL 프로세스에서 데이터를 준비 하는 경우 tempdb에 로드할 수 있습니다 (최소 기록 됨). 
- 분석 시나리오의 경우 클러스터형 columnstore 대상 테이블로 로드 합니다. 이렇게 하면 압축으로 인해 필요한 로그 전송률이 줄어듭니다. 이 기법은 CPU 사용률을 높이고 클러스터형 columnstore 인덱스를 활용 하는 데이터 집합에만 적용 됩니다. 

## <a name="next-steps"></a>다음 단계

- 일반 Azure 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.
- DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)를 참조하세요.
- tempdb 크기 한도에 대한 자세한 내용은 [Azure SQL Database의 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)를 참조하세요.
