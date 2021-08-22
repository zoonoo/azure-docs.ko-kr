---
title: SQL Server를 Azure SQL Managed Instance로 마이그레이션
description: SQL Server에서 Azure SQL Managed Instance로 데이터베이스를 마이그레이션하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: cawrites
ms.date: 06/23/2021
ms.openlocfilehash: 4bf393ea83c75f4d337c031bd6b7930e600e555e
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114676961"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>SQL Server 인스턴스를 Azure SQL Managed Instance로 마이그레이션
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 SQL Server 2005 이상 버전 인스턴스를 [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md)로 마이그레이션하는 방법에 관해 설명합니다. 단일 데이터베이스 또는 탄력적 풀로 마이그레이션하는 방법에 관한 자세한 내용은 [마이그레이션 개요: SQL Server에서 SQL Database로](../migration-guides/database/sql-server-to-sql-database-overview.md)를 참조하세요. 다른 플랫폼에서 마이그레이션하는 방법에 관한 마이그레이션 정보와 도구 및 옵션에 관한 참고 자료는 [Azure SQL로 마이그레이션](../migration-guides/index.yml)을 참조하세요.

> [!NOTE]
> Azure SQL Managed Instance를 빠르게 시작하고 사용해 보려면 이 페이지 대신 [빠른 시작 가이드](quickstart-content-reference-guide.md)로 이동하는 것이 좋습니다.

높은 수준에서 데이터베이스 마이그레이션 프로세스는 다음과 같습니다.

![마이그레이션 프로세스](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [SQL Managed Instance 호환성을 평가](#assess-sql-managed-instance-compatibility)합니다. 여기서, 마이그레이션을 방해할 수 있는 차단 문제가 없는지 확인해야 합니다.
  
  이 단계에는 원본 SQL Server 인스턴스의 리소스 사용량을 판별하기 위한 [성능 기준](#create-a-performance-baseline) 만들기도 포함됩니다. 이 단계는 적절한 크기의 관리형 인스턴스를 배포하고 마이그레이션 후 성능이 영향을 받지 않는지 확인하려는 경우 필요합니다.
- [앱 연결 옵션을 선택](connect-application-instance.md)합니다.
- [최적으로 크기가 조정된 관리형 인스턴스에 배포](#deploy-to-an-optimally-sized-managed-instance)합니다. 여기서, 관리형 인스턴스의 기술적 특성(vCore 수, 메모리 크기)과 성능 계층(중요 비즈니스용, 범용)을 선택합니다.
- [마이그레이션 방법을 선택하고 마이그레이션](#select-a-migration-method-and-migrate)합니다. 여기서, 오프라인 마이그레이션(원시 백업/복원, 데이터베이스 가져오기/내보내기) 또는 온라인 마이그레이션(Azure Data Migration Service, 트랜잭션 복제)을 사용하여 데이터베이스를 마이그레이션합니다.
- [애플리케이션을 모니터링](#monitor-applications)하여 성능이 예상과 같은지 확인합니다.

> [!NOTE]
> 개별 데이터베이스를 단일 데이터베이스 또는 탄력적 풀로 마이그레이션하려면 [Azure SQL Database로 SQL Server 데이터베이스 마이그레이션](../database/migrate-to-database-from-sql-server.md)을 참조하세요.

## <a name="assess-sql-managed-instance-compatibility"></a>SQL Managed Instance 호환성 평가

먼저 SQL Managed Instance가 애플리케이션의 데이터베이스 요구 사항과 호환되는지 확인합니다. SQL Managed Instance는 SQL Server를 사용하는 대부분의 기존 애플리케이션에서 리프트 앤 시프트 방식으로 쉽게 마이그레이션할 수 있도록 설계되었습니다. 그러나 경우에 따라 아직 지원되지 않는 기능이 필요할 수 있으며 해결 방법을 구현하는 데 드는 비용이 너무 큽니다.

[Data Migration Assistant](/sql/dma/dma-overview)를 사용하여 Azure SQL Database에서 데이터베이스 기능에 영향을 주는 잠재적인 호환성 문제를 탐지합니다. 보고된 차단 문제가 있는 경우 [Azure VM의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)와 같은 대체 옵션을 고려해야 할 수도 있습니다. 다음은 몇 가지 예입니다.

- 운영 체제 또는 파일 시스템에 직접 액세스해야 하는 경우(예: SQL Server가 있는 같은 가상 머신에 타사 또는 사용자 지정 에이전트를 설치하는 경우).
- FileStream/FileTable, PolyBase 및 인스턴스 간 트랜잭션과 같이 아직 지원되지 않는 기능에 대한 엄격한 종속성이 있는 경우.
- 특정 버전의 SQL Server(예: 2012)를 반드시 유지해야 하는 경우.
- 컴퓨팅 요구 사항이 관리형 인스턴스에서 제공하는 것보다 훨씬 낮고(예: 하나의 vCore) 데이터베이스 통합이 허용되지 않는 옵션인 경우.

식별된 모든 마이그레이션 차단기를 해결하고 SQL Managed Instance로 마이그레이션을 계속하는 경우 일부 변경 사항이 워크로드의 성능에 영향을 미칠 수 있습니다.

- 단순/대량 로그된 모델을 주기적으로 사용했거나 주문형 백업을 중지한 경우 필수 전체 복구 모델과 정기적인 자동 백업 일정이 워크로드 또는 유지 관리/ETL 작업의 성능에 영향을 미칠 수 있습니다.
- 다른 서버 또는 데이터베이스 수준 구성(예: 추적 플래그 또는 호환성 수준).
- TDE(투명한 데이터베이스 암호화) 또는 자동 장애 조치(failover) 그룹과 같이 사용 중인 새로운 기능은 CPU 및 IO 사용에 영향을 줄 수 있습니다.

SQL Managed Instance는 중요한 시나리오에서도 99.99%의 가용성을 보장하므로, 해당 기능으로 인한 오버헤드를 사용하지 않게 설정할 수 없습니다. 자세한 내용은 [SQL Server 및 Azure SQL Managed Instance에서 다른 성능을 초래할 수 있는 근본 원인](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)을 참조하세요.

#### <a name="in-memory-oltp-memory-optimized-tables"></a>메모리 내 OLTP(메모리 최적화 테이블)

SQL Server는 메모리 최적화 테이블, 메모리 최적화 테이블 형식 및 기본적으로 컴파일된 SQL 모듈을 사용하여 처리량이 많고 대기 시간이 짧은 트랜잭션 처리 요구 사항이 있는 워크로드를 실행할 수 있는 메모리 내 OLTP 기능을 제공합니다. 

> [!IMPORTANT]
> 메모리 내 OLTP는 Azure SQL Managed Instance의 중요 비즈니스용 계층에서만 지원되며 범용 계층에서는 지원되지 않습니다.

온-프레미스 SQL Server에 메모리 최적화 테이블 또는 메모리 최적화 테이블 형식이 있고 Azure SQL Managed Instance로 마이그레이션하려는 경우 다음 중 하나를 수행해야 합니다.

- 메모리 내 OLTP를 지원하는 대상 Azure SQL Managed Instance의 중요 비즈니스용 계층을 선택합니다.
- Azure SQL Managed Instance의 범용 계층으로 마이그레이션하려는 경우 데이터베이스를 마이그레이션하기 전에 메모리 최적화 테이블, 메모리 최적화 테이블 형식 및 메모리 최적화 개체와 상호 작용하는 고유하게 컴파일된 SQL 모듈을 제거합니다. 다음 T-SQL 쿼리를 사용하여 범용 계층으로 마이그레이션하기 전에 제거해야 하는 모든 개체를 식별할 수 있습니다.

```tsql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

메모리 내 기술에 관한 자세한 내용은 [Azure SQL Database 및 Azure SQL Managed Instance에서 메모리 내 기술을 사용하여 성능 최적화](../in-memory-oltp-overview.md)를 참조하세요.

### <a name="create-a-performance-baseline"></a>성능 기준 만들기

관리형 인스턴스의 워크로드 성능을 SQL Server에서 실행 중인 원래 워크로드와 비교해야 하는 경우 비교에 사용할 성능 기준을 만들어야 합니다.

성능 기준은 평균/최대 CPU 사용량, 평균/최대 디스크 IO 대기 시간, 처리량, IOPS, 평균/최대 페이지 예상 수명 및 tempdb의 평균 최대 크기와 같은 매개 변수 세트입니다. 마이그레이션 후 비슷하거나 더 나은 매개 변수를 원하므로, 매개 변수에 관한 기준값을 측정하고 기록하는 것이 중요합니다. 시스템 매개 변수 외에도 워크로드에서 대표적인 쿼리 세트 또는 가장 중요한 쿼리를 선택하고 선택한 쿼리에 대한 최소/평균/최대 기간 및 CPU 사용량을 측정해야 합니다. 이 값을 사용하면 관리형 인스턴스에서 실행되는 워크로드의 성능을 원본 SQL Server 인스턴스의 원래 값과 비교할 수 있습니다.

SQL Server 인스턴스에서 측정해야 하는 매개 변수는 다음과 같습니다.

- [SQL Server 인스턴스의 CPU 사용량을 모니터링](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131)하고 평균 및 최대 CPU 사용량을 기록합니다.
- [SQL Server 인스턴스의 메모리 사용량을 모니터링](/sql/relational-databases/performance-monitor/monitor-memory-usage)하고 버퍼 풀, 계획 캐시, 열 저장소 풀, [메모리 내 OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage) 등과 같은 다양한 구성 요소에서 사용하는 메모리 크기를 확인합니다. 또한 페이지 예상 수명 메모리 성능 카운터의 평균값과 최댓값을 찾아야 합니다.
- [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 보기 또는 [성능 카운터](/sql/relational-databases/performance-monitor/monitor-disk-usage)를 사용하여 원본 SQL Server 인스턴스에서 디스크 IO 사용량을 모니터링합니다.
- SQL Server 2016+ 버전에서 마이그레이션하는 경우 동적 관리 뷰 또는 쿼리 저장소를 검사하여 워크로드 및 쿼리 성능 또는 SQL Server 인스턴스를 모니터링합니다. 워크로드에서 가장 중요한 쿼리의 평균 기간과 CPU 사용량을 식별하여 관리형 인스턴스에서 실행 중인 쿼리와 비교합니다.

> [!Note]
> 높은 CPU 사용량, 지속적인 메모리 압력, tempdb 또는 매개 변수화 문제와 같은 SQL Server의 워크로드에 문제가 있는 경우, 기준 및 마이그레이션을 수행하기 전에 원본 SQL Server 인스턴스에서 문제를 해결해야 합니다. 알려진 문제를 새 시스템으로 마이그레이션하면 예기치 않은 결과가 발생하고 성능 비교가 무효화될 수 있습니다.

이 작업의 ​​결과로 원본 시스템의 CPU, 메모리 및 IO 사용량에 대한 평균 및 최대값은 물론 워크로드에서 가장 지배적이고 중요한 쿼리의 평균 및 최대 기간과 CPU 사용량을 문서화해야 합니다. 나중에 이 값을 사용하여 관리형 인스턴스의 워크로드 성능을 원본 SQL Server 인스턴스의 워크로드 기준 성능과 비교해야 합니다.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>최적 크기의 관리되는 인스턴스에 배포

SQL Managed Instance는 클라우드로 이동할 온-프레미스 워크로드에 맞게 조정됩니다. 작업에 적합한 수준의 리소스를 선택할 때 유연성이 높은 [새 구매 모델](../database/service-tiers-vcore.md)이 도입되었습니다. 온-프레미스 환경에서는 실제 코어 및 IO 대역폭을 사용하여 이러한 작업의 크기를 조정하는 데 익숙할 것입니다. 관리형 인스턴스에 대한 구매 모델은 가상 코어 수 또는 “vCore 수”를 기반으로 하며, 추가 스토리지 및 IO를 별도로 사용할 수 있습니다. vCore 모델은 현재 온-프레미스에서 사용하는 제품과 비교하여 클라우드의 컴퓨팅 요구 사항을 더 쉽게 이해할 수 있는 방법입니다. 새로운 이 모델을 사용하면 클라우드에서 대상 환경의 크기를 올바르게 조정할 수 있습니다. 올바른 서비스 계층 및 특성을 선택하는 데 도움이 될 수 있는 몇 가지 일반적인 지침이 여기에 설명되어 있습니다.

- [관리형 인스턴스가 설치된 VM 특성](resource-limits.md#hardware-generation-characteristics)과 일치하도록 CPU 특성을 스케일링해야 할 수도 있다는 점에 유의하여 기준 CPU 사용량을 기반으로 SQL Server에서 사용 중인 코어 수와 일치하는 관리형 인스턴스를 프로비저닝합니다.
- 기준 메모리 사용량에 따라 [ 일치하는 메모리가 있는 서비스 계층](resource-limits.md#hardware-generation-characteristics)을 선택합니다. 메모리 크기는 직접 선택할 수 없으므로 메모리가 일치하는 vCore 크기를 사용하여 관리형 인스턴스를 선택해야 합니다(예: Gen5의 5.1GB/vCore).
- 파일 하위 시스템의 기준 IO 대기 시간을 기반으로 범용(대기 시간이 5ms 초과) 및 중요 비즈니스용(대기 시간이 3ms 미만) 서비스 계층 중에서 선택합니다.
- 예상 IO 성능을 얻도록 기준 처리량을 기반으로 데이터 또는 로그 파일의 크기를 미리 할당합니다.

배포 시 컴퓨팅 및 스토리지 리소스를 선택한 다음, 나중에 [Azure Portal](../database/scale-resources.md)을 사용하여 애플리케이션의 가동 중지 시간을 도입하지 않고 변경할 수 있습니다.

![관리형 인스턴스 크기 조정](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

VNet 인프라와 관리되는 인스턴스를 만드는 방법을 알아보려면 [관리되는 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.

> [!IMPORTANT]
> 대상 VNet 및 서브넷을 [관리형 인스턴스 VNet 요구 사항](connectivity-architecture-overview.md#network-requirements)에 따라 유지해야 합니다. 호환되지 않는 경우 새 인스턴스를 만들거나 이미 만든 인스턴스를 사용하지 못할 수 있습니다. [새 네트워크 만들기](virtual-network-subnet-create-arm-template.md) 및 [기존 네트워크 구성](vnet-existing-add-subnet.md)에 대해 자세히 알아보세요.

## <a name="select-a-migration-method-and-migrate"></a>마이그레이션 방법 선택 및 마이그레이션

SQL Managed Instance는 온-프레미스 또는 Azure VM 데이터베이스 구현에서 대량의 데이터베이스 마이그레이션이 필요한 사용자 시나리오를 대상으로 합니다. 인스턴스 수준 및/또는 데이터베이스 간 기능을 정기적으로 사용하는 애플리케이션의 백 엔드를 리프트 앤 시프트 방식으로 이동해야 하는 경우에 최적의 선택입니다. 이러한 시나리오의 경우 애플리케이션을 다시 구성하지 않고도 Azure에서 전체 인스턴스를 해당 환경으로 이동할 수 있습니다.

SQL 인스턴스를 이동하려면 다음을 신중하게 계획해야 합니다.

- 배치해야 하는 모든 데이터베이스(같은 인스턴스에서 실행되는 데이터베이스)의 마이그레이션.
- 로그인, 자격 증명, SQL 에이전트 작업 및 연산자, 서버 수준 트리거를 포함하여 애플리케이션이 종속된 인스턴스 수준 개체의 마이그레이션.

SQL Managed Instance는 정기적인 DBA 작업의 일부를 기본 제공되는 플랫폼에 위임할 수 있도록 하는 관리되는 서비스입니다. 따라서 [고가용성](../database/high-availability-sla.md)이 기본 제공되므로 정기 백업을 위한 유지 관리 작업 또는 Always On 구성과 같이 일부 인스턴스 수준 데이터는 마이그레이션할 필요가 없습니다.

SQL Managed Instance에서 지원하는 데이터베이스 마이그레이션 옵션은 다음과 같습니다(현재 지원되는 유일한 마이그레이션 방법임).

- Azure Database Migration Service - 거의 제로에 가까운 가동 중지 시간으로 마이그레이션.
- 네이티브 `RESTORE DATABASE FROM URL` - SQL Server의 네이티브 백업을 사용하며 약간의 가동 중지 시간 필요

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service](../../dms/dms-overview.md)는 가동 중지 시간을 최소화하면서 여러 데이터베이스 원본에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있도록 설계된 완전 관리형 서비스입니다. 이 서비스는 기존의 타사 데이터베이스 및 SQL Server 데이터베이스를 Azure로 이동하는 데 필요한 작업을 간소화합니다. 퍼블릭 미리 보기의 배포 옵션에는 Azure SQL Database의 데이터베이스 및 Azure Virtual Machine의 SQL Server 데이터베이스가 포함됩니다. Database Migration Service는 엔터프라이즈 작업에 권장되는 마이그레이션 방법입니다.

SQL Server 온-프레미스에서 SSIS(SQL Server Integration Services)를 사용하는 경우, Database Migration Service는 SSIS 패키지를 저장하는 SSIS 카탈로그(SSISDB)의 마이그레이션을 아직 지원하지 않지만, 관리형 인스턴스에 새 SSISDB를 만들 Azure-SSIS IR(Integration Runtime)을 Azure Data Factory에 프로비저닝하고 해당 패키지를 다시 배포할 수 있습니다. [Azure Data Factory에서 Azure-SSIS IR 만들기](../../data-factory/create-azure-ssis-integration-runtime.md)를 참조하세요.

이 시나리오 및 Database Migration Service의 구성 단계에 관한 자세한 내용은 [ Database Migration Service를 사용하여 온-프레미스 데이터베이스를 관리형 인스턴스로 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.  

### <a name="native-restore-from-url"></a>URL에서 네이티브 복원

[Azure Storage](https://azure.microsoft.com/services/storage/)에서 사용할 수 있는 SQL Server 인스턴스에서 가져온 원시 백업(.bak 파일)의 복원(RESTORE)은 빠르고 쉽게 오프라인 데이터베이스를 마이그레이션하는 데 사용할 수 있는 SQL Managed Instance의 주요 기능 중 하나입니다.

다음 다이어그램은 프로세스의 상위 수준 개요를 제공합니다.

![다이어그램은 Azure Storage로 이동하는 백업(BACKUP)/URL에 업로드라고 레이블이 지정된 화살표와 Azure Storage에서 SQL의 Managed Instance로 이동하는 URL에서 복원(RESTORE)이라는 두 번째 화살표가 있는 SQL Server를 보여 줍니다.](./media/migrate-to-instance-from-sql-server/migration-flow.png)

다음 표에서는 실행되는 원본 SQL Server 버전에 따라 사용할 수 있는 방법에 대한 자세한 정보를 제공합니다.

|단계|SQL 엔진 및 버전|백업/복원 방법|
|---|---|---|
|Azure Storage에 백업 저장|2012 SP1 CU2 이전|Azure Storage에 .bak 파일 직접 업로드|
||2012 SP1 CU2~2016|사용되지 않는 [WITH CREDENTIAL](/sql/t-sql/statements/restore-statements-transact-sql) 구문을 사용하여 직접 백업|
||2016 이상|[WITH SAS CREDENTIAL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 구문을 사용하여 직접 백업|
|Azure Storage에서 관리형 인스턴스로 복원|[SAS CREDENTIAL을 사용하여 URL에서 복원](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - 원시 복원 옵션을 사용하여 [투명한 데이터 암호화](../database/transparent-data-encryption-tde-overview.md)로 보호된 데이터베이스를 관리형 인스턴스로 마이크레이션하는 경우에는 데이터베이스를 복원하기 전에 온-프레미스 또는 Azure VM SQL Server의 해당 인증서를 마이그레이션해야 합니다. 자세한 단계는 [TDE 인증서를 관리형 인스턴스로 마이그레이션](tde-certificate-migrate.md)을 참조하세요.
> - 시스템 데이터베이스의 복원은 지원되지 않습니다. master 또는 msdb 데이터베이스에 저장된 인스턴스 수준 개체를 마이그레이션하려면, 해당 개체를 스크립팅하고 대상 인스턴스에서 T-SQL 스크립트를 실행하는 것이 좋습니다.

SAS 자격 증명을 사용하여 데이터베이스 백업을 관리되는 인스턴스에 복원하는 방법을 보여주는 빠른 시작은 [백업에서 관리되는 인스턴스로 복원](restore-sample-database-quickstart.md)을 참조하세요.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>애플리케이션 모니터링

관리형 인스턴스로의 마이그레이션을 완료하고 나면 워크로드의 애플리케이션 동작 및 성능을 추적해야 합니다. 이 프로세스에는 다음 작업이 포함됩니다.

- [관리형 인스턴스에서 실행 중인 워크로드의 성능을](#compare-performance-with-the-baseline) [원본 SQL Server 인스턴스에서 만든 성능 기준과 비교합니다](#create-a-performance-baseline).
- 지속해서 [워크로드의 성능을 모니터링](#monitor-performance)하여 잠재적인 문제와 개선 사항을 식별합니다.

### <a name="compare-performance-with-the-baseline"></a>성능을 기준과 비교

마이그레이션 성공 직후 수행해야 하는 첫 번째 작업은 워크로드의 성능을 기준 워크로드 성능과 비교하는 것입니다. 이 작업의 ​​목표는 관리형 인스턴스의 워크로드 성능이 요구 사항을 충족하는지 확인하는 것입니다.

관리형 인스턴스로 데이터베이스를 마이그레이션하면 대부분의 경우 데이터베이스 설정과 원래 호환성 수준이 유지됩니다. 원본 SQL Server 인스턴스와 비교하여 성능이 저하되는 위험을 줄이기 위해 가능한 경우 원본 설정이 유지됩니다. 마이그레이션 전에 사용자 데이터베이스의 호환성 수준이 100 이상인 경우 마이그레이션 후에도 동일하게 유지됩니다. 마이그레이션 전에 사용자 데이터베이스의 호환성 수준이 90인 경우 업그레이드된 데이터베이스에서 호환성 수준은 관리형 인스턴스에서 지원되는 가장 낮은 호환성 수준인 100으로 설정됩니다. 시스템 데이터베이스의 호환성 수준은 140입니다. 관리형 인스턴스로의 마이그레이션은 실제로 최신 버전의 SQL Server 데이터베이스 엔진으로 마이그레이션되므로 몇 가지 놀라운 성능 문제를 방지하려면 워크로드의 성능을 다시 테스트해야 합니다.

필수 구성 요소로 다음 작업을 완료했는지 확인합니다.

- 다양한 인스턴스, 데이터베이스, tempdb 설정 및 구성을 조사하여 관리형 인스턴스의 설정을 원본 SQL Server 인스턴스의 설정에 맞게 조정합니다. 첫 번째 성능 비교를 실행하기 전에 호환성 수준 또는 암호화와 같은 설정을 변경하지 않았는지 확인하거나 사용으로 설정한 몇 가지 새로운 기능이 일부 쿼리에 영향을 미칠 수 있는 위험을 감수합니다. 마이그레이션 위험을 줄이려면 성능 모니터링 후에만 데이터베이스 호환성 수준을 변경합니다.
- 성능 향상을 위해 파일 크기를 사전 할당하는 것과 같은 [범용 스토리지 모범 사례 지침](https://techcommunity.microsoft.com)을 구현합니다.
- [관리형 인스턴스와 SQL Server 간의 성능 차이를 초래할 수 있는 주요 환경 차이](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)에 관해 알아보고 성능에 영향을 미칠 수 있는 위험을 식별합니다.
- 관리형 인스턴스에서 쿼리 저장소 및 자동 튜닝을 계속 사용하도록 설정해야 합니다. 해당 기능을 통해 워크로드 성능을 측정하고 잠재적인 성능 문제를 자동으로 수정할 수 있습니다. [최신 SQL Server 버전으로 업그레이드하는 동안 성능 안정성 유지](/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)에서 설명한 대로, 데이터베이스 호환성 수준 변경 전후의 워크로드 성능에 대한 정보를 얻기 위한 최적의 도구로 쿼리 저장소를 사용하는 방법을 알아봅니다.
온-프레미스 환경과 최대한 비슷한 환경을 준비했으면 워크로드 실행을 시작하고 성능을 측정할 수 있습니다. 측정 프로세스에는 [원본 SQL Server 인스턴스에서 워크로드 측정의 기준 성능을 생성하는 동안](#create-a-performance-baseline) 측정한 것과 같은 매개 변수가 포함되어야 합니다.
결과적으로 성능 매개 변수를 기준과 비교하고 중요한 차이점을 식별해야 합니다.

> [!NOTE]
> 대부분의 경우 관리형 인스턴스와 SQL Server에서 정확히 일치하는 성능을 얻을 수 없습니다. Azure SQL Managed Instance는 SQL Server 데이터베이스 엔진이지만, 관리형 인스턴스의 인프라 및 고가용성 구성은 약간의 차이가 있을 수 있습니다. 일부 쿼리는 속도가 빨라지지만 다른 쿼리는 속도가 느려질 수 있습니다. 관리형 인스턴스의 워크로드 성능이 SQL Server의 성능(평균)과 일치하는지 확인하고 성능이 원래 성능과 일치하지 않는 중요한 쿼리를 식별하는 것이 비교의 목표입니다.

성능 비교의 결과는 다음과 같을 수 있습니다.

- 관리형 인스턴스의 워크로드 성능이 SQL Server의 워크로드 성능과 일치하거나 낫습니다. 이 경우 마이그레이션이 성공적으로 완료되었음을 확인했습니다.
- 성능이 저하된 일부 예외를 제외하고 대부분의 성능 매개 변수와 워크로드의 쿼리는 정상적으로 작동합니다. 이 경우 차이점과 중요성을 식별해야 합니다. 성능이 저하된 중요한 쿼리가 있는 경우 기본 SQL 플랜이 변경되었는지 아니면 쿼리가 일부 리소스 한도에 도달하는지 조사해야 합니다. 이 경우 완화 방법은 중요한 쿼리(예: 변경된 호환성 수준, 레거시 카디널리티 예측 도구)에 관한 힌트를 직접 적용하거나 플랜 지침을 사용하여 플랜에 영향을 줄 수 있는 통계 및 인덱스를 다시 빌드하거나 만드는 것입니다.
- 대부분의 쿼리는 원본 SQL Server 인스턴스와 비교하여 관리형 인스턴스에서 속도가 느려집니다. 이 경우 IO 한도, 메모리 한도, 인스턴스 로그 속도 한도 등과 같은 [일부 리소스 한도에 도달](resource-limits.md#service-tier-characteristics)과 같은 차이점의 근본 원인을 식별합니다. 차이를 초래할 수 있는 리소스 한도가 없는 경우 데이터베이스의 호환성 수준을 변경하거나 레거시 카디널리티 추정과 같은 데이터베이스 설정을 변경하고 테스트를 다시 시작합니다. 관리형 인스턴스 또는 쿼리 저장소 보기에서 제공하는 권장 사항을 검토하여 성능을 회귀시킨 쿼리를 확인합니다.

> [!IMPORTANT]
> Azure SQL Managed Instance에는 기본적으로 사용되는 자동 플랜 수정 기능이 기본적으로 제공됩니다. 이 기능을 사용하면 붙여넣기에서 제대로 작동한 쿼리의 성능이 나중에 저하되지 않습니다. 관리형 인스턴스가 기준 성능과 플랜에 관해 학습할 수 있도록 새 설정을 변경하기 전에 이 기능이 사용으로 설정되어 있고 이전 설정을 사용하여 충분히 오래 워크로드를 실행했는지 확인합니다.

요구 사항에 맞는 워크로드 성능을 얻을 때까지 매개 변수를 변경하거나 서비스 계층을 업그레이드하여 최적의 구성을 만듭니다.

### <a name="monitor-performance"></a>성능 모니터링

SQL Managed Instance는 모니터링 및 문제 해결을 위한 많은 고급 도구를 제공하므로, 해당 도구를 사용하여 인스턴스의 성능을 모니터링해야 합니다. 모니터링해야 하는 매개 변수 중 일부는 다음과 같습니다.

- 프로비저닝한 vCore 수가 워크로드에 적합한지 확인하는 인스턴스의 CPU 사용량입니다.
- [추가 메모리 필요 여부](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)를 판별하는 관리형 인스턴스의 페이지 예상 수명입니다.
- 특히 더 나은 IO 성능을 얻기 위해 파일을 사전 할당해야 하는 범용 계층에서 스토리지 IO 문제가 있는지 알려주는 `INSTANCE_LOG_GOVERNOR` 또는 `PAGEIOLATCH`와 같은 통계입니다.

## <a name="leverage-advanced-paas-features"></a>고급 PaaS 기능 활용

완전 관리형 플랫폼에 있고 워크로드 성능이 SQL Server 워크로드 성능과 일치하는지 확인한 경우 서비스의 일부로 자동 제공되는 이점을 사용합니다.

마이그레이션 중에 관리형 인스턴스를 일부 변경하지 않더라도 최신 데이터베이스 엔진 개선 사항을 활용하기 위해 인스턴스를 운영하는 동안 몇 가지 새로운 기능을 켤 가능성이 큽니다. [데이터베이스 호환성 수준이 변경](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)된 경우에만 일부 변경 내용이 사용됩니다.

예를 들어 서비스에서 백업을 자동으로 수행하므로 관리형 인스턴스에 백업을 만들 필요가 없습니다. 백업 예약, 가져오기 및 관리에 대해 더 이상 걱정할 필요가 없습니다. SQL Managed Instance는 [PITR(지정 시간 복구)](../database/recovery-using-backups.md#point-in-time-restore)을 사용하여 이 보존 기간 내의 특정 시점으로 복원하는 기능을 제공합니다. 또한 [고가용성](../database/high-availability-sla.md)이 기본 제공되므로 고가용성 설정에 대해 걱정할 필요가 없습니다.

보안을 강화하려면 [Azure Active Directory Authentication](../database/security-overview.md), [감사](auditing-configure.md), [위협 탐지](../database/azure-defender-for-sql.md), [행 수준 보안](/sql/relational-databases/security/row-level-security) 및 [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking) 사용을 고려하세요.

고급 관리 및 보안 기능 외에도 관리형 인스턴스는 [워크로드를 모니터링하고 튜닝](../database/monitor-tune-overview.md)하는 데 도움이 되는 고급 도구 세트를 제공합니다. [Azure SQL 분석](../../azure-monitor/insights/azure-sql.md)을 사용하면 대규모 관리형 인스턴스 세트를 모니터링하고 다수의 인스턴스와 데이터베이스에 대한 모니터링을 중앙 집중화할 수 있습니다. 관리형 인스턴스에서 [자동 튜닝](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)은 SQL 플랜 실행 통계의 성능을 지속해서 모니터링하고 식별된 성능 문제를 자동으로 해결합니다.

## <a name="next-steps"></a>다음 단계

- Azure SQL Managed Instance에 관한 자세한 내용은 [Azure SQL Managed Instance란?](sql-managed-instance-paas-overview.md)을 참조하세요.
- 백업에서 복원을 포함한 자습서는 [관리되는 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.
- Database Migration Service를 사용한 마이그레이션을 보여 주는 자습서는 [Database Migration Service를 사용하여 온-프레미스 데이터베이스를 Azure SQL Managed Instance로 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.