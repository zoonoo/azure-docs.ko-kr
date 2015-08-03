<properties 
	pageTitle="Azure 가상 컴퓨터의 SQL Server" 
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
	ms.date="07/17/2015"
	ms.author="jroth"/>

# Azure 가상 컴퓨터의 SQL Server

## 개요
AlwaysOn 가용성 그룹 및 Azure 가상 네트워크를 사용하여 단일 데이터베이스 서버에서 다중 컴퓨터 구성에 이르는 다양한 구성으로 [Azure 가상 컴퓨터의 SQL Server][sqlvmlanding]를 호스트할 수 있습니다.

> [AZURE.NOTE]Azure VM에서 SQL Server를 실행하는 것은 관계형 데이터를 Azure에 저장하기 위한 한 가지 옵션입니다. Azure SQL 데이터베이스 서비스를 사용할 수도 있습니다. 자세한 내용은 [Azure SQL 데이터베이스 및 Azure VM의 SQL Server 이해][sqldbcompared]를 참조하세요.
 
## 단일 VM에 SQL Server 인스턴스 배포
자동화 또는 [Azure 포털을 사용하여 Azure 가상 컴퓨터를 만든][createvmportal] 후 라이선스가 있는 SQL Server의 모든 인스턴스를 설치할 수 있습니다. 그러나 SQL Server 컴퓨터와 다른 클라이언트 컴퓨터 간의 [연결을 설정][setupconnectivity]하려면 추가 단계를 수행해야 합니다.
 
갤러리에서 다른 많은 SQL Server 가상 컴퓨터 이미지 중 하나를 설치할 수도 있습니다. 이러한 이미지에는 VM 가격에 포함된 SQL Server의 라이선스가 포함되어 있습니다. 자세한 내용 및 단계별 연결은 [Azure에서 SQL Server 가상 컴퓨터 프로비전][provisionsqlvm]을 참조하세요.

SQL Server 가상 컴퓨터를 준비하고 실행한 후 기존 데이터베이스를 컴퓨터로 마이그레이션할 수도 있습니다. 데이터베이스 마이그레이션에 대한 자세한 내용은 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-migrate-onpremises-database.md)을 참조하세요.

## 여러 VM을 사용하여 항상 사용 가능한 구성 배포
SQL Server AlwaysOn 가용성 그룹을 사용하여 SQL Server에 대한 고가용성을 실현할 수 있습니다. 여기에는 가상 네트워크의 여러 Azure VM이 포함됩니다. Azure Preview 포털에 이 구성을 자동으로 설정하는 템플릿이 있습니다. 자세한 내용은 [Microsoft Azure 포털 갤러리의 SQL Server AlwaysOn 제품][sqlalwaysonportal]을 참조하세요. 또는 [AlwaysOn 가용성 그룹을 수동으로 구성][sqlalwaysonmanual]할 수 있습니다. 다른 고가용성 고려 사항은 [Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구][sqlhadr]를 참조하세요.

## Azure에서 비즈니스 인텔리전스, 데이터 웨어하우징 및 OLTP 작업 실행   
Azure 가상 컴퓨터에서 일반적인 SQL Server 작업을 실행할 수 있습니다. SQL Server에는 갤러리에서 사용할 수 있는 몇 가지 최적화된 가상 컴퓨터 이미지가 있습니다. 여기에는 [비즈니스 인텔리전스][sqlbi], [데이터 웨어하우징][sqldw] 및 [OLTP][sqloltp]에 대한 이미지가 포함됩니다.

## 데이터 마이그레이션
SQL Server 가상 컴퓨터를 준비하고 실행한 후 기존 데이터베이스를 컴퓨터로 마이그레이션할 수도 있습니다. 여러 가지 기술이 있지만 SQL Server Management Studio의 배포 마법사는 대부분의 시나리오에 적합합니다. 시나리오에 대한 설명 및 마법사에 대한 자습서는 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-migrate-onpremises-database.md)을 참조하세요.

## 백업 및 복원
온-프레미스 데이터베이스의 경우 Azure는 SQL Server 백업 파일을 저장하는 보조 데이터 센터의 역할을 할 수 있습니다. [URL에 대한 SQL Server 백업][backupurl]에서는 Azure 백업 파일을 Azure Blob 저장소에 저장합니다. [SQL Server 관리되는 백업][managedbackup]에서는 Azure에서의 백업 및 보존을 예약할 수 있습니다. 온-프레미스 SQL Server 인스턴스 또는 Azure VM에서 실행되는 SQL Server에서 이러한 서비스를 사용할 수 있습니다. 또한 Azure VM에서는 SQL Server에 대한 [자동화된 백업][autobackup] 및 [자동화된 패치 적용][autopatching]을 활용할 수 있습니다. 자세한 내용은 [Azure 가상 컴퓨터에서 SQL Server 관리 작업][managementtasks]을 참조하세요.

## 리소스:
[Azure VM의 SQL Server][sqlmsdnlanding]

[Azure에서 SQL Server 가상 컴퓨터 프로비전][provisionsqlvm]

[Azure 가상 컴퓨터에서 SQL Server 시작][sqlvmgetstarted]

[Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-migrate-onpremises-database.md)

[Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례][sqlperf]

[Azure 가상 컴퓨터의 SQL Server에 대한 보안 고려 사항][sqlsecurity]

  [sqlvmlanding]: http://azure.microsoft.com/services/virtual-machines/sql-server/
  [sqldbcompared]: http://azure.microsoft.com/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas
  [createvmportal]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
  [setupconnectivity]: https://msdn.microsoft.com/library/azure/dn133152.aspx
  [provisionsqlvm]: http://azure.microsoft.com/documentation/articles/virtual-machines-provision-sql-server/
  [sqlalwaysonportal]: http://go.microsoft.com/fwlink/?LinkId=526941
  [sqlalwaysonmanual]: https://msdn.microsoft.com/library/azure/dn249504.aspx
  [sqlhadr]: https://msdn.microsoft.com/library/azure/jj870962.aspx
  [sqlbi]: https://msdn.microsoft.com/library/azure/jj992719.aspx
  [sqldw]: https://msdn.microsoft.com/library/azure/dn387396.aspx
  [sqloltp]: https://msdn.microsoft.com/library/azure/eb0188e2-5569-48ff-b92c-1f6c0bf79620#about
  [migratesql]: https://msdn.microsoft.com/library/azure/dn133142.aspx
  [backupurl]: https://msdn.microsoft.com/library/dn435916(v=sql.120).aspx
  [managedbackup]: https://msdn.microsoft.com/library/dn449496.aspx
  [autobackup]: https://msdn.microsoft.com/library/azure/dn906091.aspx
  [autopatching]: https://msdn.microsoft.com/library/azure/dn961166.aspx
  [managementtasks]: https://msdn.microsoft.com/library/azure/dn906886.aspx
  [sqlmsdnlanding]: https://msdn.microsoft.com/library/azure/jj823132.aspx
  [sqlvmgetstarted]: https://msdn.microsoft.com/library/azure/dn133151.aspx
  [sqlperf]: https://msdn.microsoft.com/library/azure/dn133149.aspx
  [sqlsecurity]: https://msdn.microsoft.com/library/azure/dn133147.aspx
  [technicalarticles]: https://msdn.microsoft.com/library/azure/dn248435.aspx

<!---HONumber=July15_HO4-->