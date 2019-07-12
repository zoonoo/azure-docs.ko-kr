---
title: Azure SQL Database 관리 되는 인스턴스를 SQL Server 인스턴스에서 데이터베이스 마이그레이션 | Microsoft Docs
description: Azure SQL Database 관리 되는 인스턴스를 SQL Server 인스턴스에서 데이터베이스를 마이그레이션하는 방법에 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 11/07/2019
ms.openlocfilehash: 7cf54b79fac87905117e321574571890c59315e6
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827072"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>SQL Server 인스턴스를 Azure SQL Database 관리형 인스턴스로 마이그레이션

이 문서에서는 SQL Server 2005 이상 버전 인스턴스를 [Azure SQL Database 관리형 인스턴스](sql-database-managed-instance.md)로 마이그레이션하는 방법에 대해 설명합니다. 단일 데이터베이스 또는 탄력적 풀로 마이그레이션하는 방법에 대한 자세한 내용은 [단일 또는 풀링된 데이터베이스로 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요. 다른 플랫폼에서 마이그레이션하는 방법에 대한 마이그레이션 정보는 [Azure Database 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조하세요.

> [!NOTE]
> 이동 하려는 빠르게 시작 하 고 관리 되는 인스턴스를 시도 하려는 경우 [빠른 시작 가이드](/sql-database-managed-instance-quickstart-guide.md) 이 페이지를 대신 합니다. 

높은 수준에서 데이터베이스 마이그레이션 프로세스는 다음과 같습니다.

![마이그레이션 프로세스](./media/sql-database-managed-instance-migration/migration-process.png)

- [관리 되는 인스턴스 호환성 평가](#assess-managed-instance-compatibility) 마이그레이션을 방해할 수 있는 차단 문제가 발생 하지는 확인 해야 합니다.
  - 이 단계는 또한 복사본을 만들기 전에만 [성능 기준선](#create-performance-baseline) 원본 SQL Server 인스턴스에서 리소스 사용량을 확인 하 합니다. O 하려는 경우이 단계는 필요 합니다. 올바른 크기의 관리 되는 인스턴스를 배포 하 고 마이그레이션 후 성능 영향을 받지 않습니다 확인 합니다.
- [앱 연결 옵션을 선택 합니다.](sql-database-managed-instance-connect-app.md)
- [최적 크기의 관리 되는 인스턴스에 배포](#deploy-to-an-optimally-sized-managed-instance) 기술 특성 (Vcore 수, 메모리의 양) 및 관리 되는 인스턴스의 성능 계층 (업무용, 범용)을 선택할가 있습니다.
- [마이그레이션 방법 선택 및 마이그레이션](#select-migration-method-and-migrate) 오프 라인 마이그레이션 (기본 백업/복원, 데이터베이스 importe 내보낼) 또는 온라인 마이그레이션 (데이터 마이그레이션 서비스, 트랜잭션 복제)를 사용 하 여 데이터베이스를 마이그레이션하면 됩니다.
- [응용 프로그램 모니터링](#monitor-applications) 예상 성능 되도록 합니다.

> [!NOTE]
> 개별 데이터베이스를 단일 데이터베이스 또는 탄력적 풀로 마이그레이션하려면 [Azure SQL Database로 SQL Server 데이터베이스 마이그레이션](sql-database-single-database-migrate.md)을 참조하세요.

## <a name="assess-managed-instance-compatibility"></a>관리되는 인스턴스 호환성 평가

먼저 관리되는 인스턴스가 애플리케이션의 데이터베이스 요구 사항과 호환되는지 확인합니다. 관리되는 인스턴스 배포 옵션은 온-프레미스 또는 가상 머신에서 SQL Server를 사용하는 대부분의 기존 애플리케이션에서 리프트 앤 시프트 방식으로 쉽게 마이그레이션할 수 있도록 설계되었습니다. 그러나 경우에 따라 아직 지원되지 않는 기능이 필요할 수 있으며 해결 방법을 구현하는 데 드는 비용이 너무 높습니다.

[DMA(Data Migration Assistant)](https://docs.microsoft.com/sql/dma/dma-overview)를 사용하여 Azure SQL Database에서 데이터베이스 기능에 영향을 주는 잠재적인 호환성 문제를 검색합니다. DMA는 아직 관리형 인스턴스를 마이그레이션 대상으로 지원하지 않지만, Azure SQL Database에 대한 평가를 실행하고 제품 설명서에 대해 보고된 기능 패리티 및 호환성 문제 목록을 신중하게 검토하는 것이 좋습니다. Azure SQL Database로 마이그레이션하지 못하도록 차단하는 대부분의 문제는 관리형 인스턴스를 통해 제거되었으므로 관리되는 인스턴스에서 차단하지 않는 일부 보고된 차단 문제가 있는지 확인하려면 [Azure SQL Database 기능](sql-database-features.md)을 참조하세요. 예를 들어 데이터베이스 간 쿼리, 동일한 인스턴스 내의 데이터베이스 간 트랜잭션, 다른 SQL 원본에 연결된 서버, CLR, 글로벌 임시 테이블, 인스턴스 수준 보기, Service Broker 등과 같은 기능은 관리되는 인스턴스에서 사용할 수 있습니다.

관리되는 인스턴스 배포 옵션에서 제거되지 않은 일부 보고된 차단 문제가 있는 경우 [Azure 가상 머신의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)와 같은 대체 옵션을 고려해야 합니다. 다음은 몇 가지 예입니다.

- 운영 체제 또는 파일 시스템에 직접 액세스해야 하는 경우(예: SQL Server가 있는 동일한 가상 머신에 타사 또는 사용자 지정 에이전트를 설치하는 경우)
- FileStream/FileTable, PolyBase 및 인스턴스 간 트랜잭션과 같이 아직 지원되지 않는 기능에 대한 엄격한 종속성이 있는 경우
- 하는 경우 반드시 SQL Server의 특정 버전을 유지 해야 (2012, 예를 들어).
- 컴퓨팅 요구 사항이 관리되는 인스턴스에서 제공하는 것보다 훨씬 낮고(예: 하나의 vCore) 데이터베이스 통합이 허용되지 않는 옵션인 경우

마이그레이션 블 로커를 식별 하 고 이때 관리 되는 인스턴스로 마이그레이션을 계속 모든 해결 했으면 워크 로드 성능에 영향을 줄 수 일부 변경 합니다.
- 필수 전체 복구 모델 및 자동화 된 백업 일정 영향을 줄 수에 워크 로드 또는 유지 관리/ETL 작업의 성능을 주기적으로 간단한/대량 로그 모델을 사용 하거나 요청 시 백업을 중지 하는 경우.
- 다른 서버 또는 데이터베이스 수준 구성 호환성 수준 추적 플래그 등
- Transparent Database Encryption (TDE) 또는 자동 장애 조치 그룹과 같은 사용 중인 새로운 기능에는 CPU 및 IO 사용량을 저하 될 수 있습니다.

이러한 기능으로 인 한 오버 헤드를 비활성화할 수 없습니다 있도록 중요 한 경우에도 인스턴스 보장 99.99% 가용성을 관리 합니다. 자세한 내용은 [SQL Server 및 인스턴스 관리에서 다양 한 성능을 일으킬 수 있는 근본 원인](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)합니다.

### <a name="create-performance-baseline"></a>성능 기준 만들기

관리 되는 인스턴스에서 원래 SQL Server에서 실행 중인 워크 로드를 사용 하 여 워크 로드의 성능을 비교 하는 경우 비교에 사용할 수 있는 성능 기준선을 만드는 해야 합니다. 

성능 기준선 평균/최대 CPU 사용량, 평균/최대 디스크 IO 대기 시간, 처리량와 같은 매개 변수 집합이 IOPS, 평균/최대 페이지 수명 예상 평균 tempdb의 최대 크기입니다. 측정 하 고 이러한 매개 변수에 대 한 기준선 값을 기록 하는 일을 해야 하므로 마이그레이션 후 유사 하거나 더 나은 매개 변수가 하려고 합니다. 시스템 매개 변수 외에도 사용자 워크 로드 및 측정값 최소/평균/최대 기간, 선택한 쿼리에 대 한 CPU 사용량의에서 대표 쿼리 또는 가장 중요 한 쿼리 집합을 선택 해야 합니다. 이러한 값은 원본 SQL Server에서 관리 되는 인스턴스에서 원래 값을 실행 하는 워크 로드의 성능을 비교할 수 있습니다.

SQL Server 인스턴스에서 측정 해야 하는 매개 변수는 다음과 같습니다. 
- [SQL Server 인스턴스에서 CPU 사용량 모니터링](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) 기록 평균 및 최대 CPU 사용량입니다.
- [SQL Server 인스턴스에서 메모리 사용량을 모니터링](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) 버퍼 풀과 같은 다른 구성 요소에서 사용 되는 메모리의 양을 결정 및 계획 캐시, 열-저장소 풀 [in-memory OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)등입니다. 또한 페이지 수명 예상 메모리 성능 카운터의 평균 및 최대 값을 찾아야 합니다.
- 디스크 IO 사용량을 사용 하 여 원본 SQL Server 인스턴스 모니터링 [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 보기 또는 [성능 카운터](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)합니다.
- SQL Server 2016 이상 버전에서 마이그레이션하는 경우 동적 관리 뷰 또는 쿼리 저장소를 검사 하 여 워크 로드 및 쿼리 성능 또는 SQL Server 인스턴스를 모니터링 합니다. 평균 지속 시간 및 CPU 사용량을 관리 되는 인스턴스에서 실행 하는 쿼리를 사용 하 여 비교할 워크 로드에서 가장 중요 한 쿼리를 식별 합니다.

> [!Note]
> 높은 CPU 사용량, 상수 메모리 부족, tempdb 또는 parametrization 문제 등 SQL server 워크 로드를 사용 하 여 모든 문제를 발견 하는 경우에 초기 계획 및 마이그레이션을 수행 하기 전에 원본 SQL Server 인스턴스에서 해결 하려고 해야 합니다. 모든 새 시스템 migh 문제점 예상치 못한 결과가 발생할 하며 모든 성능 비교를 무효화 알고 마이그레이션.

이 작업의 결과와 해야 문서화 평균 CPU, 메모리 및 IO 사용량 원본 시스템에 대 한 최대 값 뿐만 아니라 평균 및 최대 기간 및 CPU 사용량의 지배적인 및 가장 중요 한 쿼리 워크 로드에서. 원본 SQL Server에서 작업의 기본 성능을 사용 하 여 관리 되는 인스턴스에서 워크 로드의 성능을 비교할 나중에 이러한 값을 사용 해야 합니다.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>최적 크기의 관리되는 인스턴스에 배포

관리되는 인스턴스는 클라우드로 이동할 온-프레미스 워크로드에 맞게 조정됩니다. 작업에 적합한 수준의 리소스를 선택할 때 유연성이 높은 [새 구매 모델](sql-database-service-tiers-vcore.md)이 도입되었습니다. 온-프레미스 환경에서는 실제 코어 및 IO 대역폭을 사용하여 이러한 작업의 크기를 조정하는 데 익숙할 것입니다. 관리되는 인스턴스에 대한 구매 모델은 가상 코어 수 또는 "vCore 수"를 기반으로 하며, 추가 스토리지 및 IO를 별도로 사용할 수 있습니다. vCore 모델은 현재 온-프레미스에서 사용하는 제품과 비교하여 클라우드의 컴퓨팅 요구 사항을 더 쉽게 이해할 수 있는 방법입니다. 새로운 이 모델을 사용하면 클라우드에서 대상 환경의 크기를 올바르게 조정할 수 있습니다. 다음은 적절 한 서비스 계층 및 특성을 선택할 수 있는 몇 가지 일반적인 지침에 대 한 설명입니다.
- SQL Server에서 사용 하 고 있는 코어 수와 일치 하는 관리 되는 인스턴스를 프로 비전 할 수는 CPU 사용량 기준에 따라 해당 CPU 특징이 포함 해야 할 수에 맞게 크기가 조정 될 [VM 특성은 관리 되는 인스턴스가 있는 설치](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)합니다.
- 초기 메모리 사용량을 기준으로 선택할 [일치 하는 메모리가 있는 서비스 계층](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)합니다. 일치 하는 메모리 (예를 들어 5.1 GB/vCore Gen5에서)에 vcore 크기를 사용 하 여 관리 되는 인스턴스를 선택 해야 하므로 메모리의 양은 직접 선택할 수 없습니다. 
- 기반 IO 기준 파일 하위 시스템의 대기 시간 (대기 시간이 5ms 보다 큰) 범용 및 중요 비즈니스용 서비스 계층 간의 선택 (대기 시간이 3 밀리초 미만).
- 초기 처리량에 따라 데이터의 크기를 미리 할당 또는 로그 파일을 하는 IO 성능이 필요 합니다.

계산을 선택할 수 있습니다 및 저장소 리소스 배포 시 시간 및 사용 하 여 응용 프로그램에 대 한 가동 중지 시간을 도입 하지 않고 나중에 변경 된 [Azure portal](sql-database-scale-resources.md):

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

관리 되는 인스턴스로의 마이그레이션을 완료 한 후 작업의 성능을 확인 하 고 응용 프로그램 동작을 추적 해야 합니다. 이 프로세스는 다음과 같은 작업을 포함합니다.
- [관리 되는 인스턴스에서 실행 하는 작업의 성능을 비교](#compare-performance-with-the-baseline) 사용 하 여 합니다 [원본 SQL Server에서 만든 성능 기준선](#create-performance-baseline)합니다.
- 계속 해 서 [워크 로드의 성능을 모니터링](#monitor-performance) 잠재적인 문제 및 향상을 식별 합니다.

### <a name="compare-performance-with-the-baseline"></a>성능 기준선과 비교

마이그레이션을 완료 한 후 즉시 수행 해야 하는 첫 번째 활동 초기 워크 로드 성능 워크 로드의 성능을 비교 하는 것입니다. 관리 되는 인스턴스에서 워크 로드 성능 요구 사항을 충족 하는지 확인 하려면이 작업의 목표가입니다. 

관리 되는 인스턴스에 데이터베이스 마이그레이션 대부분의 경우 데이터베이스 설정 및 원래의 호환성 수준을 유지합니다. 원본 SQL Server에 비해 몇 가지 성능 저하의 위험을 줄이기 위해 가능한 경우 원래 설정은 유지 됩니다. 마이그레이션 전에 사용자 데이터베이스의 호환성 수준이 100 이상인 경우 마이그레이션 후에도 동일하게 유지됩니다. 마이그레이션 전에 사용자 데이터베이스의 호환성 수준이 90인 경우 업그레이드된 데이터베이스에서 호환성 수준은 관리되는 인스턴스에서 지원되는 가장 낮은 호환성 수준인 100으로 설정됩니다. 시스템 데이터베이스의 호환성 수준은 140입니다. 최신 버전의 SQL Server 데이터베이스 엔진으로 관리 되는 인스턴스로 마이그레이션 마이그레이션한 실제로 이후 다시 몇 가지 놀라운 성능 문제를 방지 하려면 워크 로드의 성능을 테스트 해야 해야 합니다.

전제 조건으로 다음 작업을 완료 했는지 확인 합니다.
- 다양 한 인스턴스, 데이터베이스, temdb 설정 및 구성을 조사 하 여 원본 SQL Server 인스턴스 설정으로 관리 되는 인스턴스에서 설정을 맞춥니다. 첫 번째 성능 비교를 실행 하기 전에 호환성 수준 또는 암호화와 같은 설정이 변경 되지 않은 있는지 확인 하거나 일부 쿼리에 영향을 수 있습니다 사용 하도록 설정 하는 새로운 기능 중 일부는 위험을 감수 합니다. 마이그레이션 위험을 줄이려면 성능 모니터링 후에만 데이터베이스 호환성 수준을 변경합니다.
- 구현 [범용에 대 한 모범 사례 지침으로 저장소](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) 같은 더 나은 성능을 얻으려면 파일의 크기를 미리 할당 합니다.
- 에 대 한 자세한 합니다 [관리 되는 인스턴스 및 SQL Server 간의 성능 차이 일으킬 수 있는 환경 주요]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) 하 고 성능에 영향을 줄 수 있는 위험을 식별 합니다.
- 쿼리 저장소를 사용 하도록 설정된 하 고 관리 되는 인스턴스에서 자동 튜닝을 유지 하 고 있는지 확인 합니다. 이러한 기능을 사용 하 여 워크 로드 성능을 측정 하 고 잠재적 성능 문제를 자동으로 해결할 수 있습니다. 에 설명 된 대로 최적의 도구로 쿼리 저장소를 데이터베이스 호환성 수준 변경 전후의 작업 성능에 대 한 정보를 가져오기 위한 사용 하는 방법을 알아봅니다 [최신SQLServer버전으로업그레이드하는동안성능안정성유지](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
비교할 수 있는 환경을 준비한 후 온-프레미스 환경과 최대한 워크 로드를 실행을 시작 성능을 측정 합니다. 측정 프로세스를 측정 하는 동일한 매개 변수를 포함할지 [원본 SQL Server에서 기준 성능에 작업 측정값을 만드는 동안](#create-performance-baseline)합니다.
결과적으로 성능 매개 변수를 기준으로 비교 하 고 중요 한 차이 식별 해야 합니다.

> [!NOTE]
> 대부분의 경우에서에 관리 되는 인스턴스 및 SQL Server에 정확히 일치 하는 성능을 얻을 수 없습니다. 관리 되는 인스턴스는 SQL Server 데이터베이스 엔진 이지만 인프라 및 관리 되는 인스턴스에서 고가용성 구성을 일부 차이점이 발생할 수 있습니다. 다른 속도가 느려질 수 있습니다 하는 동안 일부 쿼리는 빠르게 수를 예상할 수 있습니다. 비교의 목표가 워크 로드 성능 관리 되는 인스턴스의 성능을 SQL server (평균)와 일치 하는지 확인 하 고 식별 하는 원래 성능와 일치 하지 않는 중요 한 성능 쿼리 사항이 있습니다.

성능 비교의 결과 다음과 같습니다.
- 관리 되는 인스턴스에서 워크 로드 성능 맞춰지지 이상을 사용 하는 SQL server 워크 로드 성능. 이 경우 성공적으로 마이그레이션 되었는지 확인 했습니다.
- 대부분의 성능 매개 변수 및 좋지만, 성능 저하를 사용 하 여 몇 가지 예외를 사용 하 여 워크 로드 작업에서 쿼리 됩니다. 이때 차이점 및 중요도 식별 해야 합니다. 조사 해야 몇 가지 중요 한 쿼리 성능 저하 된 경우 기본 SQL 계획 변경 하거나 쿼리는 일부 리소스 제한에 도달 합니다. 이 경우 완화 rebuild 또는 create statistics 및 계획에 영향을 주는 인덱스 계획 지침을 사용 하 여 또는 직접 몇 가지 힌트 하거나 중요 한 쿼리 (예: 변경 된 호환성 수준에서 레거시 카디널리티 추정 기가) 적용할 수 있습니다. 
- 대부분의 쿼리가 원본 SQL Server에 비해 관리 되는 인스턴스에서 느립니다. 이 경우 같은 차이의 근본 원인을 식별 하려고 [일부 리소스 제한에 도달한]( sql-database-managed-instance-resource-limits.md#instance-level-resource-limits) IO 제한, 메모리 제한, 인스턴스 로그 속도 제한 등입니다. 차이 일으키는 리소스 제한이 없는 경우 데이터베이스의 호환성 수준을 변경 하려고 하거나 데이터베이스 설정 변경 같은 레거시 카디널리티 추정 및 테스트를 다시 시작 합니다. 성능 재발 된 쿼리를 식별 하려면 관리 되는 인스턴스 또는 쿼리 저장소 보기를 제공한 권장 사항을 검토 합니다.

> [!IMPORTANT]
> 관리 되는 인스턴스는 기본적으로 사용 되는 기본 제공 자동 계획 수정 기능입니다. 이 기능은 붙여넣기에 정상적으로 작동 하는 쿼리는 나중에 저하 되지 않도록 방지 합니다. 이 기능을 활성화 하 고 기준선 성능 및 계획에 대해 자세히 알아보려면 관리 되는 인스턴스를 사용 하도록 설정 하려면 새 설정을 변경 하기 전에 이전 설정을 사용 하 여 충분 한 시간 워크 로드를 실행 하는지 확인 합니다.

매개 변수의 변경 하거나 필요에 맞는 워크 로드 성능에 도달할 때까지 최적의 구성을 수렴 서비스 계층을 업그레이드 합니다.

### <a name="monitor-performance"></a>성능 모니터링

관리 되는 인스턴스는 다양 한 모니터링 및 문제 해결을 위한 고급 도구를 제공 하 고 인스턴스 성능을 모니터링 하 고 사용 해야 합니다. 일부 매개 변수는 프로그램을 모니터링 해야는:
- 결정할 인스턴스에서 CPU 사용량은 프로 비전 vcore는 워크 로드에 대 한 올바른 일치를 수행 합니다.
- 확인 하려면 관리 되는 인스턴스 페이지 수명 예상 [메모리가 해야](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)합니다.
- 대기 작업 통계와 같은 `INSTANCE_LOG_GOVERNOR` 또는 `PAGEIOLATCH` 할 저장소 IO 문제, 특히 더 나은 IO 성능을 얻기 위해 파일을 미리 할당 해야 하는 일반적인 용도 계층에서 차례로 설명 합니다.

## <a name="leverage-advanced-paas-features"></a>고급 PaaS 기능 활용

완전히 관리 되는 플랫폼에는 워크 로드 성능을 일치 하는 하면 SQL Server 워크 로드 성능을 확인 하 고 SQL Database 서비스의 일부로 자동으로 제공 되는 이점을 활용 합니다. 

마이그레이션하는 동안 관리 되는 인스턴스의 일부 변경 내용을 지정 하지 않는, 경우에 최신 데이터베이스 엔진 향상 기능을 활용 하기 위해 인스턴스를 작동 하는 동안 설정 몇 가지 새로운 기능에는 높은 가능성이 있습니다. 몇 가지 변경만 활성화 되는 [데이터베이스 호환성 수준이 변경 되었습니다](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)합니다.


예를 들어 서비스에서 백업을 자동으로 수행하므로 관리되는 인스턴스에 백업을 만들 필요가 없습니다. 백업 예약, 가져오기 및 관리에 대해 더 이상 걱정할 필요가 없습니다. 관리되는 인스턴스는 [PITR(지정 시간 복구)](sql-database-recovery-using-backups.md#point-in-time-restore)을 사용하여 이 보존 기간 내의 특정 시점으로 복원하는 기능을 제공합니다. 또한 [고가용성](sql-database-high-availability.md)이 기본 제공되므로 고가용성 설정에 대해 걱정할 필요가 없습니다.

보안을 강화 하려면 사용을 고려 [Azure Active Directory 인증](sql-database-security-overview.md)를 [감사](sql-database-managed-instance-auditing.md)합니다 [위협 감지](sql-database-advanced-data-security.md), [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security), 및 [동적 데이터 마스킹](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ).

고급 관리 및 보안 기능을 하는 것 외에도 관리 되는 인스턴스 제공 하는 데 도움이 되는 고급 도구 집합이 [모니터링 및 작업 튜닝](sql-database-monitor-tune-overview.md)합니다. [Azure SQL analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) 다양 한 관리 되는 인스턴스를 모니터링 하 고 많은 수의 인스턴스 및 데이터베이스 모니터링을 중앙 집중화할 수 있습니다. [자동 튜닝](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) 관리 되는 인스턴스에서 SQL 계획 실행 통계의 성능을 지속적으로 모니터링 하 고 자동으로 식별 된 성능 문제를 해결 합니다.

## <a name="next-steps"></a>다음 단계

- 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스란?](sql-database-managed-instance.md)을 참조하세요.
- 백업에서 복원을 포함한 자습서는 [관리되는 인스턴스 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- DMS를 사용한 마이그레이션을 보여 주는 자습서는 [DMS를 사용하여 온-프레미스 데이터베이스를 관리되는 인스턴스로 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.  
