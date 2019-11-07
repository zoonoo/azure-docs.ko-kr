---
title: SQL Server 인스턴스에서 Azure SQL Database 관리 인스턴스로 데이터베이스 마이그레이션
description: SQL Server 인스턴스에서 Azure SQL Database 관리 되는 인스턴스로 데이터베이스를 마이그레이션하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: 228b22d9d283fe8c23cbf7a82036b7f3782cbf25
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688002"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>SQL Server 인스턴스를 Azure SQL Database 관리형 인스턴스로 마이그레이션

이 문서에서는 SQL Server 2005 이상 버전 인스턴스를 [Azure SQL Database 관리형 인스턴스](sql-database-managed-instance.md)로 마이그레이션하는 방법에 대해 설명합니다. 단일 데이터베이스 또는 탄력적 풀로 마이그레이션하는 방법에 대한 자세한 내용은 [단일 또는 풀링된 데이터베이스로 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요. 다른 플랫폼에서 마이그레이션하는 방법에 대한 마이그레이션 정보는 [Azure 데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조하세요.

> [!NOTE]
> 신속 하 게 시작 하 고 Managed Instance 하려는 경우이 페이지 대신 [빠른 시작 가이드](sql-database-managed-instance-quickstart-guide.md) 로 이동 하는 것이 좋습니다. 

높은 수준에서 데이터베이스 마이그레이션 프로세스는 다음과 같습니다.

![마이그레이션 프로세스](./media/sql-database-managed-instance-migration/migration-process.png)

- 마이그레이션을 방해할 수 있는 차단 문제가 없는지 확인 해야 하는 [관리 되는 인스턴스 호환성을 평가](#assess-managed-instance-compatibility) 합니다.
  - 또한이 단계에서는 원본 SQL Server 인스턴스에서 리소스 사용량을 확인 하는 [성능 기준선](#create-performance-baseline) 을 만듭니다. 이 단계는 Managed Instance 적절 한 크기의 배포를 수행 하 고 마이그레이션 후의 성능에 영향을 주지 않는지 확인 하는 경우에 필요 합니다.
- [앱 연결 옵션 선택](sql-database-managed-instance-connect-app.md)
- 사용자가 Managed Instance의 기술 특성 (vCores 수, 메모리 양) 및 성능 계층 (중요 비즈니스용, 범용)을 선택할 수 있는 [최적의 크기의 관리 되는 인스턴스에 배포](#deploy-to-an-optimally-sized-managed-instance) 합니다.
- 오프 라인 마이그레이션 (네이티브 백업/복원, 데이터베이스 importe/내보내기) 또는 온라인 마이그레이션 (데이터 마이그레이션 서비스, 트랜잭션 복제)을 사용 하 여 데이터베이스를 마이그레이션하는 [마이그레이션 방법 및](#select-migration-method-and-migrate) 마이그레이션을 선택 합니다.
- [응용 프로그램을 모니터링](#monitor-applications) 하 여 성능이 예상 되는지 확인 합니다.

> [!NOTE]
> 개별 데이터베이스를 단일 데이터베이스 또는 탄력적 풀로 마이그레이션하려면 [Azure SQL Database로 SQL Server 데이터베이스 마이그레이션](sql-database-single-database-migrate.md)을 참조하세요.

## <a name="assess-managed-instance-compatibility"></a>관리되는 인스턴스 호환성 평가

먼저 관리되는 인스턴스가 애플리케이션의 데이터베이스 요구 사항과 호환되는지 확인합니다. 관리되는 인스턴스 배포 옵션은 온-프레미스 또는 가상 머신에서 SQL Server를 사용하는 대부분의 기존 애플리케이션에서 리프트 앤 시프트 방식으로 쉽게 마이그레이션할 수 있도록 설계되었습니다. 그러나 경우에 따라 아직 지원되지 않는 기능이 필요할 수 있으며 해결 방법을 구현하는 데 드는 비용이 너무 높습니다.

[DMA(Data Migration Assistant)](https://docs.microsoft.com/sql/dma/dma-overview)를 사용하여 Azure SQL Database에서 데이터베이스 기능에 영향을 주는 잠재적인 호환성 문제를 검색합니다. DMA는 아직 관리형 인스턴스를 마이그레이션 대상으로 지원하지 않지만, Azure SQL Database에 대한 평가를 실행하고 제품 설명서에 대해 보고된 기능 패리티 및 호환성 문제 목록을 신중하게 검토하는 것이 좋습니다. Azure SQL Database로 마이그레이션하지 못하도록 차단하는 대부분의 문제는 관리형 인스턴스를 통해 제거되었으므로 관리되는 인스턴스에서 차단하지 않는 일부 보고된 차단 문제가 있는지 확인하려면 [Azure SQL Database 기능](sql-database-features.md)을 참조하세요. 예를 들어 데이터베이스 간 쿼리, 동일한 인스턴스 내의 데이터베이스 간 트랜잭션, 다른 SQL 원본에 연결된 서버, CLR, 글로벌 임시 테이블, 인스턴스 수준 보기, Service Broker 등과 같은 기능은 관리되는 인스턴스에서 사용할 수 있습니다.

관리되는 인스턴스 배포 옵션에서 제거되지 않은 일부 보고된 차단 문제가 있는 경우 [Azure 가상 머신의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)와 같은 대체 옵션을 고려해야 합니다. 다음은 몇 가지 예입니다.

- 운영 체제 또는 파일 시스템에 직접 액세스해야 하는 경우(예: SQL Server가 있는 동일한 가상 머신에 타사 또는 사용자 지정 에이전트를 설치하는 경우)
- FileStream/FileTable, PolyBase 및 인스턴스 간 트랜잭션과 같이 아직 지원되지 않는 기능에 대한 엄격한 종속성이 있는 경우
- 반드시 특정 버전의 SQL Server (예를 들어 2012)를 유지 해야 하는 경우입니다.
- 컴퓨팅 요구 사항이 관리되는 인스턴스에서 제공하는 것보다 훨씬 낮고(예: 하나의 vCore) 데이터베이스 통합이 허용되지 않는 옵션인 경우

식별 된 모든 마이그레이션 차단기를 해결 하 고 Managed Instance로 마이그레이션을 계속 하는 경우 일부 변경 내용이 워크 로드의 성능에 영향을 줄 수 있습니다.
- 정기적으로 단순/대량 로그 모델을 사용 했거나 요청 시 백업을 중지 한 경우 필수 전체 복구 모델 및 정기적인 자동 백업 일정은 워크 로드 또는 유지 관리/ETL 작업의 성능에 영향을 줄 수 있습니다.
- 다른 서버 또는 데이터베이스 수준 구성 (예: 추적 플래그 또는 호환성 수준)
- TDE (투명 데이터베이스 암호화) 또는 자동 장애 조치 (failover) 그룹과 같은 사용 중인 새로운 기능은 CPU 및 IO 사용에 영향을 줄 수 있습니다.

Managed Instance는 중요 한 시나리오 에서도 99.99%의 가용성을 보장 하므로 이러한 기능으로 인해 발생 하는 오버 헤드를 비활성화할 수 없습니다. 자세한 내용은 [SQL Server 및 Managed Instance에서 다른 성능을 발생 시킬 수 있는 근본 원인](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)을 참조 하세요.

### <a name="create-performance-baseline"></a>성능 기준선 만들기

SQL Server에서 실행 되는 원래 워크 로드와 Managed Instance에 대 한 작업의 성능을 비교 해야 하는 경우 비교에 사용할 성능 기준선을 만들어야 합니다. 

성능 기준은 평균/최대 CPU 사용량, 평균/최대 디스크 IO 대기 시간, 처리량, IOPS, 평균/최대 페이지 수명 예상, tempdb의 평균 최대 크기와 같은 매개 변수 집합입니다. 마이그레이션 후에 비슷하거나 더 나은 매개 변수를 사용할 수 있으므로 이러한 매개 변수의 기준 값을 측정 하 고 기록 하는 것이 중요 합니다. 시스템 매개 변수 외에도 워크 로드에서 대표적인 쿼리 집합 또는 가장 중요 한 쿼리를 선택 하 고 선택한 쿼리에 대 한 최소/평균/최대 기간, CPU 사용량을 측정 해야 합니다. 이러한 값을 사용 하 여 Managed Instance에서 실행 되는 워크 로드의 성능을 원본 SQL Server의 원래 값과 비교할 수 있습니다.

SQL Server 인스턴스에서 측정 해야 하는 매개 변수는 다음과 같습니다. 
- [SQL Server 인스턴스의 cpu 사용량을 모니터링](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) 하 고 평균 및 최대 cpu 사용량을 기록 합니다.
- [SQL Server 인스턴스의 메모리 사용량을 모니터링](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) 하 고 버퍼 풀, 계획 캐시, 열 저장소 풀, [메모리 내 OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)등의 여러 구성 요소에서 사용 하는 메모리 양을 확인 합니다. 또한 페이지 수명 예상 메모리 성능 카운터의 평균 및 최대 값을 찾아야 합니다.
- [_Io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) view 또는 [성능 카운터](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)를 사용 하 여 원본 SQL Server 인스턴스에서 디스크 IO 사용량을 모니터링 합니다.
- SQL Server 2016 이상 버전에서 마이그레이션하는 경우 동적 관리 뷰 또는 쿼리 저장소를 검토 하 여 워크 로드 및 쿼리 성능 또는 SQL Server 인스턴스를 모니터링할 수 있습니다. 작업에서 가장 중요 한 쿼리의 평균 기간 및 CPU 사용량을 확인 하 여 Managed Instance에서 실행 되는 쿼리와 비교 합니다.

> [!Note]
> 높은 CPU 사용량, 상수 메모리가 중, tempdb 또는 parametrization 문제 등 SQL Server에 대 한 문제에 대 한 문제가 발생 하는 경우에는 기준선 및 마이그레이션을 수행 하기 전에 원본 SQL Server 인스턴스에서 문제를 해결 해야 합니다. 새 시스템 migh에 대 한 알려진 문제를 마이그레이션하면 예기치 않은 결과가 발생 하 고 성능 비교가 무효화 됩니다.

이 작업의 결과로 원본 시스템의 CPU, 메모리 및 IO 사용에 대 한 평균 및 최대 기간과 작업에서 가장 중요 한 쿼리의 평균 및 최대 기간과 CPU 사용량을 문서화 해야 합니다. 나중에 이러한 값을 사용 하 여 Managed Instance에 대 한 작업의 성능을 원본 SQL Server에 대 한 작업의 기준 성능과 비교할 수 있습니다.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>최적 크기의 관리되는 인스턴스에 배포

관리되는 인스턴스는 클라우드로 이동할 온-프레미스 워크로드에 맞게 조정됩니다. 작업에 적합한 수준의 리소스를 선택할 때 유연성이 높은 [새 구매 모델](sql-database-service-tiers-vcore.md)이 도입되었습니다. 온-프레미스 환경에서는 실제 코어 및 IO 대역폭을 사용하여 이러한 작업의 크기를 조정하는 데 익숙할 것입니다. 관리되는 인스턴스에 대한 구매 모델은 가상 코어 수 또는 "vCore 수"를 기반으로 하며, 추가 스토리지 및 IO를 별도로 사용할 수 있습니다. vCore 모델은 현재 온-프레미스에서 사용하는 제품과 비교하여 클라우드의 컴퓨팅 요구 사항을 더 쉽게 이해할 수 있는 방법입니다. 새로운 이 모델을 사용하면 클라우드에서 대상 환경의 크기를 올바르게 조정할 수 있습니다. 올바른 서비스 계층 및 특성을 선택 하는 데 도움이 되는 몇 가지 일반적인 지침은 여기에 설명 되어 있습니다.
- 기준 CPU 사용량에 따라 SQL Server에서 사용 하는 코어 수와 일치 하는 Managed Instance 프로 비전 할 수 있습니다. [Managed Instance 설치 된 VM 특성과 일치 하도록 CPU 특성을 확장 해야 할 수도 있습니다. ](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- 기준 메모리 사용량에 따라 [일치 하는 메모리가 있는 서비스 계층을](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)선택 합니다. 메모리 크기를 직접 선택할 수 없기 때문에 일치 하는 메모리가 있는 Vcores의 크기 (예: Gen5의 5.1 g b/Vcores)를 사용 하 여 Managed Instance를 선택 해야 합니다. 
- 파일 하위 시스템의 기준 IO 대기 시간에 따라 일반적인 용도 (5ms 보다 긴 대기 시간)를 선택 하 고 서비스 계층 (대기 시간 3 밀리초 미만)을 중요 비즈니스용 합니다.
- 기준 처리량에 따라 데이터 또는 로그 파일의 크기를 미리 할당 하 여 예상 IO 성능을 얻습니다.

배포 시 계산 및 저장소 리소스를 선택한 다음, [Azure Portal](sql-database-scale-resources.md)를 사용 하 여 응용 프로그램에 대 한 가동 중지 시간을 발생 시 키 지 않고 나중에 변경할 수 있습니다.

![관리되는 인스턴스 크기 조정](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

VNet 인프라와 관리되는 인스턴스를 만드는 방법을 알아보려면 [관리되는 인스턴스 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.

> [!IMPORTANT]
> 언제나 대상 VNet 및 서브넷을 [관리되는 인스턴스 VNet 요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)에 따라 유지해야 합니다. 호환되지 않는 경우 새 인스턴스를 만들거나 이미 만든 인스턴스를 사용하지 못할 수 있습니다. [새 네트워크 만들기](sql-database-managed-instance-create-vnet-subnet.md) 및 [기존 네트워크 구성](sql-database-managed-instance-configure-vnet-subnet.md)에 대해 자세히 알아보세요.

## <a name="select-migration-method-and-migrate"></a>마이그레이션 방법 선택 및 마이그레이션

관리되는 인스턴스 배포 옵션은 온-프레미스 또는 IaaS 데이터베이스 구현에서 대량의 데이터베이스 마이그레이션이 필요한 사용자 시나리오를 대상으로 합니다. 인스턴스 수준 및/또는 데이터베이스 간 기능을 정기적으로 사용하는 애플리케이션의 백 엔드를 리프트 앤 시프트 방식으로 이동해야 하는 경우에 최적의 선택입니다. 이러한 시나리오의 경우 애플리케이션을 다시 구성하지 않고도 Azure에서 전체 인스턴스를 해당 환경으로 이동할 수 있습니다.

SQL 인스턴스를 이동하려면 다음을 신중하게 계획해야 합니다.

- 배치해야 하는 모든 데이터베이스(동일한 인스턴스에서 실행되는 데이터베이스)의 마이그레이션
- 로그인, 자격 증명, SQL 에이전트 작업 및 연산자, 서버 수준 트리거를 포함하여 애플리케이션이 종속된 인스턴스 수준 개체의 마이그레이션

관리되는 인스턴스는 정기적인 DBA 작업의 일부를 기본 제공되는 플랫폼에 위임할 수 있도록 하는 관리되는 서비스입니다. 따라서 [고가용성](sql-database-high-availability.md)이 기본 제공되므로 정기 백업을 위한 유지 관리 작업 또는 Always On 구성과 같이 일부 인스턴스 수준 데이터는 마이그레이션할 필요가 없습니다.

관리되는 인스턴스에서 지원하는 데이터베이스 마이그레이션 옵션은 다음과 같습니다(현재 지원되는 유일한 마이그레이션 방법임).

- Azure Database Migration Service - 거의 제로에 가까운 가동 중지 시간으로 마이그레이션
- 네이티브 `RESTORE DATABASE FROM URL` - SQL Server의 네이티브 백업을 사용하며 약간의 가동 중지 시간 필요

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure DMS(Database Migration Service)](../dms/dms-overview.md)는 가동 중지 시간을 최소화하면서 여러 데이터베이스 원본에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있도록 설계된 완벽하게 관리되는 서비스입니다. 이 서비스는 기존 타사 및 SQL Server 데이터베이스를 Azure로 이동하는 데 필요한 작업을 간소화합니다. 공개 미리 보기의 배포 옵션에는 Azure SQL Database의 데이터베이스 및 Azure Virtual Machine의 SQL Server 데이터베이스가 포함됩니다. DMS는 엔터프라이즈 작업에 권장되는 마이그레이션 방법입니다.

SQL Server 온-프레미스에서 SSIS(SQL Server Integration Services)를 사용하는 경우, DMS는 SSIS 패키지를 저장하는 SSIS 카탈로그(SSISDB)의 마이그레이션을 아직 지원하지 않지만, 관리되는 인스턴스에 새 SSISDB를 만들 Azure-SSIS IR(Integration Runtime)을 ADF(Azure Data Factory)에 프로비전하고 해당 패키지를 다시 배포할 수 있습니다. [ADF에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.

이 시나리오와 DMS 구성 단계에 대해 자세히 알아보려면 [DMS를 사용하여 온-프레미스 데이터베이스를 관리되는 인스턴스로 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.  

### <a name="native-restore-from-url"></a>URL에서 네이티브 복원

[Azure Storage](https://azure.microsoft.com/services/virtual-machines/sql-server/)에서 사용 가능한 온-프레미스 SQL Server 또는 [Virtual Machines의 SQL Server](https://azure.microsoft.com/services/storage/)에서 가져온 네이티브 백업(.bak 파일)의 복원은 SQL DB 관리되는 인스턴스 배포 옵션의 주요 기능 중 하나이며, 오프라인 데이터베이스 마이그레이션을 빠르고 쉽게 수행할 수 있도록 합니다.

다음 다이어그램은 프로세스의 상위 수준 개요를 제공합니다.

![마이그레이션 흐름](./media/sql-database-managed-instance-migration/migration-flow.png)

다음 표에서는 실행되는 원본 SQL Server 버전에 따라 사용할 수 있는 방법에 대한 자세한 정보를 제공합니다.

|단계|SQL 엔진 및 버전|백업/복원 방법|
|---|---|---|
|Azure Storage에 백업 저장|SQL 2012 SP1 CU2 이전|Azure Storage에 .bak 파일 직접 업로드|
||2012 SP1 CU2~2016|사용되지 않는 [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) 구문을 사용하여 직접 백업|
||2016 이상|[WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) 구문을 사용하여 직접 백업|
|Azure Storage에서 관리되는 인스턴스로 복원|[SAS CREDENTIAL을 사용하여 URL에서 복원](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - 원시 복원 옵션을 사용하여 [투명한 데이터 암호화](transparent-data-encryption-azure-sql.md)로 보호된 데이터베이스를 관리되는 인스턴스로 마이크레이션하는 경우에는 데이터베이스를 복원하기 전에 온-프레미스 또는 IaaS SQL Server의 해당 인증서를 마이그레이션해야 합니다. 자세한 단계는 [관리되는 인스턴스로 TDE 인증서 마이그레이션](sql-database-managed-instance-migrate-tde-certificate.md)을 참조하세요.
> - 시스템 데이터베이스의 복원은 지원되지 않습니다. master 또는 msdb 데이터베이스에 저장된 인스턴스 수준 개체를 마이그레이션하려면, 이러한 개체를 스크립팅하고 대상 인스턴스에서 T-SQL 스크립트를 실행하는 것이 좋습니다.

SAS 자격 증명을 사용하여 데이터베이스 백업을 관리되는 인스턴스에 복원하는 방법을 보여주는 빠른 시작은 [백업에서 관리되는 인스턴스로 복원](sql-database-managed-instance-get-started-restore.md)을 참조하세요.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>애플리케이션 모니터링

Managed Instance로 마이그레이션을 완료 한 후에는 작업의 응용 프로그램 동작 및 성능을 추적 해야 합니다. 이 프로세스에는 다음과 같은 작업이 포함 됩니다.
- [Managed Instance에서 실행 되는 워크 로드의 성능을](#compare-performance-with-the-baseline) [원본 SQL Server에서 만든 성능 기준선](#create-performance-baseline)과 비교 합니다.
- [작업 성능을](#monitor-performance) 지속적으로 모니터링 하 여 잠재적인 문제 및 개선을 파악 합니다.

### <a name="compare-performance-with-the-baseline"></a>성과를 기준선과 비교

성공적으로 마이그레이션한 후 즉시 수행 해야 하는 첫 번째 작업은 작업의 성능과 기준 워크 로드 성능을 비교 하는 것입니다. 이 작업의 목표는 Managed Instance의 워크 로드 성능이 요구를 충족 하는지 확인 하는 것입니다. 

Managed Instance에 대 한 데이터베이스 마이그레이션은 대부분의 경우 데이터베이스 설정과 원래 호환성 수준을 유지 합니다. 원본 SQL Server에 비해 일부 성능 저하 위험을 줄이기 위해 가능한 경우 원래 설정이 유지 됩니다. 마이그레이션 전에 사용자 데이터베이스의 호환성 수준이 100 이상인 경우 마이그레이션 후에도 동일하게 유지됩니다. 마이그레이션 전에 사용자 데이터베이스의 호환성 수준이 90인 경우 업그레이드된 데이터베이스에서 호환성 수준은 관리되는 인스턴스에서 지원되는 가장 낮은 호환성 수준인 100으로 설정됩니다. 시스템 데이터베이스의 호환성 수준은 140입니다. Managed Instance로 마이그레이션하는 작업은 실제로 SQL Server 데이터베이스 엔진 최신 버전으로 마이그레이션하기 때문에 몇 가지 놀라운 성능 문제를 방지 하기 위해 워크 로드의 성능을 다시 테스트 해야 한다는 점에 유의 해야 합니다.

필수 구성 요소로, 다음 작업을 완료 했는지 확인 합니다.
- 다양 한 인스턴스, 데이터베이스, temdb 설정 및 구성을 조사 하 여 Managed Instance에 대 한 설정을 원본 SQL Server 인스턴스의 설정과 맞춥니다. 첫 번째 성능 비교를 실행 하기 전에 호환성 수준 또는 암호화와 같은 설정을 변경 하지 않았는지 확인 하거나 사용 하도록 설정한 일부 새로운 기능이 일부 쿼리에 영향을 줄 수 있다는 것을 허용 합니다. 마이그레이션 위험을 줄이려면 성능 모니터링 후에만 데이터베이스 호환성 수준을 변경합니다.
- 더 나은 성능을 얻기 위해 파일 크기를 미리 할당 하는 것과 같은 [일반적인 용도로 저장소 모범 사례 지침](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) 을 구현 합니다.
- [Managed Instance와 SQL Server 간의 성능 차이를 일으킬 수 있는 주요 환경 차이점]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) 에 대해 알아보고 성능에 영향을 줄 수 있는 위험을 식별 합니다.
- Managed Instance에서 쿼리 저장소 및 자동 튜닝을 사용 하도록 설정 했는지 확인 합니다. 이러한 기능을 사용 하 여 워크 로드 성능을 측정 하 고 잠재적인 성능 문제를 자동으로 해결할 수 있습니다. [최신 SQL Server 버전으로 업그레이드 하는 동안 성능 안정성 유지](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)의 설명에 따라 데이터베이스 호환성 수준 변경 전후에 워크 로드 성능에 대 한 정보를 얻기 위한 최적의 도구로 쿼리 저장소를 사용 하는 방법을 알아봅니다.
온-프레미스 환경과 최대한 유사한 환경을 준비한 후에는 워크 로드 실행을 시작 하 고 성능을 측정할 수 있습니다. 측정 프로세스 [에는 원본 SQL Server에 대 한 작업 측정값의 기준 성능을 만드는 동안](#create-performance-baseline)측정 한 것과 동일한 매개 변수가 포함 되어야 합니다.
결과적으로 성능 매개 변수를 기준선과 비교 하 고 중요 한 차이점을 식별 해야 합니다.

> [!NOTE]
> 대부분의 경우 Managed Instance 및 SQL Server에서 정확히 일치 하는 성능을 얻을 수 없습니다. Managed Instance은 SQL Server 데이터베이스 엔진 이지만 Managed Instance에서 인프라 및 고가용성 구성이 약간의 차이가 발생할 수 있습니다. 일부 쿼리는 속도가 더 빠르지만 다른 쿼리는 속도가 느릴 수 있습니다. 비교의 목표는 Managed Instance의 워크 로드 성능이 SQL Server (평균)의 성능과 일치 하는지 확인 하 고, 원래 성능과 일치 하지 않는 성능으로 중요 한 쿼리가 있는지 확인 하는 것입니다.

성능 비교의 결과는 다음과 같을 수 있습니다.
- Managed Instance에 대 한 워크 로드 성능은 SQL Server의 워크 로드 성능 보다 잘 맞춰져 있습니다. 이 경우 마이그레이션이 성공 했음을 성공적으로 확인 했습니다.
- 대부분의 성능 매개 변수 및 워크 로드의 쿼리는 성능이 저하 되는 몇 가지 예외를 제외 하 고는 정상적으로 작동 합니다. 이 경우 차이점 및 중요도를 확인 해야 합니다. 성능이 저하 된 몇 가지 중요 한 쿼리가 있는 경우 기본 SQL 계획이 변경 되었거나 쿼리가 일부 리소스 제한에 도달 하 고 있는지 조사 해야 합니다. 이 경우 완화는 계획 지침을 사용 하거나 계획 지침을 사용 하 여 계획에 영향을 줄 수 있는 통계 및 인덱스를 다시 작성 하거나 작성 하는 중요 한 쿼리 (예: 호환성 수준 변경, 기존 카디널리티 평가기)에 몇 가지 힌트를 적용 하는 것일 수 있습니다. 
- 대부분의 쿼리는 원본 SQL Server에 비해 Managed Instance 속도가 느립니다. 이 경우 IO 제한, 메모리 제한, 인스턴스 로그 전송률 제한 등과 같은 [리소스 제한에 도달]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) 하는 것과 같은 차이의 근본 원인을 파악 하려고 합니다. 차이를 일으킬 수 있는 리소스 제한이 없는 경우 데이터베이스의 호환성 수준을 변경 하거나 레거시 카디널리티 예측 등의 데이터베이스 설정을 변경 하 고 테스트를 다시 시작 하십시오. Managed Instance 또는 쿼리 저장소 보기에서 제공 하는 권장 사항을 검토 하 여 성능을 재발 시킨 쿼리를 확인 합니다.

> [!IMPORTANT]
> Managed Instance에는 기본적으로 사용 되는 자동 계획 수정 기능이 기본적으로 제공 됩니다. 이 기능을 사용 하면 나중에 붙여넣을 때 잘 작동 하 던 쿼리가 저하 되지 않습니다. 이 기능을 사용 하도록 설정 하 고 새 설정을 변경 하기 전에 이전 설정으로 충분 한 시간 동안 워크 로드를 실행 했는지 확인 하 여 기준 성능 및 계획에 대 한 자세한 정보를 Managed Instance 수 있도록 합니다.

요구 사항에 맞는 워크 로드 성능을 얻을 때까지 최적의 구성으로 수렴 하도록 매개 변수 또는 업그레이드 서비스 계층을 변경 합니다.

### <a name="monitor-performance"></a>성능 모니터링

Managed Instance는 모니터링 및 문제 해결을 위한 다양 한 고급 도구를 제공 하며, 이러한 도구를 사용 하 여 인스턴스의 성능을 모니터링 해야 합니다. 모니터링 해야 하는 매개 변수 중 일부는 다음과 같습니다.
- 인스턴스의 CPU 사용량은 프로 비전 한 vCores 수가 워크 로드와 적절 한지 확인 합니다.
- [추가 메모리가 필요한](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)지 여부를 확인 하기 위해 Managed Instance의 페이지 수명 예상
- 특히 IO 성능을 향상 시키기 위해 파일을 미리 할당 해야 하는 일반적인 용도의 계층에서 저장소 IO 문제가 있음을 알리는 `INSTANCE_LOG_GOVERNOR` 또는 `PAGEIOLATCH`와 같은 대기 통계입니다.

## <a name="leverage-advanced-paas-features"></a>고급 PaaS 기능 활용

완전히 관리 되는 플랫폼을 사용 하 고 워크 로드 성능이 SQL Server 워크 로드 성능과 일치 하는지 확인 한 후에는 SQL Database 서비스의 일부로 자동으로 제공 되는 이점을 얻을 수 있습니다. 

마이그레이션하는 동안 관리 되는 인스턴스를 일부 변경 하지 않는 경우에도 최신 데이터베이스 엔진의 향상 된 기능을 활용 하기 위해 인스턴스를 운영 하는 동안 몇 가지 새로운 기능을 설정할 가능성이 높습니다. 일부 변경 내용은 [데이터베이스 호환성 수준이 변경](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)된 후에만 사용할 수 있습니다.


예를 들어 서비스에서 백업을 자동으로 수행하므로 관리되는 인스턴스에 백업을 만들 필요가 없습니다. 백업 예약, 가져오기 및 관리에 대해 더 이상 걱정할 필요가 없습니다. 관리되는 인스턴스는 [PITR(지정 시간 복구)](sql-database-recovery-using-backups.md#point-in-time-restore)을 사용하여 이 보존 기간 내의 특정 시점으로 복원하는 기능을 제공합니다. 또한 [고가용성](sql-database-high-availability.md)이 기본 제공되므로 고가용성 설정에 대해 걱정할 필요가 없습니다.

보안을 강화 하려면 [Azure Active Directory 인증](sql-database-security-overview.md), [감사](sql-database-managed-instance-auditing.md), [위협 검색](sql-database-advanced-data-security.md), [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)및 [동적 데이터 마스킹](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) )를 사용 하는 것이 좋습니다.

Managed Instance는 고급 관리 및 보안 기능 외에도, [작업을 모니터링 하 고 조정](sql-database-monitor-tune-overview.md)하는 데 도움이 되는 고급 도구 집합을 제공 합니다. [AZURE SQL 분석](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) 을 사용 하면 많은 수의 관리 되는 인스턴스를 모니터링 하 고 많은 수의 인스턴스와 데이터베이스를 중앙 집중식으로 모니터링할 수 있습니다. [자동 조정](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) MANAGED INSTANCE는 SQL 계획 실행 통계의 성능을 지속적으로 모니터링 하 고 식별 된 성능 문제를 자동으로 해결 합니다.

## <a name="next-steps"></a>다음 단계

- 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스란?](sql-database-managed-instance.md)을 참조하세요.
- 백업에서 복원을 포함한 자습서는 [관리되는 인스턴스 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- DMS를 사용한 마이그레이션을 보여 주는 자습서는 [DMS를 사용하여 온-프레미스 데이터베이스를 관리되는 인스턴스로 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.  
