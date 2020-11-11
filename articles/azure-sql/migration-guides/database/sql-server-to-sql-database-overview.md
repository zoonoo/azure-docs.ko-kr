---
title: SQL Database로 SQL Server-마이그레이션 개요
description: SQL Server 데이터베이스를 Azure SQL Database으로 마이그레이션하는 데 사용할 수 있는 다양 한 도구와 옵션에 대해 알아봅니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: cafb32e5bd91c6b7f3cfef4641828963e0731797
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94497387"
---
# <a name="migration-overview-sql-server-to-sql-database"></a>마이그레이션 개요: SQL Database SQL Server
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

SQL Server를 Azure SQL Database으로 마이그레이션하기 위한 다양 한 마이그레이션 옵션 및 고려 사항에 대해 알아봅니다. 

온-프레미스 또는에서 실행 중인 SQL Server를 마이그레이션할 수 있습니다. 

- Virtual Machines의 SQL Server  
- Amazon Web Services (AWS) EC2 
- AWS RDS (Amazon 관계형 데이터베이스 서비스) 
- 계산 엔진 (Google Cloud Platform GCP)  
- SQL Server에 대 한 클라우드 SQL (Google Cloud Platform – GCP) 

다른 시나리오는 [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조 하세요. 

## <a name="overview"></a>개요

[Azure SQL Database](../../database/sql-database-paas-overview.md) 는 완전히 관리 되는 PaaS (Platform As a Service)를 필요로 하는 SQL Server 워크 로드에 권장 되는 대상 옵션입니다. SQL Database은 다양 한 응용 프로그램 유형에 맞게 기본 제공 되는 고가용성, 지능형 쿼리 처리, 확장성 및 성능 기능과 함께 대부분의 데이터베이스 관리 기능을 처리 합니다. 

SQL Database는 여러 유형의 응용 프로그램 또는 워크 로드를 제공 하는 여러 [배포 모델](../../database/sql-database-paas-overview.md#deployment-models) 및 [서비스 계층](../../database/service-tiers-vcore.md#service-tiers) 에 유연성을 제공 합니다.

SQL Database로 마이그레이션할 경우의 주요 이점 중 하나는 PaaS 기능을 활용 하 여 응용 프로그램을 현대화 SQL 에이전트 작업과 같은 인스턴스 수준으로 범위가 지정 된 기술 구성 요소에 대 한 종속성을 제거 하는 것입니다.

SQL Server 온-프레미스 라이선스를 Azure SQL Database으로 마이그레이션하여 [Vcore 기반 구매 모델](../../database/service-tiers-vcore.md)을 선택 하는 SQL Server [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 를 사용 하 여 비용을 절감할 수도 있습니다.

이 가이드에서는 SQL Server 데이터베이스를 Azure SQL Database으로 마이그레이션하기 위해 준비 하는 경우 마이그레이션 옵션과 고려 사항을 설명 합니다.  

## <a name="considerations"></a>고려 사항 

마이그레이션 옵션을 평가할 때 고려해 야 할 주요 요소는 다음에 따라 달라 집니다. 

- 서버 및 데이터베이스 수
- 데이터베이스 크기
- 마이그레이션 프로세스 중에 허용 되는 비즈니스 가동 중지 시간 

이 가이드에 나열 된 마이그레이션 옵션은 이러한 요인을 고려 합니다. Azure SQL Database에 대 한 논리적 데이터 마이그레이션의 경우 마이그레이션 시간은 데이터베이스의 개체 수와 데이터베이스 크기에 따라 다를 수 있습니다. 

다양 한 도구를 다양 한 워크 로드 및 사용자 기본 설정에 사용할 수 있습니다. 일부 도구는 UI 기반 도구를 사용 하 여 단일 데이터베이스의 신속한 마이그레이션을 수행 하는 데 사용할 수 있는 반면, 다른 도구는 대규모 마이그레이션을 처리 하기 위해 자동화 될 수 있는 여러 데이터베이스를 마이그레이션할 수 있습니다. 

## <a name="choose-appropriate-target"></a>적절 한 대상 선택

Azure SQL Database의 올바른 배포 모델 및 서비스 계층을 선택 하는 데 도움이 되는 일반적인 지침을 고려 합니다. 배포 중에 계산 및 저장소 리소스를 선택 하 고 나중에 응용 프로그램에 대 한 가동 중지 시간 없이  [Azure Portal](../../database/scale-resources.md)  사용 하 여 변경할 수 있습니다.


**배포 모델** : 단일 데이터베이스 또는 탄력적 풀을 결정 하는 데 사용 되는 응용 프로그램 워크 로드 및 사용 패턴을 이해 합니다. 

- [단일 데이터베이스](../../database/single-database-overview.md) 는 대부분의 최신 클라우드 응용 프로그램 및 마이크로 서비스에 적합 한 완전히 관리 되는 데이터베이스를 나타냅니다.
- [탄력적 풀](../../database/elastic-pool-overview.md) 은 CPU 또는 메모리와 같은 공유 리소스 집합을 포함 하는 단일 데이터베이스 컬렉션으로, 동일한 리소스 집합을 효과적으로 공유할 수 있는 예측 가능한 사용 패턴을 사용 하 여 풀의 데이터베이스를 결합 하는 데 적합 합니다.

**모델 구매** : VCORE, DTU 또는 서버를 사용 하지 않는 구매 모델 중에서 선택 합니다. 

- [Vcore 모델](../../database/service-tiers-vcore.md) 을 사용 하면 Azure SQL Database에 대 한 vcore 수를 선택 하 여 온-프레미스 SQL Server에서 변환할 때 가장 쉽게 선택할 수 있습니다. 이 옵션은 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)를 사용 하 여 라이선스 비용을 절감할 수 있도록 지원 합니다. 
- [Dtu 모델](../../database/service-tiers-dtu.md) 은 혼합 DTU를 제공 하기 위해 기본 계산, 메모리 및 IO 리소스를 추상화 합니다. 
- [서버 리스 모델](../../database/serverless-tier-overview.md) 은 사용량의 초당 청구 되는 계산 리소스와 함께 자동 주문형 확장을 요구 하는 워크 로드를 위한 것입니다. 서버를 사용 하지 않는 계산 계층은 비활성 기간 (저장소 요금이 청구 되는 경우) 동안 데이터베이스를 자동으로 일시 중지 하 고 작업이 반환 될 때 데이터베이스를 자동으로 다시 시작 

**서비스 계층** : 다양 한 유형의 응용 프로그램에 대해 디자인 된 세 개의 서비스 계층 중에서 선택 합니다.

- 범용 [/표준 서비스 계층](../../database/service-tier-general-purpose.md) 은 중간 하위 계층 응용 프로그램을 제공 하는 데 적합 한 계산 및 저장소를 사용 하 여 분산 된 예산 기반 옵션을 제공 합니다 .이 옵션은 저장소 계층에서 중복성을 제공 하 여 오류를 복구 합니다. 대부분의 데이터베이스 작업을 위해 설계 되었습니다. 
- [중요 비즈니스용/프리미엄 서비스 계층](../../database/service-tier-business-critical.md) 은 높은 트랜잭션 속도, 짧은 대기 시간 IO 및 장애 조치 (failover) 및 읽기 작업을 오프 로드 하는 데 사용할 수 있는 보조 복제본을 포함 하는 높은 수준의 복원 력을 필요로 하는 높은 계층 응용 프로그램용입니다.
- [Hyperscale service 계층](../../database/service-tier-hyperscale.md) 은 데이터 볼륨이 증가 하는 데이터베이스에 대 한 것으로, 자동으로 100 TB의 데이터베이스 크기를 확장 해야 합니다. 매우 큰 데이터베이스용으로 설계 되었습니다. 

> [!IMPORTANT]
> [트랜잭션 로그 속도는](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) 높은 수집 속도를 제한 하는 Azure SQL Database에 따라 결정 됩니다. 따라서 마이그레이션하는 동안 CPU 또는 처리량을 쉽게 하기 위해 대상 데이터베이스 리소스 (vCores/Dtu)를 확장 해야 할 수도 있습니다. 적절 한 크기의 대상 데이터베이스를 선택 하 고 필요한 경우 마이그레이션에 대 한 리소스의 크기를 조정 하도록 계획 합니다. 


### <a name="sql-server-on-azure-vm-alternative"></a>Azure VM 대체 SQL Server

비즈니스에는 Azure SQL Database 보다 더 적합 한 대상 [Virtual Machines Azure에서 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) 하는 요구 사항이 있을 수 있습니다. 

다음이 비즈니스에 적용 되는 경우 대신 Azure VM에서 SQL Server로 이동 하는 것이 좋습니다. 

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
|[DMA(Data Migration Assistant)](/sql/dma/dma-migrateonpremsqltosqldb)|Data Migration Assistant는 Azure SQL Database (스키마와 데이터 모두)로의 SQL Server 및 마이그레이션에 대 한 원활한 평가를 제공 하는 데스크톱 도구입니다. 이 도구는 원본 데이터베이스에 연결 된 서버 온-프레미스 또는 로컬 컴퓨터에 설치할 수 있습니다. 마이그레이션 프로세스는 원본 데이터베이스와 대상 데이터베이스의 개체 간 논리적 데이터 이동입니다. </br> -단일 데이터베이스 마이그레이션 (스키마 및 데이터 모두)|
|[Azure DMS(Database Migration Service)](../../../dms/tutorial-sql-server-to-azure-sql.md)|Azure Portal 또는 PowerShell을 사용 하 여 Azure SQL Database으로 SQL Server 데이터베이스를 마이그레이션할 수 있는 자사 Azure 서비스입니다. Azure DMS를 사용 하려면 프로 비전 중에 기본 설정 된 VNet (Azure Virtual Network)을 선택 하 여 원본 SQL Server 데이터베이스에 연결 되어 있는지 확인 해야 합니다. </br> -단일 데이터베이스 또는 대규모로 마이그레이션 |
| | |


### <a name="alternative-tools"></a>대체 도구

다음 표에서는 대체 마이그레이션 도구를 보여 줍니다. 

|기술 |설명  |
|---------|---------|
|[트랜잭션 복제](../../database/replication-to-sql-database.md)|트랜잭션 일관성을 유지 하면서 게시자-구독자 유형 마이그레이션 옵션을 제공 하 여 원본 SQL Server 데이터베이스 테이블에서 SQL Database로 데이터를 복제 합니다. 증분 데이터 변경 내용은 게시자에서 발생 하는 구독자로 전파 됩니다.|
|[가져오기/내보내기 서비스/BACPAC](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 는 `.bacpac` 데이터베이스의 스키마와 데이터를 캡슐화 하는 확장명을 포함 하는 Windows 파일입니다. BACPAC를 사용 하 여 원본 SQL Server에서 데이터를 내보내고 Azure SQL Database 데이터를 가져올 수 있습니다. BACPAC 파일은 Azure Portal를 사용 하 여 새 Azure SQL Database 가져올 수 있습니다. </br></br> 대규모 데이터베이스 크기 또는 많은 수의 데이터베이스를 사용 하는 규모 및 성능에 대해 [SqlPackage](../../database/database-import.md#using-sqlpackage) 명령줄 유틸리티를 사용 하 여 데이터베이스를 내보내고 가져오는 것이 좋습니다.|
|[대량 복사](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|[Bcp (대량 복사 프로그램) 유틸리티](/sql/tools/bcp-utility) 는 SQL Server 인스턴스에서 데이터 파일로 데이터를 복사 합니다. BCP 유틸리티를 사용 하 여 원본에서 데이터를 내보내고 대상 SQL Database 데이터 파일을 가져옵니다. </br></br> Azure SQL Database으로 데이터를 이동 하는 고속 대량 복사 작업의 경우, [효율적인 대량 복사 도구](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) 를 사용 하 여 병렬 복사 작업을 활용 하 여 전송 속도를 최대화할 수 있습니다.|
|[ADF(Azure Data Factory)](../../../data-factory/connector-azure-sql-database.md)|Azure Data Factory의 [복사 작업](../../../data-factory/copy-activity-overview.md) 은 기본 제공 커넥터 및 [Integration Runtime](../../../data-factory/concepts-integration-runtime.md)를 사용 하 여 원본 SQL Server 데이터베이스의 데이터를 SQL Database로 마이그레이션합니다.</br> </br> ADF는 SQL Server 원본에서 SQL Database로 데이터를 이동 하는 다양 한 범위의 [커넥터](../../../data-factory/connector-overview.md) 를 지원 합니다.|
|[SQL 데이터 동기화](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL 데이터 동기화은 사용자가 선택한 데이터를 온-프레미스와 클라우드 모두에서 여러 데이터베이스에 걸쳐 동기화 할 수 있도록 하는 Azure SQL Database 기반 서비스입니다.</br>데이터 동기화는 Azure SQL Database 또는 SQL Server에서 여러 데이터베이스에 걸쳐 데이터를 업데이트 해야 하는 경우에 유용 합니다.|
| | |

## <a name="compare-migration-options"></a>마이그레이션 옵션 비교

마이그레이션 옵션을 비교 하 여 비즈니스 요구에 적합 한 경로를 선택 합니다. 

### <a name="recommended-options"></a>권장 옵션

다음 표에서는 권장 되는 마이그레이션 옵션을 비교 합니다. 

|마이그레이션 옵션  |사용 시기  |고려 사항  |
|---------|---------|---------|
|[DMA(Data Migration Assistant)](/sql/dma/dma-migrateonpremsqltosqldb) | -단일 데이터베이스 (스키마와 데이터 모두)를 마이그레이션합니다.  </br> -데이터 마이그레이션 프로세스 중 가동 중지 시간을 수용할 수 있습니다. </br> </br> 지원 되는 원본: </br> -SQL Server (2005-2019) 온-프레미스 또는 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 계산 SQL Server VM | -마이그레이션 작업은 데이터베이스 개체 (원본에서 대상으로) 간에 데이터 이동을 수행 하므로 사용량이 적은 시간에 실행 하는 것이 좋습니다. </br> -DMA는 마이그레이션된 행 수를 포함 하 여 데이터베이스 개체별 마이그레이션 상태를 보고 합니다.  </br> -큰 마이그레이션의 경우 (데이터베이스 수/데이터베이스 크기) 아래에 나열 된 Azure Database Migration Service를 사용 합니다.|
|[Azure DMS(Database Migration Service)](../../../dms/tutorial-sql-server-to-azure-sql.md)| -단일 데이터베이스 또는 대규모로 마이그레이션 </br> -마이그레이션 프로세스 중 가동 중지 시간을 수용할 수 있습니다. </br> </br> 지원 되는 원본: </br> -SQL Server (2005-2019) 온-프레미스 또는 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 계산 SQL Server VM | -규모의 마이그레이션은 [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md)을 통해 자동화할 수 있습니다. </br> -마이그레이션을 완료 하는 데 걸리는 시간은 데이터베이스 크기와 데이터베이스의 개체 수에 따라 달라 집니다. </br> -원본 데이터베이스를 읽기 전용으로 설정 해야 합니다. |
| | | |

### <a name="alternative-options"></a>대체 옵션

다음 표에서는 대체 마이그레이션 옵션을 비교 합니다. 

|메서드/기술 |사용 시기    |고려 사항  |
|---------|---------|---------|
|[트랜잭션 복제](../../database/replication-to-sql-database.md)| -원본 데이터베이스 테이블의 변경 내용을 대상 SQL Database 테이블로 지속적으로 게시 하 여 마이그레이션합니다. </br> -선택한 테이블 (데이터베이스의 하위 집합)의 전체 또는 부분 데이터베이스 마이그레이션  </br> </br> 지원 되는 원본: </br> - [몇 가지 제한 사항이 있는 SQL Server (2016-2019)](/sql/relational-databases/replication/replication-backward-compatibility) </br> -AWS EC2  </br> -GCP 계산 SQL Server VM  | -설치는 다른 마이그레이션 옵션에 비해 비교적 복잡 합니다.   </br> -데이터베이스를 오프 라인으로 전환 하지 않고 데이터를 마이그레이션하는 연속 복제 옵션을 제공 합니다.  </br> -트랜잭션 복제에는 원본 SQL Server에서 게시자를 설정할 때 고려해 야 할 몇 가지 제한 사항이 있습니다. 자세히 알아보려면 [개체 게시에 대 한 제한 사항](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) 을 참조 하세요. </br>- [복제 작업을 모니터링할](/sql/relational-databases/replication/monitor/monitoring-replication)수 있습니다.    |
|[가져오기/내보내기 서비스/BACPAC](../../database/database-import.md)| -개별 lob (기간 업무) 응용 프로그램 데이터베이스를 마이그레이션합니다. </br>-작은 데이터베이스에 적합 합니다.  </br>  -별도의 마이그레이션 서비스 또는 도구가 필요 하지 않습니다. </br> </br> 지원 되는 원본: </br> -SQL Server (2005-2019) 온-프레미스 또는 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 계산 SQL Server VM  |  -데이터를 원본에서 내보내고 대상에서 가져와야 하므로 가동 중지 시간이 필요 합니다.   </br> -잘림/데이터 형식 불일치 오류를 방지 하기 위해 내보내기/가져오기에 사용 되는 파일 형식 및 데이터 형식이 테이블 스키마와 일치 해야 합니다.  </br> -많은 수의 개체를 포함 하는 데이터베이스를 내보내는 데 걸리는 시간이 훨씬 더 높을 수 있습니다.       |
|[대량 복사](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -전체 또는 부분 데이터 마이그레이션을 마이그레이션합니다. </br> -가동 중지 시간을 수용할 수 있습니다. </br> </br> 지원 되는 원본: </br> -SQL Server (2005-2019) 온-프레미스 또는 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP 계산 SQL Server VM   | -원본에서 데이터를 내보내고 대상으로 가져오기 위한 가동 중지 시간이 필요 합니다. </br> -내보내기/가져오기에 사용 되는 파일 형식 및 데이터 형식은 테이블 스키마와 일치 해야 합니다. |
|[ADF(Azure Data Factory)](../../../data-factory/connector-azure-sql-database.md)| -원본 SQL Server 데이터베이스에서 데이터를 마이그레이션 및/또는 변환 합니다. </br> -일반적으로 BI (비즈니스 인텔리전스) 워크 로드를 위해 여러 데이터 원본의 데이터를 Azure SQL Database 병합 합니다.  |  -원본에서 대상으로 데이터를 이동 하려면 ADF에서 데이터 이동 파이프라인을 만들어야 합니다.   </br> - [비용은](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 중요 한 고려 사항이 며 파이프라인 트리거, 작업 실행, 데이터 이동 기간 등을 기준으로 합니다. |
|[SQL 데이터 동기화](../../database/sql-data-sync-data-sql-server-sql-database.md)| -원본 데이터베이스와 대상 데이터베이스 간에 데이터를 동기화 합니다.</br> -양방향 흐름에서 Azure SQL Database와 온-프레미스 SQL Server 간에 연속 동기화를 실행 하는 데 적합 합니다. | -Azure SQL Database 온-프레미스 SQL Server 데이터베이스와 동기화 할 수 있는 허브 데이터베이스는 멤버 데이터베이스로 사용 해야 합니다.</br> -SQL 데이터 동기화 트랜잭션 복제와 비교 하 여 온-프레미스와 Azure SQL Database 간에 양방향 데이터 동기화를 지원 합니다. </br> -워크 로드에 따라 성능에 더 큰 영향을 줄 수 있습니다.|
| | | |

## <a name="feature-interoperability"></a>기능 상호 운용성 

다른 SQL Server 기능을 사용 하는 워크 로드를 마이그레이션하는 경우 추가로 고려해 야 할 사항이 있습니다.

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services
[Azure Data Factory](../../../data-factory/introduction.md)에서 azure ssis 런타임에 패키지를 다시 배포 하 여 ssis (SQL Server Integration Services) 패키지를 azure로 마이그레이션합니다. Azure Data Factory는 Azure에서 SSIS 패키지를 실행 하도록 빌드된 런타임을 제공 하 여 [ssis 패키지의 마이그레이션을 지원](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) 합니다. 또는 [데이터 흐름](../../../data-factory/concepts-data-flow-overview.md)를 사용 하 여 기본적으로 ADF에 SSIS ETL 논리를 다시 작성할 수도 있습니다.


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Power BI에서 페이지가 매겨진 보고서로 SQL Server Reporting Services (SSRS) 보고서를 마이그레이션합니다.  [RDL 마이그레이션 도구](https://github.com/microsoft/RdlMigration) 를 사용 하 여 보고서를 준비 하 고 마이그레이션할 수 있습니다. 이 도구는 고객이 SSRS 서버에서 Power BI로 RDL 보고서를 마이그레이션하는 데 도움을 주기 위해 Microsoft에서 개발한 것입니다. GitHub에서 사용할 수 있으며 마이그레이션 시나리오에 대 한 종단 간 연습을 문서화 합니다. 

#### <a name="high-availability"></a>고가용성
Always On 장애 조치 (failover) 클러스터 인스턴스와 같은 SQL Server 고가용성 기능을 수동으로 설정 하 고 Always On 가용성 그룹, 고가용성 아키텍처가 [일반 용도 (표준 가용성 모델)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) 와 [중요 비즈니스용 (프리미엄 가용성 모델)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) SQL Database 둘 다에 이미 내장 되어 있으므로 대상 Azure SQL Database에서 사용 되지 않습니다. 또한 중요 비즈니스용/프리미엄 서비스 계층은 읽기 전용을 위해 보조 노드 중 하나에 연결할 수 있는 읽기 확장을 제공 합니다. 

SQL Database에 포함 된 고가용성 아키텍처 외에도 관리 되는 인스턴스의 데이터베이스 복제 및 장애 조치 (failover)를 다른 지역으로 관리할 수 있는 [자동 장애 조치 (failover) 그룹](../../database/auto-failover-group-overview.md) 기능도 있습니다. 

#### <a name="sql-agent-jobs"></a>SQL 에이전트 작업
SQL 에이전트 작업은 Azure SQL Database에서 직접 지원 되지 않으므로 [Elastic Database 작업 (미리 보기)](../../database/job-automation-overview.md#elastic-database-jobs-preview)에 배포 해야 합니다.

#### <a name="logins-and-groups"></a>로그인 및 그룹
오프 라인 모드에서 DMS (Database Migration Service)를 사용 하 여 원본 SQL Server에서 Azure SQL Database로 SQL 로그인을 이동 합니다.  **마이그레이션 마법사** 에서 **선택한 로그인** 블레이드를 사용 하 여 대상 SQL Database 로그인을 마이그레이션합니다. 

DMS 구성 페이지에서 해당 설정/해제 단추를 사용 하도록 설정 하 여 DMS를 사용 하 여 Windows 사용자 및 그룹을 마이그레이션할 수도 있습니다. 

또는 Microsoft 데이터 마이그레이션 설계자가 특별히 설계한 [PowerShell 유틸리티 도구](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) 를 사용할 수 있습니다. 유틸리티는 PowerShell을 사용 하 여 T-sql (Transact-sql) 스크립트를 만들어 로그인을 다시 만들고 원본에서 대상으로 데이터베이스 사용자를 선택 합니다. 이 도구는 자동으로 Windows AD 계정을 Azure AD 계정에 매핑하고 원본 Active Directory에 대 한 각 로그인에 대해 UPN 조회를 수행할 수 있습니다. 이 도구는 역할 멤버 자격, 데이터베이스 역할 및 사용자 권한 뿐만 아니라 사용자 지정 서버 및 데이터베이스 역할을 스크립팅 합니다. 포함 된 데이터베이스는 아직 지원 되지 않으며 가능한 SQL Server 권한 하위 집합만 스크립팅됩니다. 


#### <a name="system-databases"></a>시스템 데이터베이스
Azure SQL Database의 경우 해당 시스템 데이터베이스만 [master](/sql/relational-databases/databases/master-database) 및 tempdb입니다. 자세히 알아보려면 [Azure SQL Database의 Tempdb](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)를 참조 하세요.

## <a name="leverage-advanced-features"></a>고급 기능 활용 

SQL Database에서 제공 하는 고급 클라우드 기반 기능을 활용 해야 합니다. 예를 들어 서비스에서 백업 관리에 대해 더 이상 걱정할 필요가 없습니다. [보존 기간 내의 특정 시점](../../database/recovery-using-backups.md#point-in-time-restore)으로 복원할 수 있습니다. 

보안을 강화 하려면 [Azure Active Directory 인증](../../database/authentication-aad-overview.md), [감사](../../database/auditing-overview.md), [위협 검색](../../database/advanced-data-security.md), [행 수준 보안](/sql/relational-databases/security/row-level-security)및 [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking)을 사용 하는 것이 좋습니다.

SQL Database는 고급 관리 및 보안 기능 외에도 [워크 로드를 모니터링 하 고 조정](../../database/monitor-tune-overview.md)하는 데 도움이 되는 고급 도구 집합을 제공 합니다. [Azure SQL 분석 (미리 보기)](../../../azure-monitor/insights/azure-sql.md) 는 단일 보기의 여러 구독에서 규모에 맞게 Azure SQL Database 모든 데이터베이스의 성능을 모니터링 하기 위한 고급 클라우드 모니터링 솔루션입니다. Azure SQL 분석는 성능 문제 해결을 위한 기본 제공 인텔리전스를 사용 하 여 주요 성능 메트릭을 수집 하 고 시각화 합니다.

[자동 조정](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   SQL 실행 계획 통계의 성능을 지속적으로 모니터링 하 고 식별 된 성능 문제를 자동으로 수정 합니다. 


## <a name="migration-assets"></a>마이그레이션 자산 

추가 지원이 필요한 경우 실제 마이그레이션 프로젝트용으로 개발 된 다음 리소스를 참조 하세요.

|자산  |Description  |
|---------|---------|
|[데이터 워크로드 평가 모델 및 도구](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| 이 도구는 권장되는 “최적의” 대상 플랫폼, 클라우드 준비 상태 및 애플리케이션/데이터베이스 수정 수준을 지정된 워크로드에 제공합니다. 이는 자동화 되 고 일관 된 대상 플랫폼 결정 프로세스를 제공 하 여 많은 부동산 평가를 가속화 하는 데 도움이 되는 간단한 단일 클릭 계산 및 보고서 생성 기능을 제공 합니다.|
|[DBLoader 유틸리티](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader는 분리 된 텍스트 파일의 데이터를 SQL Server 로드 하는 데 사용할 수 있습니다. 이 Windows 콘솔 유틸리티는 SQL Server native client bulkload 인터페이스를 사용 합니다 .이 인터페이스는 Azure SQL Database를 비롯 한 모든 SQL Server 버전에서 작동 합니다.|
|[PowerShell을 사용 하 여 대량 데이터베이스 만들기](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|여기에는 리소스 그룹 (create_rg.ps1), [Azure에서 논리 서버](../../database/logical-servers.md) (create_sqlserver.ps1) 및 Azure SQL Database (create_sqldb.ps1)를 만드는 세 가지 PowerShell 스크립트 집합이 포함 됩니다. 스크립트에는 필요한 만큼의 서버와 데이터베이스를 반복 하 고 만들 수 있도록 루프 기능이 포함 되어 있습니다.|
|[MSSQL-Scripter & PowerShell을 사용 하 여 대량 스키마 배포](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|이 자산은 Azure에서 Azure SQL Database를 호스트 하는 리소스 그룹 하나 또는 여러 개의 [논리 서버](../../database/logical-servers.md) 를 만들고, 온-프레미스 SQL Server (또는 여러 SQL server (2005 +)에서 모든 스키마를 내보낸 후 Azure SQL Database으로 가져옵니다.|
|[SQL Server 에이전트 작업을 Elastic Database 작업으로 변환](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|이 스크립트는 원본 SQL Server 에이전트 작업을 Elastic Database 작업으로 마이그레이션합니다.|
|[Azure SQL Database에서 메일 보내기](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|이는 온-프레미스 SQL Server에서 사용할 수 있는 SendMail 기능 대신 사용할 수 있는 솔루션을 제공 합니다. 솔루션은 Azure Functions 및 Azure SendGrid 서비스를 사용 하 여 Azure SQL Database에서 전자 메일을 보냅니다.|
|[온-프레미스 SQL Server 로그인을 Azure SQL Database로 이동 하는 유틸리티](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|로그인을 다시 만들고 온-프레미스 SQL Server Azure SQL Database에서 데이터베이스 사용자를 선택 하는 T-sql 명령 스크립트를 만드는 PowerShell 스크립트입니다. 이 도구를 사용 하면 Azure AD 계정에 대 한 Windows AD 계정을 자동으로 매핑하고 선택적으로 SQL Server 네이티브 로그인을 마이그레이션할 수 있습니다.|
|[Logman를 사용 하 여 PerfMon 데이터 수집 자동화](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|기본 성능을 이해 하 고 마이그레이션 대상 권장 사항을 지원 하기 위해 PerMon 데이터를 수집 하는 도구입니다. 이 도구는 logman.exe을 사용 하 여 원격 SQL Server에 설정 된 성능 카운터를 생성, 시작, 중지 및 삭제 하는 명령을 만듭니다.|
|[백서-BACPAC를 사용 하 여 Azure SQL DB로 데이터베이스 마이그레이션](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|이 백서에서는 BACPAC 파일을 사용 하 여 SQL Server에서 Azure SQL Database로의 마이그레이션을 가속화 하는 지침과 단계를 제공 합니다.|

위 리소스는 Azure 데이터 그룹 엔지니어링 팀에서 후원하는 Data SQL Ninja 프로그램의 일부로 개발되었습니다. Data SQL Ninja 프로그램의 핵심 선언은 복잡한 현대화의 장애물을 제거하고 속도를 높이며 Microsoft의 Azure 데이터 플랫폼에 대한 데이터 플랫폼 마이그레이션 기회를 놓고 경쟁하는 것입니다. 조직이 Data SQL Ninja 프로그램에 참여하는 데 관심이 있다고 생각되면 계정 팀에 문의하여 추천서를 제출하도록 요청하세요.


## <a name="next-steps"></a>다음 단계

Azure SQL Database로 SQL Server 마이그레이션을 시작 하려면 [SQL Server 마이그레이션 가이드 SQL Database를](sql-server-to-sql-database-guide.md)참조 하세요.

- 다양 한 데이터베이스 및 데이터 마이그레이션 시나리오와 전문 작업을 지 원하는 데 사용할 수 있는 Microsoft 및 타사 서비스 및 도구의 행렬은 [데이터 마이그레이션을 위한 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조 하세요.

- SQL Database에 대 한 자세한 내용은 다음을 참조 하세요.
   - [Azure SQL Database 개요](../../database/sql-database-paas-overview.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 

- 클라우드 마이그레이션의 프레임 워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조 하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [작업의 비용 및 크기 조정 작업에 대 한 모범 사례 Azure로 마이그레이션](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- 응용 프로그램 액세스 계층을 평가 하려면 [데이터 액세스 마이그레이션 도구 키트 (미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) 를 참조 하세요.
- 데이터 액세스 계층 A/B 테스트를 수행 하는 방법에 대 한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조 하세요.
