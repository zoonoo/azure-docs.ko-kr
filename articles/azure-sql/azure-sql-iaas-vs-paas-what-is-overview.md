---
title: Azure SQL란?
description: Azure SQL 서비스 제품군의 다양 한 옵션에 대해 알아봅니다. azure VM의 Azure SQL Database, Azure SQL Managed Instance 및 SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server 클라우드, 클라우드, PaaS 데이터베이스, 클라우드 SQL Server, DBaaS, IaaS에서 SQL Server
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: 344e52da8ef41e26ab0bfa7ef8e09a31f5d135ca
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89611836"
---
# <a name="what-is-azure-sql"></a>Azure SQL란? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL은 Azure 클라우드에서 SQL Server 데이터베이스 엔진을 사용 하는 관리, 보안 및 인텔리전트 제품의 패밀리입니다.

- **Azure SQL Database**: 서버 리스 계산을 포함 하는 지능적인 관리 되는 데이터베이스 서비스에서 최신 클라우드 응용 프로그램을 지원 합니다. 
- **AZURE SQL Managed Instance**: SQL Server 데이터베이스 엔진과 거의 100% 기능 패리티가 있는 지능형 완전히 관리 되는 인스턴스를 사용 하 여 기존 SQL Server 응용 프로그램을 대규모로 현대화. 클라우드로의 대부분 마이그레이션에 적합 합니다.
- **Azure vm에서 SQL Server**: SQL Server 워크 로드를 쉽게 리프트 앤 시프트 하 고 100% SQL Server 호환성 및 운영 체제 수준 액세스를 유지 합니다. 
 
Azure SQL은 친숙 한 SQL Server 엔진을 기반으로 하므로 응용 프로그램을 쉽게 마이그레이션할 수 있으며 친숙 한 도구, 언어 및 리소스를 계속 사용할 수 있습니다. 자신의 기술과 경험이 클라우드로 전송 되므로 이미 보유 한 기능을 사용 하 여 더 많은 작업을 수행할 수 있습니다. 

비즈니스 요구 사항에 적합 한 옵션을 일치 시키기 위해 각 제품이 Microsoft Azure SQL data platform에 어떻게 부합 하는지 알아보세요. 비용 절감 또는 최소 관리의 우선 순위를 지정 하는 경우이 문서를 통해 가장 관심 있는 비즈니스 요구 사항에 대해 제공 하는 방법을 결정 하는 데 도움이 될 수 있습니다.


Azure SQL을 처음 접하는 경우 심층 [AZURE sql 비디오 시리즈](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)의 *azure sql* 비디오를 확인 하세요.
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]



## <a name="overview"></a>개요

오늘날의 데이터 기반 세계에서 디지털 변화를 증가 시키는 것은 방대한 양의 데이터를 관리 하 고 잠재력을 활용 하는 능력에 따라 좌우 됩니다. 하지만 오늘날의 데이터 자산는 온-프레미스, 클라우드 또는 네트워크의에 지에 호스트 되는 데이터를 사용 하 여 점점 더 복잡 합니다. 지능적인 응용 프로그램을 빌드하는 개발자는 궁극적으로 자신의 경험에 영향을 미칠 수 있는 제한 사항에 의해 제약을 받을 수 있습니다. 호환 되지 않는 플랫폼에서 발생 하는 제한 사항, 부적절 한 데이터 보안, 리소스 부족 및 가격-성능 장애물은 앱 현대화 및 개발을 방해할 수 있는 복잡성을 만듭니다. 

Azure와 온-프레미스 SQL Server 데이터베이스를 비교 논의할 때 우선 이 둘을 모두 사용할 수 있다는 점을 이해해야 합니다. Microsoft의 데이터 플랫폼은 SQL Server 기술을 활용 하며 물리적 온-프레미스 컴퓨터, 사설 클라우드 환경, 타사 호스팅 사설 클라우드 환경 및 공용 클라우드에 걸쳐 사용할 수 있도록 합니다. 


### <a name="fully-managed-and-always-up-to-date"></a>완전히 관리 되 고 항상 최신 상태 

더 많은 시간을 혁신 하 고 데이터베이스를 패치, 업데이트 및 백업 하는 시간을 단축 합니다. Azure는 최신 업데이트와 패치를 자동으로 적용 하 여 최신 업데이트와 패치를 자동으로 적용 하는 SQL의 유일한 클라우드로, 데이터베이스를 항상 최신 상태로 유지 하 여 지원 종료를 제거 합니다. 성능 튜닝, 고가용성, 재해 복구, 백업 등의 복잡 한 작업을 자동화 하 여 응용 프로그램에 집중할 수 있습니다.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>기본 제공 지능형 보안을 사용 하 여 데이터 보호 

Azure는 위협에 대 한 데이터를 지속적으로 모니터링 합니다. Azure SQL을 사용 하 여 다음을 수행할 수 있습니다.

- 지능형 [고급 위협 검색](https://docs.microsoft.com/azure/security/fundamentals/threat-detection#advanced-threat-detection-features-other-azure-services) 및 자동 관리 취약성 평가 경고를 사용 하 여 잠재적 위협을 실시간으로 수정 합니다. 
- T-sql, 인증, 네트워킹 및 키 관리를 비롯 한 [기본 제공 보안 컨트롤](https://azure.microsoft.com/overview/security/) 을 사용 하 여 업계 최고의 다중 계층 보호를 제공 하세요. 
- 클라우드 데이터베이스 서비스의 가장 포괄적인 [규정 준수](https://azure.microsoft.com/overview/trusted-cloud/compliance/) 범위를 활용 하세요. 


### <a name="business-motivations"></a>비즈니스 동기

여러 가지 요소를 선택 하 여 다양 한 데이터 제공을 선택 하는 데 영향을 줄 수 있습니다.

- [비용](#cost): PaaS 및 IaaS 옵션은 기본 인프라와 라이선스를 포함 하는 기본 가격을 포함 합니다. 그러나 IaaS 옵션을 사용 하면 데이터베이스를 관리 하기 위해 추가 시간과 리소스를 투자 해야 하며, PaaS에서는 가격에 이러한 관리 기능을 제공 합니다. IaaS를 사용 하면 리소스를 사용 하지 않는 동안 리소스를 종료 하 여 비용을 줄일 수 있으며, 필요한 경우 리소스를 삭제 하 고 다시 만들지 않는 한 PaaS는 항상 실행 됩니다.
- [관리](#administration): PaaS 옵션을 통해 데이터베이스를 관리 하는 데 투자 해야 하는 시간을 줄일 수 있습니다. 그러나 사용자 지정 관리 태스크 및 사용자가 실행 하거나 실행할 수 있는 스크립트의 범위를 제한 하기도 합니다. 예를 들어 CLR은 SQL Database에서 지원 되지 않지만 SQL Managed Instance 인스턴스에 대해서는 지원 됩니다. 또한 PaaS의 배포 옵션은 추적 플래그 사용을 지원 하지 않습니다.
- [서비스 수준 계약](#service-level-agreement-sla): IaaS 및 PaaS는 모두 업계 표준 SLA를 제공 합니다. PaaS 옵션은 99.99%의 SLA를 보장하지만, IaaS는 인프라에 대해 99.95%의 SLA를 보장합니다. 따라서 데이터베이스의 가용성을 보장하려면 추가 메커니즘을 구현해야 합니다. 추가 SQL 가상 머신을 만들고 SQL Server Always On 가용성 그룹 고가용성 솔루션을 구현 하 여 99.99% SLA를 얻을 수 있습니다. 
- Azure [로 이동 하는 시간](#market): azure vm의 SQL Server 사용자 환경과 정확히 일치 하므로 온-프레미스에서 azure vm으로 마이그레이션하는 것은 온-프레미스 서버 간에 데이터베이스를 이동 하는 것과는 다릅니다. SQL Managed Instance를 사용 하면 쉽게 마이그레이션할 수 있습니다. 그러나 마이그레이션 전에 적용 해야 하는 변경 내용이 있을 수 있습니다. 


## <a name="service-comparison"></a>서비스 비교

   ![클라우드 SQL Server 옵션: IaaS 또는 클라우드의 SaaS SQL Database SQL Server.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

다이어그램에 표시 된 것 처럼 각 서비스 제품은 인프라를 통해 보유 한 관리 수준 및 비용 효율성을 기준으로 지정할 수 있습니다.

Azure에서 SQL Server 작업을 호스트 서비스 ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) 또는 호스트 된 인프라 ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/))로 실행할 수 있습니다. PaaS 내에서 각 옵션에는 여러 제품 옵션과 서비스 계층이 있습니다. PaaS 또는 IaaS를 결정할 때 확인 해야 하는 주요 질문은 데이터베이스를 관리 하 고, 패치를 적용 하 고, 백업을 수행 하 시겠습니까? 아니면 이러한 작업을 Azure로 위임 하 시겠습니까?

### <a name="azure-sql-database"></a>Azure SQL Database

[Azure SQL Database](database/sql-database-paas-overview.md) 은 Azure에서 호스트 되는 관계형 Dbaas ( *PaaS)로, PaaS (Platform as a service)* 의 산업 범주에 해당 합니다. 
- 안정적인 최신 SQL Server 기능을 사용 하 고 개발 및 마케팅에 시간 제한이 있는 최신 클라우드 응용 프로그램에 가장 적합 합니다. 
- SQL Server의 안정적인 최신 Enterprise Edition을 기반으로 하는 완전히 관리 되는 SQL Server 데이터베이스 엔진입니다. SQL Database에는 Microsoft에서 소유 하 고 호스트 하 고 유지 관리 하는 표준화 된 하드웨어 및 소프트웨어를 기반으로 하는 두 가지 배포 옵션이 있습니다. 

SQL Server를 사용 하면 광범위 한 구성 (온-프레미스 또는 Azure 가상 컴퓨터)을 필요로 하는 기본 제공 기능 및 기능을 사용할 수 있습니다. SQL Database를 사용할 경우 추가 성능에 맞게 중단 없이 강화 및 확장하는 옵션을 통해 사용량에 따라 지불합니다. SQL Database에는 기본 제공 고가용성, 인텔리전스 및 관리와 같이 SQL Server에서 사용할 수 없는 몇 가지 추가 기능이 있습니다.


Azure SQL Database는 다음과 같은 배포 옵션을 제공합니다.
  - [논리 SQL server](database/logical-servers.md)를 통해 관리 되는 자체 리소스 집합이 포함 된 [*단일 데이터베이스*](database/single-database-overview.md) 단일 데이터베이스는 SQL Server의 포함 된 [데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) 와 비슷합니다. 이 옵션은 새로운 클라우드 기반 애플리케이션의 최신 애플리케이션 개발에 맞게 최적화되어 있습니다. [Hyperscale](database/service-tier-hyperscale.md) 및 [서버](database/serverless-tier-overview.md) 리스 옵션을 사용할 수 있습니다.
  - [논리 SQL server](database/logical-servers.md)를 통해 관리 되는 리소스 집합을 포함 하는 데이터베이스 컬렉션인 [*탄력적 풀*](database/elastic-pool-overview.md). 단일 데이터베이스를 탄력적 풀로 이동하거나 탄력적 풀에서 제거할 수 있습니다. 이 옵션은 다중 테 넌 트 SaaS 응용 프로그램 패턴을 사용 하 여 새로운 클라우드를 사용 하는 응용 프로그램의 최신 응용 프로그램 개발에 최적화 되어 있습니다. 탄력적 풀은 변수 사용 패턴이 있는 여러 데이터베이스의 성능을 관리 하는 비용 효율적인 솔루션을 제공 합니다.

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

[AZURE SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) 는 *PaaS (Platform as a Service)* 의 업계 범주로 나뉩니다. 클라우드로의 대부분 마이그레이션에 가장 적합 합니다. SQL Managed Instance는 리프트 앤 시프트 준비 인 공유 리소스 집합을 포함 하는 시스템 및 사용자 데이터베이스 모음입니다.  
- 안정적인 최신 SQL Server 기능을 사용 하 고 최소한의 변경으로 클라우드로 마이그레이션되는 새 응용 프로그램 또는 기존 온-프레미스 응용 프로그램에 가장 적합 합니다. SQL Managed Instance의 인스턴스는 데이터베이스 엔진과 추가 인스턴스 범위 기능에 대 한 공유 리소스를 제공 하는 [Microsoft SQL Server 데이터베이스 엔진과](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview) 비슷합니다. 
- SQL Managed Instance는 데이터베이스 변경을 최소화 하면서 온-프레미스에서 데이터베이스 마이그레이션을 지원 합니다. 이 옵션은 Azure SQL Database의 모든 PaaS 혜택을 제공 하지만, 이전에 SQL Server Vm 에서만 사용할 수 있었던 기능을 추가 합니다. 여기에는 네이티브 가상 네트워크 및 온-프레미스 SQL Server와 거의 100%의 호환성이 포함 됩니다. Sql Managed Instance 인스턴스는 SQL Server를 Azure로 마이그레이션하기 위한 전체 SQL Server 액세스 및 기능 호환성을 제공 합니다.

### <a name="sql-server-on-azure-vm"></a>Azure VM의 SQL Server

[AZURE vm의 SQL Server](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) 은 업계 범주 *IaaS (Infrastructure as a Service* )로, azure에서 완전히 관리 되는 VM (가상 머신) 내에서 SQL Server을 실행할 수 있습니다. 
- OS 수준 액세스를 필요로 하는 마이그레이션 및 응용 프로그램에 가장 적합 합니다. Azure의 SQL 가상 머신은 변경 내용을 최소화 하거나 변경 하지 않고 클라우드로 신속 하 게 마이그레이션해야 하는 기존 응용 프로그램에 대해 리프트 앤 시프트가 준비 됩니다. SQL 가상 머신은 Azure로의 마이그레이션을 위해 SQL Server 인스턴스 및 기본 OS에 대 한 모든 관리 권한을 제공 합니다. 
- 온-프레미스 개발 및 테스팅 SQL Server 하드웨어를 구입하지 않는 빠른 개발 및 테스트 시나리오 SQL 가상 머신은 Microsoft에서 소유 하 고 호스트 하며 유지 관리 하는 표준화 된 하드웨어 에서도 실행 됩니다. SQL 가상 컴퓨터를 사용 하는 경우 이미 SQL Server 이미지에 포함 된 SQL Server 라이선스를 지불 하거나 기존 라이선스를 쉽게 사용할 수 있습니다. 필요에 따라 VM을 중지하거나 다시 시작할 수도 있습니다. 
- 클라우드에 설치 되 고 호스트 되는 SQL Server는 Azure에서 실행 되는 Windows Server 또는 Linux 가상 컴퓨터 (IaaS (infrastructure as a service) 라고도 함)에서 실행 됩니다. SQL 가상 머신은 데이터베이스를 변경 하지 않고 온-프레미스 SQL Server 데이터베이스 및 응용 프로그램을 마이그레이션하는 데 적합 한 옵션입니다. 모든 최신 버전의 SQL Server를 IaaS 가상 머신에 설치할 수 있습니다. 

    SQL Database와 SQL Managed Instance의 가장 큰 차이점은 Azure Virtual Machines SQL Server 데이터베이스 엔진에 대 한 모든 권한을 허용 한다는 것입니다. 유지 관리/패치를 시작할 시기를 선택 하 고, 복구 모델을 단순 또는 대량 로그로 변경 하거나, 필요에 따라 서비스를 일시 중지 하거나 시작 하 고, SQL Server 데이터베이스 엔진을 완벽 하 게 사용자 지정할 수 있습니다. 이 추가 컨트롤을 사용 하면 가상 머신을 관리 하는 추가 책임이 있습니다.
- 기존 애플리케이션을 Azure로 마이그레이션하거나 기존 온-프레미스 애플리케이션을 하이브리드 배포의 클라우드로 확장하는 데 최적화됩니다. 또한 가상 머신에서 SQL Server를 사용하여 기존 SQL Server 애플리케이션을 개발하고 테스트할 수 있습니다. SQL 가상 머신에서는 전용 SQL Server 인스턴스와 클라우드 기반 VM에 대 한 전체 관리 권한이 있습니다. 이 옵션은 가상 머신을 유지 관리하는 IT 리소스를 이미 갖춘 조직에게 가장 적합합니다. 이러한 기능을 사용하면 애플리케이션 관련 성능 및 가용성 요구 사항을 해결하는 고도로 사용자 지정된 시스템을 구축할 수 있습니다.

다음 표에는 추가 차이점이 나와 있습니다 *. 하지만 많은 데이터베이스를 프로 비전 하 고 관리 하는 데 필요한 최소한의 전체 관리 비용을 줄이기 위해 SQL Database 및 SQL Managed Instance 모두 최적화 되어 있습니다.* 가상 컴퓨터, 운영 체제 또는 데이터베이스 소프트웨어를 관리할 필요가 없으므로 지속적인 관리 비용이 줄어듭니다. 업그레이드, 고가용성 또는 [백업](database/automated-backups-overview.md)을 관리할 필요가 없습니다. 

일반적으로 SQL Database 및 SQL Managed Instance는 단일 IT 또는 개발 리소스에서 관리 하는 데이터베이스 수를 크게 늘릴 수 있습니다. 또한 [탄력적 풀](database/elastic-pool-overview.md)은 테넌트 격리, 데이터베이스 간에 리소스를 공유하여 비용을 줄이는 기능을 비롯한 기능으로 SaaS 다중 테넌트 애플리케이션 아키텍처를 지원합니다. [SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) 은 기존 응용 프로그램을 쉽게 마이그레이션할 수 있을 뿐만 아니라 데이터베이스 간에 리소스를 공유할 수 있도록 인스턴스 범위 기능을 지원 합니다.

### <a name="comparison-table"></a>비교 표

| Azure SQL Database | Azure SQL Managed Instance | Azure VM의 SQL Server |
| :--- | :--- | :--- |
|대부분의 온-프레미스 데이터베이스 수준 기능을 지원합니다. 가장 자주 사용되는 SQL Server 기능을 사용할 수 있습니다.<br/>99.995%의 가용성을 보장합니다.<br/>기본 제공 백업, 패치, 복구<br/>안정적인 최신 데이터베이스 엔진 버전<br/>필요한 리소스(CPU/스토리지)를 개별 데이터베이스에 할당할 수 있습니다.<br/>기본 제공 고급 인텔리전스 및 보안<br/>온라인에서 리소스(CPU/스토리지)를 변경할 수 있습니다.| 거의 모든 온-프레미스 인스턴스 수준 및 데이터베이스 수준 기능을 지원합니다. SQL Server와의 호환성이 높습니다.<br/>99.99% 가용성 보장<br/>기본 제공 백업, 패치, 복구<br/>안정적인 최신 데이터베이스 엔진 버전<br/>SQL Server에서 쉽게 마이그레이션할 수 있습니다.<br/>Azure Virtual Network 내의 개인 IP 주소입니다.<br/>기본 제공 고급 인텔리전스 및 보안<br/>온라인에서 리소스(CPU/스토리지)를 변경할 수 있습니다.| SQL Server 엔진을 완전히 제어할 수 있습니다. 모든 온-프레미스 기능을 지원합니다.<br/>최대 99.99%의 가용성.<br/>일치하는 온-프레미스 SQL Server의 버전과 완전히 동등합니다.<br/>수정 되 고 잘 알려진 데이터베이스 엔진 버전입니다.<br/>SQL Server에서 쉽게 마이그레이션할 수 있습니다.<br/>Azure Virtual Network 내의 개인 IP 주소입니다.<br/>SQL Server 배치 된 호스트에서 응용 프로그램 또는 서비스를 배포할 수 있습니다.|
|SQL Server에서 마이그레이션하는 것은 어려울 수 있습니다.<br/>일부 SQL Server 기능을 사용할 수 없습니다.<br/>정확한 유지 관리 시간이 보장되지 않습니다(하지만 거의 투명함).<br/>SQL Server 버전과의 호환성은 데이터베이스 호환성 수준을 사용해야만 달성될 수 있습니다.<br/>[Azure 개인 링크](database/private-endpoint-overview.md)를 사용 하 여 개인 IP 주소 지원.|사용할 수 없는 최소한의 SQL Server 기능이 아직 있습니다.<br/>정확한 유지 관리 시간이 보장되지 않습니다(하지만 거의 투명함).<br/>SQL Server 버전과의 호환성은 데이터베이스 호환성 수준을 사용해야만 달성될 수 있습니다.|사용자가 백업 및 패치를 관리해야 합니다.<br>사용자 고유의 고가용성 솔루션을 구현해야 합니다.<br/>리소스(CPU/스토리지)를 변경하는 동안 가동 중지 시간이 발생합니다.|
| 최대 100TB의 데이터베이스. | 최대 8TB. | 최대 256 TB의 저장소를 포함 하는 인스턴스를 SQL Server 합니다. 인스턴스는 필요한 만큼 많은 데이터베이스를 지원할 수 있습니다. |
| 온-프레미스 응용 프로그램은 Azure SQL Database의 데이터에 액세스할 수 있습니다. | Azure Express 경로 또는 VPN Gateway를 사용 하 여 [기본 가상 네트워크 구현](managed-instance/vnet-existing-add-subnet.md) 및 온-프레미스 환경에 연결 | SQL 가상 컴퓨터를 사용 하면 클라우드에서 부분적으로 실행 되 고 부분적으로 온-프레미스로 실행 되는 응용 프로그램을 사용할 수 있습니다. 예를 들어 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)를 통해 클라우드로 온-프레미스 네트워크 및 Active Directory 도메인을 확장할 수 있습니다. 하이브리드 클라우드 솔루션에 대한 자세한 내용은 [클라우드로 온-프레미스 데이터 솔루션 확장](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud)을 참조하세요. |


## <a name="cost"></a>Cost

현금 strapped 시작 하 든, 예산 제약 조건에 따라 작동 하는 설정 된 회사의 팀이 든 상관 없이 데이터베이스를 호스트 하는 방법을 결정할 때 제한 된 자금은 종종 기본 드라이버입니다. 이 섹션에서는 azure SQL 서비스 제품군과 관련 된 Azure의 청구 및 라이선스 기본 사항에 대해 알아봅니다.  또한 총 애플리케이션 비용을 계산하는 방법에 대해 알아봅니다.

### <a name="billing-and-licensing-basics"></a>청구 및 라이선스 기본 사항

현재 **SQL Database** 및 **SQL Managed Instance** 모두 서비스로 판매 되 고 리소스에 대 한 가격이 다른 몇 가지 서비스 계층에서 사용할 수 있으며,이는 모두 선택한 서비스 계층 및 계산 크기에 따라 고정 요금으로 시간당 청구 됩니다. 현재 지원 되는 서비스 계층, 계산 크기 및 저장소 용량에 대 한 최신 정보는 [SQL Database에 대 한 DTU 기반 구매 모델](database/service-tiers-dtu.md) 및 [SQL Database와 SQL Managed Instance 둘 다에 대 한 vcore 기반 구매 모델](database/service-tiers-vcore.md)을 참조 하세요.

- SQL Database를 사용 하 여 기본 계층에 대해 5 $/월부터 광범위 한 가격의 요구에 맞는 서비스 계층을 선택할 수 있으며, [탄력적 풀](database/elastic-pool-overview.md) 을 만들어 데이터베이스 간에 리소스를 공유 하 여 비용을 절감 하 고 사용량 급증을 수용할 수 있습니다.
- SQL Managed Instance를 사용 하 여 사용자 고유의 라이선스를 가져올 수도 있습니다. 사용자 라이선스를 가져오는 방법에 대 한 자세한 내용은 [Azure의 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/) 또는 [Azure 하이브리드 혜택 계산기](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) 를 사용 하 여 **최대 40%를 절감**하는 방법을 참조 하세요.

또한 일반 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/)으로 발신 인터넷 트래픽에 대해 요금이 청구됩니다. 애플리케이션의 다양한 처리량 요구에 맞게 서비스 계층과 컴퓨팅 크기를 동적으로 조정할 수 있습니다.

**SQL Database** 및 **SQL Managed Instance**를 사용 하 여 데이터베이스 소프트웨어는 Azure에 의해 자동으로 구성, 패치 및 업그레이드 되므로 관리 비용이 줄어듭니다. 또한 [기본 제공 백업](database/automated-backups-overview.md) 기능을 사용하여 비용을 크게 절감할 수 있으며, 특히 데이터베이스 수가 많을 경우 그 효과가 큽니다.

**Azure vm의 SQL**을 사용 하면 플랫폼에서 제공 하는 SQL Server 이미지 (라이선스 포함)를 사용 하거나 SQL Server 라이선스를 가져올 수 있습니다. 지원 되는 모든 SQL Server 버전 (2008 R2, 2012, 2014, 2016, 2017, 2019) 및 버전 (Developer, Express, Web, Standard, Enterprise)을 사용할 수 있습니다. 또한 이미지의 BYOL(사용자 라이선스 필요)를 사용할 수 있습니다. Azure에서 제공하는 이미지를 사용할 경우 VM 크기 및 선택하는 SQL Server 버전에 따라 운영 비용이 달라집니다. VM 크기 또는 SQL Server 버전과 관계없이 VM 디스크에 대한 Azure Storage 비용과 함께 SQL Server와 Windows 또는 Linux Server의 분당 라이선스 비용을 지불합니다. 분당 청구 옵션을 사용하면 추가 SQL Server 라이선스를 구입하지 않고 필요한 시간 동안만 SQL Server를 사용할 수 있습니다. SQL Server 사용자 라이선스를 Azure로 가져오는 경우, 서버 및 스토리지 비용만 청구됩니다. 고유한 라이선스 가져오기에 대한 자세한 내용은 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)을 참조하세요. 또한 일반 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/)으로 발신 인터넷 트래픽에 대해 요금이 청구됩니다.

#### <a name="calculating-the-total-application-cost"></a>총 애플리케이션 비용 계산

클라우드 플랫폼을 사용하기 시작하면 애플리케이션을 실행하는 비용에는 새 개발 및 진행 중인 관리 비용과 퍼블릭 클라우드 플랫폼 서비스 비용이 포함됩니다.

가격에 대한 자세한 내용은 다음 리소스를 참조하세요.

- [SQL Database & SQL Managed Instance 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)
- [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) 용 [가상 머신 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>관리

많은 비즈니스의 경우 클라우드 서비스로 전환하는 결정은 드는 비용 만큼 관리의 복잡성을 오프로드하는 것과 관련됩니다. IaaS 및 PaaS를 통해 Azure는 기본 인프라를 관리 하 고 모든 데이터를 자동으로 복제 하 여 재해 복구를 제공 하 고, 데이터베이스 소프트웨어를 구성 및 업그레이드 하 고, 부하 분산을 관리 하 고, 데이터 센터 내에 서버 오류가 있는 경우 투명 장애 조치를 수행 합니다.

- **SQL Database** 및 **SQL Managed Instance**를 사용 하면 데이터베이스를 계속 관리할 수 있지만 더 이상 데이터베이스 엔진, 운영 체제 또는 하드웨어를 관리할 필요가 없습니다. 계속 관리할 수 있는 항목의 예는 데이터베이스 및 로그인, 인덱스 및 쿼리 튜닝 및 감사 및 보안을 들 수 있습니다. 또한 다른 데이터 센터에 고가용성을 구성하는 데 필요한 구성 및 관리가 최소화됩니다.
- **AZURE VM의 SQL**을 사용 하면 운영 체제 및 SQL Server 인스턴스 구성에 대 한 모든 권한을 갖습니다. VM을 사용 하 여 운영 체제 및 데이터베이스 소프트웨어를 업데이트/업그레이드할 시기 및 바이러스 백신 등의 추가 소프트웨어를 설치할 시기를 결정할 수 있습니다. 자동화된 일부 기능이 제공되어 패치, 백업 및 고가용성을 크게 간소화합니다. 또한 VM 크기, 디스크 수 및 해당 스토리지 구성도 사용자가 제어할 수 있습니다. Azure를 사용하면 VM이 필요한 크기를 변경할 수 있습니다. 자세한 내용은 [Azure용 Virtual Machine 및 클라우드 서비스 크기](../virtual-machines/windows/sizes.md)를 참조하세요.

## <a name="service-level-agreement-sla"></a>SLA (서비스 수준 계약)

많은 IT 부서의 경우 SLA (서비스 수준 계약)의 최신 의무를 충족 하는 것이 가장 중요 합니다. 이 섹션에서는 각 데이터베이스 호스팅 옵션에 어떤 SLA를 적용하는지 살펴봅니다.

**Azure SQL Database** 및 **Azure SQL Managed Instance**의 경우 Microsoft는 99.99%의 가용성 SLA를 제공 합니다. 최신 정보는 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/sql-database/)을 참조 하세요.

**AZURE VM의 SQL**의 경우 Microsoft는 가상 머신만 포함 하는 99.95%의 가용성 SLA를 제공 합니다. 이 SLA는 VM에서 실행 중인 프로세스(예: SQL Server)를 다루지 않으며 가용성 집합에 두 개 이상의 VM 인스턴스를 호스트해야 합니다. 최신 정보는 [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 참조하세요. Vm 내에서 데이터베이스 고가용성 (HA)을 사용 하려면 [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)와 같이 SQL Server 지원 되는 고가용성 옵션 중 하나를 구성 해야 합니다. 지원되는 고가용성 옵션을 사용하면 추가 SLA를 제공하지 않지만 >99.99%의 데이터베이스 가용성을 달성할 수 있습니다.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Azure로 이동해야 하는 경우

**Azure SQL Database** 는 개발자의 생산성과 새 솔루션에 대 한 빠른 출시 시간에 매우 중요 한 경우 클라우드로 설계 된 응용 프로그램에 적합 한 솔루션입니다. 프로그래밍 방식 DBA와 비슷한 기능을 사용하여 기본 운영 체제 및 데이터베이스를 관리할 필요성을 낮추므로 클라우드 설계자와 개발자에게 이상적인 솔루션입니다.

Azure **SQL Managed Instance** 를 사용 하면 기존 응용 프로그램을 azure로 마이그레이션하는 작업이 대폭 간소화 되어 마이그레이션된 데이터베이스 응용 프로그램을 azure의 시장에 빠르게 제공할 수 있습니다.

기존 응용 프로그램이 나 새 응용 프로그램에 용량이 크거나 SQL Server 또는 Windows/Linux의 모든 기능에 대 한 액세스가 필요 하 고 새로운 온-프레미스 하드웨어를 얻는 시간과 비용을 들이지 않으려는 경우 **AZURE VM의 SQL** 은 완벽 합니다. 또한 기존 온-프레미스 응용 프로그램 및 데이터베이스를 Azure로 마이그레이션하는 경우 SQL Database 또는 SQL Managed Instance 적합 하지 않은 경우에 적합 합니다. 프레젠테이션, 애플리케이션 및 데이터 계층을 변경할 필요가 없으므로 기존 솔루션 재설계에 따른 비용과 예산이 절감됩니다. 대신 모든 솔루션을 Azure에 마이그레이션하고 Azure 플랫폼에 필요할 수 있는 일부 성능 최적화를 수행하는 데 집중할 수 있습니다. 자세한 내용은 [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](virtual-machines/windows/performance-guidelines-best-practices.md)를 참조하세요.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>다음 단계

- SQL Database를 시작하려면 [첫 Azure SQL Database](database/single-database-create-quickstart.md)를 참조하세요.
- SQL Managed Instance를 시작 하려면 [첫 번째 AZURE sql Managed Instance](managed-instance/instance-create-quickstart.md) 를 참조 하세요. 
- [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.
- Azure VM에서 SQL Server를 시작하려면 [Azure에서 SQL Server 가상 머신 프로비전](virtual-machines/windows/create-sql-vm-portal.md)을 참조하세요.
- [온-프레미스 데이터베이스에 적합 한 SQL Database 또는 SQL MANAGED INSTANCE SKU를 확인](/sql/dma/dma-sku-recommend-sql-db/)합니다.
