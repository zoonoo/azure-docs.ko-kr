---
title: Azure Vm에서 SQL Server SQL Server (마이그레이션 가이드)
description: 이 가이드에 따라 개별 SQL Server 데이터베이스를 Azure Virtual Machines (Vm)에서 SQL Server로 마이그레이션합니다.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: ''
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 1558c396566b2fcfc098a749407d5e7a28316b6f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019452"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-vms"></a>마이그레이션 가이드: Azure Vm에서 SQL Server으로 SQL Server 
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

이 마이그레이션 가이드에서는 평가를 위해 [DMA (데이터베이스 Migration Assistant)](/sql/dma/dma-overview) 를 활용 하는 백업 및 복원 및 로그 전달을 사용 하 여 SQL Server에서 Azure Virtual Machines (vm)의 SQL Server 인스턴스로 사용자 데이터베이스를 **검색** 하 고 **평가** 하 고 **마이그레이션하** 는 방법을 설명 합니다. 

온-프레미스 또는에서 실행 중인 SQL Server를 마이그레이션할 수 있습니다.

- Virtual Machines의 SQL Server  
- Amazon Web Services (AWS) EC2 
- AWS RDS (Amazon 관계형 데이터베이스 서비스) 
- 계산 엔진 (Google Cloud Platform GCP)

추가 마이그레이션 전략에 대 한 자세한 내용은 [SQL Server VM 마이그레이션 개요](sql-server-to-sql-on-azure-vm-migration-overview.md)를 참조 하세요.

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="마이그레이션 프로세스 흐름":::

## <a name="prerequisites"></a>필수 조건

Azure Vm에서 SQL Server로 마이그레이션하려면 다음을 수행 해야 합니다. 

- [데이터베이스 Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
- [Azure Migrate 프로젝트](/azure/migrate/create-manage-projects)입니다.
- [AZURE VM의](/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal) 준비 된 대상 SQL Server 원본 SQL Server와 동일 하거나 더 많은 버전입니다.
- [Azure와 온-프레미스 간의 연결](/azure/architecture/reference-architectures/hybrid-networking).
- [적절 한 마이그레이션 전략 선택](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)

## <a name="pre-migration"></a>마이그레이션 전 단계

마이그레이션을 시작 하기 전에 SQL 환경의 토폴로지를 검색 하 고 원하는 마이그레이션의 실행 가능성을 평가 합니다. 

### <a name="discover"></a>검색

Azure Migrate은 온-프레미스 컴퓨터의 마이그레이션 적합성을 평가 하 고, 성능 기반 크기 조정을 수행 하 고, 온-프레미스에서 실행 하기 위한 비용 추정치를 제공 합니다. 마이그레이션을 계획 하려면 Azure Migrate를 사용 하 여 기존 데이터 원본과 SQL Server 인스턴스에서 사용 하 [는 기능에 대 한 세부 정보를 확인](../../../migrate/concepts-assessment-calculation.md) 합니다. 이 프로세스에는 사용 중인 버전 및 기능을 사용 하 여 조직의 모든 SQL Server 인스턴스를 식별 하기 위해 네트워크를 검색 하는 작업이 포함 됩니다. 

> [!IMPORTANT]
> SQL Server 인스턴스에 대 한 대상 Azure virtual machine을 선택 하는 경우 [Azure vm의 SQL Server에 대 한 성능 지침](../../virtual-machines/windows/performance-guidelines-best-practices.md)을 고려해 야 합니다.

추가 검색 도구는 데이터 마이그레이션 시나리오에 사용할 수 있는 [서비스 및 도구](../../../dms/dms-tools-matrix.md#business-justification-phase) 를 참조 하세요.


### <a name="assess"></a>평가

모든 데이터 원본을 검색 한 후 [Data Migration Assistant (DMA)](/sql/dma/dma-overview) 를 사용 하 여 온-프레미스 SQL Server 인스턴스를 Azure VM의 SQL Server 인스턴스로 마이그레이션하는 것을 평가 하 여 원본 및 대상 인스턴스 간의 간격을 파악 합니다. 


> [!NOTE]
> SQL Server 버전을 업그레이드 _하지 않는_ 경우이 단계를 건너뛰고 [마이그레이션](#migrate) 섹션으로 이동 합니다. 


#### <a name="assess-user-databases"></a>사용자 데이터베이스 평가 

DMA (Data Migration Assistant)는 새 버전의 SQL Server 데이터베이스 기능에 영향을 줄 수 있는 호환성 문제를 검색 하 여 최신 데이터 플랫폼으로의 마이그레이션을 지원 합니다. DMA는 대상 환경에 대 한 성능 및 안정성 향상을 권장 하며 스키마, 데이터 및 로그인 개체를 원본 서버에서 대상 서버로 이동할 수도 있습니다.

자세히 알아보려면 [평가](/sql/dma/dma-migrateonpremsql) 를 참조 하세요. 


> [!IMPORTANT]
>평가 유형에 따라 원본 SQL Server에 필요한 사용 권한이 다를 수 있습니다. 
   > - **기능 패리티** 관리자의 경우 원본 SQL Server 데이터베이스에 연결 하기 위해 제공 된 자격 증명은 *sysadmin* 서버 역할의 멤버 여야 합니다.
   > - 호환성 문제 advisor의 경우 제공 된 자격 증명에는 최소한 `CONNECT SQL` `VIEW SERVER STATE` 및 `VIEW ANY DEFINITION` 사용 권한이 있어야 합니다.
   > - DMA는 평가를 실행 하기 전에 선택한 advisor에 필요한 사용 권한을 강조 표시 합니다.


#### <a name="assess-applications"></a>응용 프로그램 평가

일반적으로 응용 프로그램 계층은 사용자 데이터베이스에 액세스 하 여 데이터를 유지 하 고 수정 합니다.  DMA는 다음과 같은 두 가지 방법으로 응용 프로그램의 데이터 액세스 계층을 평가할 수 있습니다. 

- 캡처한 [확장 이벤트](/sql/relational-databases/extended-events/extended-events) 를 사용 하거나 사용자 데이터베이스의 [SQL Server Profiler 추적 ](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) 을 사용 합니다. 또한 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-capture-trace) 를 사용 하 여 a/B 테스트에도 사용할 수 있는 추적 로그를 만들 수 있습니다.

- 코드 내에서 SQL 쿼리를 검색 하 고 평가 하는 [데이터 액세스 마이그레이션 도구 키트 (](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) DAMT)를 사용 하 여 응용 프로그램 소스 코드를 한 데이터베이스 플랫폼에서 다른 플랫폼으로 마이그레이션하는 데 사용 됩니다. 이 도구는 c # 및 Java, XML 및 Plaint 텍스트를 비롯 한 다양 한 인기 파일 형식을 지원 합니다. DAMT 평가를 수행 하는 방법에 대 한 가이드는 [USE DMAT](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) 블로그를 참조 하세요.

사용자 데이터베이스를 평가 하는 동안에는 DMA를 사용 하 여 캡처된 추적 파일 또는 DAMT 파일을 [가져옵니다](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) . 


#### <a name="scale-assessments"></a>규모 평가

DMA 평가가 필요한 서버가 여러 대 있는 경우 [명령줄 인터페이스](/sql/dma/dma-commandline)를 통해 프로세스를 자동화할 수 있습니다. 인터페이스를 사용 하 여 마이그레이션 범위의 각 SQL Server 인스턴스에 대해 평가 명령을 미리 준비할 수 있습니다. 

Large 자산에서 요약 보고를 위해 Data Migration Assistant (DMA) 평가를 [Azure Migrate에 통합할](/sql/dma/dma-assess-sql-data-estate-to-sqldb)수 있습니다.

#### <a name="refactor-databases-with-dma"></a>DMA를 사용 하 여 데이터베이스 리팩터링

DMA 평가 결과에 따라 사용자 데이터베이스가 마이그레이션 후 제대로 수행 되 고 제대로 작동 하는지 확인 하는 일련의 권장 사항이 있을 수 있습니다. DMA는 영향을 받는 개체 및 각 문제를 해결 하는 방법에 대 한 리소스에 대 한 세부 정보를 제공 합니다. 프로덕션 마이그레이션 전에 모든 주요 변경 내용 및 동작 변경 내용을 확인 하는 것이 좋습니다.

더 이상 사용 되지 않는 기능의 경우 이러한 변경 작업을 수행 하지 않고 마이그레이션을 가속화 하려는 경우 원본 [호환성](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) 모드에서 사용자 데이터베이스를 실행 하도록 선택할 수 있습니다. 그러나 이렇게 하면 사용 되지 않는 항목이 해결 될 때까지 [데이터베이스 호환성이 업그레이드](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) 되지 않습니다.

[마이그레이션 후](#post-migration)에는 모든 DMA 수정 프로그램이 스크립팅된 후 대상 SQL Server 데이터베이스에 적용 되는 것이 좋습니다.

> [!CAUTION]
> 모든 SQL Server 버전에서 모든 호환성 모드를 지 원하는 것은 아닙니다. [대상 SQL Server 버전](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) 에서 선택한 데이터베이스 호환성을 지원 하는지 확인 합니다. 예를 들어 SQL Server 2019는 수준 90 호환성 (SQL Server 2005)을 포함 하는 데이터베이스를 지원 하지 않습니다. 이러한 데이터베이스는 최소한 호환성 수준 100로 업그레이드 해야 합니다.
>

## <a name="migrate"></a>Migrate

마이그레이션 전 단계를 완료 한 후에는 사용자 데이터베이스 및 구성 요소를 마이그레이션할 준비가 된 것입니다. 기본 설정 [마이그레이션 방법을](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)사용 하 여 데이터베이스를 마이그레이션합니다.  

다음은 백업 및 복원을 사용 하 여 마이그레이션을 수행 하는 단계 또는 로그 전달과 함께 백업 및 복원을 사용 하 여 최소 가동 중지 시간 마이그레이션을 수행 하는 단계를 제공 합니다. 

### <a name="backup-and-restore"></a>백업 및 복원

백업 및 복원을 사용 하 여 표준 마이그레이션을 수행 하려면 다음 단계를 수행 합니다. 

1. 요구 사항에 따라 Azure VM의 대상 SQL Server에 대 한 연결을 설정 합니다. [Azure에서 SQL Server Virtual Machine 연결(Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md)을 참조하세요.
1. 마이그레이션을 위한 데이터베이스를 사용 하는 응용 프로그램을 일시 중지/중지 합니다. 
1. [단일 사용자 모드](/sql/relational-databases/databases/set-a-database-to-single-user-mode)를 사용 하 여 사용자 데이터베이스가 비활성화 되어 있는지 확인 합니다. 
1. 온-프레미스 위치에 전체 데이터베이스 백업을 수행합니다.
1. 원격 데스크톱, [Azure 데이터 탐색기](/azure/data-explorer/data-explorer-overview)또는 [AZCopy 명령줄 유틸리티](../../../storage/common/storage-use-azcopy-v10.md) (> 2tb 백업 권장)를 사용 하 여 온-프레미스 백업 파일을 VM에 복사 합니다.
1. Azure VM에서 SQL Server 전체 데이터베이스 백업을 복원 합니다.

### <a name="log-shipping--minimize-downtime"></a>로그 전달 (가동 중지 시간 최소화)

백업, 복원 및 로그 전달을 사용 하 여 최소 가동 중지 시간 마이그레이션을 수행 하려면 다음 단계를 수행 합니다. 

1. 요구 사항에 따라 Azure VM의 대상 SQL Server에 대 한 연결을 설정 합니다. [Azure에서 SQL Server Virtual Machine 연결(Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md)을 참조하세요.
1. 마이그레이션할 온-프레미스 사용자 데이터베이스가 전체 또는 대량 로그 복구 모델에 있는지 확인 하세요.
1. 온-프레미스 위치에 대 한 전체 데이터베이스 백업을 수행 하 고 [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) 키워드를 사용 하 여 로그 체인을 유지 하는 기존 전체 데이터베이스 백업 작업을 수정 합니다.
1. 원격 데스크톱, [Azure 데이터 탐색기](/azure/data-explorer/data-explorer-overview)또는 [AZCopy 명령줄 유틸리티](../../../storage/common/storage-use-azcopy-v10.md) (>1tb 백업 권장)를 사용 하 여 온-프레미스 백업 파일을 VM에 복사 합니다.
1. Azure VM에서 SQL Server 전체 데이터베이스 백업을 복원 합니다.
1. Azure VM에서 온-프레미스 데이터베이스와 대상 SQL Server 간의 [로그 전달을](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) 설정 합니다. 이전 단계에서 이미 완료 되었으므로 데이터베이스를 다시 초기화 하지 않아야 합니다.
1. **Cut over** 대상 서버로 이동 합니다. 
   1. 마이그레이션할 데이터베이스를 사용 하는 응용 프로그램을 일시 중지/중지 합니다. 
   1. [단일 사용자 모드](/sql/relational-databases/databases/set-a-database-to-single-user-mode)를 사용 하 여 사용자 데이터베이스가 비활성화 되어 있는지 확인 합니다. 
   1. 준비가 되 면 온-프레미스 데이터베이스의 로그 전달 [제어 장애](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) 조치 (failover)를 수행 하 여 Azure VM에서 SQL Server 대상으로 설정 합니다.



### <a name="migrating-objects-outside-user-databases"></a>사용자 데이터베이스 외부의 개체 마이그레이션

마이그레이션 후 사용자 데이터베이스를 원활 하 게 운영 하는 데 필요한 추가 SQL Server 개체가 있을 수 있습니다. 

다음 표에서는 사용자 데이터베이스의 마이그레이션 전이나 후에 완료할 수 있는 목록 구성 요소 및 권장 마이그레이션 방법을 제공 합니다. 


| **기능** | **구성 요소** | **마이그레이션 방법** |
| --- | --- | --- |
| **데이터베이스** | 모델  | SQL Server Management Studio 스크립팅 |
|| TempDB | 최상의 성능을 위해 TempDB를 [AZURE VM 임시 디스크 (SSD](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk))로 이동할 계획입니다. TempDB를 수용 하기에 충분 한 로컬 SSD를 포함 하는 VM 크기를 선택 해야 합니다. |
|| Filestream이 있는 사용자 데이터베이스 |  마이그레이션에 대 한 [백업 및 복원](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) 방법을 사용 합니다. DMA는 Filestream을 사용 하는 데이터베이스를 지원 하지 않습니다. |
| **보안** | SQL Server 및 Windows 로그인 | DMA를 사용 하 여 [사용자 로그인을 마이그레이션합니다](/sql/dma/dma-migrateserverlogins). |
|| SQL Server 역할 | SQL Server Management Studio 스크립팅 |
|| 암호화 공급자 | [Azure Key Vault 서비스를 사용 하도록 변환 하](../../virtual-machines/windows/azure-key-vault-integration-configure.md)는 것이 좋습니다. 이 절차에서는 [SQL VM 리소스 공급자](../../virtual-machines/windows/sql-vm-resource-provider-register.md)를 사용 합니다. |
| **서버 개체** | 백업 디바이스 | [Azure Backup 서비스](../../../backup/backup-sql-server-database-azure-vms.md) 를 사용 하 여 데이터베이스 백업으로 대체 하거나 [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) 에 백업을 작성 합니다 (SQL Server 2012 SP1 CU2 +). 이 절차에서는 [SQL VM 리소스 공급자](../../virtual-machines/windows/sql-vm-resource-provider-register.md)를 사용 합니다.|
|| 연결된 서버 | SQL Server Management Studio를 사용 하 여 스크립팅 합니다. |
|| 서버 트리거 | SQL Server Management Studio를 사용 하 여 스크립팅 합니다. |
| **복제** | 로컬 게시 | SQL Server Management Studio를 사용 하 여 스크립팅 합니다. |
|| 로컬 구독자 | SQL Server Management Studio를 사용 하 여 스크립팅 합니다. |
| **Polybase** | Polybase | SQL Server Management Studio를 사용 하 여 스크립팅 합니다. |
| **관리** | 데이터베이스 메일 | SQL Server Management Studio를 사용 하 여 스크립팅 합니다. |
| **SQL Server 에이전트** | 작업 | SQL Server Management Studio를 사용 하 여 스크립팅 합니다. |
|| 경고 | SQL Server Management Studio를 사용 하 여 스크립팅 합니다. |
|| 연산자 | SQL Server Management Studio를 사용 하 여 스크립팅 합니다. |
|| 프록시 | SQL Server Management Studio를 사용 하 여 스크립팅 합니다. |
| **운영 체제** | 파일, 파일 공유 | SQL 서버에서 사용 하 고 Azure VM 대상에서 복제 하는 추가 파일 또는 파일 공유를 적어 둡니다. |


## <a name="post-migration"></a>마이그레이션 후 작업

마이그레이션 단계를 성공적으로 완료 한 후에는 일련의 마이그레이션 후 작업을 수행 하 여 모든 것이 최대한 원활 하 고 효율적으로 작동 하는지 확인 합니다.

### <a name="remediate-applications"></a>애플리케이션 수정

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이를 수행 하는 경우 응용 프로그램을 변경 해야 하는 경우도 있습니다.

모든 데이터베이스 Migration Assistant 권장 픽스를 사용자 데이터베이스에 적용 합니다. 일관성을 유지 하 고 자동화를 허용 하기 위해 스크립트를 작성 하는 것이 좋습니다.

### <a name="perform-tests"></a>테스트 수행

데이터베이스 마이그레이션에 대 한 테스트 접근 방식은 다음 작업을 수행 하는 것으로 구성 됩니다.

1. **유효성 검사 테스트를 개발 합니다.**  SQL 쿼리를 사용 하 여 데이터베이스 마이그레이션을 테스트 합니다. 원본 및 대상 데이터베이스에 대해 실행 되는 유효성 검사 쿼리를 만듭니다. 유효성 검사 쿼리는 정의한 범위를 포함해야 합니다.
2. **테스트 환경을 설정 합니다.**  테스트 환경에는 원본 데이터베이스와 대상 데이터베이스의 복사본이 있어야 합니다. 테스트 환경을 격리해야 합니다.
3. **유효성 검사 테스트를 실행 합니다.**  원본 및 대상에 대해 유효성 검사 테스트를 실행 한 다음 결과를 분석 합니다.
4. **성능 테스트를 실행 합니다.**  원본 및 대상에 대해 성능 테스트를 실행 한 다음 결과를 분석 하 고 비교 합니다.

> [!TIP]
> [데이터베이스 실험 도우미 (DEA)](/sql/dea/database-experimentation-assistant-overview) 를 사용 하 여 대상 SQL Server 성능 평가를 지원할 수 있습니다.
>

### <a name="optimize"></a>최적화

마이그레이션 후 단계는 데이터 정확도 및 완성도와 관련 된 문제를 조정 하 고 워크 로드의 잠재적 성능 문제를 해결 하는 데 중요 합니다.

이러한 문제와 이러한 문제를 완화 하는 특정 단계에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

- [마이그레이션 후 유효성 검사 및 최적화 가이드.](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [AZURE SQL Virtual Machines의 성능 튜닝](../../virtual-machines/windows/performance-guidelines-best-practices.md).
- [Azure cost 최적화 센터](https://azure.microsoft.com/overview/cost-optimization/).

## <a name="next-steps"></a>다음 단계

- 에 적용할 수 있는 서비스의 가용성을 확인 하려면 [Azure 글로벌 인프라 센터](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database) 를 참조 SQL Server.

- 다양 한 데이터베이스 및 데이터 마이그레이션 시나리오와 전문 작업을 지 원하는 데 사용할 수 있는 Microsoft 및 타사 서비스 및 도구에 대 한 매트릭스는 [데이터 마이그레이션을 위한 서비스 및 도구](../../../dms/dms-tools-matrix.md) 문서를 참조 하세요.

- Azure SQL에 대 한 자세한 내용은 다음을 참조 하세요.
   - [배포 옵션](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure VM의 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 


- 클라우드 마이그레이션의 프레임 워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조 하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [작업의 비용 및 크기 조정 작업에 대 한 모범 사례 Azure로 마이그레이션](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 라이선스에 대 한 자세한 내용은 다음을 참조 하세요.
   - [Azure 하이브리드 혜택를 사용 하 여 사용자 고유의 라이선스 가져오기](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [SQL Server 2008 및 SQL Server 2008 R2에 대 한 무료 지원 받기](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- 응용 프로그램 액세스 계층을 평가 하려면 [데이터 액세스 마이그레이션 도구 키트 (미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) 를 참조 하세요.
- 데이터 액세스 계층 A/B 테스트를 수행 하는 방법에 대 한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조 하세요.
