---
title: Azure VM에서 SQL Server SQL Server (마이그레이션 개요)
description: SQL Server를 Azure Vm에서 SQL Server로 마이그레이션하려는 여러 마이그레이션 전략에 대해 알아봅니다.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: ''
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 64334b17060879a2e587b13b062c81e86df33831
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94743442"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>마이그레이션 개요: Azure Vm에서 SQL Server으로 SQL Server
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

SQL Server를 Azure Virtual Machines (Vm)에서 SQL Server로 마이그레이션하는 다양 한 마이그레이션 전략에 대해 알아봅니다. 

온-프레미스 또는에서 실행 중인 SQL Server를 마이그레이션할 수 있습니다.

- Virtual Machines의 SQL Server  
- Amazon Web Services (AWS) EC2 
- AWS RDS (Amazon 관계형 데이터베이스 서비스) 
- 계산 엔진 (Google Cloud Platform GCP)

다른 시나리오는 [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조 하세요. 

## <a name="overview"></a>개요

OS 제어와 함께 친숙 한 SQL Server 환경을 사용 하 고 기본 제공 VM 고가용성, [자동화 된 백업](../../virtual-machines/windows/automated-backup.md)및 [자동화 된 패치와](../../virtual-machines/windows/automated-patching.md)같은 클라우드 제공 기능을 활용 하려는 경우 [Azure Virtual Machines (vm)에서 SQL Server](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview) 로 마이그레이션합니다. 

[Azure 하이브리드 혜택 라이선스 모델](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) 을 사용 하 여 라이선스를 [확보 하거나 무료 보안 업데이트](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)를 통해 SQL Server 2008 및 SQL Server 2008 r 2에 대 한 지원을 확장 하 여 비용을 절감할 수 있습니다. 


## <a name="choosing-appropriate-target"></a>적절 한 대상 선택

Azure Virtual Machines 다양 한 Azure 지역에서 실행 되며 다양 한 [컴퓨터 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 와 [저장소 옵션도](https://docs.microsoft.com/azure/virtual-machines/disks-types)제공 합니다. SQL Server 워크 로드에 대 한 VM 및 저장소의 올바른 크기를 결정할 때 [Azure Virtual Machines의 SQL Server에 대 한 성능 지침](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices#vm-size-guidance)을 참조 하세요. 워크 로드에 대 한 VM 크기 및 저장소 요구 사항을 확인 합니다. Performance-Based [Azure Migrate 평가](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#types-of-assessments)를 통해 크기를 조정 하는 것이 좋습니다. 이 옵션을 사용할 수 없는 경우에는 [성능을 위한 고유한 기준선](https://azure.microsoft.com/services/virtual-machines/sql-server/)을 만드는 방법에 대 한 다음 문서를 참조 하세요.

VM에서 SQL Server를 올바르게 설치 하 고 구성 하는 것도 고려해 야 합니다. 올바른 버전, 버전 및 운영 체제를 사용 하 여 SQL Server VM를 만들 수 있도록 [AZURE SQL 가상 컴퓨터 이미지 갤러리](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal) 를 사용 하는 것이 좋습니다. 그러면 자동 백업 및 자동화 된 패치 같은 기능을 사용할 수 있도록 자동으로 SQL Server [리소스 공급자](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal) 를 사용 하 여 Azure VM을 등록 합니다.

## <a name="migration-strategies"></a>마이그레이션 전략

Azure Vm에서 사용자 데이터베이스를 SQL Server 인스턴스로 마이그레이션하는 마이그레이션 전략에는 **마이그레이션, 마이그레이션** 및 **이동** 이라는 두 가지가 있습니다. 

비즈니스에 적합 한 접근 방식은 일반적으로 다음 요소에 따라 달라 집니다. 

- 마이그레이션의 크기 및 규모
- 마이그레이션 속도
- 코드 변경에 대 한 응용 프로그램 지원
- SQL Server 버전, 운영 체제 또는 둘 다를 변경 해야 합니다.
- 기존 제품의 지원 가능성 수명 주기
- 마이그레이션 중 응용 프로그램 가동 중지 시간에 대 한 창

다음 표에서는 두 마이그레이션 전략의 차이점을 설명 합니다.
<br />

| **마이그레이션 전략** | **설명** | **사용하는 경우** |
| --- | --- | --- |
| **리프트 & shift** | 리프트 앤 시프트 마이그레이션 전략을 사용 하 여 실제 또는 가상 SQL Server 전체를 현재 위치에서 운영 체제 또는 SQL Server 버전을 변경 하지 않고 Azure VM의 SQL Server 인스턴스로 이동 합니다. 리프트 앤 시프트 마이그레이션을 완료 하려면 [Azure Migrate](../../../migrate/migrate-services-overview.md)를 참조 하세요. <br /><br /> 원본 서버는 온라인 및 서비스 요청을 유지 하는 반면, 원본 및 대상 서버는 거의 원활한 마이그레이션을 위해 데이터를 동기화 합니다. | 데이터 센터 종료와 같은 시나리오에도 적용 되는 단일에서 매우 큰 마이그레이션에 사용 합니다. <br /><br /> 사용자 SQL 데이터베이스 또는 응용 프로그램에 대 한 코드 변경을 최소화 하 여 전반적인 마이그레이션을 더 빠르게 수행할 수 있습니다. <br /><br />[SSIS](/sql/integration-services/sql-server-integration-services), [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports), [SSAS](/analysis-services/analysis-services-overview)등의 비즈니스 인텔리전스 서비스를 마이그레이션하는 데 필요한 추가 단계는 없습니다. |
|**마이그레이션** | 대상 SQL Server 및/또는 운영 체제 버전을 업그레이드 하려는 경우 마이그레이션 전략을 사용 합니다. <br /> <br /> Azure Marketplace에서 Azure VM을 선택 하거나 원본 SQL Server 버전과 일치 하는 준비 된 SQL Server 이미지를 선택 합니다. | 최신 버전의 SQL Server에서 사용 가능한 기능을 사용 하거나 더 이상 지원 하지 않는 레거시 SQL Server 및/또는 OS 버전을 업그레이드 해야 하는 경우에 사용 합니다.  <br /> <br /> SQL Server 업그레이드를 지원 하기 위해 일부 응용 프로그램 또는 사용자 데이터베이스를 변경 해야 할 수 있습니다. <br /><br />마이그레이션 범위에서 [비즈니스 인텔리전스](#business-intelligence) 서비스를 마이그레이션하기 위한 추가 고려 사항이 있을 수 있습니다. |


## <a name="lift-and-shift"></a>리프트 앤 시프트  

다음 표에서는 SQL Server 데이터베이스를 Azure Vm에서 SQL Server로 마이그레이션하는 **리프트 및 시프트** 마이그레이션 전략에 대해 자세히 설명 합니다.
<br />

|**메서드** | **최소 원본 버전** | **최소 대상 버전** | **원본 백업 크기 제약 조건** |  **참고** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Azure VM 스토리지 제한](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  기존 SQL Server를 Azure VM에서 SQL Server 인스턴스로 이동 합니다. 최대 35000 Vm의 마이그레이션 워크 로드를 확장할 수 있습니다. <br /><br /> 원본 서버는 서버 데이터 동기화 중 온라인 상태에서 요청을 처리 하 여 가동 중지 시간을 최소화 합니다. <br /><br /> **자동화 & 스크립팅**: [Azure에 대 한 확장 된 마이그레이션 및 계획의](/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale) [Azure Site Recovery 스크립트](../../../migrate/how-to-migrate-at-scale.md) 및 예제|

## <a name="migrate"></a>Migrate  

설치의 용이성 때문에 권장 되는 마이그레이션 방법은 네이티브 SQL Server [백업을](/sql/t-sql/statements/backup-transact-sql) 로컬로 수행 하 고 파일을 Azure에 복사 하는 것입니다. 이 메서드는 2008 이상의 데이터베이스 백업 (>1tb)부터 시작 하 여 모든 버전의 SQL Server에 대해 더 큰 데이터베이스 (>1tb)를 지원 합니다. 그러나 SQL Server 2014에서 시작 하 고 1TB 보다 작고 Azure에 대 한 연결이 좋은 데이터베이스의 경우 [URL에 백업 SQL Server](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 하는 것이 더 나은 방법입니다. 

SQL Server 데이터베이스를 Azure Vm의 SQL Server 인스턴스로 마이그레이션하는 경우에는 응용 프로그램 가동 중지 시간에 영향을 주므로 대상 서버에 대 한 조치를 수행 해야 하는 경우에 적합 한 접근 방식을 선택 하는 것이 중요 합니다.

다음 표에서는 SQL Server 데이터베이스를 Azure Vm에서 SQL Server로 마이그레이션하는 데 사용할 수 있는 모든 방법을 자세히 설명 합니다.
<br />

|**메서드** | **최소 원본 버전** | **최소 대상 버전** | **원본 백업 크기 제약 조건** | **참고** |
| --- | --- | --- | --- | --- |
| **[파일에 백업](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Azure VM 스토리지 제한](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  이는 컴퓨터 간에 데이터베이스를 이동 하기 위한 간단 하 고 잘 테스트 된 기술입니다. 압축을 사용 하 여 전송에 대 한 백업 크기를 최소화 합니다. <br /><br /> **자동화 & 스크립팅**: [transact-sql (T-sql)](/sql/t-sql/statements/backup-transact-sql) 및 [AzCopy to Blob storage](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[URL에 백업](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| SQL Server 2016의 경우 12.8 TB, 그렇지 않으면 1tb | Azure storage를 사용 하 여 VM으로 백업 파일을 이동 하는 대체 방법입니다. 압축을 사용 하 여 전송에 대 한 백업 크기를 최소화 합니다. <br /><br /> **자동화 & 스크립팅**:  [t-sql 또는 유지 관리 계획](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[DMA(Database Migration Assistant)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Azure VM 스토리지 제한](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  [DMA](/sql/dma/dma-overview) 는 온-프레미스 SQL Server을 평가 하 고 SQL Server의 이후 버전으로 원활 하 게 업그레이드 하거나 azure vm, Azure SQL Database 또는 azure SQL Managed Instance의 SQL Server로 마이그레이션합니다. <br /><br /> Filestream 사용 사용자 데이터베이스에서 사용 하면 안 됩니다.<br /><br /> 또한 DMA에는 [SQL 및 Windows 로그인](/sql/dma/dma-migrateserverlogins) 을 마이그레이션하고 [SSIS 패키지](/sql/dma/dma-assess-ssis)를 평가 하는 기능이 포함 되어 있습니다. <br /><br /> **자동화 & 스크립팅**: [명령줄 인터페이스](/sql/dma/dma-commandline) |
| **[분리 및 연결](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Azure VM 스토리지 제한](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | [Azure Blob storage 서비스를 사용 하 여 이러한 파일을 저장](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) 하 고 azure VM의 SQL Server 인스턴스에 연결 하는 경우이 방법을 사용 합니다. 특히 매우 큰 데이터베이스에서 또는 백업 및 복원 시간이 너무 긴 경우 유용 합니다. <br /><br /> **자동화 & 스크립팅**:  [t-sql](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) 및 [Blob 저장소에 대 한 AzCopy](../../../storage/common/storage-use-azcopy-v10.md)|
|**[로그 전달](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 (Windows에만 해당) | SQL Server 2008 SP4 (Windows에만 해당) | [Azure VM 스토리지 제한](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | 로그 전달은 온-프레미스의 트랜잭션 로그 파일을 Azure VM의 SQL Server 인스턴스로 복제 합니다. <br /><br /> 이는 장애 조치 (failover) 중에 최소의 가동 중지 시간을 제공 하며, Always On 가용성 그룹 설정 보다 구성 오버 헤드가 적습니다. <br /><br /> **자동화 & 스크립팅**: [t-sql](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[분산 가용성 그룹](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Azure VM 스토리지 제한](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  [분산 가용성 그룹](/sql/database-engine/availability-groups/windows/distributed-availability-groups) 은 두 개의 개별 가용성 그룹에 걸쳐 있는 특수 한 유형의 가용성 그룹입니다. 분산 가용성 그룹에 참여 하는 가용성 그룹은 동일한 위치에 있을 필요가 없으며 도메인 간 지원을 포함 합니다. <br /><br /> 이 메서드는 가동 중지 시간을 최소화 하 고 가용성 그룹이 온-프레미스로 구성 된 경우 사용 합니다. <br /><br /> **자동화 & 스크립팅**: [t-sql](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> 네트워크 옵션을 제한 하지 않는 대량 데이터 전송의 경우 [연결이 제한 된 대량 데이터 전송](../../../storage/common/storage-solution-large-dataset-low-network.md)을 참조 하세요.
> 

### <a name="considerations"></a>고려 사항

다음은 마이그레이션 방법을 검토할 때 고려해 야 할 주요 사항 목록입니다.

- 최적의 데이터 전송 성능을 위해 압축 된 백업 파일을 사용 하 여 Azure VM의 SQL Server 인스턴스로 데이터베이스와 파일을 마이그레이션합니다. 대용량 데이터베이스의 경우 백업 및 전송 중에 성능 향상을 위해 [백업 파일을 더 작은 파일로 분할](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) 합니다. 
- SQL Server 2014 이상에서 마이그레이션하는 경우 [백업을 암호화](/sql/relational-databases/backup-restore/backup-encryption) 하 여 네트워크 전송 중 데이터를 보호 하는 것이 좋습니다.
- 데이터베이스를 마이그레이션하는 동안 가동 중지 시간을 최소화 하려면 가용성 그룹 Always On 옵션을 사용 합니다. 
- 가용성 그룹을 구성 하는 오버 헤드 없이 가동 중지 시간을 최소화 하려면 로그 전달 옵션을 사용 합니다. 
- 네트워크 옵션을 사용 하지 않도록 제한 된 경우 백업 및 복원과 같은 오프 라인 마이그레이션 방법 또는 Azure에서 사용할 수 있는 [디스크 전송 서비스](../../../storage/common/storage-solution-large-dataset-low-network.md) 를 사용 합니다.
- 또한 Azure VM에서 SQL Server의 SQL Server 버전을 변경 하려면 [SQL Server 버전 변경](../../virtual-machines/windows/change-sql-server-edition.md)을 참조 하세요.

## <a name="business-intelligence"></a>비즈니스 인텔리전스 

사용자 데이터베이스 마이그레이션의 범위 밖에 서 SQL Server 비즈니스 인텔리전스 서비스를 마이그레이션할 때 추가 고려 사항이 있을 수 있습니다. 

이러한 서비스에는 다음이 포함됩니다.

- [**SQL Server Integration Services (SSIS)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**SQL Server Reporting Services (SSRS)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SSAS(SQL Server Analysis Services)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>지원되는 버전

Azure Vm에서 SQL Server 데이터베이스를 SQL Server으로 마이그레이션하기 위해 준비할 때 지원 되는 SQL Server 버전을 고려해 야 합니다. Azure Vm에서 현재 지원 되는 SQL Server 버전 목록은 [Azure vm의 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

Azure Vm에서 SQL Server 데이터베이스를 SQL Server으로 마이그레이션하기 시작 하려면 [개별 데이터베이스 마이그레이션 가이드](sql-server-to-sql-on-azure-vm-individual-databases-guide.md)를 참조 하세요. 

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
