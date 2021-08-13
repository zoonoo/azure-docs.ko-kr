---
title: 'SQL Server를 Azure SQL Database로: 마이그레이션 가이드'
description: 이 가이드에 따라 SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션합니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 03/19/2021
ms.openlocfilehash: 437b7bd6e919cd6b462cb8653354631fbeb438ed
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110785574"
---
# <a name="migration-guide-sql-server-to-azure-sql-database"></a>마이그레이션 가이드: SQL Server를 Azure SQL Database로
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

이 가이드에서는 SQL Server 인스턴스를 Azure SQL Database로 [마이그레이션하는 방법](https://azure.microsoft.com/migration/migration-journey)을 알아봅니다. 

온-프레미스 또는 다음에서 실행 중인 SQL Server를 마이그레이션할 수 있습니다. 

- Virtual Machines의 SQL Server  
- AWS(Amazon Web Services) EC2 
- AWS RDS(Amazon Relational Database Service) 
- Compute Engine(Google Cloud Platform - GCP)  
- SQL Server용 Cloud SQL(Google Cloud Platform - GCP) 

마이그레이션에 대한 자세한 내용은 [마이그레이션 개요](sql-server-to-sql-database-overview.md)를 참조하세요. 다른 마이그레이션 가이드는 [데이터베이스 마이그레이션](/data-migration)을 참조하세요. 

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="마이그레이션 프로세스 흐름":::

## <a name="prerequisites"></a>사전 요구 사항 

Azure SQL Database로 [SQL Server 마이그레이션](https://azure.microsoft.com/en-us/migration/sql-server/)을 수행하려면 다음을 확인합니다. 

- [마이그레이션 방법](sql-server-to-sql-database-overview.md#compare-migration-options) 및 해당 도구를 선택했는지 확인합니다.
- 원본 SQL Server에 연결할 수 있는 머신에 [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)가 설치되어 있는지 확인합니다.
- [Azure SQL Database](../../database/single-database-create-quickstart.md) 대상을 만들었는지 확인합니다. 
- 원본과 대상 모두에 액세스할 수 있도록 연결 및 적합한 권한을 구성했는지 확인합니다. 
- [Azure SQL Database에서 사용할 수 있는](../../database/features-comparison.md) 데이터베이스 엔진 기능을 검토했는지 확인합니다. 



## <a name="pre-migration"></a>사전 마이그레이션

원본 환경이 지원되는지 확인한 후에 마이그레이션 전 단계부터 시작합니다. 모든 기존 데이터 원본을 검색하고, 마이그레이션 가능성을 평가하고, [Azure 클라우드 마이그레이션](https://azure.microsoft.com/migration)을 방해할 수 있는 차단 문제를 식별합니다.

### <a name="discover"></a>검색

검색 단계에서 네트워크를 스캔하여 조직에서 사용하는 모든 SQL Server 인스턴스와 기능을 식별합니다. 

[Azure Migrate](../../../migrate/migrate-services-overview.md)를 사용하여 온-프레미스 서버의 마이그레이션 적합성을 평가하고, 성능 기반 크기 조정을 수행하고, Azure에서의 해당 작업에 대한 비용 예측을 제공합니다. 

또는  [Microsoft Assessment and Planning Toolkit (MAP Toolkit)](https://www.microsoft.com/download/details.aspx?id=7826)를 사용하여 현재 IT 인프라를 평가합니다. 도구 키트는 강력한 인벤토리, 평가, 보고 도구를 제공하여 마이그레이션 계획 프로세스를 간소화합니다. 

검색 단계에서 사용할 수 있는 도구에 대한 자세한 내용은 [데이터 마이그레이션 시나리오에 사용할 수 있는 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조하세요. 

### <a name="assess"></a>평가 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

데이터 원본을 검색한 후에는 Azure SQL Database로 마이그레이션할 수 있는 온-프레미스 SQL Server 데이터베이스를 평가하여 마이그레이션 차단 또는 호환성 문제를 식별합니다. 

Data Migration Assistant(버전 4.1 이상)를 사용하여 다음 데이터베이스를 평가할 수 있습니다. 

- [Azure 대상 권장 사항](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure SKU 권장 사항](/sql/dma/dma-sku-recommend-sql-db)

Database Migration Assessment를 사용하여 환경을 평가하려면 다음 단계를 수행합니다. 

1. [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)를 엽니다. 
1. **파일** 을 선택한 다음, **새 평가** 를 선택합니다. 
1. 프로젝트 이름을 지정하고, 원본 서버 유형으로 SQL Server를 선택한 다음, 대상 서버 유형으로 Azure SQL Database 를 선택합니다. 
1. 생성하려는 평가 보고서의 유형을 선택합니다. 예를 들면 데이터베이스 호환성 및 기능 패리티입니다. 평가 유형에 따라 원본 SQL Server에 필요한 권한이 다를 수 있습니다.  DMA가 평가를 실행하기 전에 선택한 관리자에 필요한 권한을 강조 표시합니다.
    - **기능 패리티** 범주는 전체 권장 사항 집합, Azure에서 사용 가능한 대체 방법 및 마이그레이션 프로젝트에 대한 작업을 계획하는 데 도움이 되는 완화 단계를 제공합니다. (sysadmin 권한 필요)
    - **호환성 문제** 범주는 마이그레이션을 차단할 수 있는 부분적으로 지원되거나 지원되지 않는 기능 호환성 문제와 해당 문제를 해결하기 위한 권장 사항을 확인합니다(`CONNECT SQL`, `VIEW SERVER STATE`, `VIEW ANY DEFINITION` 권한 필요).
1. SQL Server에 대한 원본 연결의 세부 정보를 지정하고 원본 데이터베이스에 연결합니다.
1. **평가 시작** 을 선택합니다. 
1. 프로세스가 완료되면 마이그레이션 차단 및 기능 패리티 문제에 대한 평가 보고서를 선택하고 검토합니다. 평가 보고서를 조직의 다른 팀 또는 직원과 공유할 수 있는 파일로 내보낼 수도 있습니다. 
1. 마이그레이션 후 활동을 최소화하는 데이터베이스 호환성 수준을 결정합니다.  
1. 온-프레미스 워크로드에 적합한 Azure SQL Database SKU를 파악합니다. 

자세히 알아보려면 [Data Migration Assistant를 사용하여 SQL Server 마이그레이션 평가 수행](/sql/dma/dma-assesssqlonprem)을 참조하세요.

평가에서 여러 장애 요소가 발견되어 데이터베이스가 Azure SQL Database로 마이그레이션할 준비가 되지 않은 것으로 확인되는 경우 다음을 고려하세요.

- 여러 인스턴스 범위 종속성이 있는 경우 [Azure SQL Managed Instance](../managed-instance/sql-server-to-managed-instance-overview.md)
- SQL Database와 SQL Managed Instance 모두 적절한 대상이 되지 않는 경우 [Azure Virtual Machines의 SQL Server](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md) 



#### <a name="scaled-assessments-and-analysis"></a>확장된 평가 및 분석
Data Migration Assistant에서는 분석을 위해 평가 보고서의 확장된 평가 및 통합을 수행할 수 있습니다. 

여러 서버 및 데이터베이스를 대규모로 평가하고 분석하여 데이터 자산을 광범위하게 확인하려면 다음 링크를 클릭하여 자세한 내용을 참조하세요.

- [PowerShell을 사용하여 확장된 평가 수행](/sql/dma/dma-consolidatereports)
- [Power BI를 사용하여 평가 보고서 분석](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> 추가 분석 및 대상의 준비 상태를 위해 [DMA 명령줄 유틸리티](/sql/dma/dma-commandline)를 사용하여 여러 데이터베이스(특히 대규모 데이터베이스)에 대한 대규모 평가 실행을 자동화하고 [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results)로 업로드할 수 있습니다.

## <a name="migrate"></a>마이그레이션

마이그레이션 전 단계와 관련된 작업을 완료하면 스키마 및 데이터 마이그레이션을 수행할 준비가 된 것입니다. 

선택한 [마이그레이션 방법](sql-server-to-sql-database-overview.md#compare-migration-options)을 사용하여 데이터를 마이그레이션합니다. 

이 가이드에서는 가장 인기 있는 두 가지 옵션 Data Migration Assistant 및 Azure Database Migration Service에 대해 설명합니다. 

### <a name="data-migration-assistant-dma"></a>Data Migration Assistant(DMA)

DMA를 사용하여 데이터베이스를 SQL Server에서 Azure SQL Database로 마이그레이션하려면 다음 단계를 수행합니다. 

1. [Database Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)를 다운로드하여 설치합니다.
1. 새 프로젝트를 만들고 프로젝트 형식으로 **마이그레이션** 을 선택합니다.
1. 원본 서버 유형을 **SQL Server** 로, 대상 서버 유형을 **Azure SQL Database** 로 각각 설정하고 마이그레이션 범위로 **스키마 및 데이터** 를 선택한 다음 **만들기** 를 선택합니다.
1. 마이그레이션 프로젝트에서 서버 이름, 서버에 연결할 자격 증명, 마이그레이션할 원본 데이터베이스와 같은 원본 서버 세부 정보를 지정합니다.
1. 대상 서버 세부 정보에서 Azure SQL Database 서버 이름, 서버에 연결할 자격 증명, 마이그레이션할 대상 데이터베이스를 지정합니다.
1. 스키마 개체를 선택하고 대상 Azure SQL Database에 배포합니다.
1. 마지막으로 **데이터 마이그레이션 시작** 을 선택하고 마이그레이션의 진행률을 모니터링합니다.

자세한 자습서는 [Data Migration Assistant를 사용하여 온-프레미스 SQL Server 또는 Azure VM의 SQL Server를 Azure SQL Database로 마이그레이션](/sql/dma/dma-migrateonpremsqltosqldb)을 참조하세요. 


> [!NOTE]
> - 더 많은 리소스를 제공하여 가져오기 속도를 높이려면 가져오기 프로세스 중에 데이터베이스를 더 높은 서비스 계층 및 컴퓨팅 크기로 확장합니다. 그런 다음, 가져오기가 성공하면 크기를 축소할 수 있습니다.</br>
> - 가져온 데이터베이스 호환성 수준은 원본 데이터베이스의 호환성 수준에 따라 결정됩니다. 


### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

DMS를 사용하여 데이터베이스를 SQL Server에서 Azure SQL Database로 마이그레이션하려면 다음 단계를 수행합니다.

1. 아직 등록하지 않은 경우 구독에 **Microsoft.DataMigration** 리소스 공급자를 등록합니다. 
1. 원하는 위치(가급적 대상 Azure SQL Database와 동일한 지역)에 Azure Database Migration Service 인스턴스를 만듭니다. 기존 가상 네트워크를 선택하거나 DMS 인스턴스를 호스트할 새 가상 네트워크를 만듭니다.
1. DMS 인스턴스가 만들어지면 새 마이그레이션 프로젝트를 만들고 원본 서버 유형을 **SQL Server** 로, 대상 서버 유형을 **Azure SQL Database** 로 각각 지정합니다. 마이그레이션 프로젝트 만들기 블레이드에서 작업 유형으로 **오프라인 데이터 마이그레이션** 을 선택합니다.
1. **마이그레이션 원본** 세부 정보 페이지에서 원본 SQL Server의 세부 정보를, **마이그레이션 대상** 세부 정보 페이지에서 대상 Azure SQL Database의 세부 정보를 각각 지정합니다.
1. 마이그레이션을 위해 원본 및 대상 데이터베이스를 매핑한 다음 마이그레이션할 테이블을 선택합니다.
1. 마이그레이션 요약을 검토하고 **마이그레이션 실행** 을 선택합니다. 그런 다음 마이그레이션 작업을 모니터링하고 데이터베이스 마이그레이션의 진행 상황을 확인할 수 있습니다.

자세한 자습서는 [DMS를 사용하여 SQL Server를 Azure SQL Database로 마이그레이션](../../../dms/tutorial-sql-server-to-azure-sql.md)을 참조하세요. 

## <a name="data-sync-and-cutover"></a>데이터 동기화 및 전환

원본에서 대상으로 데이터의 변경 내용을 지속적으로 복제/동기화하는 마이그레이션 옵션을 사용하는 경우 원본 데이터와 스키마가 대상에서 변경되고 드리프트될 수 있습니다. 데이터 동기화를 진행할 때, 마이그레이션 프로세스 도중에 원본에 대한 모든 변경 내용이 대상에 캡처되고 적용되었는지 확인하세요. 

원본 및 대상의 데이터가 동일한지 확인한 후에 원본에서 대상 환경으로의 전환을 수행할 수 있습니다. 비즈니스/애플리케이션 팀과 함께 전환 프로세스를 계획하여 전환 시 비즈니스 연속성에 영향을 미치지 않는 선에서 최소한의 전환만 발생하도록 하는 것이 중요합니다. 

> [!IMPORTANT]
> DMS를 사용하는 마이그레이션의 일부로 전환을 수행하는 것과 관련된 특정 단계에 대한 자세한 내용은 [마이그레이션 전환 수행](../../../dms/tutorial-sql-server-to-azure-sql.md)을 참조하세요.

## <a name="migration-recommendations"></a>마이그레이션 권장 사항

Azure SQL Database로의 마이그레이션을 가속화하려면 다음과 같은 권장 사항을 고려해야 합니다.

|  | 리소스 경합 | 권장 |
|--|--|--|
| **원본(일반적으로 온-프레미스)** |원본에서 마이그레이션하는 동안 발생하는 기본 병목 상태는 신중한 모니터링이 필요한 데이터 파일의 데이터 I/O 및 대기 시간입니다.  |데이터 IO 및 데이터 파일 대기 시간을 기반으로 또한 가상 머신인지 물리적 서버인지에 따라 스토리지 관리자와 협력하여 병목 상태를 완화할 옵션을 탐색해야 합니다. |
|**대상(Azure SQL Database)**|가장 큰 제한 요소는 로그 생성 속도 및 로그 파일에 대한 대기 시간입니다. Azure SQL Database에서는 최대 96MB/s의 로그 생성 속도를 얻을 수 있습니다. | 마이그레이션 속도를 높이려면 대상 SQL DB를 중요 비즈니스용 Gen5 8 vCore로 확장하여 최대 로그 생성 속도를 96MB/s로 설정하고 로그 파일에 대한 짧은 대기 시간을 확보합니다. [하이퍼스케일](../../database/service-tier-hyperscale.md) 서비스 계층은 선택한 서비스 수준과 관계없이 100MB/s의 로그 속도를 제공합니다. |
|**Network** |필요한 네트워크 대역폭은 최대 로그 수집 속도 96MB/s(768Mb/s)와 동일합니다. |온-프레미스 데이터 센터에서 Azure로의 네트워크 연결에 따라 최대 로그 수집 속도를 수용할 수 있는 네트워크 대역폭(일반적으로 [Azure ExpressRoute](../../../expressroute/expressroute-introduction.md#bandwidth-options))을 확인합니다. |
|**DMA(Data Migration Assistant)에 사용되는 가상 머신** |CPU가 DMA를 실행하는 가상 머신의 기본 병목 상태입니다. |데이터 마이그레이션 속도를 높이기 위해 고려할 사항 </br>- Azure 컴퓨팅 집약적 VM을 사용 </br>- DMA를 실행하는 데 F8s_v2(8 vcore) VM을 사용 </br>- VM을 대상과 동일한 Azure 지역에서 실행 |
|**Azure DMS(Database Migration Service)** |DMS에 대한 컴퓨팅 리소스 경합 및 데이터베이스 개체 고려 사항 |프리미엄 4 vCore를 사용합니다. DMS는 외래 키, 트리거, 제약 조건 및 비클러스터형 인덱스와 같은 데이터베이스 개체를 자동으로 처리하므로 수동 작업이 필요하지 않습니다.  |


## <a name="post-migration"></a>마이그레이션 후 작업

마이그레이션 단계를 성공적으로 완료한 후 모든 것이 원활하고 효율적으로 작동하게 하려면 일련의 마이그레이션 후 작업을 수행해야 합니다. 

마이그레이션 후 단계는 데이터 정확도 문제를 조정하고 완성도를 확인할 뿐만 아니라 워크로드 관련 성능 문제를 해결하는 데 매우 중요합니다. 

### <a name="remediate-applications"></a>애플리케이션 수정 

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이를 위해 애플리케이션을 변경해야 하는 경우도 있습니다.

### <a name="perform-tests"></a>테스트 수행

데이터베이스 마이그레이션의 테스트 접근 방식은 다음 작업으로 구성됩니다.

1. **유효성 검사 테스트 개발**: 데이터베이스 마이그레이션을 테스트하려면 SQL 쿼리를 사용해야 합니다. 원본 데이터베이스와 대상 데이터베이스 둘 다에서 실행할 유효성 검사 쿼리를 만들어야 합니다. 유효성 검사 쿼리는 정의한 범위를 포함해야 합니다.
1. **테스트 환경 설정**: 테스트 환경에는 원본 데이터베이스와 대상 데이터베이스의 복사본이 포함되어야 합니다. 테스트 환경을 격리해야 합니다.
1. **유효성 검사 테스트 실행**: 원본 및 대상에서 유효성 검사 테스트를 실행한 다음, 결과를 분석합니다.
1. **성능 테스트 실행**: 원본 및 대상에서 성능 테스트를 실행한 다음, 결과를 분석하고 비교합니다.


## <a name="leverage-advanced-features"></a>고급 기능 활용 

SQL Database에서 제공하는 고급 클라우드 기반 기능(예:[기본 제공되는 고가용성](../../database/high-availability-sla.md), [위협 검색](../../database/azure-defender-for-sql.md), [워크로드 모니터링 및 튜닝](../../database/monitor-tune-overview.md))을 활용하는 것이 좋습니다. 

일부 SQL Server 기능은 [데이터베이스 호환성 수준](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)이 최신 호환성 수준(150)으로 변경된 후에만 사용할 수 있습니다. 

자세히 알아보려면 [마이그레이션 후 Azure SQL Database 관리](../../database/manage-data-after-migrating-to-database.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

- 다양한 데이터베이스 및 데이터 마이그레이션 시나리오와 특수 작업을 지원하는 데 사용할 수 있는 Microsoft와 타사의 서비스 및 도구 표는 [데이터 마이그레이션에 사용할 수 있는 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조하세요.


- [Azure Migrate](https://azure.microsoft.com/services/azure-migrate)에 대해 자세히 알아보려면 다음을 참조하세요.
   - [Azure Migrate](../../../migrate/migrate-services-overview.md)

- SQL Database에 대해 자세히 알아보려면 다음을 참조하세요.
    - [Azure SQL Database 개요](../../database/sql-database-paas-overview.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 


- 클라우드 마이그레이션의 프레임워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure로 마이그레이션하기 위한 워크로드의 비용 계산 및 크기 조정 모범 사례](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [클라우드 마이그레이션 리소스](https://azure.microsoft.com/migration/resources)

- 애플리케이션 액세스 레이어를 평가하려면 [Data Access Migration Toolkit(미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)을 참조하세요.
- 데이터 액세스 레이어 A/B 테스트를 수행하는 방법에 관한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조하세요.