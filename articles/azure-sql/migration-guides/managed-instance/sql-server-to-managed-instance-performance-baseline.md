---
title: SQL Managed Instance에 SQL Server-성능 분석
description: SQL Server 데이터베이스를 Azure SQL Managed Instance로 마이그레이션할 때 성능 기준선을 만들고 비교 하는 방법에 대해 알아봅니다.
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: df53f4912108962e9d50400c4c2516aefaa50976
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496722"
---
# <a name="migration-performance-sql-server-to-sql-managed-instance-performance-analysis"></a>마이그레이션 성능: SQL Server SQL Managed Instance 성능 분석
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

SQL Server에서 실행 되는 원래 워크 로드와 SQL Managed Instance 작업의 성능을 비교 하는 성능 기준선을 만듭니다. 

## <a name="create-a-baseline"></a>기준선 만들기

이상적으로는 마이그레이션 후 성능이 비슷하거나 더 우수 하므로 원본에서 기준 성능 값을 측정 하 고 기록 하 고 대상 환경과 비교 하는 것이 중요 합니다. 성능 기준은 원본에서 평균 작업을 정의 하는 매개 변수 집합입니다. 

에 중요 한 쿼리 집합을 선택 하 고 비즈니스 작업을 파악 합니다. 평균/최대 CPU 사용량, 평균/최대 디스크 IO 대기 시간, 처리량, IOPS, 평균/최대 페이지 수명 예상 및 tempdb의 평균 최대 크기와 같이 이러한 쿼리에 대 한 최소/평균/최대 기간 및 CPU 사용량과 원본 서버의 성능 메트릭을 측정 하 고 문서화 합니다. 

다음 리소스는 성능 기준선을 정의 하는 데 도움이 될 수 있습니다. 

   - [CPU 사용량 모니터링 ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [메모리 사용 모니터링](/sql/relational-databases/performance-monitor/monitor-memory-usage)   및는 버퍼 풀, 계획 캐시, 열 저장소 풀, [메모리 내 OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)등의 여러 구성 요소에서 사용 하는 메모리의 양을 결정 합니다. 또한 페이지 수명 예상 메모리 성능 카운터의 평균 및 피크 값을 찾아야 합니다. 
   -  [Sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)   뷰 또는 [성능 카운터](/sql/relational-databases/performance-monitor/monitor-disk-usage)를 사용 하 여 원본 SQL Server 인스턴스에서 디스크 IO 사용량을 모니터링 합니다. 
   - 동적 관리 뷰 (또는 SQL Server 2016 이상에서 마이그레이션하는 경우 쿼리 저장소)를 검사 하 여 작업 및 쿼리 성능을 모니터링 합니다. 워크 로드에서 가장 중요 한 쿼리의 평균 기간 및 CPU 사용량을 확인 합니다. 

원본 SQL Server의 성능 문제는 마이그레이션하기 전에 해결 해야 합니다. 알려진 문제를 새 시스템으로 마이그레이션하면 예기치 않은 결과가 발생 하 고 성능 비교가 무효화 될 수 있습니다. 


## <a name="compare-performance"></a>성능 비교 

기준을 정의한 후에는 대상 SQL Managed Instance에서 유사한 워크 로드 성능을 비교 합니다. 정확성을 위해 SQL Managed Instance 환경을 가능한 한 SQL Server 환경과 비교할 수 있어야 합니다. 

일치 하는 성능을 정확 하 게 유지 하는 SQL Managed Instance 인프라 차이가 있습니다. 일부 쿼리는 예상 보다 빠르게 실행 될 수 있지만 다른 쿼리는 속도가 느릴 수 있습니다. 이 비교의 목표는 관리 되는 인스턴스의 워크 로드 성능이 SQL Server (평균)의 성능과 일치 하는지 확인 하 고, 원래 성능과 일치 하지 않는 성능으로 중요 한 쿼리를 식별 하는 것입니다. 

성능 비교 시 다음과 같은 결과가 발생할 수 있습니다. 

- 관리 되는 인스턴스의 워크 로드 성능이 원본 SQL Server의 워크 로드 성능 보다 잘 맞춰져 있습니다. 이 경우 마이그레이션이 성공적으로 완료 되었음을 확인 했습니다. 

- 작업에 포함 된 대부분의 성능 매개 변수 및 쿼리가 예상 대로 수행 되며,이로 인해 성능이 저하 됩니다. 이 경우 차이점 및 중요도를 확인 합니다. 성능이 저하 된 몇 가지 중요 한 쿼리가 있는 경우 기본 SQL 계획이 변경 되었는지 또는 쿼리가 리소스 제한에 도달 하는지 여부를 조사 합니다. 이는 직접 또는 계획 지침을 사용 하 여 중요 한 쿼리에 일부 힌트 (예: 호환성 수준 변경, 레거시 카디널리티 평가기)를 적용 하 여 완화할 수 있습니다. 통계와 인덱스가 모두 최신이 고 두 환경에서 동일한 지 확인 합니다. 

- 대부분의 쿼리는 원본 SQL Server 인스턴스와 비교 하 여 관리 되는 인스턴스에서 속도가 느립니다. 이 경우 IO, 메모리 또는 인스턴스 로그 전송률 제한과 같은 [일부 리소스 제한에 도달](../../managed-instance/resource-limits.md#service-tier-characteristics) 하는 것과 같은 차이의 근본 원인을 파악 하려고 합니다. 리소스 제한으로 인해 차이가 발생 하지 않는 경우 데이터베이스의 호환성 수준을 변경 하거나 레거시 카디널리티 예측과 같은 데이터베이스 설정을 변경 하 고 테스트를 다시 실행 하십시오. 관리 되는 인스턴스 또는 쿼리 저장소 보기에서 제공 하는 권장 사항을 검토 하 여 재발 된 성능이 포함 된 쿼리를 확인 합니다. 

SQL Managed Instance에는 기본적으로 사용 되는 자동 계획 수정 기능이 기본적으로 제공 됩니다. 이 기능을 사용 하면 이전에 잘 작동 했던 쿼리가 나중에 저하 되지 않습니다. 이 기능을 사용 하지 않는 경우 SQL Managed Instance에서 성능 기준선을 학습할 수 있도록 이전 설정으로 작업을 실행 합니다. 그런 다음 기능을 사용 하도록 설정 하 고 새 설정을 사용 하 여 작업을 다시 실행 합니다. 

테스트 매개 변수를 변경 하거나 더 높은 서비스 계층으로 업그레이드 하 여 요구에 맞는 워크 로드 성능에 대 한 최적의 구성에 연결 합니다. 

## <a name="monitor-performance"></a>성능 모니터링 

SQL Managed Instance는 모니터링 및 문제 해결을 위한 고급 도구를 제공 하며, 이러한 도구를 사용 하 여 인스턴스의 성능을 모니터링 해야 합니다. 모니터링할 주요 메트릭 중 일부는 다음과 같습니다. 

- 프로 비전 한 vCores 수가 워크 로드와 적절 한지 확인 하기 위해 인스턴스의 CPU 사용량입니다. 
- [추가 메모리가](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444)필요한 지 여부를 확인 하기 위해 관리 되는 인스턴스의 페이지 수명 예상입니다.
-  특히 일반적인 용도 계층에서 저장소 IO 문제를 식별 하는 INSTANCE_LOG_GOVERNOR 또는 PAGEIOLATCH와 같은 통계 이며, 더 나은 IO 성능을 얻기 위해 파일을 미리 할당 해야 할 수도 있습니다. 


## <a name="considerations"></a>고려 사항  

성능을 비교할 때 다음 사항을 고려 하십시오. 

- 원본과 대상 간의 설정이 일치 합니다. 두 환경 간에 다양 한 인스턴스, 데이터베이스 및 tempdb 설정이 동일한 지 확인 합니다. 구성, 호환성 수준, 암호화 설정, 추적 플래그 등의 차이점은 모두 성능을 저하 시킬 수 있습니다. 

- 저장소는 [모범 사례](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)에 따라 구성 됩니다. 예를 들어 일반적인 용도로 파일 크기를 미리 할당 하 여 성능을 향상 시켜야 할 수 있습니다. 

- 관리 되는 인스턴스와 SQL Server 간의 성능 차이를 일으킬 수 있는 [주요 환경 차이점이](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) 있습니다. 성능 문제에 기여할 수 있는 사용자 환경과 관련 된 위험을 식별 합니다. 

- 쿼리 저장소 및 자동 튜닝은 워크 로드 성능을 측정 하 고 잠재적인 성능 문제를 자동으로 완화 하는 데 도움이 되는 SQL Managed Instance에서 사용 하도록 설정 해야 합니다. 



## <a name="next-steps"></a>다음 단계

새 Azure SQL Managed Instance 환경을 최적화 하는 방법에 대 한 자세한 내용은 다음 리소스를 참조 하세요. 

- [Azure SQL Managed Instance의 워크 로드 성능이 SQL Server와 다른 이유를 확인 하는 방법](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [SQL Managed Instance와 SQL Server 간 성능 차이의 주요 원인](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [저장소 성능 모범 사례 및 Azure SQL Managed Instance에 대 한 고려 사항 (범용)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Azure SQL Managed Instance에 대 한 실시간 성능 모니터링 (보관 됨, 의도 한 대상)](https://docs.microsoft.com/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)
