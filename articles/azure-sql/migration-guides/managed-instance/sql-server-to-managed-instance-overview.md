---
title: SQL Managed Instance SQL Server-마이그레이션 개요
description: SQL Server 데이터베이스를 Azure SQL Managed Instance로 마이그레이션하는 데 사용할 수 있는 다양 한 도구와 옵션에 대해 알아봅니다.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 211ad590ab01d0be26d799064e1227accc619585
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94497366"
---
# <a name="migration-overview-sql-server-to-sql-managed-instance"></a>마이그레이션 개요: SQL Managed Instance SQL Server
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

SQL Server를 Azure SQL Managed Instance로 마이그레이션하기 위한 다양 한 마이그레이션 옵션 및 고려 사항에 대해 알아봅니다. 

온-프레미스 또는에서 실행 중인 SQL Server를 마이그레이션할 수 있습니다. 

- Virtual Machines의 SQL Server  
- Amazon Web Services (AWS) EC2 
- AWS RDS (Amazon 관계형 데이터베이스 서비스) 
- 계산 엔진 (Google Cloud Platform GCP)  
- SQL Server에 대 한 클라우드 SQL (Google Cloud Platform – GCP) 

다른 시나리오는 [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조 하세요. 

## <a name="overview"></a>개요

[AZURE SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md) 는 가상 컴퓨터 또는 해당 운영 체제를 관리할 필요 없이 완전히 관리 되는 서비스가 필요한 SQL Server 워크 로드에 권장 되는 대상 옵션입니다. SQL Managed Instance를 사용 하면 기본 VNet (가상 네트워크) 지원을 사용 하 여 인스턴스를 완전히 격리 하면서 응용 프로그램 또는 데이터베이스를 최소한으로 변경 하 여 온-프레미스 응용 프로그램을 Azure로 리프트 앤 시프트 할 수 있습니다. 

## <a name="considerations"></a>고려 사항 

마이그레이션 옵션을 평가할 때 고려해 야 할 주요 요소는 다음에 따라 달라 집니다. 
- 서버 및 데이터베이스 수
- 데이터베이스 크기
- 마이그레이션 프로세스 중에 허용 되는 비즈니스 가동 중지 시간 

Sql Server를 SQL Managed Instance로 마이그레이션하는 주요 이점 중 하나는 전체 인스턴스 또는 개별 데이터베이스의 하위 집합만 마이그레이션하도록 선택할 수 있다는 것입니다. 마이그레이션 프로세스에 다음을 포함 하도록 신중 하 게 계획 합니다. 
- 동일한 인스턴스에 공동 배치 해야 하는 모든 데이터베이스 
- 로그인, 자격 증명, SQL 에이전트 작업 및 연산자 및 서버 수준 트리거를 포함 하 여 응용 프로그램에 필요한 인스턴스 수준 개체입니다. 

> [!NOTE]
> Azure SQL Managed Instance는 중요 한 시나리오 에서도 99.99%의 가용성을 보장 하므로 SQL MI의 일부 기능으로 인해 발생 하는 오버 헤드는 사용 하지 않도록 설정할 수 없습니다. 자세한 내용은 [SQL Server 및 AZURE SQL Managed Instance 블로그에서 다른 성능을 발생 시킬 수 있는 근본 원인](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) 을 참조 하세요. 


## <a name="choose-appropriate-target"></a>적절 한 대상 선택

[성능 기준선](sql-server-to-managed-instance-performance-baseline.md)을 일치 시킬 수 있도록 SQL Managed Instance의 적절 한 서비스 계층 및 특성을 선택 하는 데 도움이 되는 몇 가지 일반적인 지침입니다. 

- CPU 사용량 기준을 사용 하 여 SQL Server 인스턴스에서 사용 하는 코어 수와 일치 하는 관리 되는 인스턴스를 프로 비전 합니다. [하드웨어 생성 특성](../../managed-instance/resource-limits.md#hardware-generation-characteristics)에 맞게 리소스 크기를 조정 해야 할 수 있습니다. 
- 메모리 사용 기준을 사용 하 여 메모리 할당과 적절 하 게 일치 하는 [Vcore 옵션](../../managed-instance/resource-limits.md#service-tier-characteristics) 을 선택 합니다. 
- 파일 하위 시스템의 기준 IO 대기 시간을 사용 하 여 일반적인 용도 (5 밀리초를 초과 하는 대기 시간)와 중요 비즈니스용 (3 밀리초 미만의 대기 시간) 서비스 계층을 선택 합니다. 
- 기본 처리량을 사용 하 여 데이터 및 로그 파일의 크기를 미리 할당 하 여 예상 IO 성능을 달성할 수 있습니다. 

배포 중에 계산 및 저장소 리소스를 선택한 다음 응용 프로그램에 대 한 가동 중지 시간 없이 [Azure Portal](../../database/scale-resources.md) 사용 하 여 변경할 수 있습니다. 

> [!IMPORTANT]
> [관리 되는 인스턴스 가상 네트워크 요구 사항의](/../../managed-instance/connectivity-architecture-overview.md#network-requirements) 차이로 인해 새 인스턴스를 만들거나 기존 인스턴스를 사용 하지 못할 수 있습니다.  [새 네트워크 만들기](/../../managed-instance/virtual-network-subnet-create-arm-template?branch=release-ignite-arc-data)   및 기존 네트워크 [구성](/../../managed-instance/vnet-existing-add-subnet?branch=release-ignite-arc-data)에 대해 자세히 알아보세요   . 

### <a name="sql-server-vm-alternative"></a>SQL Server VM 대체

Azure Vm에서 Azure SQL Managed Instance 보다 더 적합 한 대상에 SQL Server 하는 요구 사항이 비즈니스에 있을 수 있습니다. 

다음이 비즈니스에 적용 되는 경우 대신 SQL Server VM로 이동 하는 것이 좋습니다. 

- 운영 체제 또는 파일 시스템에 직접 액세스 해야 하는 경우 (예: SQL Server을 사용 하는 동일한 가상 컴퓨터에 타사 또는 사용자 지정 에이전트를 설치 하는 경우) 
- FileStream/FileTable, PolyBase 및 인스턴스 간 트랜잭션과 같이 아직 지원 되지 않는 기능에 대해 엄격한 종속성이 있는 경우 
- 특정 버전의 SQL Server을 유지 해야 하는 경우 (예를 들어 2012) 
- 계산 요구 사항이 관리 되는 인스턴스 (예를 들어 vCore 하나)에서 제공 하는 것 보다 훨씬 더 낮은 경우 데이터베이스 통합은 허용 되는 옵션이 아닙니다. 


## <a name="migration-tools"></a>마이그레이션 도구


마이그레이션에 권장 되는 도구는 Data Migration Assistant 및 Azure Database Migration Service입니다. 다른 대체 마이그레이션 옵션도 사용할 수 있습니다. 

### <a name="recommended-tools"></a>권장 도구

다음 표에는 권장 마이그레이션 도구가 나열 되어 있습니다. 

|기술 | 설명|
|---------|---------|
|[Azure DMS(Database Migration Service)](/azure/dms/tutorial-sql-server-to-managed-instance)  | 마이그레이션 프로세스 중 가동 중지 시간을 감당할 수 있는 응용 프로그램에 대해 오프 라인 모드에서 마이그레이션을 지 원하는 자사 Azure 서비스입니다. 온라인 모드의 연속 마이그레이션과 달리 오프 라인 모드 마이그레이션은 원본에서 대상으로 전체 데이터베이스 백업에 대 한 일회성 복원을 실행 합니다. | 
|[네이티브 백업 및 복원](../../managed-instance/restore-sample-database-quickstart.md) | SQL Managed Instance는 기본 SQL Server 데이터베이스 백업 (.bak 파일)의 복원을 지원 하 여 Azure storage에 전체 데이터베이스 백업을 제공할 수 있는 고객에 게 가장 쉬운 마이그레이션 옵션을 제공 합니다. 전체 및 차등 백업은이 문서의 뒷부분에 있는 [마이그레이션 자산 섹션](#migration-assets) 에도 지원 되며 문서화 되어 있습니다.| 
| | |

### <a name="alternative-tools"></a>대체 도구

다음 표에서는 대체 마이그레이션 도구를 보여 줍니다. 

|기술 |설명  |
|---------|---------|
|[트랜잭션 복제](../../managed-instance/replication-transactional-overview.md) | 트랜잭션 일관성을 유지 하면서 게시자-구독자 유형 마이그레이션 옵션을 제공 하 여 원본 SQL Server 데이터베이스 테이블에서 SQL Managed Instance로 데이터를 복제 합니다. |  |
|[대량 복사](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [Bcp (대량 복사 프로그램) 유틸리티](/sql/tools/bcp-utility) 는 SQL Server 인스턴스에서 데이터 파일로 데이터를 복사 합니다. BCP 유틸리티를 사용 하 여 원본에서 데이터를 내보내고 대상 SQL Managed Instance로 데이터 파일을 가져옵니다.</br></br> Azure SQL Database으로 데이터를 이동 하는 고속 대량 복사 작업의 경우, [효율적인 대량 복사 도구](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) 를 사용 하 여 병렬 복사 작업을 활용 하 여 전송 속도를 최대화할 수 있습니다. | 
|[가져오기 내보내기 마법사/BACPAC](/azure/azure-sql/database/database-import?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 는 `.bacpac` 데이터베이스의 스키마와 데이터를 캡슐화 하는 확장명을 포함 하는 Windows 파일입니다. BACPAC를 사용 하 여 원본 SQL Server에서 데이터를 내보내고 Azure SQL Managed Instance에 다시 파일을 가져올 수 있습니다.  |  
|[ADF(Azure Data Factory)](/azure/data-factory/connector-azure-sql-managed-instance)| Azure Data Factory의 [복사 작업](/azure/data-factory/copy-activity-overview) 은 기본 제공 커넥터 및 [Integration Runtime](/azure/data-factory/concepts-integration-runtime)를 사용 하 여 원본 SQL Server 데이터베이스에서 SQL Managed Instance로 데이터를 마이그레이션합니다.</br> </br> ADF는 SQL Server 원본에서 SQL Managed Instance로 데이터를 이동 하는 다양 한 [커넥터](/azure/data-factory/connector-overview) 를 지원 합니다. |
| | |

## <a name="compare-migration-options"></a>마이그레이션 옵션 비교

마이그레이션 옵션을 비교 하 여 비즈니스 요구에 적합 한 경로를 선택 합니다. 

### <a name="recommended-options"></a>권장 옵션

다음 표에서는 권장 되는 마이그레이션 옵션을 비교 합니다. 

|마이그레이션 옵션  |사용 시기  |고려 사항  |
|---------|---------|---------|
|[Azure DMS(Database Migration Service)](/azure/dms/tutorial-sql-server-to-managed-instance) | -단일 데이터베이스나 여러 데이터베이스를 대규모로 마이그레이션합니다. </br> -마이그레이션 프로세스 중 가동 중지 시간을 수용할 수 있습니다. </br> </br> 지원 되는 원본: </br> -SQL Server (2005-2019) 온-프레미스 또는 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 계산 SQL Server VM |  -규모의 마이그레이션은 [PowerShell](/azure/dms/howto-sql-server-to-azure-sql-mi-powershell)을 통해 자동화할 수 있습니다. </br> -마이그레이션을 완료 하는 데 걸리는 시간은 데이터베이스 크기에 따라 다르며 백업 및 복원 시간의 영향을 받습니다. </br> -충분 한 가동 중지 시간이 필요할 수 있습니다. |
|[네이티브 백업 및 복원](../../managed-instance/restore-sample-database-quickstart.md) | -개별 lob (기간 업무) 응용 프로그램 데이터베이스를 마이그레이션합니다.  </br> -별도의 마이그레이션 서비스 또는 도구를 사용 하지 않고 빠르고 쉽게 마이그레이션할 수 있습니다.  </br> </br> 지원 되는 원본: </br> -SQL Server (2005-2019) 온-프레미스 또는 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 계산 SQL Server VM | -데이터베이스 백업은 여러 스레드를 사용 하 여 Azure Blob 저장소로의 데이터 전송을 최적화 하지만 ISV 대역폭과 데이터베이스 크기는 전송 요금에 영향을 줄 수 있습니다. </br> -가동 중지 시간은 전체 백업 및 복원을 수행 하는 데 필요한 시간 (데이터 작업의 크기)을 수용할 수 있어야 합니다.| 
| | | |

### <a name="alternative-options"></a>대체 옵션

다음 표에서는 대체 마이그레이션 옵션을 비교 합니다. 

|메서드/기술 |사용 시기 |고려 사항  |
|---------|---------|---------|
|[트랜잭션 복제](../../managed-instance/replication-transactional-overview.md) | -원본 데이터베이스 테이블의 변경 내용을 대상 SQL Managed Instance 데이터베이스 테이블로 지속적으로 게시 하 여 마이그레이션합니다. </br> -선택한 테이블 (데이터베이스의 하위 집합)의 전체 또는 부분 데이터베이스 마이그레이션  </br> </br> 지원 되는 원본: </br> -SQL Server (2012-2019), 몇 가지 제한 사항이 있음 </br> -AWS EC2  </br> -GCP 계산 SQL Server VM | </br> -설치는 다른 마이그레이션 옵션에 비해 비교적 복잡 합니다.   </br> -데이터베이스를 오프 라인으로 전환 하지 않고 데이터를 마이그레이션하는 연속 복제 옵션을 제공 합니다.</br> -트랜잭션 복제에는 원본 SQL Server에서 게시자를 설정할 때 고려해 야 할 몇 가지 제한 사항이 있습니다. 자세히 알아보려면 [개체 게시에 대 한 제한 사항](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) 을 참조 하세요.  </br> - [복제 작업을 모니터링](/sql/relational-databases/replication/monitor/monitoring-replication) 하는 기능을 사용할 수 있습니다.    |
|[대량 복사](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -전체 또는 부분 데이터 마이그레이션을 마이그레이션합니다. </br> -가동 중지 시간을 수용할 수 있습니다. </br> </br> 지원 되는 원본: </br> -SQL Server (2005-2019) 온-프레미스 또는 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 계산 SQL Server VM   | -원본에서 데이터를 내보내고 대상으로 가져오기 위한 가동 중지 시간이 필요 합니다. </br> -내보내기/가져오기에 사용 되는 파일 형식 및 데이터 형식은 테이블 스키마와 일치 해야 합니다. |
|[가져오기 내보내기 마법사/BACPAC](/azure/azure-sql/database/database-import)| -개별 lob (기간 업무) 응용 프로그램 데이터베이스를 마이그레이션합니다. </br>-작은 데이터베이스에 적합 합니다.  </br>  별도의 마이그레이션 서비스 또는 도구가 필요 하지 않습니다. </br> </br> 지원 되는 원본: </br> -SQL Server (2005-2019) 온-프레미스 또는 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 계산 SQL Server VM  |   </br> -데이터를 원본에서 내보내고 대상에서 가져와야 하므로 가동 중지 시간이 필요 합니다.   </br> -잘림/데이터 형식 불일치 오류를 방지 하기 위해 내보내기/가져오기에 사용 되는 파일 형식 및 데이터 형식이 테이블 스키마와 일치 해야 합니다. </br> -많은 수의 개체를 포함 하는 데이터베이스를 내보내는 데 걸리는 시간이 훨씬 더 높을 수 있습니다. |
|[ADF(Azure Data Factory)](/azure/data-factory/connector-azure-sql-managed-instance)| -원본 SQL Server 데이터베이스에서 데이터를 마이그레이션 및/또는 변환 합니다.</br> -일반적으로 BI (비즈니스 인텔리전스) 작업에 대 한 여러 데이터 원본에서 Azure SQL Managed Instance로 데이터를 병합 합니다.   </br> -원본에서 대상으로 데이터를 이동 하려면 ADF에서 데이터 이동 파이프라인을 만들어야 합니다.   </br> - [비용은](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 중요 한 고려 사항이 며 파이프라인 트리거, 작업 실행, 데이터 이동 기간 등을 기준으로 합니다. |
| | | |

## <a name="feature-interoperability"></a>기능 상호 운용성 

다른 SQL Server 기능을 사용 하는 워크 로드를 마이그레이션하는 경우 추가로 고려해 야 할 사항이 있습니다. 

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services

[DMS (Azure Database Migration Service](/azure/dms/how-to-migrate-ssis-packages-managed-instance))를 사용 하 여 SSISDB의 SQL SERVER INTEGRATION SERVICES (SSIS) 패키지 및 프로젝트를 Azure SQL Managed Instance로 마이그레이션합니다. 

SQL Server 2012부터 SSISDB의 SSIS 패키지만 마이그레이션할 수 있습니다. 마이그레이션하기 전에 레거시 SSIS 패키지를 변환 합니다. 자세히 알아보려면 [프로젝트 변환 자습서](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) 를 참조 하세요. 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

SQL Server Reporting Services (SSRS) 보고서는 Power BI에서 페이지가 매겨진 보고서로 마이그레이션될 수 있습니다.  [RDL 마이그레이션 도구](https://github.com/microsoft/RdlMigration) 를 사용 하 여 보고서를 준비 하 고 마이그레이션하는 데 도움이 됩니다. 이 도구는 고객이 SSRS 서버에서 Power BI로 RDL 보고서를 마이그레이션하는 데 도움을 주기 위해 Microsoft에서 개발한 것입니다. GitHub에서 사용할 수 있으며 마이그레이션 시나리오에 대 한 종단 간 연습을 문서화 합니다. 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server 2012 이상에서 SQL Server Analysis Services 테이블 형식 모델은 Azure의 Analysis Services 테이블 형식 모델에 대 한 PaaS 배포 모델인 Azure Analysis Services로 마이그레이션할 수 있습니다. 프레미스 모델을 마이그레이션하는 방법에 대 한 자세한 내용은이 [비디오 자습서](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/)에서 Azure Analysis Services.

또는 [새로운 XMLA 읽기/쓰기 끝점을 사용 하 여](https://docs.microsoft.com/power-bi/admin/service-premium-connect-tools)온-프레미스 Analysis Services 테이블 형식 모델을 Power BI Premium로 마이그레이션할 수도 있습니다. 
> [!NOTE]
> Power BI XMLA 읽기/쓰기 끝점 기능은 현재 공개 미리 보기로 제공 되며 기능이 일반 공급 될 때까지 프로덕션 워크 로드에 대해 고려 하지 않아야 합니다.

#### <a name="high-availability"></a>고가용성

고가용성 아키텍처는 [일반 용도 (표준 가용성 모델)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) 와 [중요 비즈니스용 (프리미엄 가용성 모델)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) SQL Managed Instance 둘 다에 이미 내장 되어 있으므로 장애 조치 (failover) 클러스터 인스턴스를 Always On 하는 SQL Server 고가용성 기능이 Always On 가용성 그룹 대상 Azure sql Managed Instance에서 사용 되지 않습니다. 프리미엄 가용성 모델은 읽기 전용 용도로 보조 노드 중 하나에 연결할 수 있는 읽기 확장도 제공 합니다.     

SQL Managed Instance에 포함 된 고가용성 아키텍처 외에도 관리 되는 인스턴스의 데이터베이스 복제 및 장애 조치 (failover)를 다른 지역으로 관리할 수 있는 [자동 장애 조치 (failover) 그룹](../../database/auto-failover-group-overview.md) 기능도 있습니다. 

#### <a name="sql-agent-jobs"></a>SQL 에이전트 작업

[SQL 에이전트 작업](/azure/dms/howto-sql-server-to-azure-sql-mi-powershell#offline-migrations)을 마이그레이션하려면 DMS (offline Azure Database Migration Service) 옵션을 사용 합니다. 그렇지 않으면 SQL Server Management Studio를 사용 하 여 Transact-sql (T-sql)에서 작업을 스크립트 한 다음 대상 SQL Managed Instance에서 수동으로 다시 만듭니다. 

> [!IMPORTANT]
> 현재 Azure DMS는 T-sql 하위 시스템 단계로 작업을 지원 합니다. SSIS 패키지 단계를 사용 하는 작업은 수동으로 마이그레이션해야 합니다. 

#### <a name="logins-and-groups"></a>로그인 및 그룹

원본 SQL Server의 SQL 로그인은 오프 라인 모드에서 DMS (Database Migration Service)를 사용 하 여 Azure SQL Managed Instance로 이동할 수 있습니다.  **마이그레이션 마법사** 의 **[로그인 선택](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)** 블레이드를 사용 하 여 대상 SQL Managed Instance 로그인을 마이그레이션합니다. 

기본적으로 Azure Database Migration Service는 SQL 로그인 마이그레이션만 지원합니다. 하지만 다음과 같은 방법으로 Windows 로그인을 마이그레이션하는 기능을 사용하도록 설정할 수 있습니다.

대상 SQL Managed Instance에 Azure AD 읽기 액세스 권한이 있는지 확인 합니다 .이 액세스는 **회사 관리자** 또는 **전역 관리자** 역할을 가진 사용자가 Azure Portal를 통해 구성할 수 있습니다.
Windows 사용자/그룹 로그인 마이그레이션이 가능하도록 Azure Database Migration Service 인스턴스를 구성합니다. 이 구성은 Azure Portal을 통해 구성 페이지에서 설정할 수 있습니다. 이 설정을 사용하도록 설정한 후 서비스를 다시 시작하면 변경 사항이 적용됩니다.

서비스를 다시 시작하면 마이그레이션에 사용할 수 있는 로그인 목록에 Windows 사용자/그룹 로그인이 나타납니다. 마이그레이션하는 Windows 사용자/그룹 로그인에 대해서는 연결된 도메인 이름을 입력하라는 메시지가 표시됩니다. 서비스 사용자 계정(도메인 이름이 NT AUTHORITY인 계정) 및 가상 사용자 계정(도메인 이름이 NT SERVICE인 계정 이름)은 지원 되지 않습니다.

자세히 알아보려면 [t-sql을 사용 하 여 SQL Server 인스턴스에서 windows 사용자 및 그룹을 AZURE SQL Managed Instance로 마이그레이션하는 방법](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md)을 참조 하세요.

또는 Microsoft 데이터 마이그레이션 설계자가 특별히 설계한 [PowerShell 유틸리티 도구](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) 를 사용할 수 있습니다. 유틸리티는 PowerShell을 사용 하 여 T-sql 스크립트를 만들어 로그인을 다시 만들고 원본에서 대상으로 데이터베이스 사용자를 선택 합니다. 이 도구는 자동으로 Windows AD 계정을 Azure AD 계정에 매핑하고 원본 Active Directory에 대 한 각 로그인에 대해 UPN 조회를 수행할 수 있습니다. 이 도구는 역할 멤버 자격, 데이터베이스 역할 및 사용자 권한 뿐만 아니라 사용자 지정 서버 및 데이터베이스 역할을 스크립팅 합니다. 포함 된 데이터베이스는 현재 지원 되지 않으며 가능한 SQL Server 권한 하위 집합만 스크립팅됩니다. 

#### <a name="encryption"></a>암호화

 [투명한 데이터 암호화](../../database/transparent-data-encryption-tde-overview.md)로 보호 되는 데이터베이스를   네이티브 복원 옵션을 사용 하 여 관리 되는 인스턴스로 마이그레이션하는 경우 원본 SQL Server의 [해당 인증서](../../managed-instance/tde-certificate-migrate.md) 를 데이터베이스 복원 *전에* 대상 SQL Managed Instance로 마이그레이션합니다. 

#### <a name="system-databases"></a>시스템 데이터베이스

시스템 데이터베이스의 복원은 지원되지 않습니다. Master 또는 msdb 데이터베이스에 저장 된 인스턴스 수준 개체를 마이그레이션하려면 Transact-sql (T-sql)을 사용 하 여 스크립트를 작성 한 다음 대상 관리 되는 인스턴스에서 다시 만듭니다. 

## <a name="leverage-advanced-features"></a>고급 기능 활용 

SQL Managed Instance에서 제공 하는 고급 클라우드 기반 기능을 활용 해야 합니다. 예를 들어 서비스에서 백업 관리에 대해 더 이상 걱정할 필요가 없습니다. [보존 기간 내의 특정 시점](../../database/recovery-using-backups.md#point-in-time-restore)으로 복원할 수 있습니다. 또한 고가용성 [이 기본적으로 제공 되므로](../../database/high-availability-sla.md)고가용성을 설정 하는 것에 대해 걱정할 필요가 없습니다. 

보안을 강화 하려면 [Azure Active Directory 인증](../../database/authentication-aad-overview.md), [감사](../../managed-instance/auditing-configure.md), [위협 검색](../../database/advanced-data-security.md), [행 수준 보안](/sql/relational-databases/security/row-level-security)및 [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking)을 사용 하는 것이 좋습니다.

고급 관리 및 보안 기능 외에도 SQL Managed Instance는 [워크 로드를 모니터링 하 고 조정](../../database/monitor-tune-overview.md)하는 데 도움이 되는 고급 도구 집합을 제공 합니다. [Azure SQL 분석](../../../azure-monitor/insights/azure-sql.md) 를 사용 하 여 중앙 집중식으로 관리 되는 인스턴스의 많은 집합을 모니터링할 수 있습니다.  [자동 조정](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   에서 관리 되는 인스턴스는 SQL 계획 실행 통계의 성능을 지속적으로 모니터링 하 고 식별 된 성능 문제를 자동으로 해결 합니다. 

일부 기능은 [데이터베이스 호환성 수준이](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) 최신 호환성 수준 (150)으로 변경 된 후에만 사용할 수 있습니다. 

## <a name="migration-assets"></a>마이그레이션 자산 

추가 지원이 필요한 경우 실제 마이그레이션 프로젝트용으로 개발 된 다음 리소스를 참조 하세요.

|자산  |Description  |
|---------|---------|
|[데이터 워크로드 평가 모델 및 도구](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| 이 도구는 권장되는 “최적의” 대상 플랫폼, 클라우드 준비 상태 및 애플리케이션/데이터베이스 수정 수준을 지정된 워크로드에 제공합니다. 또한 자동화되고 균일한 대상 플랫폼 결정 프로세스를 제공하여 대규모 자산 평가를 가속화하는 데 도움이 되는 간단한 원클릭 계산 및 보고서 생성 기능을 제공합니다.|
|[DBLoader 유틸리티](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader는 분리 된 텍스트 파일의 데이터를 SQL Server 로드 하는 데 사용할 수 있습니다. 이 Windows 콘솔 유틸리티는 SQL Server native client bulkload 인터페이스를 사용 합니다 .이 인터페이스는 Azure SQL MI를 비롯 한 모든 SQL Server 버전에서 작동 합니다.|
|[온-프레미스 SQL Server 로그인을 Azure SQL Managed Instance 이동 하는 유틸리티](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|로그인을 다시 만들고 온-프레미스 SQL Server에서 Azure SQL Managed Instance로 데이터베이스 사용자를 선택 하는 T-sql 명령 스크립트를 만드는 PowerShell 스크립트입니다. 이 도구를 사용 하면 Azure AD 계정에 대 한 Windows AD 계정을 자동으로 매핑하고 선택적으로 SQL Server 네이티브 로그인을 마이그레이션할 수 있습니다.|
|[Logman를 사용 하 여 Perfmon 데이터 수집 자동화](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|마이그레이션 대상 권장 사항에 도움이 되는 기준 성능을 이해 하기 위해 데이터를 수집 하는 도구입니다. 이 도구는 logman.exe을 사용 하 여 원격 SQL Server에 설정 된 성능 카운터를 만들고, 시작 하 고, 중지 하 고, 삭제 하는 명령을 만듭니다.|
|[백서-전체 및 차등 백업을 복원 하 여 Azure SQL Managed Instance로 데이터베이스 마이그레이션](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|이 백서에서는 전체 및 차등 백업 (로그 백업 기능 없음)만 있는 경우 SQL Server에서 Azure SQL Managed Instance로의 마이그레이션을 가속화 하는 데 도움이 되는 지침과 단계를 제공 합니다.|

위 리소스는 Azure 데이터 그룹 엔지니어링 팀에서 후원하는 Data SQL Ninja 프로그램의 일부로 개발되었습니다. Data SQL Ninja 프로그램의 핵심 선언은 복잡한 현대화의 장애물을 제거하고 속도를 높이며 Microsoft의 Azure 데이터 플랫폼에 대한 데이터 플랫폼 마이그레이션 기회를 놓고 경쟁하는 것입니다. 조직이 Data SQL Ninja 프로그램에 참여하는 데 관심이 있다고 생각되면 계정 팀에 문의하여 추천서를 제출하도록 요청하세요.


## <a name="next-steps"></a>다음 단계

Azure SQL Managed Instance에 대 한 SQL Server 마이그레이션을 시작 하려면 [SQL SERVER sql Managed Instance 마이그레이션 가이드](sql-server-to-managed-instance-guide.md)를 참조 하세요.

- 다양 한 데이터베이스 및 데이터 마이그레이션 시나리오와 전문 작업을 지 원하는 데 사용할 수 있는 Microsoft 및 타사 서비스 및 도구의 행렬은 [데이터 마이그레이션을 위한 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조 하세요.

- Azure SQL Managed Instance에 대 한 자세한 내용은 다음을 참조 하세요.
   - [Azure SQL Managed Instance의 서비스 계층](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server와 Azure SQL Managed Instance의 차이점](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 


- 클라우드 마이그레이션의 프레임 워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조 하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [작업의 비용 및 크기 조정 작업에 대 한 모범 사례 Azure로 마이그레이션](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- 응용 프로그램 액세스 계층을 평가 하려면 [데이터 액세스 마이그레이션 도구 키트 (미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) 를 참조 하세요.
- 데이터 액세스 계층 A/B 테스트를 수행 하는 방법에 대 한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조 하세요.
