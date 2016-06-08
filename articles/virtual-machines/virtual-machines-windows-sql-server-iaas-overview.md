<properties
	pageTitle="가상 컴퓨터의 SQL Server 개요 | Microsoft Azure"
	description="Azure 가상 컴퓨터에서 클라우드의 SQL Server 데이터베이스 실행 시작 이 IaaS(Infrastructure-as-a-Service) 모델을 사용하면 Azure에서 SQL Server 워크로드를 실행할 수 있습니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/25/2016"
	ms.author="jroth"/>

# Azure 가상 컴퓨터의 SQL Server 개요

[Azure 가상 컴퓨터에서 실행되는 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)를 사용하면 SQL Server 데이터베이스를 클라우드에 호스트할 수 있습니다. 예를 들어, 온-프레미스 데이터베이스를 Windows Server 2012 R2 및 SQL Server 2014 Enterprise edition을 사용하여 미리 구성된 Azure VM에 마이그레이션할 수 있습니다. 하지만, 온-프레미스 네트워크에 대한 연결과 함께 고가용성 또는 하이브리드 아키텍처를 지원하는 다중 컴퓨터 구성 같은 다른 가능한 시나리오가 많이 있습니다.

보다 명확한 개요를 보려면 다음 동영상을 시청합니다.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## SQL 제품

Azure VM에서 SQL Server를 실행하는 것은 관계형 데이터를 Azure에 저장하기 위한 한 가지 옵션입니다. 다음 테이블은 여러 가지 제품에 대한 요약입니다.

|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| SQL 제품 | 설명 |
|---:|---|---|
|![Azure 가상 컴퓨터의 SQL Server](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-virtual-machine.png)|[Azure 가상 컴퓨터의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)|Azure 가상 컴퓨터에서 SQL Server를 실행합니다. 가상 컴퓨터를 직접 관리하고 SQL Server 정품 버전에서 데이터베이스를 실행합니다. |
|![SQL 데이터베이스](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-database.png)|[SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/)|SQL 데이터베이스 서비스를 사용하여 기본 인프라를 관리할 필요 없이, 데이터베이스를 액세스하고 크기를 조정합니다.|
|![SQL 데이터 웨어하우스](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-data-warehouse.png)|[SQL 데이터 웨어하우스](https://azure.microsoft.com/services/sql-data-warehouse/)|Azure SQL 데이터 웨어하우스를 사용하여 대량의 관계형 및 비관계형 데이터를 처리합니다. 확장성 있는 데이터 웨어하우징 기능을 서비스로 제공합니다.|
|![SQL Server 스트레치 데이터베이스](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-stretch-database.png)|[SQL Server 스트레치 데이터베이스](https://azure.microsoft.com/services/sql-server-stretch-database/)|Microsoft SQL Server 2016의 온-프레미스 트랜잭션 데이터를 Azure에 동적으로 스트레치합니다.|

>[AZURE.NOTE] SQL VM과 SQL 데이터베이스 간의 자세한 비교는 [클라우드 SQL Server 옵션 선택: Azure SQL(PaaS) 데이터베이스 또는 Azure VM의 SQL Server(IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)를 참조하세요.

## SQL Server VM 배포

Azure에서 SQL Server 가상 컴퓨터를 만들려면 먼저 Azure 플랫폼 구독을 얻어야 합니다. [구입 옵션](https://azure.microsoft.com/pricing/purchase-options/)에서 Azure 구독을 구입할 수 있습니다. 무료로 사용해 보려면 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 방문하세요.

구독에 등록한 후 Azure에서 SQL Server 가상 컴퓨터를 배포하는 가장 쉬운 방법은 [Azure에서 SQL Server 컴퓨터 갤러리 이미지를 프로비전](virtual-machines-windows-portal-sql-server-provision.md)하는 것입니다. 이러한 이미지에는 VM 가격에 포함된 SQL Server의 라이선스가 포함되어 있습니다.

Azure 가상 컴퓨터를 만들고 관리하기 위한 모델이 두 가지(클래식 및 리소스 관리자) 있다는 점에 유의해야 합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](../resource-manager-deployment-model.md)를 참조하세요. 이 문서처럼 클래식과 Resource Manager 양쪽 모두에 적용되는 게 아니라면, 각 항목에 대한 대상 모델이 분명하게 언급됩니다.

## SQL VM 이미지 선택
다음 표에서는 가상 컴퓨터 갤러리의 사용 가능한 SQL Server 이미지의 매트릭스를 제공합니다. 표에서 버전 및 운영 체제를 기반으로 링크를 클릭합니다. 그런 다음 마켓플레이스 페이지에서 **가상 컴퓨터 만들기** 단추를 클릭합니다.

|SQL Server 버전|운영 체제|SQL Server 버전|
|---|---|---|
|**SQL Server 2016 RC**|Windows Server 2012 R2|[평가](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rc3evaluationwindowsserver2012r2/)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1standardwindowsserver2012r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1webwindowsserver2012r2/), [Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1expresswindowsserver2012r2/)|
|**SQL Server 2014**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/), [Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2expresswindowsserver2012/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3enterprisewindowsserver2008r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3standardwindowsserver2008r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3webwindowsserver2008r2/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2012|[Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3expresswindowsserver2012/)|

>[AZURE.NOTE] CEIP(사용자 환경 개선 프로그램)를 사용하도록 기본 설정되어 있습니다. 필요한 경우, 가상 컴퓨터를 프로비전한 후에 CEIP를 사용자 지정하거나 비활성화할 수 있습니다. 원격 데스크톱을 사용하여 VM에 연결하고 **SQL Server 오류 및 사용 보고** 유틸리티를 실행합니다.

새 SQL VM을 만드는 데 지원이 필요한가요? [프로비전 자습서](virtual-machines-windows-portal-sql-server-provision.md)에서 자세한 설명을 참조하세요.

미리 구성된 이미지뿐 아니라 사전 설치된 SQL Server 없이 [Azure 가상 컴퓨터 만들기](virtual-machines-windows-hero-tutorial.md)를 수행할 수도 있습니다. 라이선스가 있다면 어떤 SQL Server 인스턴스든 설치할 수 있습니다. Azure 가상 컴퓨터에서 SQL Server를 실행하기 위해 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)을 사용하여 라이선스를 Azure로 마이그레이션합니다. 이 시나리오에서는 가상 컴퓨터와 관련된 Azure 계산 및 저장소 [비용](https://azure.microsoft.com/pricing/details/virtual-machines/)에 대해서만 지불합니다.

SQL Server 이미지에 가장 적합한 가상 컴퓨터 구성 설정을 확인하려면 [SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)를 검토하세요. 프로덕션 워크로드의 경우 SQL Server Enterprise 버전에 대한 가상 컴퓨터의 최소 권장 크기는 **DS3**입니다. Standard 버전에서 프로덕션 워크로드에 대한 가상 컴퓨터의 최소 권장 크기는 **DS2**입니다.

## 데이터 마이그레이션

SQL Server 가상 컴퓨터를 준비하고 실행한 후 기존 데이터베이스를 컴퓨터로 마이그레이션할 수도 있습니다. 마이그레이션 옵션 목록 및 지침은 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)을 참조하세요.

## 고가용성

고가용성이 필요한 경우 SQL Server 가용성 그룹을 구성하는 것이 좋습니다. 여기에는 가상 네트워크의 여러 Azure VM이 포함됩니다. Azure 포털에는 사용자를 위해 이 구성을 설정하는 템플릿이 있습니다. 자세한 내용은 [Azure Resource Manager 가상 컴퓨터에서 AlwaysOn 가용성 그룹 구성](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)을 참조하세요.

가용성 그룹과 연결된 수신기를 수동으로 구성하려면 [Azure VM의 AlwaysOn 가용성 그룹 구성](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)을 참조하세요.

다른 고가용성 고려 사항은 [Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)를 참조하세요.

## 백업 및 복원
온-프레미스 데이터베이스의 경우 Azure는 SQL Server 백업 파일을 저장하는 보조 데이터 센터의 역할을 할 수 있습니다. 백업 및 복원 옵션에 대한 개요는 [Azure 가상 컴퓨터에서 SQL Server 백업 및 복원](virtual-machines-windows-sql-backup-recovery.md)을 참조하세요.

[URL에 대한 SQL Server 백업](https://msdn.microsoft.com/library/dn435916.aspx)에서는 Azure 백업 파일을 Azure Blob 저장소에 저장합니다. [SQL Server 관리되는 백업](https://msdn.microsoft.com/library/dn449496.aspx)에서는 Azure에서의 백업 및 보존을 예약할 수 있습니다. 온-프레미스 SQL Server 인스턴스 또는 Azure VM에서 실행되는 SQL Server에서 이러한 서비스를 사용할 수 있습니다. 또한 Azure VM에서는 SQL Server에 대한 [자동화된 백업](virtual-machines-windows-classic-sql-automated-backup.md) 및 [자동화된 패치 적용](virtual-machines-windows-classic-sql-automated-patching.md)을 활용할 수 있습니다.

## 다음 단계

먼저, [Azure 포털에 자신의 SQL Server VM을 만듭니다](virtual-machines-windows-portal-sql-server-provision.md).

그런 다음 SQL Server 워크로드를 Azure VM으로 이동하는 것을 고려하며 [성능 모범 사례](virtual-machines-windows-sql-performance.md) 및 [마이그레이션 기법](virtual-machines-windows-migrate-sql.md)을 검토합니다.

Azure 가상 컴퓨터의 SQL Server에 대한 질문이 더 있으신가요? 먼저 [Azure 가상 컴퓨터의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md)를 참조하세요. 또한 SQL VM 항목의 맨 아래에 질문 또는 의견을 추가하여 Microsoft 및 커뮤니티와 상호 의견을 교환하세요.

<!---HONumber=AcomDC_0525_2016-->