---
title: 'SQL Server에서 Azure SQL Managed Instance: 성능 기준'
description: SQL Server 데이터베이스를 Azure SQL Managed Instance로 마이그레이션할 때 성능 기준선을 만들고 비교하는 방법에 대해 알아봅니다.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: ee51e3ee0fd9f0084e8dddf03a9e181b38bc2fb4
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110689400"
---
# <a name="migration-performance-sql-server-to--azure-sql-managed-instance-performance-baseline"></a>마이그레이션 성능: SQL Server에서 Azure SQL Managed Instance 성능 기준
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

SQL Managed Instance의 워크로드 성능을 SQL Server에서 실행 중인 원래 워크로드와 비교해야 하는 경우 비교에 사용할 성능 기준선을 만들어야 합니다. 

## <a name="create-a-baseline"></a>기준선 만들기

이상적으로는 마이그레이션 후 성능이 비슷하거나 더 향상되므로, 원본에서 기준 성능 값을 측정하여 기록하고 대상 환경과 비교하는 것이 중요합니다. 성능 기준은 원본에서 평균 작업을 정의하는 매개 변수 집합입니다. 

중요한 쿼리 집합을 선택하고 비즈니스 워크로드의 Representative를 파악합니다. 평균/최대 CPU 사용량, 평균/최대 디스크 IO 지연 시간, 처리량, IOPS, 평균/최대 페이지 수명, tempdb의 평균 최대 크기 등 소스 서버의 성능 메트릭을 측정하고 문서화합니다. 

다음 리소스는 성능 기준선을 정의하는 데 도움이 될 수 있습니다. 

   - [CPU 사용량 모니터링](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [메모리 사용량 ](/sql/relational-databases/performance-monitor/monitor-memory-usage) 을 모니터링하고 버퍼 풀, 계획 캐시, 열 저장소 풀,  [In-Memory OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage) 등 여러 구성 요소에 사용되는 메모리 양을 확인합니다. 또한 페이지 수명 예상 메모리 성능 카운터의 평균 및 최댓값을 찾아야 합니다. 
   -  [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 뷰 또는   [성능 카운터](/sql/relational-databases/performance-monitor/monitor-disk-usage) 를 사용하여 원본 SQL Server 인스턴스에서 디스크 IO 사용량을 모니터링합니다. 
   - (SQL Server 2016 이상 버전에서 마이그레이션하는 경우 쿼리 데이터 저장소 또는) 동적 관리 뷰를 검사하여 워크로드 및 쿼리 성능 또는 SQL Server 인스턴스를 모니터링합니다. 워크로드에서 가장 중요한 쿼리의 평균 기간 및 CPU 사용량을 확인합니다. 

원본 SQL Server의 성능 문제는 마이그레이션 전에 해결해야 합니다. 알려진 문제를 새 시스템으로 마이그레이션하면 예기치 않은 결과가 발생하고 성능 비교가 무효화될 수 있습니다. 


## <a name="compare-performance"></a>성능 비교 

기준을 정의한 후에는 대상 SQL Managed Instance에서 유사한 워크로드 성능을 비교합니다. 정확성을 위해 SQL Managed Instance 환경을 가능한 한 많이 SQL Server 환경과 비교할 수 있어야 합니다. 

일치하는 성능을 정확히 찾기 힘들게 하는 SQL Managed Instance 인프라 차이가 있습니다. 일부 쿼리는 예상보다 빠르게 실행될 수 있지만, 다른 쿼리는 속도가 느릴 수 있습니다. 관리되는 인스턴스의 워크로드 성능이 SQL Server의 성능(평균적인)과 일치하는지 확인하고, 성능이 원래 성능과 일치하지 않는 중요한 쿼리를 식별하는 것이 이 비교의 목표입니다. 

성능 비교 시 다음과 같은 결과가 발생할 수 있습니다. 

- 관리형 인스턴스의 워크로드 성능이 SQL Server의 워크로드 성능과 일치하거나 낫습니다. 이 경우 마이그레이션이 성공적으로 완료되었음을 확인했습니다. 

- 작업에 포함된 대부분의 성능 매개 변수 및 쿼리가 예상대로 수행되지만 일부 예외는 성능 저하를 초래합니다. 이 경우 차이점과 그 중요성을 식별해야 합니다. 성능이 저하된 중요한 쿼리가 있는 경우 기본 SQL 플랜이 변경되었는지 혹은 쿼리가 리소스 한도에 도달하는지 조사해야 합니다. 이는 직접 또는 계획 지침을 사용하여 중요한 쿼리에 일부 힌트(예: 호환성 수준 변경, 레거시 카디널리티 추정)를 적용하여 완화할 수 있습니다. 통계와 인덱스가 모두 최신이고 두 환경에서 동등한지 확인합니다. 

- 대부분의 쿼리는 원본 SQL Server 인스턴스와 비교하여 관리형 인스턴스에서 속도가 느려집니다. 이 경우 IO, 메모리, 또는 인스턴스 로그 전송률 제한과 같은 [일부 리소스 제한에 도달](../../managed-instance/resource-limits.md#service-tier-characteristics)하는 것과 같은 차이의 근본 원인을 파악하려고 합니다. 차이의 원인이 되는 리소스 제한이 없는 경우 데이터베이스의 호환성 수준을 변경하거나 레거시 카디널리티 추정과 같은 데이터베이스 설정을 변경하고 테스트를 다시 실행하십시오. 관리형 인스턴스 또는 쿼리 저장소 보기에서 제공하는 권장 사항을 검토하여 성능이 퇴화된 쿼리를 확인합니다. 

SQL Managed Instance에는 기본적으로 사용하도록 설정된 자동 계획 수정 기능이 내장되어 있습니다. 이 기능을 사용하면 이전에 제대로 작동한 쿼리의 성능이 나중에 저하되지 않습니다. 이 기능을 사용하지 않는 경우 SQL Managed Instance에서 성능 기준선을 학습할 수 있도록 이전 설정으로 워크로드를 실행합니다. 그런 다음 기능을 사용하도록 설정하고 새 설정을 사용하여 작업을 다시 실행합니다. 

테스트 매개 변수를 변경하거나 더 높은 서비스 계층으로 업그레이드하여 요구에 맞는 워크로드 성능에 대한 최적의 구성에 도달합니다. 

## <a name="monitor-performance"></a>성능 모니터링 

SQL Managed Instance는 모니터링 및 문제 해결을 위한 많은 고급 도구를 제공하고, 해당 도구를 사용하여 인스턴스의 성능을 모니터링해야 합니다. 모니터링할 주요 메트릭 중 일부는 다음과 같습니다. 

- 프로비저닝한 vCore 수가 워크로드에 적합한지 여부를 확인하는 인스턴스의 CPU 사용량입니다. 
- 페이지-[추가 메모리](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444) 필요 여부를 판별하는 관리형 인스턴스의 예상 수명입니다.
-  스토리지 IO 문제를 식별하는 Instance_LOG_GUNDER 또는 PAGIOLatch과 같은 통계, 특히 범용 목적 계층에서 더 나은 IO 성능을 얻으려면 파일을 미리 할당해야 할 수 있습니다. 


## <a name="considerations"></a>고려 사항  

성능을 최적화할 때 다음을 고려합니다. 

- 원본과 대상 간에 설정이 일치합니다. 두 환경 간에 다양한 인스턴스, 데이터베이스 및 tempdb 설정이 동일한지 확인합니다. 구성, 호환성 수준, 암호화 설정, 추적 플래그 등의 차이점은 모두 성능을 저하시킬 수 있습니다. 

- 스토리지는 [모범 사례](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)에 따라 구성됩니다. 예를 들어, 일반적인 용도로 파일 크기를 사전 할당하여 성능을 향상시켜야 할 수 있습니다. 

- 관리되는 인스턴스와 SQL Server 간의 성능 차이를 일으킬 수 있는 [주요 환경 차이가](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) 있습니다. 성능 문제에 기여할 수 있는 사용자 환경과 관련된 위험을 식별합니다. 

- 쿼리 저장소 및 자동 튜닝은 워크로드 성능을 측정하고 잠재적인 성능 문제를 자동으로 완화하는 데 도움이 되는 SQL Managed Instance에서 사용되도록 설정해야 합니다. 



## <a name="next-steps"></a>다음 단계

새로운 Azure SQL Managed Instance 환경 최적화에 대한 자세한 내용은 다음 리소스를 참조하세요. 

- [Azure SQL Managed Instance의 워크로드 성능이 SQL Server와 다른 이유를 식별하는 방법](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [SQL Managed Instance와 SQL Server 간의 성능 차이를 일으키는 주요 원인](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [스토리지 성능 모범 사례 및 Azure SQL Managed Instance에 대한 고려 사항 (범용)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Azure SQL Managed Instance에 대한 실시간 성능 모니터링 (아카이브된 대상입니까, 혹은 의도된 대상입니까?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)