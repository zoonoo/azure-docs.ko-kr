<properties 
	pageTitle="가상 컴퓨터의 SQL Server 개요 | Microsoft Azure"
	description="이 문서에서는 Azure 가상 컴퓨터에서 호스트되는 SQL Server에 대한 개요를 제공합니다. 여기에는 세부 콘텐츠에 대한 링크가 포함되어 있습니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="11/12/2015"
	ms.author="jroth"/>

# Azure 가상 컴퓨터의 SQL Server 개요

## 시작
AlwaysOn 가용성 그룹 및 Azure 가상 네트워크를 사용하여 단일 데이터베이스 서버에서 다중 컴퓨터 구성에 이르는 다양한 구성으로 [Azure 가상 컴퓨터의 SQL Server](http://azure.microsoft.com/services/virtual-machines/sql-server/)를 호스트할 수 있습니다.

>[AZURE.NOTE]Azure VM에서 SQL Server를 실행하는 것은 관계형 데이터를 Azure에 저장하기 위한 한 가지 옵션입니다. Azure SQL 데이터베이스 서비스를 사용할 수도 있습니다. 자세한 내용은 [Azure SQL 데이터베이스 및 Azure VM의 SQL Server 이해](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)를 참조하세요.

Azure에서 SQL Server 가상 컴퓨터를 만들려면 먼저 Azure 플랫폼 구독을 얻어야 합니다. [구입 옵션](http://azure.microsoft.com/pricing/purchase-options/)에서 Azure 구독을 구입할 수 있습니다. 무료로 사용해 보려면 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 방문하세요.

### 단일 VM에 SQL Server 인스턴스 배포

구독에 등록한 후 Azure에서 SQL Server 가상 컴퓨터를 배포하는 가장 쉬운 방법은 [Azure 관리 포털에서 SQL Server 컴퓨터 갤러리 이미지를 프로비전](virtual-machines-provision-sql-server.md)하는 것입니다. 이러한 이미지에는 VM 가격에 포함된 SQL Server의 라이선스가 포함되어 있습니다.

>[AZURE.NOTE] [새 포털](https://manage.windowsazure.com)을 사용하여 SQL Server 가상 컴퓨터를 프로비전하고 관리합니다. 기본적으로 프리미엄 저장소를 사용하고 자동화된 패치, 자동화된 백업 및 AlwaysOn 구성을 제공합니다.

다음 표에서는 가상 컴퓨터 갤러리의 사용 가능한 SQL Server 이미지의 매트릭스를 제공합니다.

|SQL Server 버전|운영 체제|SQL Server 버전|
|---|---|---|
|SQL Server 2008 R2 SP2|Windows Server 2008 R2|Enterprise, Standard, Web|
|SQL Server 2008 R2 SP3|Windows Server 2008 R2|Enterprise, Standard, Web|
|SQL Server 2012 SP2|Windows Server 2012|Enterprise, Standard, Web|
|SQL Server 2012 SP2|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2014|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2014 SP1|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2016 CTP|Windows Server 2012 R2|평가|

>[AZURE.NOTE]데이터 웨어하우징 및 트랜잭션 작업에 대한 가상 컴퓨터 갤러리 이미지(위에 표시되지 않음)는 사용되지 않으며 갤러리에서 곧 제거될 예정입니다. 이전 표에 나와 있는 표준 이미지를 사용하여 특정 작업에 대한 성능을 최적화하세요.

미리 구성된 이미지뿐 아니라 사전 설치된 SQL Server 없이 [Azure 가상 컴퓨터 만들기](virtual-machines-windows-tutorial.md)를 수행할 수도 있습니다. 라이선스가 있다면 어떤 SQL Server 인스턴스든 설치할 수 있습니다. Azure 가상 컴퓨터에서 SQL Server를 실행하기 위해 [Azure에서 Software Assurance를 통한 라이선스 이동](http://azure.microsoft.com/pricing/license-mobility/)을 사용하여 라이선스를 Azure로 마이그레이션합니다. 이 시나리오에서는 가상 컴퓨터와 관련된 Azure 계산 및 저장소 [비용](http://azure.microsoft.com/pricing/details/virtual-machines)에 대해서만 지불합니다.

SQL Server 이미지에 가장 적합한 가상 컴퓨터 구성 설정을 확인하려면 [Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례](virtual-machines-sql-server-performance-best-practices.md)를 검토하세요. 프로덕션 작업의 경우 SQL Server Enterprise Edition에 대한 최소 권장 가상 컴퓨터 크기는 **DS3**이며 Standard Edition에 대한 최소 권장 가상 컴퓨터 크기는 **DS2**입니다.

성능 모범 사례 검토 외에도 다른 초기 작업에는 다음이 포함됩니다.

- [Azure VM의 SQL Server에 대한 보안 모범 사례 검토](virtual-machines-sql-server-security-considerations.md)
- [연결 설정](virtual-machines-sql-server-connectivity.md)

### 데이터 마이그레이션

SQL Server 가상 컴퓨터를 준비하고 실행한 후 기존 데이터베이스를 컴퓨터로 마이그레이션할 수도 있습니다. 여러 가지 기술이 있지만 SQL Server Management Studio의 배포 마법사는 대부분의 시나리오에 적합합니다. 시나리오에 대한 설명 및 마법사 자습서는 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-migrate-onpremises-database.md)을 참조하세요.

## 고가용성

고가용성이 필요한 경우 SQL Server AlwaysOn 가용성 그룹을 구성하는 것이 좋습니다. 여기에는 가상 네트워크의 여러 Azure VM이 포함됩니다. Azure Preview 포털에 이 구성을 자동으로 설정하는 템플릿이 있습니다. 자세한 내용은 [Microsoft Azure 포털 갤러리의 SQL Server AlwaysOn 제품](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)을 참조하세요.

가용성 그룹과 연결된 수신기를 수동으로 구성하려는 경우 다음 문서를 참조하세요.

- [Azure의 AlwaysOn 가용성 그룹 구성(GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Azure에서 AlwaysOn 가용성 그룹에 대한 ILB 수신기 구성](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [Azure 리소스 관리자 템플릿을 사용하여 SQL Server AlwaysOn 배포](virtual-machines-workload-template-sql-alwayson.md)
- [온-프레미스 AlwaysOn 가용성 그룹을 Azure에 확장](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

다른 고가용성 고려 사항은 [Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)를 참조하세요.

## 백업 및 복원
온-프레미스 데이터베이스의 경우 Azure는 SQL Server 백업 파일을 저장하는 보조 데이터 센터의 역할을 할 수 있습니다. 백업 및 복원 옵션에 대한 개요는 [Azure 가상 컴퓨터에서 SQL Server 백업 및 복원](virtual-machines-sql-server-backup-and-restore.md)을 참조하세요.

[URL에 대한 SQL Server 백업](https://msdn.microsoft.com/library/dn435916.aspx)에서는 Azure 백업 파일을 Azure Blob 저장소에 저장합니다. [SQL Server 관리되는 백업](https://msdn.microsoft.com/library/dn449496.aspx)에서는 Azure에서의 백업 및 보존을 예약할 수 있습니다. 온-프레미스 SQL Server 인스턴스 또는 Azure VM에서 실행되는 SQL Server에서 이러한 서비스를 사용할 수 있습니다. 또한 Azure VM에서는 SQL Server에 대한 [자동화된 백업](virtual-machines-sql-server-automated-backup.md) 및 [자동화된 패치 적용](virtual-machines-sql-server-automated-patching.md)을 활용할 수 있습니다.

## SQL Server VM 이미지 구성 세부 정보

다음 표에서는 플랫폼 제공 SQL Server 가상 컴퓨터 이미지의 구성에 대해 설명합니다.

### Windows Server

플랫폼 이미지의 Windows Server 설치에는 다음과 같은 구성 설정과 구성 요소가 포함됩니다.

|기능|구성
|---|---|
|원격 데스크톱|관리자 계정에 사용|
|Windows 업데이트|사용|
|사용자 계정|기본적으로 프로비전 중에 지정된 사용자 계정은 로컬 관리자 그룹의 구성원입니다. 이 관리자 계정은 SQL Server sysadmin 서버 역할의 구성원이기도 합니다.|
|작업 그룹|가상 컴퓨터가 WORKGROUP이라는 작업 그룹의 구성원입니다.|
|게스트 계정|사용 안 함|
|고급 보안이 포함된 Windows 방화벽|다른|
|.NET Framework|버전 4|
|디스크|선택한 크기는 구성할 수 있는 데이터 디스크 수를 제한합니다. [Azure에 대한 가상 컴퓨터 크기](virtual-machines-size-specs.md)를 참조하세요.|

### SQL Server

플랫폼 이미지의 SQL Server 설치에는 다음과 같은 구성 설정과 구성 요소가 포함됩니다.

|기능|구성|
|---|---|
|데이터베이스 엔진|설치|
|Analysis Services|설치|
|Integration Services|설치|
|Reporting Services|기본 모드로 구성됨|
|AlwaysOn 가용성 그룹|사용 가능(SQL Server 2012 이상). [추가 구성](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md) 필요
|복제|설치|
|검색을 위한 전체 텍스트 및 의미 체계 추출|설치됨(의미 체계 추출, SQL Server 2012 이상에만 해당)|
|Data Quality Services|설치됨(SQL Server 2012 이상에만 해당)|
|Master Data Services|설치됨(SQL Server 2012 이상에만 해당). [추가 구성 및 구성 요소](https://msdn.microsoft.com/library/ee633752.aspx) 필요
|SharePoint용 PowerPivot|사용 가능(SQL Server 2012 이상에만 해당). 추가 구성 및 구성 요소(SharePoint 포함) 필요|
|Distributed Replay Client|사용 가능(SQL Server 2012 이상에만 해당), 설치되지 않음. [플랫폼 제공 SQL Server 이미지에서 SQL Server 설치 프로그램 실행](#run-sql-server-setup-from-the-platform-provided-sql-server-image)을 참조하세요.|
|도구|SQL Server Management Studio, SQL Server 구성 관리자, Business Intelligence Development Studio, SQL Server 설치 프로그램, 클라이언트 도구 연결, 클라이언트 도구 SDK 및 SQL 클라이언트 연결 SDK, 업그레이드 및 마이그레이션 도구(예: DAC(데이터 계층 응용 프로그램), 백업, 복원, 연결 및 분리)를 포함한 모든 도구|
|SQL Server 온라인 설명서|설치되었지만 도움말 뷰어를 사용한 구성 필요|

### 데이터베이스 엔진 구성

다음 데이터베이스 엔진 설정이 구성됩니다. 추가 설정은 SQL Server의 인스턴스를 검사하세요.

|기능|구성|
|---|---|
|인스턴스|공유 메모리 프로토콜에서만 수신 대기하는 SQL Server 데이터베이스 엔진의 기본(명명되지 않은) 인스턴스를 포함합니다.|
|인증|기본적으로 Azure는 SQL Server 가상 컴퓨터를 설치하는 동안 Windows 인증을 선택합니다. sa 로그인을 사용하거나 새 SQL Server 계정을 만들려면 인증 모드를 변경해야 합니다. 자세한 내용은 [Azure 가상 컴퓨터의 SQL Server에 대한 보안 고려 사항](virtual-machines-sql-server-security-considerations.md)을 참조하세요.|
|sysadmin|가상 컴퓨터를 설치한 Azure 사용자는 처음에 SQL Server sysadmin 고정 서버 역할의 유일한 구성원입니다.|
|메모리|데이터베이스 엔진 메모리는 동적 메모리 구성으로 설정됩니다.|
|데이터베이스 인증 포함|꺼짐|
|기본 언어|영어|
|데이터베이스 간 소유권 체인|꺼짐|

### CEIP(사용자 환경 개선 프로그램)

[CEIP(사용자 환경 개선 프로그램)](https://technet.microsoft.com/library/cc730757.aspx)는 사용하도록 설정되어 있습니다. SQL Server 오류 및 사용 보고 유틸리티를 사용하여 CEIP를 사용하지 않도록 설정할 수 있습니다. SQL Server 오류 및 사용 보고 유틸리티를 시작하려면 시작 메뉴에서 모든 프로그램, Microsoft SQL Server 버전, 구성 도구, SQL Server 오류 및 사용 보고를 순서대로 클릭합니다. CEIP를 사용하도록 설정된 SQL Server의 인스턴스를 사용하지 않으려면 해당 가상 컴퓨터 이미지를 Azure에 배포하는 것을 고려할 수도 있습니다. 자세한 내용은 [Windows Server 운영 체제가 포함된 가상 하드 디스크 만들기 및 업로드](virtual-machines-create-upload-vhd-windows-server.md)를 참조하세요.

## 플랫폼 제공 SQL Server 이미지에서 SQL Server 설치 프로그램 실행

플랫폼 제공 SQL Server 이미지를 사용하여 가상 컴퓨터를 만드는 경우 가상 컴퓨터에 저장된 SQL Server 설치 미디어를 **C:\\SqlServer\_SQLMajorVersion.SQLMinorVersion\_Full** 디렉터리에서 찾을 수 있습니다. 디스크 공간이 허용한다면 이 디렉터리에서 설치 프로그램을 실행하여 기능 추가/제거, 새 인스턴스 추가 또는 인스턴스 복구를 포함한 설치 작업을 수행할 수 있습니다.

>[AZURE.NOTE]Azure는 포털에서 여러 버전의 SQL Server 이미지를 제공합니다. 플랫폼 제공 SQL Server 이미지의 버전 릴리스 날짜가 2014년 5월 15일 이후이면 기본적으로 제품 키를 포함합니다. 이 날짜 이전에 게시된 플랫폼 제공 SQL Server 이미지를 사용하여 가상 컴퓨터를 프로비전하면 해당 VM에는 제품 키가 포함되지 않습니다. 새 VM을 프로비전할 때 항상 최신 이미지 버전을 선택하는 것이 좋습니다.

## 리소스

- [Azure에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-provision-sql-server.md)
- [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-migrate-onpremises-database.md)
- [Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)
- [Azure 가상 컴퓨터의 SQL Server에 대한 응용 프로그램 패턴 및 개발 전략](virtual-machines-sql-server-application-patterns-and-development-strategies.md)
- [Azure 가상 컴퓨터](virtual-machines-about.md) 

<!---HONumber=Nov15_HO4-->