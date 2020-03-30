---
title: SQL Server에서 관리되는 인스턴스로 마이그레이션
description: SQL Server 인스턴스에서 Azure SQL Database - 관리형 인스턴스로 데이터베이스를 마이그레이션하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: 6bae9e871be2a5d56d057d2a077de53329b8c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208937"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>SQL Server 인스턴스를 Azure SQL Database 관리형 인스턴스로 마이그레이션

이 문서에서는 SQL Server 2005 이상 버전 인스턴스를 [Azure SQL Database 관리형 인스턴스](sql-database-managed-instance.md)로 마이그레이션하는 방법에 대해 설명합니다. 단일 데이터베이스 또는 탄력적 풀로 마이그레이션하는 방법에 대한 자세한 내용은 [단일 또는 풀링된 데이터베이스로 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요. 다른 플랫폼에서 마이그레이션하는 방법에 대한 마이그레이션 정보는 [Azure Database 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조하세요.

> [!NOTE]
> 관리되는 인스턴스를 빠르게 시작하고 사용하려는 경우 이 페이지 대신 [빠른 시작 가이드로](sql-database-managed-instance-quickstart-guide.md) 이동해야 할 수 있습니다. 

높은 수준에서 데이터베이스 마이그레이션 프로세스는 다음과 같습니다.

![마이그레이션 프로세스](./media/sql-database-managed-instance-migration/migration-process.png)

- 마이그레이션을 방지할 수 있는 차단 문제가 없는지 확인해야 하는 [관리되는 인스턴스 호환성을 평가합니다.](#assess-managed-instance-compatibility)
  - 이 단계에서는 원본 SQL Server 인스턴스에서 리소스 사용량을 결정하는 [성능 기준을](#create-performance-baseline) 만드는 것도 포함됩니다. 이 단계는 o 배포 적절한 크기의 관리형 인스턴스를 배포하고 마이그레이션 후의 성능이 영향을 받지 않는지 확인하는 경우에 필요합니다.
- [앱 연결 옵션 선택](sql-database-managed-instance-connect-app.md)
- 관리되는 [인스턴스의](#deploy-to-an-optimally-sized-managed-instance) 기술적 특성(vCore, 메모리 양) 및 성능 계층(비즈니스 중요, 범용)을 선택하는 최적의 크기의 관리형 인스턴스에 배포합니다.
- [오프라인 마이그레이션(기본](#select-migration-method-and-migrate) 백업/복원, 데이터베이스 가져오기/내보내기) 또는 온라인 마이그레이션(데이터 마이그레이션 서비스, 트랜잭션 복제)을 사용하여 마이그레이션 방법을 선택하고 데이터베이스를 마이그레이션하는 위치를 마이그레이션합니다.
- [응용 프로그램을 모니터링하여](#monitor-applications) 성능이 예상되는지 확인합니다.

> [!NOTE]
> 개별 데이터베이스를 단일 데이터베이스 또는 탄력적 풀로 마이그레이션하려면 [Azure SQL Database로 SQL Server 데이터베이스 마이그레이션](sql-database-single-database-migrate.md)을 참조하세요.

## <a name="assess-managed-instance-compatibility"></a>관리되는 인스턴스 호환성 평가

먼저 관리되는 인스턴스가 애플리케이션의 데이터베이스 요구 사항과 호환되는지 확인합니다. 관리되는 인스턴스 배포 옵션은 온-프레미스 또는 가상 머신에서 SQL Server를 사용하는 대부분의 기존 애플리케이션에서 리프트 앤 시프트 방식으로 쉽게 마이그레이션할 수 있도록 설계되었습니다. 그러나 경우에 따라 아직 지원되지 않는 기능이 필요할 수 있으며 해결 방법을 구현하는 데 드는 비용이 너무 높습니다.

[DMA(Data Migration Assistant)](https://docs.microsoft.com/sql/dma/dma-overview)를 사용하여 Azure SQL Database에서 데이터베이스 기능에 영향을 주는 잠재적인 호환성 문제를 검색합니다. DMA는 아직 관리형 인스턴스를 마이그레이션 대상으로 지원하지 않지만, Azure SQL Database에 대한 평가를 실행하고 제품 설명서에 대해 보고된 기능 패리티 및 호환성 문제 목록을 신중하게 검토하는 것이 좋습니다. Azure SQL Database로 마이그레이션하지 못하도록 차단하는 대부분의 문제는 관리형 인스턴스를 통해 제거되었으므로 관리되는 인스턴스에서 차단하지 않는 일부 보고된 차단 문제가 있는지 확인하려면 [Azure SQL Database 기능](sql-database-features.md)을 참조하세요. 예를 들어 데이터베이스 간 쿼리, 동일한 인스턴스 내의 데이터베이스 간 트랜잭션, 다른 SQL 원본에 연결된 서버, CLR, 글로벌 임시 테이블, 인스턴스 수준 보기, Service Broker 등과 같은 기능은 관리되는 인스턴스에서 사용할 수 있습니다.

관리되는 인스턴스 배포 옵션에서 제거되지 않은 일부 보고된 차단 문제가 있는 경우 [Azure 가상 머신의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)와 같은 대체 옵션을 고려해야 합니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.

- 운영 체제 또는 파일 시스템에 직접 액세스해야 하는 경우(예: SQL Server가 있는 동일한 가상 머신에 타사 또는 사용자 지정 에이전트를 설치하는 경우)
- FileStream/FileTable, PolyBase 및 인스턴스 간 트랜잭션과 같이 아직 지원되지 않는 기능에 대한 엄격한 종속성이 있는 경우
- SQL Server의 특정 버전(예: 2012)을 유지해야 하는 경우
- 컴퓨팅 요구 사항이 관리되는 인스턴스에서 제공하는 것보다 훨씬 낮고(예: 하나의 vCore) 데이터베이스 통합이 허용되지 않는 옵션인 경우

확인된 모든 마이그레이션 차단기를 해결하고 관리형 인스턴스로 마이그레이션을 계속한 경우 일부 변경 사항이 워크로드의 성능에 영향을 줄 수 있습니다.
- 필수 전체 복구 모델과 정기적인 자동 백업 일정은 단순/대량 로깅 모델을 주기적으로 사용했거나 요청 시 백업을 중지한 경우 워크로드 또는 유지 관리/ETL 작업의 성능에 영향을 미칠 수 있습니다.
- 추적 플래그 또는 호환성 수준과 같은 다양한 서버 또는 데이터베이스 수준 구성
- 투명 데이터베이스 암호화(TDE) 또는 자동 장애 조치 그룹과 같은 새로운 기능은 CPU 및 IO 사용에 영향을 미칠 수 있습니다.

관리형 인스턴스는 중요한 시나리오에서도 99.99%의 가용성을 보장하므로 이러한 기능으로 인한 오버헤드를 비활성화할 수 없습니다. 자세한 내용은 [SQL Server 및 관리되는 인스턴스에서 성능이 다를 수 있는 근본 원인을](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)참조하세요.

### <a name="create-performance-baseline"></a>성능 기준 만들기

관리되는 인스턴스에서 워크로드의 성능을 SQL Server에서 실행 중인 원래 워크로드와 비교하는 데 사용할 성능 기준을 만들어야 합니다. 

성능 기준은 평균/최대 CPU 사용량, 평균/최대 디스크 IO 대기 시간, 처리량, IOPS, 평균/최대 페이지 수명, tempdb의 평균 최대 크기와 같은 매개 변수 집합입니다. 마이그레이션 후 유사하거나 더 나은 매개 변수를 사용하려고 하므로 이러한 매개 변수에 대한 기준 값을 측정하고 기록하는 것이 중요합니다. 시스템 매개 변수 외에도 워크로드에서 대표 쿼리 집합 또는 가장 중요한 쿼리 집합을 선택하고 선택한 쿼리에 대한 최소/평균/최대 기간, CPU 사용량을 측정해야 합니다. 이러한 값을 사용하면 관리형 인스턴스에서 실행되는 워크로드의 성능을 원본 SQL Server의 원래 값과 비교할 수 있습니다.

SQL Server 인스턴스에서 측정해야 하는 매개 변수는 다음과 같습니다. 
- [SQL Server 인스턴스에서 CPU 사용량을 모니터링하고](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) 평균 및 최대 CPU 사용량을 기록합니다.
- [SQL Server 인스턴스에서 메모리 사용량을 모니터링하고](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) 버퍼 풀, 계획 캐시, 열 저장소 풀, [메모리 내 OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)등과 같은 다른 구성 요소에서 사용되는 메모리 양을 결정합니다. 또한 페이지 기대 수명 메모리 성능 카운터의 평균 및 최대 값을 찾아야 합니다.
- [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 보기 또는 성능 카운터를 사용하여 원본 SQL Server 인스턴스에서 디스크 IO [사용량을 모니터링합니다.](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)
- SQL Server 2016+ 버전에서 마이그레이션하는 경우 동적 관리 보기 또는 쿼리 저장소를 검사하여 워크로드 및 쿼리 성능 또는 SQL Server 인스턴스를 모니터링합니다. 워크로드에서 가장 중요한 쿼리의 평균 기간 및 CPU 사용량을 식별하여 관리되는 인스턴스에서 실행 중인 쿼리와 비교합니다.

> [!Note]
> 높은 CPU 사용량, 일정한 메모리 압력, tempdb 또는 매개 변수화 문제와 같은 SQL Server의 워크로드에 문제가 있는 경우 기준 및 마이그레이션을 수행하기 전에 원본 SQL Server 인스턴스에서 이를 해결해야 합니다. 알고 있는 문제를 새 시스템 migh로 마이그레이션하면 예기치 않은 결과가 발생하고 성능 비교가 무효화됩니다.

이 작업의 결과로 소스 시스템의 CPU, 메모리 및 IO 사용량에 대한 평균 및 피크 값뿐만 아니라 워크로드에서 가장 중요한 쿼리의 평균 및 최대 지속 시간 및 CPU 사용량을 문서화해야 합니다. 나중에 이러한 값을 사용하여 관리되는 인스턴스에서 워크로드의 성능을 원본 SQL Server에서 워크로드의 기본 성능과 비교해야 합니다.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>최적 크기의 관리되는 인스턴스에 배포

관리되는 인스턴스는 클라우드로 이동할 온-프레미스 워크로드에 맞게 조정됩니다. 작업에 적합한 수준의 리소스를 선택할 때 유연성이 높은 [새 구매 모델](sql-database-service-tiers-vcore.md)이 도입되었습니다. 온-프레미스 환경에서는 실제 코어 및 IO 대역폭을 사용하여 이러한 작업의 크기를 조정하는 데 익숙할 것입니다. 관리되는 인스턴스에 대한 구매 모델은 가상 코어 수 또는 "vCore 수"를 기반으로 하며, 추가 스토리지 및 IO를 별도로 사용할 수 있습니다. vCore 모델은 현재 온-프레미스에서 사용하는 제품과 비교하여 클라우드의 컴퓨팅 요구 사항을 더 쉽게 이해할 수 있는 방법입니다. 새로운 이 모델을 사용하면 클라우드에서 대상 환경의 크기를 올바르게 조정할 수 있습니다. 올바른 서비스 계층과 특성을 선택하는 데 도움이 될 수 있는 몇 가지 일반적인 지침은 다음과 같습니다.
- 기준 CPU 사용량에 따라 SQL Server에서 사용 중인 코어 수와 일치하는 관리형 인스턴스를 프로비전할 수 있으며, [관리형 인스턴스가 설치된 VM 특성과](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)일치하도록 CPU 특성을 확장해야 할 수 있습니다.
- 기준 메모리 사용량에 따라 [일치하는 메모리가 있는 서비스 계층을](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)선택합니다. 메모리 양을 직접 선택할 수 없으므로 일치하는 메모리가 있는 vCore의 양(예: Gen5의 5.1GB/vCore)을 사용하여 관리되는 인스턴스를 선택해야 합니다. 
- 파일 하위 시스템의 기본 IO 대기 시간에 따라 범용(5ms보다 큰 대기 시간)과 비즈니스 중요 서비스 계층(대기 시간이 3ms 미만)을 선택합니다.
- 기준 처리량에 따라 데이터 또는 로그 파일의 크기를 미리 할당하여 예상되는 IO 성능을 얻을 수 있습니다.

배포 시 계산 및 저장소 리소스를 선택한 다음 [Azure 포털을](sql-database-scale-resources.md)사용하여 응용 프로그램에 대한 가동 중지 시간을 도입하지 않고 나중에 변경할 수 있습니다.

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

[Azure Storage](https://azure.microsoft.com/services/storage/)에서 사용 가능한 온-프레미스 SQL Server 또는 [Virtual Machines의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)에서 가져온 네이티브 백업(.bak 파일)의 복원은 SQL DB 관리되는 인스턴스 배포 옵션의 주요 기능 중 하나이며, 오프라인 데이터베이스 마이그레이션을 빠르고 쉽게 수행할 수 있도록 합니다.

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

관리형 인스턴스로의 마이그레이션을 완료한 후에는 워크로드의 응용 프로그램 동작과 성능을 추적해야 합니다. 이 프로세스에는 다음 활동이 포함됩니다.
- [관리되는 인스턴스에서 실행되는 워크로드의 성능을](#compare-performance-with-the-baseline) [원본 SQL Server에서 만든 성능 기준과](#create-performance-baseline)비교합니다.
- 워크로드의 성능을 지속적으로 모니터링하여 잠재적인 문제 및 개선 사항을 [식별합니다.](#monitor-performance)

### <a name="compare-performance-with-the-baseline"></a>성능과 기준 비교

성공적인 마이그레이션 후 즉시 수행해야 하는 첫 번째 작업은 워크로드의 성능을 기본 워크로드 성능과 비교하는 것입니다. 이 활동의 목표는 관리되는 인스턴스의 워크로드 성능이 요구 사항을 충족하는지 확인하는 것입니다. 

관리되는 인스턴스로 의 데이터베이스 마이그레이션은 대부분의 경우 데이터베이스 설정과 원래 호환성 수준을 유지합니다. 원본 설정은 원본 SQL Server에 비해 일부 성능 저하의 위험을 줄이기 위해 가능한 경우 보존됩니다. 마이그레이션 전에 사용자 데이터베이스의 호환성 수준이 100 이상인 경우 마이그레이션 후에도 동일하게 유지됩니다. 마이그레이션 전에 사용자 데이터베이스의 호환성 수준이 90인 경우 업그레이드된 데이터베이스에서 호환성 수준은 관리되는 인스턴스에서 지원되는 가장 낮은 호환성 수준인 100으로 설정됩니다. 시스템 데이터베이스의 호환성 수준은 140입니다. 관리형 인스턴스로의 마이그레이션은 실제로 최신 버전의 SQL Server Database Engine으로 마이그레이션되므로 놀라운 성능 문제를 피하기 위해 워크로드의 성능을 다시 테스트해야 합니다.

필수 구성 조건으로 다음 활동을 완료했는지 확인합니다.
- 다양한 인스턴스, 데이터베이스, temdb 설정 및 구성을 조사하여 관리형 인스턴스의 설정을 원본 SQL Server 인스턴스의 설정과 정렬합니다. 첫 번째 성능 비교를 실행하기 전에 호환성 수준 또는 암호화와 같은 설정을 변경하지 않았는지 확인하거나 활성화한 새 기능 중 일부가 일부 쿼리에 영향을 줄 수 있는 위험을 감수해야 합니다. 마이그레이션 위험을 줄이려면 성능 모니터링 후에만 데이터베이스 호환성 수준을 변경합니다.
- 더 나은 성능을 얻기 위해 파일 크기를 미리 할당하는 것과 같은 [범용](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) 저장소 모범 사례 지침을 구현합니다.
- [관리형 인스턴스와 SQL Server 간의 성능 차이를 유발할 수 있는 주요 환경 차이점에]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) 대해 알아보고 성능에 영향을 줄 수 있는 위험을 식별합니다.
- 관리되는 인스턴스에서 쿼리 저장소 및 자동 조정을 사용하도록 설정했는지 확인합니다. 이러한 기능을 사용하면 워크로드 성능을 측정하고 잠재적인 성능 문제를 자동으로 해결할 수 있습니다. [최신 SQL Server 버전으로 업그레이드하는 동안 성능 안정성을 유지하는 방법에](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)대해 설명한 대로 쿼리 저장소를 데이터베이스 호환성 수준 변경 전후의 워크로드 성능에 대한 정보를 얻기 위한 최적의 도구로 사용하는 방법을 알아봅니다.
온-프레미스 환경과 가능한 한 비슷한 환경을 준비한 후에는 워크로드 실행을 시작하고 성능을 측정할 수 있습니다. 측정 프로세스에는 원본 SQL Server 에서 [워크로드 측정값의 기준 성능을 만드는 동안](#create-performance-baseline)측정한 것과 동일한 매개 변수가 포함되어야 합니다.
따라서 성능 매개 변수를 기준과 비교하고 중요한 차이점을 식별해야 합니다.

> [!NOTE]
> 대부분의 경우 관리되는 인스턴스 및 SQL Server에서 정확히 일치하는 성능을 얻을 수 없습니다. 관리되는 인스턴스는 SQL Server 데이터베이스 엔진이지만 관리되는 인스턴스의 인프라 및 고가용성 구성에는 약간의 차이가 있을 수 있습니다. 일부 쿼리는 더 빨라지고 다른 쿼리는 느릴 수 있습니다. 비교의 목표는 관리형 인스턴스의 워크로드 성능이 SQL Server의 성능(평균)과 일치하는지 확인하고 원래 성능과 일치하지 않는 성능에 중요한 쿼리가 있는지 확인하는 것입니다.

성능 비교의 결과는 다음과 같은 것일 수 있습니다.
- 관리되는 인스턴스의 워크로드 성능이 정렬되거나 SQL Server의 워크로드 성능보다 좋습니다. 이 경우 마이그레이션이 성공적임을 성공적으로 확인했습니다.
- 성능 매개 변수와 워크로드의 쿼리의 대부분은 성능저하를 제외한 일부 예외를 제외하고 정상적으로 작동합니다. 이 경우 차이점과 그 중요성을 식별해야 합니다. 성능이 저하된 몇 가지 중요한 쿼리가 있는 경우 기본 SQL 계획이 변경되었거나 쿼리가 일부 리소스 제한에 부딪히는지 조사해야 합니다. 이 경우 완화는 직접 또는 계획 가이드를 사용하여 중요한 쿼리(예: 변경된 호환성 수준, 레거시 카디널리티 추정기)에 몇 가지 힌트를 적용하거나 계획에 영향을 줄 수 있는 통계 및 인덱스를 작성할 수 있습니다. 
- 대부분의 쿼리는 원본 SQL Server에 비해 관리되는 인스턴스에서 느립니다. 이 경우 IO 제한, 메모리 제한, 인스턴스 로그 속도 제한 등과 같은 [일부 리소스 제한에 도달하는]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) 것과 같은 차이의 근본 원인을 식별하십시오. 차이를 유발할 수 있는 리소스 제한이 없는 경우 데이터베이스의 호환성 수준을 변경하거나 레거시 카디널리티 추정과 같은 데이터베이스 설정을 변경하고 테스트를 다시 시작하십시오. 관리되는 인스턴스 또는 쿼리 저장소 보기에서 제공하는 권장 사항을 검토하여 성능을 회귀한 쿼리를 식별합니다.

> [!IMPORTANT]
> 관리형 인스턴스에는 기본적으로 활성화된 자동 계획 수정 기능이 내장되어 있습니다. 이 기능을 사용하면 붙여넣기에서 잘 작동한 쿼리가 나중에 성능이 저하되지 않습니다. 관리되는 인스턴스에서 기준 성능 및 계획에 대해 알아볼 수 있도록 활성화하기 위해 새 설정을 변경하기 전에 이 기능이 활성화되어 있고 이전 설정으로 워크로드를 충분히 오래 실행했는지 확인합니다.

필요에 맞는 워크로드 성능을 얻을 때까지 매개 변수를 변경하거나 서비스 계층을 업그레이드하여 최적의 구성으로 수렴합니다.

### <a name="monitor-performance"></a>성능 모니터링

관리형 인스턴스는 모니터링 및 문제 해결을 위한 많은 고급 도구를 제공하며 이를 사용하여 인스턴스의 성능을 모니터링해야 합니다. 모니터링해야 하는 매개 변수 중 일부는 다음과 같습니다.
- 인스턴스에서 CPU 사용량을 결정하여 프로비저닝한 vCore의 수가 워크로드에 적합한 일치를 수행합니다.
- 관리되는 인스턴스의 페이지 수명이 [추가 메모리가 필요한지](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)확인합니다.
- `INSTANCE_LOG_GOVERNOR` 더 나은 IO 성능을 얻기 위해 파일을 미리 할당해야 할 수도 있는 범용 계층에서 스토리지 IO 문제가 있는지 를 알 수 `PAGEIOLATCH` 있습니다.

## <a name="leverage-advanced-paas-features"></a>고급 PaaS 기능 활용

완전히 관리되는 플랫폼에 있고 워크로드 성능이 SQL Server 워크로드 성능과 일치한다는 것을 확인한 후에는 SQL Database 서비스의 일부로 자동으로 제공되는 이점을 활용할 수 있습니다. 

마이그레이션 하는 동안 관리되는 인스턴스에서 일부 변경 하지 않는 경우에 최신 데이터베이스 엔진 향상을 활용 하기 위해 인스턴스를 운영 하는 동안 일부 새로운 기능을 설정 하는 높은 기회가 있다. 일부 변경 사항은 데이터베이스 호환성 수준이 변경된 후에만 [활성화됩니다.](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)


예를 들어 서비스에서 백업을 자동으로 수행하므로 관리되는 인스턴스에 백업을 만들 필요가 없습니다. 백업 예약, 가져오기 및 관리에 대해 더 이상 걱정할 필요가 없습니다. 관리되는 인스턴스는 [PITR(지정 시간 복구)](sql-database-recovery-using-backups.md#point-in-time-restore)을 사용하여 이 보존 기간 내의 특정 시점으로 복원하는 기능을 제공합니다. 또한 [고가용성](sql-database-high-availability.md)이 기본 제공되므로 고가용성 설정에 대해 걱정할 필요가 없습니다.

보안을 강화하려면 [Azure Active Directory 인증,](sql-database-security-overview.md) [감사,](sql-database-managed-instance-auditing.md) [위협 검색,](sql-database-advanced-data-security.md) [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)및 동적 [데이터 마스킹)을](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) 사용하는 것이 좋습니다.

관리되는 인스턴스는 고급 관리 및 보안 기능 외에도 [워크로드를 모니터링하고 조정하는](sql-database-monitor-tune-overview.md)데 도움이 되는 고급 도구 집합을 제공합니다. [Azure SQL 분석을](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) 사용하면 관리되는 인스턴스의 큰 집합을 모니터링하고 많은 수의 인스턴스 및 데이터베이스의 모니터링을 중앙 집중화할 수 있습니다. 관리되는 인스턴스의 [자동 조정은](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) SQL 계획 실행 통계의 성능을 지속적으로 모니터링하고 식별된 성능 문제를 자동으로 해결합니다.

## <a name="next-steps"></a>다음 단계

- 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스란?](sql-database-managed-instance.md)을 참조하세요.
- 백업에서 복원을 포함한 자습서는 [관리되는 인스턴스 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- DMS를 사용한 마이그레이션을 보여 주는 자습서는 [DMS를 사용하여 온-프레미스 데이터베이스를 관리되는 인스턴스로 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.  
