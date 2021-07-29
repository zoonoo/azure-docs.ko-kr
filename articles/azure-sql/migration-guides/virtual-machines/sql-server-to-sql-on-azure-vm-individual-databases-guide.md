---
title: 'SQL Server에서 Azure Virtual Machines의 SQL Server로: 마이그레이션 가이드'
description: 이 가이드에서는 개별 SQL Server 데이터베이스를 Azure Virtual Machines의 SQL Server로 마이그레이션하는 방법을 알아봅니다.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: chadam
ms.date: 03/19/2021
ms.openlocfilehash: 4c6e1f052dd23621e269b59fb573216f68e49cfb
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110784405"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-virtual-machines"></a>마이그레이션 가이드: SQL Server에서 Azure Virtual Machines의 SQL Server로

[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

이 가이드에서는 백업 및 복원 그리고 로그 전달(평가를 위해 [Data Migration Assistant](/sql/dma/dma-overview) 사용)을 사용하여 SQL Server에서 Azure Virtual Machines의 SQL Server 인스턴스로 데이터베이스를 *검색*, *평가* 및 *마이그레이션* 하는 방법을 알아봅니다.

온-프레미스 또는 다음에서 실행 중인 SQL Server를 마이그레이션할 수 있습니다.

- VM(Virtual Machines)의 SQL Server.
- AWS(Amazon Web Services) EC2.
- AWS RDS(Amazon Relational Database Service).
- 컴퓨팅 엔진(Google Cloud Platform [GCP]).

추가 마이그레이션 전략에 대한 정보는 [SQL Server VM 마이그레이션 개요](sql-server-to-sql-on-azure-vm-migration-overview.md)를 참조하세요. 다른 마이그레이션 가이드는 [Azure 데이터베이스 마이그레이션 가이드](/data-migration)를 참조하세요.

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="마이그레이션 프로세스 흐름을 보여주는 다이어그램.":::

## <a name="prerequisites"></a>사전 요구 사항

Azure Virtual Machines에서 SQL Server로 마이그레이션하려면 다음 리소스가 필요합니다.

- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
- [Azure Migrate 프로젝트](../../../migrate/create-manage-projects.md).
- SQL Server 원본과 같거나 더 높은 버전의 준비된 대상 [Azure Virtual Machines의 SQL Server](../../virtual-machines/windows/create-sql-vm-portal.md) 인스턴스.
- [Azure와 온-프레미스 간의 연결](/azure/architecture/reference-architectures/hybrid-networking).
- [적절한 마이그레이션 전략 선택](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>사전 마이그레이션

마이그레이션을 시작하기 전에 SQL 환경의 토폴로지를 검색하고 의도한 마이그레이션의 실행 가능성을 평가해야 합니다.

### <a name="discover"></a>검색

Azure Migrate는 온-프레미스 컴퓨터의 마이그레이션 적합성을 평가하고, 성능 기반 크기 조정을 수행하고, 온-프레미스에서 실행하는 비용 예상을 제공합니다. 마이그레이션을 계획하려면 Azure Migrate를 사용하여 SQL Server 인스턴스가 사용하는 [기존 데이터 원본 및 기능에 대한 세부 정보를 식별](../../../migrate/concepts-assessment-calculation.md)합니다. 이 프로세스에는 사용 중인 버전 및 기능과 함께 조직의 모든 SQL Server 인스턴스를 식별하기 위해 네트워크를 검색하는 작업이 포함됩니다.

> [!IMPORTANT]
> SQL Server 인스턴스에 대한 대상 Azure 가상 머신을 선택할 때 [Azure Virtual Machines의 SQL Server에 대한 성능 지침](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md)을 고려해야 합니다.

더 많은 검색 도구는 데이터 마이그레이션 시나리오에 사용할 수 있는 [서비스 및 도구](../../../dms/dms-tools-matrix.md#business-justification-phase)를 참조하세요.

### <a name="assess"></a>평가

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

모든 데이터 원본을 검색한 후 [데이터 마이그레이션 도우미](/sql/dma/dma-overview)를 사용하여 Azure Virtual Machines의 SQL Server 인스턴스로 마이그레이션하는 온-프레미스 SQL Server 인스턴스를 평가하여 원본 인스턴스와 대상 인스턴스 간의 차이를 파악합니다.

> [!NOTE]
> SQL Server 버전을 업그레이드하지 _않는_ 경우 이 단계를 건너뛰고 [마이그레이션](#migrate) 섹션으로 이동합니다.

#### <a name="assess-user-databases"></a>사용자 데이터베이스 액세스

Data Migration Assistant를 사용하면 새 버전의 SQL Server의 데이터베이스 기능에 영향을 줄 수 있는 호환성 이슈를 감지하여 최신 데이터 플랫폼으로 마이그레이션할 수 있습니다. Data Migration Assistant는 대상 환경을 위한 성능 및 안정성 개선 사항을 권장하며, 또한 원본 서버에서 대상 서버로 스키마, 데이터 및 로그인 개체를 이동할 수 있게 해줍니다.

자세한 내용은 [평가](/sql/dma/dma-migrateonpremsql)를 참조하세요.

> [!IMPORTANT]
>평가 유형에 따라 원본 SQL Server에 필요한 권한이 다를 수 있습니다.
   > - *기능 패리티* 관리자의 경우, 원본 SQL Server 데이터베이스에 연결하기 위해 제공된 자격 증명은 *sysadmin* 서버 역할의 구성원이어야 합니다.
   > - *호환성 문제* 관리자의 경우, 제공된 자격 증명에 최소한 `CONNECT SQL`, `VIEW SERVER STATE` 및 `VIEW ANY DEFINITION` 권한이 있어야 합니다.
   > - Data Migration Assistant는 평가를 실행하기 전에 선택한 관리자에 필요한 사용 권한을 강조 표시합니다.

#### <a name="assess-the-applications"></a>애플리케이션 평가

일반적으로 애플리케이션 계층은 데이터를 유지하고 수정하기 위해 사용자 데이터베이스에 액세스합니다. Data Migration Assistant는 다음 두 가지 방법으로 애플리케이션의 데이터 액세스 계층을 평가할 수 있습니다.

- 캡처된 [확장 이벤트](/sql/relational-databases/extended-events/extended-events) 또는 사용자 데이터베이스의 [SQL Server Profiler 추적](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler)을 사용하여 평가합니다. 또한 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-capture-trace)를 사용하여 A/B 테스트에도 사용할 수 있는 추적 로그를 만들 수 있습니다.
- 코드 내에서 SQL 쿼리의 검색 및 평가를 제공하고 한 데이터베이스 플랫폼에서 다른 데이터베이스 플랫폼으로 애플리케이션 소스 코드를 마이그레이션하는 데 사용되는 [Data Access Migration Toolkit(미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)을 사용하여 평가합니다. 이 도구는 C#, Java, XML 및 일반 텍스트와 같이 널리 사용되는 파일 형식을 지원합니다. Data Access Migration Toolkit 평가를 수행하는 방법에 대한 지침은 [Data Migration Assistant 사용](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) 블로그 게시물을 참조하세요.

사용자 데이터베이스를 평가하는 동안 Data Migration Assistant를 사용하여 캡처된 추적 파일 또는 Data Access Migration Toolkit 파일을 [가져옵니다](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess).

#### <a name="assessments-at-scale"></a>대규모 평가

Data Migration Assistant 평가가 필요한 여러 서버가 있는 경우 [명령줄 인터페이스](/sql/dma/dma-commandline)를 사용하여 프로세스를 자동화할 수 있습니다. 인터페이스를 사용하여 마이그레이션 범위의 각 SQL Server 인스턴스에 대한 평가 명령을 미리 준비할 수 있습니다.

대규모 자산에 대한 요약 보고를 위해 이제 Data Migration Assistant 평가를 [Azure Migrate로 통합](/sql/dma/dma-assess-sql-data-estate-to-sqldb)할 수 있습니다.

#### <a name="refactor-databases-with-data-migration-assistant"></a>Data Migration Assistant를 사용하여 데이터베이스 리팩터링

Data Migration Assistant 평가 결과에 따라 마이그레이션 후 사용자 데이터베이스가 올바르게 수행하고 작동하는지 확인하기 위한 일련의 권장 사항이 있을 수 있습니다. Data Migration Assistant는 영향을 받는 개체와 각 문제를 해결하는 방법에 대한 리소스에 대한 세부 정보를 제공합니다. 프로덕션 마이그레이션을 시작하기 전에 모든 주요 변경 사항 및 동작 변경 사항을 해결해야 합니다.

더 이상 사용되지 않는 기능의 경우 이러한 변경을 방지하고 마이그레이션 속도를 높이려면 원래 [호환성](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) 모드에서 사용자 데이터베이스를 실행하도록 선택할 수 있습니다. 이 작업은 더 이상 사용되지 않는 항목이 해결될 때까지 [데이터베이스 호환성 업그레이드](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades)를 방지합니다.

모든 Data Migration Assistant 수정 사항을 스크립팅하고 [마이그레이션 후](#post-migration) 단계 동안 대상 SQL Server 데이터베이스에 적용해야 합니다.

> [!CAUTION]
> 모든 SQL Server 버전이 모든 호환성 모드를 지원하는 것은 아닙니다. [대상 SQL Server 버전](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)이 선택한 데이터베이스 호환성을 지원하는지 확인합니다. 예를 들어 SQL Server 2019는 호환성 수준이 90인 데이터베이스(SQL Server 2005)를 지원하지 않습니다. 이러한 데이터베이스는 적어도 호환성 수준 100으로 업그레이드해야 합니다.
>

## <a name="migrate"></a>Migrate

마이그레이션 전 단계를 완료하면 사용자 데이터베이스 및 구성 요소를 마이그레이션할 준비가 된 것입니다. 선호하는 [마이그레이션 방법](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)을 사용하여 데이터베이스를 마이그레이션합니다.

다음 섹션에서는 백업 및 복원을 사용하여 마이그레이션을 수행하거나 로그 전달과 함께 백업 및 복원을 사용하여 최소 가동 중지 시간 마이그레이션을 수행하는 단계를 제공합니다.

### <a name="backup-and-restore"></a>백업 및 복원

백업 및 복원을 사용하여 표준 마이그레이션을 수행하려면 다음을 수행합니다.

1. 요구 사항에 따라 Azure Virtual Machines에서 SQL Server에 대한 연결을 설정합니다. 자세한 내용은 [Azure(Resource Manager)에서 SQL Server Virtual Machine에 연결](../../virtual-machines/windows/ways-to-connect-to-sql.md)을 참조하세요.
1. 마이그레이션용 데이터베이스를 사용하는 모든 애플리케이션을 일시 중지하거나 중지합니다.
1. [단일 사용자 모드](/sql/relational-databases/databases/set-a-database-to-single-user-mode)를 사용하여 사용자 데이터베이스가 비활성 상태인지 확인합니다.
1. 온-프레미스 위치에 전체 데이터베이스 백업을 수행합니다.
1. 원격 데스크톱, [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) 또는 [AZCopy 명령줄 유틸리티](../../../storage/common/storage-use-azcopy-v10.md)를 사용하여 온-프레미스 백업 파일을 VM에 복사합니다. (2TB 이상의 백업이 권장됩니다.)
1. Azure Virtual Machines의 SQL Server에 전체 데이터베이스 백업을 복원합니다.

### <a name="log-shipping-minimize-downtime"></a>로그 전달(가동 중지 시간 최소화)

백업 및 복원 그리고 로그 전달을 사용하여 최소 가동 중지 시간 마이그레이션을 수행하려면 다음을 수행합니다.

1. 요구 사항에 따라 Azure Virtual Machines에서 SQL Server에 대한 연결을 설정합니다. 자세한 내용은 [Azure(Resource Manager)에서 SQL Server Virtual Machine에 연결](../../virtual-machines/windows/ways-to-connect-to-sql.md)을 참조하세요.
1. 마이그레이션할 온-프레미스 사용자 데이터베이스가 전체 복구 모델인지 대량 로그된 복구 모델인지 확인합니다.
1. 온 프레미스 위치에 전체 데이터베이스 백업을 수행하고 기존 전체 데이터베이스 백업 작업을 수정하여 로그 체인을 보존하기 위해 [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) 키워드를 사용합니다.
1. 원격 데스크톱, [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) 또는 [AZCopy 명령줄 유틸리티](../../../storage/common/storage-use-azcopy-v10.md)를 사용하여 온-프레미스 백업 파일을 VM에 복사합니다. (1TB 이상의 백업이 권장됩니다.)
1. Azure Virtual Machines의 SQL Server에서 전체 데이터베이스 백업을 복원합니다.
1. 온-프레미스 데이터베이스와 Azure Virtual Machines의 SQL Server간에 [로그 전달](/sql/database-engine/log-shipping/configure-log-shipping-sql-server)을 설정합니다. 이 작업은 이전 단계에서 이미 완료되었으므로 데이터베이스를 다시 초기화하지 마세요.
1. 대상 서버로 바로 이동합니다.
   1. 마이그레이션할 데이터베이스를 사용하여 애플리케이션을 일시 중지하거나 중지합니다.
   1. [단일 사용자 모드](/sql/relational-databases/databases/set-a-database-to-single-user-mode)를 사용하여 사용자 데이터베이스가 비활성 상태인지 확인합니다.
   1. 준비가 되면, 온-프레미스 데이터베이스의 로그 전달 [ 제어된 장애 조치(failover)](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server)를 Azure Virtual Machines의 SQL Server로 수행합니다.

### <a name="migrate-objects-outside-user-databases"></a>사용자 데이터베이스 외부에 있는 개체 마이그레이션

마이그레이션 후 사용자 데이터베이스의 원활한 작업을 위해 더 많은 SQL Server 개체가 필요할 수 있습니다.

다음 표는 사용자 데이터베이스 마이그레이션 전후에 완료할 수 있는 구성 요소 및 권장 마이그레이션 방법의 목록을 제공합니다.

| **기능** | **구성 요소** | **마이그레이션 방법** |
| --- | --- | --- |
| **데이터베이스** | 모델 | SQL Server Management Studio로 스크립트. |
|| TempDB | 최상의 성능을 위해 tempDB를 [Azure VM 임시 디스크(SSD)](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md#storage))로 이동하도록 계획합니다. tempDB를 수용하기에 충분한 로컬 SSD가 있는 VM 크기를 선택해야 합니다. |
|| FileStream이 있는 사용자 데이터베이스 | 마이그레이션에 대한 [백업 및 복원](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) 방법을 사용합니다. Data Migration Assistant는 FileStream이 있는 데이터베이스를 지원하지 않습니다. |
| **보안** | SQL Server 및 Windows 로그인 | Data Migration Assistant를 사용하여 [사용자 로그인을 마이그레이션](/sql/dma/dma-migrateserverlogins)합니다. |
|| SQL Server 역할 | SQL Server Management Studio로 스크립트. |
|| 암호화 공급자 | [Azure Key Vault를 사용하도록 전환](../../virtual-machines/windows/azure-key-vault-integration-configure.md)하는 것이 좋습니다. 이 절차는 [SQL VM 리소스 공급자](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)를 사용합니다. |
| **서버 개체** | 백업 디바이스 | [Azure Backup](../../../backup/backup-sql-server-database-azure-vms.md)을 사용하여 데이터베이스 백업으로 바꾸거나 [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md)(SQL Server 2012 SP1 CU2 +)에 백업을 작성합니다. 이 절차는 [SQL VM 리소스 공급자](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)를 사용합니다.|
|| 연결된 서버 | SQL Server Management Studio로 스크립트. |
|| 서버 트리거 | SQL Server Management Studio로 스크립트. |
| **복제** | 로컬 게시 | SQL Server Management Studio로 스크립트. |
|| 로컬 구독자 | SQL Server Management Studio로 스크립트. |
| **PolyBase** | PolyBase | SQL Server Management Studio로 스크립트. |
| **관리** | 데이터베이스 메일 | SQL Server Management Studio로 스크립트. |
| **SQL Server 에이전트** | 작업 | SQL Server Management Studio로 스크립트. |
|| 경고 | SQL Server Management Studio로 스크립트. |
|| 연산자 | SQL Server Management Studio로 스크립트. |
|| 프록시 | SQL Server Management Studio로 스크립트. |
| **운영 체제** | 파일, 파일 공유 | SQL 서버에서 사용되고 Azure Virtual Machines 대상에서 복제하는 다른 파일 또는 파일 공유를 기록합니다. |

## <a name="post-migration"></a>마이그레이션 후 작업

마이그레이션 단계를 성공적으로 완료한 후 모든 것이 최대한 원활하고 효율적으로 작동하게 하려면 일련의 마이그레이션 후 작업을 완료해야 합니다.

### <a name="remediate-applications"></a>애플리케이션 수정

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이 작업을 수행하려면 경우에 따라 애플리케이션을 변경해야 할 수도 있습니다.

Data Migration Assistant에서 권장하는 모든 수정 사항을 사용자 데이터베이스에 적용합니다. 일관성을 보장하고 자동화를 허용하려면 이러한 수정 사항을 스크립팅해야 합니다.

### <a name="perform-tests"></a>테스트 수행

데이터베이스 마이그레이션의 테스트 접근 방식은 다음 작업으로 구성됩니다.

1. **유효성 검사 테스트 개발**: 데이터베이스 마이그레이션을 테스트하려면 SQL 쿼리를 사용해야 합니다. 원본 데이터베이스와 대상 데이터베이스 둘 다에서 실행할 유효성 검사 쿼리를 만듭니다. 유효성 검사 쿼리는 정의한 범위를 포함해야 합니다.
1. **테스트 환경 설정**: 테스트 환경에는 원본 데이터베이스와 대상 데이터베이스의 복사본이 포함되어야 합니다. 테스트 환경을 격리해야 합니다.
1. **유효성 검사 테스트 실행**: 원본 및 대상에서 유효성 검사 테스트를 실행한 다음, 결과를 분석합니다.
1. **성능 테스트 실행**: 원본 및 대상에서 성능 테스트를 실행한 다음, 결과를 분석하고 비교합니다.

> [!TIP]
> [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 사용하여 대상 SQL 서버 성능을 평가합니다.

### <a name="optimize"></a>최적화

마이그레이션 후 단계는 데이터 정확도 문제를 조정하고 완성도를 확인할 뿐만 아니라 워크로드 관련 잠재적 성능 문제를 해결하는 데 매우 중요합니다.

이러한 문제 및 이를 완화하는 단계에 대한 자세한 내용은 다음을 참조하세요.

- [마이그레이션 후 유효성 검사 및 최적화 가이드](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Azure SQL 가상 머신에서 성능 튜닝](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md)
- [Azure 비용 최적화 센터](https://azure.microsoft.com/overview/cost-optimization/)

## <a name="next-steps"></a>다음 단계

- SQL Server에 적용되는 서비스의 가용성을 확인하려면 [Azure 글로벌 인프라 센터](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)를 참조하세요.
- 다양한 데이터베이스 및 데이터 마이그레이션 시나리오와 특수 작업을 지원하는 데 사용할 수 있는 Microsoft와 타사의 서비스 및 도구 표는 [데이터 마이그레이션 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조하세요.
- Azure SQL에 관한 자세한 내용은 다음을 참조하세요.
   - [배포 옵션](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure Virtual Machines의 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure TCO(총 소유 비용) 계산기](https://azure.microsoft.com/pricing/tco/calculator/)

- 클라우드 마이그레이션의 프레임워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조하세요.
   - [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   - [Azure로 마이그레이션하기 위한 워크로드의 비용 계산 및 크기 조정 모범 사례](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- 라이선스에 관한 자세한 내용은 다음을 참조하세요.
   - [Azure 하이브리드 혜택을 사용하는 사용자 라이선스 필요](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [SQL Server 2008 및 SQL Server 2008 R2에 관한 무료 지원 확장 받기](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- 애플리케이션 액세스 레이어를 평가하려면 [Data Access Migration Toolkit(미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)를 참조하세요.
- 데이터 액세스 계층에 대한 A/B 테스트를 수행하는 방법에 대한 자세한 내용은 [데이터베이스 실험 도우미 개요](/sql/dea/database-experimentation-assistant-overview)를 참조하세요.
