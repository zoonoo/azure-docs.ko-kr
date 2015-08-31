<properties 
	pageTitle="Azure 가상 컴퓨터의 SQL Server 개요" 
	description="이 문서에서는 Azure IaaS 가상 컴퓨터에서 호스트되는 SQL Server에 대한 개요를 제공합니다. 여기에는 세부 콘텐츠에 대한 링크가 포함되어 있습니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="08/18/2015"
	ms.author="jroth"/>

# Azure 가상 컴퓨터의 SQL Server 개요

## 개요
AlwaysOn 가용성 그룹 및 Azure 가상 네트워크를 사용하여 단일 데이터베이스 서버에서 다중 컴퓨터 구성에 이르는 다양한 구성으로 [Azure 가상 컴퓨터의 SQL Server](http://azure.microsoft.com/services/virtual-machines/sql-server/)를 호스트할 수 있습니다. 이 항목에서는 Azure 가상 컴퓨터에서 SQL Server 실행을 시작하는 데 도움이 되는 몇 가지 최고의 리소스를 소개해 드려려 합니다.

>[AZURE.NOTE]Azure VM에서 SQL Server를 실행하는 것은 관계형 데이터를 Azure에 저장하기 위한 한 가지 옵션입니다. Azure SQL 데이터베이스 서비스를 사용할 수도 있습니다. 자세한 내용은 [Azure SQL 데이터베이스 및 Azure VM의 SQL Server 이해](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)를 참조하세요.
 
## 단일 VM에 SQL Server 인스턴스 배포

Azure에 SQL Server 가상 컴퓨터를 배포하는 가장 쉬운 방법은 [Azure 관리 포털에서 SQL Server 컴퓨터 갤러리 이미지를 프로비저닝](virtual-machines-provision-sql-server.md)하는 것입니다. 이러한 이미지에는 VM 가격에 포함된 SQL Server의 라이선스가 포함되어 있습니다.

그러나 SQL Server가 미리 설치되지 않은 [Azure 가상 컴퓨터 만들기](virtual-machines-windows-tutorial.md)도 가능합니다. 라이선스가 있다면 어떤 SQL Server 인스턴스든 설치할 수 있습니다.

프로비저닝과 구성의 이러한 초기 단계에는 다음과 같은 일반적인 작업이 수행됩니다.

- [Azure VM의 SQL Server에 대한 성능 모범 사례 검토](https://msdn.microsoft.com/library/azure/dn133149.aspx)
- [Azure VM의 SQL Server에 대한 보안 모범 사례 검토](https://msdn.microsoft.com/library/azure/dn133147.aspx)
- [연결 설정](virtual-machines-sql-server-connectivity.md)

## 여러 VM을 사용하여 항상 사용 가능한 구성 배포

SQL Server AlwaysOn 가용성 그룹을 사용하여 SQL Server에 대한 고가용성을 실현할 수 있습니다. 여기에는 가상 네트워크의 여러 Azure VM이 포함됩니다. Azure Preview 포털에 이 구성을 자동으로 설정하는 템플릿이 있습니다. 자세한 내용은 [Microsoft Azure 포털 갤러리의 SQL Server AlwaysOn 제품](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)을 참조하세요.

가용성 그룹과 연결된 수신기를 수동으로 구성하려는 경우 다음 문서를 참조하세요.

- [Azure의 AlwaysOn 가용성 그룹 구성(GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Azure에서 AlwaysOn 가용성 그룹에 대한 ILB 수신기 구성](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [온-프레미스 AlwaysOn 가용성 그룹을 Azure에 확장](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

다른 고가용성 고려 사항은 [Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)를 참조하세요.

## Azure에서 비즈니스 인텔리전스, 데이터 웨어하우징 및 OLTP 작업 실행   
Azure 가상 컴퓨터에서 일반적인 SQL Server 작업을 실행할 수 있습니다. SQL Server에는 갤러리에서 사용할 수 있는 몇 가지 최적화된 가상 컴퓨터 이미지가 있습니다. 여기에는 다음과 같은 이미지가 포함되어 있습니다.

- [비즈니스 인텔리전스](https://msdn.microsoft.com/library/azure/jj992719.aspx)
- [데이터 웨어하우징](https://msdn.microsoft.com/library/azure/dn387396.aspx)
- [OLTP](https://msdn.microsoft.com/library/azure/dn387396.aspx)

## 데이터 마이그레이션

SQL Server 가상 컴퓨터를 준비하고 실행한 후 기존 데이터베이스를 컴퓨터로 마이그레이션할 수도 있습니다. 여러 가지 기술이 있지만 SQL Server Management Studio의 배포 마법사는 대부분의 시나리오에 적합합니다. 시나리오에 대한 설명 및 마법사에 대한 자습서는 [Azure VM의 SQL Server로 데이터베이스 마이그레이션](virtual-machines-migrate-onpremises-database.md)을 참조하세요.

## 백업 및 복원
온-프레미스 데이터베이스의 경우 Azure는 SQL Server 백업 파일을 저장하는 보조 데이터 센터의 역할을 할 수 있습니다. 백업 및 복원 옵션의 개요는 [Azure 가상 컴퓨터의 SQL Server를 위한 백업 및 복원](virtual-machines-sql-server-backup-and-restore.md)을 참조하세요.

[URL로 SQL Server 백업](https://msdn.microsoft.com/library/dn435916.aspx)에서는 Azure 백업 파일을 Azure Blob 저장소에 저장합니다. [SQL Server 관리되는 백업](https://msdn.microsoft.com/library/dn449496.aspx)에서는 Azure에서의 백업 및 보존을 예약할 수 있습니다. 온-프레미스 SQL Server 인스턴스 또는 Azure VM에서 실행되는 SQL Server에서 이러한 서비스를 사용할 수 있습니다. 또한 Azure VM에서는 SQL Server에 대한 [자동화된 백업](virtual-machines-sql-server-automated-backup.md) 및 [자동화된 패치 적용](virtual-machines-sql-server-automated-patching.md)을 활용할 수 있습니다.

<!---HONumber=August15_HO8-->