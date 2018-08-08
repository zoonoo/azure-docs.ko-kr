---
title: SQL(PaaS) 데이터베이스 vs  VM에서 클라우드의 SQL Server(IaaS) | Microsoft Docs
description: 다음 중 어떤 클라우드 SQL Server 옵션이 응용 프로그램에 맞는지 알아봅니다. Azure SQL (PaaS) 데이터베이스 또는 Azure Virtual Machines 클라우드의 SQL Server.
services: sql-database, virtual-machines
keywords: SQL Server 클라우드, 클라우드의 SQL Server, PaaS 데이터베이스, 클라우드 SQL Server, DBaaS
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: a6d6a7639d3db0cc7d194ca9fae126ad9a2cc3ba
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413657"
---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>클라우드 SQL Server 옵션 선택: Azure SQL(PaaS) 데이터베이스 또는 Azure VM의 SQL Server(IaaS)

Azure에서 SQL Server 워크로드가 호스트된 인프라(IaaS)에서 실행 중이거나 호스트된 서비스([PaaS](https://azure.microsoft.com/overview/what-is-paas/))로 실행 중일 수 있습니다.

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): 최신 응용 프로그램 개발에 최적화된 SQL Server Enterprise Edition을 기반으로 하는 SQL 데이터베이스 엔진입니다. Azure SQL Database는 여러 가지 배포 옵션을 제공합니다.
  - 단일 데이터베이스를 [논리 서버](sql-database-logical-servers.md)에 배포할 수 있습니다.
  - [논리 서버](sql-database-logical-servers.md)의 [탄력적 풀](sql-database-elastic-pool.md)에 배포하여 리소스를 공유하고 비용을 줄일 수 있습니다. 

      > [!NOTE]
      > 단일 데이터베이스 및 풀링된 데이터베이스를 포함하는 Azure SQL Database는 SQL Server의 데이터베이스 범위 기능을 대부분 제공합니다.

      다음 그림에서는 이러한 배포 옵션을 보여줍니다.

      ![배포 옵션](./media/sql-database-technical-overview/deployment-options.png) 
  - [Azure SQL Database Managed Instance(미리 보기)](sql-database-managed-instance.md)에 배포할 수 있습니다. 

      > [!NOTE]
      > 두 버전 모두에서 Azure SQL Database는 기본 제공 인텔리전스 및 관리와 같은, SQL Server에서 사용할 수 없는 추가 기능을 제공합니다. 첫 번째 버전의 경우 Azure SQL Database Managed Instance에서 Azure SQL Database는 데이터베이스 및 추가 인스턴스 범위 기능에 대한 공유 리소스를 제공합니다. Azure SQL Database 관리되는 인스턴스는 데이터베이스 변경 없이 또는 최소한의 변경으로 데이터베이스 마이그레이션을 지원합니다.
- [Azure Virtual Machines의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/): Azure에서 실행 중인 Windows Server 또는 Linux VM(Virtual Machines)의 클라우드에 설치되고 호스트된 SQL Server로, IaaS(infrastructure as a service)라고도 합니다. Azure Virtual Machines의 SQL Server는 데이터베이스 변경 없이 온-프레미스 SQL Server 데이터베이스 및 응용 프로그램을 마이그레이션하는 좋은 옵션입니다. 모든 최신 버전의 SQL Server를 IaaS 가상 머신에 설치할 수 있습니다. SQL Database와의 가장 중요한 차이점은 SQL Server VM에서는 데이터베이스 엔진을 완전히 제어할 수 있다는 것입니다. 유지 관리/패치가 시작되는 시기를 선택하고, 더 적은 로그로 더 빠르게 로드되도록 복구 모델을 단순 또는 대량 로그로 변경하고, 필요한 경우, 엔진을 일시 중지 또는 시작하고, SQL Server 데이터베이스 엔진을 완전히 사용자 지정할 수 있습니다. 이러한 추가 제어 기능과 더불어 가상 머신을 관리해야 하는 책임도 추가됩니다.

각 배포 옵션이 Microsoft 데이터 플랫폼에 얼마나 적합한지 알아보고 비즈니스 요구 사항에 적합한 옵션을 찾는 데 도움이 됩니다. 비용 절감이 우선이든 관리 최소화가 무엇보다 중요하든 이 문서에서는 가장 관심 있는 비즈니스 요구 사항에 대해 어떤 접근 방식이 결과를 가져오는지 결정하도록 도울 수 있습니다.

## <a name="microsofts-sql-data-platform"></a>Microsoft SQL 데이터 플랫폼

Azure와 온-프레미스 SQL Server 데이터베이스를 비교 논의할 때 우선 이 둘을 모두 사용할 수 있다는 점을 이해해야 합니다. Microsoft의 데이터 플랫폼에서는 SQL Server 기술을 활용하며 물리적 온-프레미스 컴퓨터, 사설 클라우드 환경, 타사 호스팅 사설 클라우드 환경 및 공용 클라우드 전반에서 이 기술을 사용할 수 있습니다. Azure 가상 머신에서 SQL Server를 사용하면 온-프레미스 배포와 클라우드에 호스트된 배포를 조합하여 고유하고 다양한 비즈니스 요구 사항을 충족하고, 이러한 환경 전반에서 일련의 서버 제품, 개발 도구 및 전문 지식을 동일하게 사용할 수 있습니다.

   ![클라우드 SQL Server 옵션: IaaS의 SQL server 또는 클라우드의 SaaS SQL 데이터베이스.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

다이어그램에 나온 대로 각 제품의 특징을 인프라에 대한 관리 수준(X축)과 데이터베이스 수준 통합 및 자동화를 통해 얻는 비용 효율성의 정도(Y축)를 기준으로 설명할 수 있습니다.

응용 프로그램을 설계할 때 다음 네 가지 기본 옵션을 사용하여 응용 프로그램의 SQL Server 부분을 호스트할 수 있습니다.

* 가상화되지 않은 물리적 컴퓨터의 SQL Server
* 온-프레미스 가상화 컴퓨터의 SQL Server(사설 클라우드)
* Azure Virtual Machine의 SQL Server(Microsoft 공용 클라우드)
* Azure SQL Database(Microsoft 공용 클라우드)

다음 섹션에서는 Microsoft 공용 클라우드의 SQL Server인 Azure SQL Database 및 Azure VM의 SQL Server에 대해 알아봅니다. 또한 응용 프로그램에 가장 적합한 옵션을 결정하는 데 영향을 미치는 일반적인 비즈니스 동인도 살펴봅니다.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Azure SQL Database 및 Azure VM의 SQL Server에서 자세히 보기

**Azure SQL Database**는 *PaaS(Platform as a Service)* 산업 범주에 해당하는, Azure 클라우드에 호스트된 관계형 DBaaS(Database as a Service)입니다. [SQL 데이터베이스](sql-database-technical-overview.md) 는 Microsoft에서 소유하고 호스트하고 유지 관리하는 표준화된 하드웨어 및 소프트웨어를 기반으로 구축됩니다. SQL Database를 통해 SQL Server의 광범위한 구성이 필요한 기본 제공 기능을 사용할 수 있습니다. SQL Database를 사용할 경우 추가 성능에 맞게 중단 없이 강화 및 확장하는 옵션을 통해 사용량에 따라 지불합니다. Azure SQL Database는 클라우드에서 새 응용 프로그램을 개발하는 데 이상적인 환경입니다. [Azure SQL Database 관리되는 인스턴스](sql-database-managed-instance.md)로 사용자 라이선스를 가져올 수도 있습니다. 또한 이 옵션은 Azure SQL Database의 모든 PaaS 혜택을 제공하지만 이전에 SQL VM에서만 사용할 수 있었던 기능을 추가합니다. 여기에는 기본 가상 네트워크(VNet) 및 온-프레미스 SQL Server와의 거의 100% 호환성이 포함됩니다. [관리되는 인스턴스](sql-database-managed-instance.md)는 최소 변경으로 온-프레미스 데이터베이스를 Azure로 마이그레이션하는 데 이상적입니다. 

**Azure VM(Virtual Machines)의 SQL Server** 는 업계 범주에 속하는 *IaaS(Infrastructure as a service)* 산업 부분으로 분류되며 클라우드의 Virtual Machines 내에서 SQL Server를 실행할 수 있도록 지원합니다. [SQL Server 가상 머신](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)은 Microsoft에서 소유, 호스팅 및 유지 관리하는 표준화된 하드웨어에서도 실행됩니다. VM에서 SQL Server를 사용하는 경우 SQL Server 이미지에 이미 포함된 SQL Server 라이선스에 종량제를 사용하거나 기존 라이선스를 쉽게 사용할 수 있습니다. 필요에 따라 VM을 중지하거나 다시 시작할 수도 있습니다.

일반적으로 이러한 두 SQL 옵션은 다음과 같이 최적의 용도가 서로 다릅니다.

* **Azure SQL Database**는 여러 데이터베이스를 프로비전 및 관리하는 전체 관리 비용을 최소한으로 줄이는 데 최적화되어 있습니다. 가상 머신, 운영 체제 또는 데이터베이스 소프트웨어를 관리할 필요가 없으므로 지속적인 관리 비용을 줄입니다. 업그레이드, 고가용성 또는 [백업](sql-database-automated-backups.md)을 관리할 필요가 없습니다. 일반적으로 Azure SQL Database는 단일 IT 또는 개발 리소스로 관리되는 데이터베이스 수를 크게 늘립니다. 또한 [탄력적 풀](sql-database-elastic-pool.md)은 테넌트 격리, 데이터베이스 간에 리소스를 공유하여 비용을 줄이는 기능을 비롯한 기능으로 SaaS 다중 테넌트 응용 프로그램 아키텍처를 지원합니다. [Azure SQL Database 관리되는 인스턴스](sql-database-managed-instance.md)는 데이터베이스 간에 리소스를 공유하는 기능뿐만 아니라 기존 응용 프로그램을 쉽게 마이그레이션할 수 있게 해주는 인스턴스 범위 기능을 지원합니다.
* **Azure VM에서 실행 중인 SQL Server** 는 기존 응용 프로그램을 Azure로 마이그레이션하거나 기존 온-프레미스 응용 프로그램을 하이브리드 배포의 클라우드로 확장하는 데 최적화됩니다. 또한 가상 머신에서 SQL Server를 사용하여 기존 SQL Server 응용 프로그램을 개발하고 테스트할 수 있습니다. Azure VM의 SQL Server를 사용할 경우 전용 SQL Server 인스턴스와 클라우드 기반 VM에 대한 모든 관리 권한을 갖습니다. 이 옵션은 가상 머신을 유지 관리하는 IT 리소스를 이미 갖춘 조직에게 가장 적합합니다. 이러한 기능을 사용하면 응용 프로그램 관련 성능 및 가용성 요구 사항을 해결하는 고도로 사용자 지정된 시스템을 구축할 수 있습니다.

다음 테이블에는 SQL Database와 Azure VM의 SQL Server의 주요 특징이 요약되어 있습니다.

| | Azure SQL Database<br>논리 서버, 탄력적 풀 및 단일 데이터베이스 | Azure SQL Database<br>관리되는 인스턴스 |Azure Virtual Machine<br>SQL Server |
| --- | --- | --- |---|
| **최적 용도:** |개발 및 마케팅에서 시간 제약 조건이 있고 안정적인 최신 SQL Server 기능을 사용하려는, 클라우드용으로 설계된 새 응용 프로그램 | 최소한의 변경으로 클라우드로 마이그레이션되며 안정적인 최신 SQL Server 기능을 사용하려는 새 응용 프로그램 또는 기존 온-프레미스 응용 프로그램  | 변경 없이 또는 최소한의 변경으로 클라우드로 빠르게 마이그레이션해야 하는 기존 응용 프로그램 온-프레미스 개발 및 테스팅 SQL Server 하드웨어를 구입하지 않는 빠른 개발 및 테스트 시나리오 |
|  | 데이터베이스에 대한 기본 제공 고가용성, 재해 복구 및 업그레이드가 필요한 팀 | SQL Database와 동일 | SQL Server에 대한 고가용성, 재해 복구 및 패치를 구성, 미세 조정, 사용자 지정 및 관리할 수 있는 팀 자동화된 기능을 제공하는 일부 팀은 이를 크게 간소화합니다. | |
|  | 기본 운영 체제 및 구성 설정을 관리하지 않으려는 팀입니다. | SQL Database와 동일 | 모든 관리 권한이 있는 사용자 지정 환경이 필요합니다. | |
|  | 확장 패턴을 사용하여 [가로 또는 세로로 분할](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) 될 수 있는 최대 4TB 이상의 데이터베이스 | SQL Database와 동일 | 저장소의 최대 64TB를 사용하는 SQL Server 인스턴스 인스턴스는 필요한 만큼 많은 데이터베이스를 지원할 수 있습니다. |
| **호환성** | 대부분의 온-프레미스 데이터베이스 수준 기능을 지원합니다. | 거의 모든 온-프레미스 인스턴스 수준 및 데이터베이스 수준 기능을 지원합니다. | 모든 온-프레미스 기능을 지원합니다. |
| **리소스:** | 기본 인프라를 구성하고 관리하는 데 IT 리소스를 사용하지 않지만 응용 프로그램 계층에 중점을 두려고 합니다. | SQL Database와 동일 | 구성 및 관리를 위한 일부 IT 리소스가 있습니다. 자동화된 기능을 제공하는 일부 팀은 이를 크게 간소화합니다. |
| **총 소유 비용** | 하드웨어 비용을 제거하고 관리 비용을 절감합니다. | SQL Database와 동일 | 하드웨어 비용을 제거합니다. |
| **비즈니스 연속성:** |Azure SQL Database는 [기본 제공된 내결함성 인프라 기능](sql-database-high-availability.md) 외에도 [자동화된 백업](sql-database-automated-backups.md), [특정 시점 복원](sql-database-recovery-using-backups.md#point-in-time-restore), [지리적 복원](sql-database-recovery-using-backups.md#geo-restore), [장애 조치(failover) 그룹 및 활성 지역 복제](sql-database-geo-replication-overview.md) 등의 기능을 제공하여 비즈니스 연속성을 향상합니다. 자세한 내용은 [SQL Database 비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요. | SQL Database와 동일하고, 사용자 시작 복사 전용 백업을 사용할 수 있습니다. | Azure VM의 SQL Server를 사용하면 데이터베이스의 특정 요구에 맞게 고가용성 및 재해 복구 솔루션을 설정할 수 있습니다. 따라서 시스템을 응용 프로그램에 최적화할 수 있습니다. 필요한 경우 장애 조치(Failover)를 직접 테스트하고 실행할 수 있습니다. 자세한 내용은 [Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md)를 참조하세요. |
| **하이브리드 클라우드:** |온-프레미스 응용 프로그램은 Azure SQL Database의 데이터에 액세스할 수 있습니다. | Azure ExpressRoute 또는 VPN Gateway를 사용하여 [기본 가상 네트워크 구현](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-vnet-configuration) 및 온-프레미스 환경에 연결 | Azure VM의 SQL Server를 사용하면 응용 프로그램을 일부는 클라우드에서, 일부는 온-프레미스에서 실행할 수 있습니다. 예를 들어 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)를 통해 클라우드로 온-프레미스 네트워크 및 Active Directory 도메인을 확장할 수 있습니다. 또한 [Azure의 SQL Server 데이터 파일](http://msdn.microsoft.com/library/dn385720.aspx)을 사용하여 온-프레미스 데이터 파일을 Azure Storage에 저장할 수도 있습니다. 자세한 내용은 [SQL Server 2014 하이브리드 클라우드 소개](http://msdn.microsoft.com/library/dn606154.aspx)를 참조하세요. |
|  | [SQL Server 트랜잭션 복제](https://msdn.microsoft.com/library/mt589530.aspx) 를 구독자로 지원하여 데이터를 복제합니다. | Azure SQL Database 관리되는 인스턴스에서는 복제가 지원되지 않습니다. | [SQL Server 트랜잭션 복제](https://msdn.microsoft.com/library/mt589530.aspx), [Always On 가용성 그룹](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services 및 로그 전달을 완전히 지원하여 데이터를 복제합니다. 또한 기존의 SQL Server 백업은 완벽하게 지원됩니다. | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Azure SQL Database 또는 Azure VM의 SQL Server를 선택하는 경우 비즈니스 동기
### <a name="cost"></a>비용
현금이 쪼들리는 신생 기업이든 자리를 잡은 회사에서 빠듯한 예산으로 운영되는 팀이든 데이터베이스를 호스트하는 방법을 결정할 때 제한된 자금이 주된 동인으로 작용하는 경우가 많습니다. 이 섹션에서는 SQL Database 및 Azure VM의 SQL Server 등 두 가지 관계형 데이터베이스 옵션에 대한 Azure의 청구 및 라이선스 기본 사항을 알아봅니다. 또한 총 응용 프로그램 비용을 계산하는 방법에 대해 알아봅니다.

#### <a name="billing-and-licensing-basics"></a>청구 및 라이선스 기본 사항

현재 **SQL Database**는 서비스로 판매되며, 각기 다른 리소스 요금이 책정된 여러 서비스 계층에서 사용할 수 있습니다. 모두 선택하는 서비스 계층과 성능 수준을 기준으로 고정 요금이 시간당 청구됩니다. SQL Database 관리되는 인스턴스에서 사용자 라이선스를 가져올 수도 있습니다. 고유한 라이선스 가져오기에 대한 자세한 내용은 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)을 참조하세요. 또한 일반 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/)으로 발신 인터넷 트래픽에 대해 요금이 청구됩니다. 응용 프로그램의 다양한 처리량 요구에 맞게 서비스 계층과 성능 수준을 동적으로 조정할 수 있습니다. 현재 지원되는 서비스 계층에 대한 최신 정보는 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 참조하세요. 비용을 줄이고 사용량 급증을 수용하기 위해 [탄력적 풀](sql-database-elastic-pool.md)을 만들어 데이터베이스 인스턴스 간에 리소스를 공유할 수도 있습니다.

**SQL Database**에서 데이터베이스 소프트웨어는 Microsoft에서 구성, 패치 적용 및 업그레이드를 수행하며 이는 관리 비용을 줄입니다. 또한 [기본 제공 백업](sql-database-automated-backups.md) 기능을 사용하여 비용을 크게 절감할 수 있으며, 특히 데이터베이스 수가 많을 경우 그 효과가 큽니다. 

**Azure VM의 SQL Server**로 플랫폼에서 제공하는 SQL Server 이미지(라이선스 포함)를 사용하거나 사용자의 SQL Server 라이선스를 가져올 수 있습니다. 지원되는 모든 SQL Server 버전(2008R2, 2012, 2014, 2016) 및 버전(Developer, Express, Web, Standard, Enterprise)을 사용할 수 있습니다. 또한 이미지의 BYOL(사용자 라이선스 필요)를 사용할 수 있습니다. Azure에서 제공하는 이미지를 사용할 경우 VM 크기 및 선택하는 SQL Server 버전에 따라 운영 비용이 달라집니다. VM 크기 또는 SQL Server 버전과 관계없이 VM 디스크에 대한 Azure Storage 비용과 함께 SQL Server와 Windows 또는 Linux Server의 분당 라이선스 비용을 지불합니다. 분당 청구 옵션을 사용하면 추가 SQL Server 라이선스를 구입하지 않고 필요한 시간 동안만 SQL Server를 사용할 수 있습니다. SQL Server 사용자 라이선스를 Azure로 가져오는 경우, 서버 및 저장소 비용만 청구됩니다. 고유한 라이선스 가져오기에 대한 자세한 내용은 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)을 참조하세요. 또한 일반 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/)으로 발신 인터넷 트래픽에 대해 요금이 청구됩니다.

#### <a name="calculating-the-total-application-cost"></a>총 응용 프로그램 비용 계산
클라우드 플랫폼을 사용하기 시작하면 응용 프로그램을 실행하는 비용에는 새 개발 및 진행 중인 관리 비용과 공용 클라우드 플랫폼 서비스 비용이 포함됩니다.

**Azure SQL Database를 사용하는 경우:**

- 최소화된 관리 비용
- 마이그레이션된 응용 프로그램에 대한 제한된 개발 비용
- SQL Database 서비스 비용
- 하드웨어 구매 비용 없음

**Azure VM의 SQL Server를 사용하는 경우:**

- 높은 관리 비용
- 마이그레이션된 응용 프로그램에 대한 제한된 개발 비용 또는 개발 비용 없음
- 가상 머신 서비스 비용
- SQL Server 라이선스 비용
- 하드웨어 구매 비용 없음

가격에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [SQL Database 가격](https://azure.microsoft.com/pricing/details/sql-database/)
* [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)에 대한 [Virtual Machine 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>관리
많은 비즈니스의 경우 클라우드 서비스로 전환하는 결정은 드는 비용 만큼 관리의 복잡성을 오프로드하는 것과 관련됩니다. IaaS 및 PaaS에서는 Microsoft가 기본 인프라를 관리하고, 모든 데이터를 자동으로 복제하여 재해 복구를 제공하고, 데이터베이스 소프트웨어를 구성 및 업그레이드하고, 부하 분산을 관리하고, 데이터 센터 내에서 서버 오류가 발생하는 경우 장애 조치(Failover)를 투명하게 수행합니다. 

- **Azure SQL Database**에서는 데이터베이스를 계속 관리할 수 있지만 데이터베이스 엔진, 서버 운영 체제 또는 하드웨어를 더 이상 관리할 필요가 없습니다.  계속 관리할 수 있는 항목의 예는 데이터베이스 및 로그인, 인덱스 및 쿼리 튜닝 및 감사 및 보안을 들 수 있습니다. 또한 다른 데이터 센터에 고가용성을 구성하는 데 필요한 구성 및 관리가 최소화됩니다.
- **Azure VM에 대한 SQL Server**에서는 운영 체제와 SQL Server 인스턴스 구성을 완벽히 제어할 수 있습니다. VM에서는 운영 체제 및 데이터베이스 소프트웨어를 업데이트/업그레이드할 시기 및 바이러스 백신 등의 추가 소프트웨어를 설치할 시기를 사용자가 결정합니다. 자동화된 일부 기능이 제공되어 패치, 백업 및 고가용성을 크게 간소화합니다. 또한 VM 크기, 디스크 수 및 해당 저장소 구성도 사용자가 제어할 수 있습니다. Azure를 사용하면 VM이 필요한 크기를 변경할 수 있습니다. 자세한 내용은 [Azure용 Virtual Machine 및 클라우드 서비스 크기](../virtual-machines/windows/sizes.md)를 참조하세요. 

### <a name="service-level-agreement-sla"></a>SLA(서비스 수준 계약)
IT 부서의 경우 SLA(서비스 수준 계약)의 작동 시간 의무를 충족하는 일이 가장 우선합니다. 이 섹션에서는 각 데이터베이스 호스팅 옵션에 어떤 SLA를 적용하는지 살펴봅니다.

Microsoft는 **SQL Database**에 대해 99.99%의 가용성 SLA를 제공합니다. 최신 정보는 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/sql-database/)을 참조하세요. 

**Azure VM에서 실행 중인 SQL Server**의 경우 Microsoft는 Virtual Machine을 다루는 99.95%의 가용성 SLA를 제공합니다. 이 SLA는 VM에서 실행 중인 프로세스(예: SQL Server)를 다루지 않으며 가용성 집합에 두 개 이상의 VM 인스턴스를 호스트해야 합니다. 최신 정보는 [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 참조하세요. VM 내에서 데이터베이스 고가용성(HA)을 이용하려면 SQL Server에서 지원되는 고가용성 옵션 중 하나(예: [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server))를 구성해야 합니다. 지원되는 고가용성 옵션을 사용하면 추가 SLA를 제공하지 않지만 >99.99%의 데이터베이스 가용성을 달성할 수 있습니다.

### <a name="market"></a>Azure로 이동해야 하는 경우
**SQL Database 논리 서버, 탄력적 풀 및 단일 데이터베이스**는 새 솔루션에 대한 개발자 생산성과 빠른 출시 시간이 중요한 경우,클라우드용으로 설계된 응용 프로그램에 적합한 솔루션입니다. 프로그래밍 방식 DBA와 비슷한 기능을 사용하여 기본 운영 체제 및 데이터베이스를 관리할 필요성을 낮추므로 클라우드 설계자와 개발자에게 이상적인 솔루션입니다. 

**SQL Database 관리되는 인스턴스**는 기존 응용 프로그램을 Azure SQL Database로 마이그레이션하는 과정을 간소화하므로 Azure에서 마이그레이션된 데이터베이스 응용 프로그램을 빠르게 출시할 수 있습니다.

**Azure VM에서 실행 중인 SQL Server**는 기존 응용 프로그램 또는 새 응용 프로그램에 큰 데이터베이스가 필요하거나 SQL Server 또는 Windows/Linux의 모든 기능에 액세스해야 하며 새로운 온-프레미스 하드웨어를 획득하는 시간과 비용을 피하려는 경우에 유용합니다. 또한 기존 온-프레미스 응용 프로그램 및 데이터베이스를 Azure로 그대로 마이그레이션하려고 하며 Azure SQL Database 관리되는 인스턴스가 적합하지 않은 경우에 효과적입니다. 프레젠테이션, 응용 프로그램 및 데이터 계층을 변경할 필요가 없으므로 기존 솔루션 재설계에 따른 비용과 예산이 절감됩니다. 대신 모든 솔루션을 Azure에 마이그레이션하고 Azure 플랫폼에 필요할 수 있는 일부 성능 최적화를 수행하는 데 집중할 수 있습니다. 자세한 내용은 [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)를 참조하세요.

## <a name="summary"></a>요약
이 문서는 Azure Virtual Machines(VM)에서 SQL Database 및 SQL Server를 탐색했고 결정하는 데 영향을 줄 수 있는 일반적인 비즈니스 동인을 설명했습니다. 다음은 고려할 제안 사항을 요약한 내용입니다.

**Azure SQL Database** 를 선택하는 경우:

* 새로운 클라우드 기반 응용 프로그램을 빌드하여 클라우드 서비스가 제공하는 비용 절감 및 성능 최적화를 활용합니다. 이 방법은 완전히 관리되는 클라우드 서비스의 이점을 제공하고 초기 출시 시기를 낮춰주며 장기 비용을 최적화할 수 있습니다.
* 데이터베이스의 일반 관리 작업을 Microsoft로 하여금 수행하게 하고 데이터베이스에 대해 높은 가용성 SLA가 필요한 경우.
* 기존 응용 프로그램을 Azure SQL Database 관리되는 인스턴스로 그대로 마이그레이션하고 SQL Server 및/또는 고급 보안 및 네트워킹을 통해 추가 패리티를 활용하려는 경우. 관리되는 인스턴스는 새 응용 프로그램과 기존 응용 프로그램 둘 다에 적합한 선택입니다.

**Azure VM의 SQL Server** 를 선택하는 경우:

* 기존 온-프레미스 응용 프로그램을 마이그레이션 또는 클라우드로 확장하거나 4TB보다 큰 엔터프라이즈 응용 프로그램을 빌드하려는 경우. 이 방법은 선택한 SQL Server 버전 사용, 큰 데이터베이스 용량, SQL Server 및 Windows/Linux에 대한 완벽한 제어, 온-프레미스에 대한 안전한 터널링이라는 이점을 제공합니다. 이 방법은 기존 응용 프로그램의 개발 및 수정에 대한 비용을 최소화합니다.
* 기존 IT 리소스가 있다면 패치, 백업 및 데이터베이스 고가용성을 궁극적으로 활용할 수 있습니다. 일부 자동화된 기능은 이러한 작업을 크게 간소화합니다. 

## <a name="next-steps"></a>다음 단계

* SQL Database를 시작하려면 [첫 Azure SQL Database](sql-database-get-started-portal.md)를 참조하세요.
* [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.
* Azure VM에서 SQL Server를 시작하려면 [Azure에서 SQL Server 가상 머신 프로비전](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요.