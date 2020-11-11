---
title: SQL Database로 SQL Server-마이그레이션 가이드
description: 이 가이드에 따라 SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션합니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 55ce3747aaf105c7e2cbb830b1175769a658fd72
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94497051"
---
# <a name="migration-guide-sql-server-to-sql-database"></a>마이그레이션 가이드: SQL Database SQL Server
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

이 가이드는 SQL Server 인스턴스를 Azure SQL Database으로 마이그레이션하는 데 도움이 됩니다. 

온-프레미스 또는에서 실행 중인 SQL Server를 마이그레이션할 수 있습니다. 

- Virtual Machines의 SQL Server  
- Amazon Web Services (AWS) EC2 
- AWS RDS (Amazon 관계형 데이터베이스 서비스) 
- 계산 엔진 (Google Cloud Platform GCP)  
- SQL Server에 대 한 클라우드 SQL (Google Cloud Platform – GCP) 

마이그레이션에 대 한 자세한 내용은 [마이그레이션 개요](sql-server-to-sql-database-overview.md)를 참조 하세요. 다른 시나리오는 [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조 하세요.

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="마이그레이션 프로세스 흐름":::

## <a name="prerequisites"></a>사전 요구 사항 

SQL Server을 Azure SQL Database로 마이그레이션하려면 다음 필수 구성 요소가 있는지 확인 합니다. 

- 선택한 [마이그레이션 방법](sql-server-to-sql-database-overview.md#compare-migration-options) 및 해당 도구 
- 원본에 연결할 수 있는 컴퓨터에 설치 된 [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) SQL Server
- 대상 [Azure SQL Database](../../database/single-database-create-quickstart.md)


## <a name="pre-migration"></a>사전 마이그레이션

원본 환경이 지원 되는지 확인 한 후에는 마이그레이션 전 단계부터 시작 합니다. 모든 기존 데이터 원본을 검색 하 고, 마이그레이션 가능성을 평가 하 고, 마이그레이션을 방해할 수 있는 차단 문제를 식별 합니다. 

### <a name="discover"></a>검색

검색 단계에서 네트워크를 검색 하 여 조직에서 사용 하는 모든 SQL Server 인스턴스와 기능을 식별 합니다. 

[Azure Migrate](../../../migrate/migrate-services-overview.md) 를 사용 하 여 온-프레미스 서버의 마이그레이션 적합성을 평가 하 고, 성능 기반 크기 조정을 수행 하 고, Azure에서 실행 하는 데 드는 비용을 예측 합니다. 

또는 [Microsoft 평가 및 계획 도구 키트 ("MAP toolkit")](https://www.microsoft.com/download/details.aspx?id=7826) 를 사용 하 여 현재 IT 인프라를 평가 합니다. 도구 키트는 마이그레이션 계획 프로세스를 간소화 하기 위한 강력한 인벤토리, 평가 및 보고 도구를 제공 합니다. 

검색 단계에 사용할 수 있는 도구에 대 한 자세한 내용은 [데이터 마이그레이션 시나리오에 사용할 수 있는 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조 하세요. 

### <a name="assess"></a>평가 

데이터 원본을 검색 한 후에 Azure SQL Database 마이그레이션할 수 있는 온-프레미스 SQL Server 데이터베이스를 평가 하 여 마이그레이션 차단 또는 호환성 문제를 식별 합니다. 

Data Migration Assistant (버전 4.1 이상)을 사용 하 여 가져올 데이터베이스를 평가할 수 있습니다. 

- [Azure 대상 권장 사항](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure SKU 권장 사항](/sql/dma/dma-sku-recommend-sql-db)

데이터베이스 마이그레이션 평가를 사용 하 여 환경을 평가 하려면 다음 단계를 수행 합니다. 

1. [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)를 엽니다. 
1. **파일** 을 선택한 다음, **새 평가** 를 선택 합니다. 
1. 프로젝트 이름을 지정 하 고 SQL Server 원본 서버 유형으로 선택한 다음 대상 서버 유형으로 Azure SQL Database를 선택 합니다. 
1. 생성 하려는 평가 보고서의 유형을 선택 합니다. 예를 들어 데이터베이스 호환성 및 기능 패리티가 있습니다. 평가 유형에 따라 원본 SQL Server에 필요한 사용 권한이 다를 수 있습니다.  DMA는 평가를 실행 하기 전에 선택한 advisor에 필요한 사용 권한을 강조 표시 합니다.
    - **기능 패리티** 범주는 포괄적인 권장 사항 집합, Azure에서 사용할 수 있는 대체 방법 및 마이그레이션 프로젝트를 계획 하는 데 도움이 되는 완화 단계를 제공 합니다. (sysadmin 권한 필요)
    - **호환성 문제** 범주는 마이그레이션을 차단할 수 있는 부분적으로 지원 되거나 지원 되지 않는 기능 호환성 문제와 이러한 문제 ( `CONNECT SQL` , `VIEW SERVER STATE` 및 `VIEW ANY DEFINITION` 필요한 권한)를 해결 하는 권장 사항을 식별 합니다.
1. SQL Server에 대 한 원본 연결 정보를 지정 하 고 원본 데이터베이스에 연결 합니다.
1. **평가 시작** 을 선택 합니다. 
1. 프로세스가 완료 된 후 마이그레이션 차단 및 기능 패리티 문제에 대 한 평가 보고서를 선택 하 고 검토 합니다. 평가 보고서는 조직의 다른 팀 또는 직원과 공유할 수 있는 파일로 내보낼 수도 있습니다. 
1. 마이그레이션 후 활동을 최소화 하는 데이터베이스 호환성 수준을 결정 합니다.  
1. 온-프레미스 워크 로드에 대 한 최상의 Azure SQL Database SKU를 확인 합니다. 

자세히 알아보려면 [Data Migration Assistant를 사용 하 여 SQL Server 마이그레이션 평가 수행](/sql/dma/dma-assesssqlonprem)을 참조 하세요.

평가가 여러 개의 방해를 발생 하 여 데이터베이스에서 Azure SQL Database 마이그레이션을 수행할 준비가 되지 않았는지 확인 하는 경우 다음을 고려 하십시오.

- 여러 인스턴스 범위 종속성이 있는 경우 [AZURE SQL Managed Instance](../managed-instance/sql-server-to-managed-instance-overview.md)
- SQL Database 및 SQL Managed Instance 모두 적절 한 대상이 되지 않는 경우 [Azure Virtual Machines에서 SQL Server](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md) 합니다. 



#### <a name="scaled-assessments-and-analysis"></a>확장 된 평가 및 분석
Data Migration Assistant에서는 분석을 위해 평가 보고서의 확장 된 평가 및 통합을 수행할 수 있습니다. 

여러 서버 및 데이터베이스를 대규모로 평가 하 고 분석 하 여 데이터 공간을 광범위 하 게 표시 하려면 다음 링크를 참조 하 여 자세한 내용을 알아보세요.

- [PowerShell을 사용 하 여 확장 된 평가 수행](/sql/dma/dma-consolidatereports)
- [Power BI를 사용 하 여 평가 보고서 분석](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> 여러 데이터베이스, 특히 대규모 데이터베이스에 대해 대규모 평가를 실행 하는 것은 [DMA 명령줄 유틸리티](/sql/dma/dma-commandline) 를 사용 하 여 자동화 하 고 추가 분석 및 대상 준비를 위해 [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) 에 업로드할 수도 있습니다.

## <a name="migrate"></a>Migrate

마이그레이션 전 단계와 관련 된 작업을 완료 하면 스키마 및 데이터 마이그레이션을 수행할 준비가 된 것입니다. 

선택한 [마이그레이션 방법을](sql-server-to-sql-database-overview.md#compare-migration-options)사용 하 여 데이터를 마이그레이션합니다. 

이 가이드에서는 Data Migration Assistant 및 Azure Database Migration Service의 가장 인기 있는 두 가지 옵션에 대해 설명 합니다. 

### <a name="data-migration-assistant-dma"></a>Data Migration Assistant(DMA)

DMA를 사용 하 여 SQL Server에서 Azure SQL Database로 데이터베이스를 마이그레이션하려면 다음 단계를 수행 합니다. 

1. [Migration Assistant 데이터베이스](https://www.microsoft.com/download/details.aspx?id=53595)를 다운로드 하 여 설치 합니다.
1. 새 프로젝트를 만들고 프로젝트 형식으로 **마이그레이션** 을 선택 합니다.
1. 원본 서버 유형을 **SQL Server** 로 설정 하 고 대상 서버 유형을 **Azure SQL Database** 로 설정 하 고 마이그레이션 범위를 **스키마 및 데이터로** 선택 하 고 **만들기** 를 선택 합니다.
1. 마이그레이션 프로젝트에서 서버 이름, 서버에 연결할 자격 증명 및 마이그레이션할 원본 데이터베이스와 같은 원본 서버 세부 정보를 지정 합니다.
1. 대상 서버 세부 정보에서 Azure SQL Database 서버 이름, 서버에 연결할 자격 증명 및 마이그레이션할 대상 데이터베이스를 지정 합니다.
1. 스키마 개체를 선택 하 고 대상 Azure SQL Database에 배포 합니다.
1. 마지막으로 **데이터 마이그레이션 시작** 을 선택 하 고 마이그레이션의 진행률을 모니터링 합니다.

자세한 자습서는 [Data Migration Assistant를 사용 하 여 온-프레미스 SQL Server 또는 Azure vm의 SQL Server를 Azure SQL Database로 마이그레이션](/sql/dma/dma-migrateonpremsqltosqldb)을 참조 하세요. 


> [!NOTE]
> - 더 많은 리소스를 제공 하 여 가져오기 속도를 최대화 하기 위해 가져오기 프로세스 중에 데이터베이스를 상위 서비스 계층으로 확장 하 고 크기를 계산 합니다. 그런 다음, 가져오기가 성공하면 크기를 축소할 수 있습니다.</br>
> - 가져온 데이터베이스의 호환성 수준은 원본 데이터베이스의 호환성 수준에 따라 달라 집니다. 


### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

DMS를 사용 하 여 SQL Server에서 Azure SQL Database로 데이터베이스를 마이그레이션하려면 다음 단계를 수행 합니다.

1. 아직 등록 하지 않은 경우 구독에 **microsoft.datamigration** 리소스 공급자를 등록 합니다. 
1. 원하는 위치 (대상 Azure SQL Database와 동일한 지역)에 Azure Database Migration Service 인스턴스를 만듭니다. 기존 가상 네트워크를 선택 하거나 DMS 인스턴스를 호스트할 새 가상 네트워크를 만듭니다.
1. DMS 인스턴스를 만든 후 새 마이그레이션 프로젝트를 만들고 원본 서버 유형을 **SQL Server** 로 지정 하 고 대상 서버 유형을 **Azure SQL Database** 으로 지정 합니다. 마이그레이션 프로젝트 만들기 블레이드에서 작업 유형으로 **오프 라인 데이터 마이그레이션** 을 선택 합니다.
1. 마이그레이션 **대상** 세부 정보 페이지에서 원본 **Migration source** SQL Server 세부 정보를 지정 하 고 대상 Azure SQL Database 세부 정보를 지정 합니다.
1. 마이그레이션을 위해 원본 및 대상 데이터베이스를 매핑한 다음 마이그레이션할 테이블을 선택 합니다.
1. 마이그레이션 요약을 검토 하 고 **마이그레이션 실행** 을 선택 합니다. 그런 다음 마이그레이션 작업을 모니터링 하 고 데이터베이스 마이그레이션의 진행률을 확인할 수 있습니다.

자세한 자습서는 [DMS를 사용 하 여 Azure SQL Database SQL Server 마이그레이션](../../../dms/tutorial-sql-server-to-azure-sql.md)을 참조 하세요. 

## <a name="data-sync-and-cutover"></a>데이터 동기화 및 가공선

원본에서 대상으로 데이터 변경 내용을 지속적으로 복제/동기화 하는 마이그레이션 옵션을 사용 하는 경우 원본 데이터와 스키마가 대상에서 변경 되 고 드리프트 될 수 있습니다. 데이터 동기화 중에 마이그레이션 프로세스 중에 원본에 대 한 모든 변경 내용이 캡처되고 대상에 적용 되는지 확인 합니다. 

원본과 대상 모두에서 데이터가 동일한 지 확인 한 후 원본에서 대상 환경으로 건너뛸 수 있습니다. 비즈니스 연속성에 영향을 주지 않도록 비즈니스/응용 프로그램 팀과 함께 가공선을 계획 하는 것이 중요 합니다. 

> [!IMPORTANT]
> DMS를 사용 하 여 마이그레이션의 일부로 수행 하는 것과 관련 된 특정 단계에 대 한 자세한 내용은 [마이그레이션](../../../dms/tutorial-sql-server-azure-sql-online.md#perform-migration-cutover)시작을 참조 하세요.


## <a name="post-migration"></a>마이그레이션 후 작업

마이그레이션 단계를 성공적으로 완료 한 후에는 일련의 마이그레이션 후 작업을 진행 하 여 모든 것이 원활 하 고 효율적으로 기능 하는지 확인 합니다. 

마이그레이션 후 단계는 데이터 정확도 문제를 조정 하 고 완성도를 확인 하는 데 중요 하며 워크 로드와 관련 된 성능 문제를 해결 하는 데 중요 합니다. 

### <a name="remediate-applications"></a>애플리케이션 수정 

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이를 수행 하는 경우 응용 프로그램을 변경 해야 하는 경우도 있습니다.

### <a name="perform-tests"></a>테스트 수행

데이터베이스 마이그레이션의 테스트 접근 방식은 다음 작업으로 구성됩니다.

1. **유효성 검사 테스트 개발** : 데이터베이스 마이그레이션을 테스트하려면 SQL 쿼리를 사용해야 합니다. 원본 데이터베이스와 대상 데이터베이스 둘 다에서 실행할 유효성 검사 쿼리를 만들어야 합니다. 유효성 검사 쿼리는 정의한 범위를 포함해야 합니다.
1. **테스트 환경 설정** : 테스트 환경에는 원본 데이터베이스와 대상 데이터베이스의 복사본이 포함되어야 합니다. 테스트 환경을 격리해야 합니다.
1. **유효성 검사 테스트 실행** : 원본 및 대상에서 유효성 검사 테스트를 실행한 다음, 결과를 분석합니다.
1. **성능 테스트 실행** : 원본 및 대상에서 성능 테스트를 실행한 다음, 결과를 분석하고 비교합니다.

   > [!NOTE]
   > 마이그레이션 후 유효성 검사 테스트를 개발하고 실행하는 데 도움이 필요한 경우 파트너 [QuerySurge](https://www.querysurge.com/company/partners/microsoft)에서 제공하는 데이터 품질 솔루션을 사용하는 것이 좋습니다. 


## <a name="leverage-advanced-features"></a>고급 기능 활용 

SQL Database에서 제공 하는 고급 클라우드 기반 기능 (예: [기본 제공](../../database/high-availability-sla.md)되는 고가용성, [위협 검색](../../database/advanced-data-security.md), [작업 모니터링 및 튜닝](../../database/monitor-tune-overview.md))을 활용 해야 합니다. 

일부 SQL Server 기능은 [데이터베이스 호환성 수준이](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) 최신 호환성 수준 (150)으로 변경 된 후에만 사용할 수 있습니다. 

자세히 알아보려면 [마이그레이션 후 Azure SQL Database 관리](../../database/manage-data-after-migrating-to-database.md) 를 참조 하세요.


## <a name="next-steps"></a>다음 단계

- 다양 한 데이터베이스 및 데이터 마이그레이션 시나리오와 전문 작업을 지 원하는 데 사용할 수 있는 Microsoft 및 타사 서비스 및 도구의 행렬은 [데이터 마이그레이션을 위한 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조 하세요.

- SQL Database에 대 한 자세한 내용은 다음을 참조 하세요.
    - [Azure SQL Database 개요](../../database/sql-database-paas-overview.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 


- 클라우드 마이그레이션의 프레임 워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조 하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [작업의 비용 및 크기 조정 작업에 대 한 모범 사례 Azure로 마이그레이션](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 응용 프로그램 액세스 계층을 평가 하려면 [데이터 액세스 마이그레이션 도구 키트 (미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) 를 참조 하세요.
- 데이터 액세스 계층 A/B 테스트를 수행 하는 방법에 대 한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조 하세요.
