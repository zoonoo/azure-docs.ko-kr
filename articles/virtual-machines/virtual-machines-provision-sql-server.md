<properties 
	pageTitle="Azure에서 SQL Server 가상 컴퓨터 프로비전"
	description="이 자습서에서는 Azure에서 SQL Server VM을 만들고 구성하는 방법에 대해 설명합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>

<tags 
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2015"
	ms.author="jroth"/>

# Azure에서 SQL Server 가상 컴퓨터 프로비전

> [AZURE.SELECTOR]
- [Portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)

## 개요

Azure 가상 컴퓨터 갤러리에는 Microsoft SQL Server가 포함된 몇 개의 이미지가 있습니다. 갤러리에서 가상 컴퓨터 이미지 중 하나를 선택하고 몇 번의 클릭으로 Azure 환경에 가상 컴퓨터를 프로비전할 수 있습니다.

이 자습서에서는 다음을 수행합니다.

* [Azure 관리 포털에 연결 및 갤러리에서 가상 컴퓨터 프로비전](#Provision)
* [원격 데스크톱을 사용하여 가상 컴퓨터 열기 및 설치 완료](#RemoteDesktop)
* [다른 컴퓨터의 SQL Server Management Studio를 사용하여 가상 컴퓨터에 연결하는 구성 단계 완료](#SSMS)
* [다음 단계](#Optional)

##<a id="Provision">갤러리에서 SQL Server 가상 컴퓨터 프로비전</a>

1. 사용자 계정을 사용하여 [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다. Azure 계정이 없는 경우 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 방문하십시오.

2. Azure 관리 포털 웹 페이지의 왼쪽 아래에서 **+새로 만들기**, **계산**, **가상 컴퓨터**, **갤러리에서**를 차례로 클릭합니다.

3. **이미지 선택** 페이지에서 **SQL SERVER**를 클릭합니다. 그런 다음 SQL Server 이미지를 선택합니다. 페이지 오른쪽 아래에 있는 다음 화살표를 클릭합니다.

	![이미지 선택](./media/virtual-machines-provision-sql-server/choose-sql-vm.png)

Azure에서 지원되는 SQL Server 이미지에 관한 최신 정보는 [Azure 가상 컴퓨터의 SQL Server 개요](virtual-machines-sql-server-infrastructure-services.md)를 참조하세요.

>[AZURE.NOTE]플랫폼 이미지인 SQL Server 평가 버전을 사용하여 만든 가상 컴퓨터가 있는 경우 갤러리에서 분당 유료 버전 이미지로 업그레이드할 수 없습니다. 두 가지 옵션 중에서 선택할 수 있습니다.
>
> - 갤러리에서 분당 유료 SQL Server 버전을 사용하여 새 가상 컴퓨터를 만들고 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-migrate-onpremises-database)의 단계를 따라 이 새 가상 컴퓨터에 데이터베이스 파일을 마이그레이션할 수 있습니다.
> - 또는 [다른 버전의 SQL Server로 업그레이드](https://msdn.microsoft.com/library/cc707783.aspx)의 단계를 수행하여 [Azure에서 Software Assurance를 통한 라이선스 이동](http://azure.microsoft.com/pricing/license-mobility/) 계약에 따라 SQL Server 평가판 버전의 기존 인스턴스를 다른 버전의 SQL Server로 업그레이드할 수 있습니다. 라이선스가 부여된 SQL Server를 구입하는 방법에 대한 자세한 내용은 [SQL Server 구매 방법](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx)(영문)을 참조하십시오.

4. 첫 번째 **가상 컴퓨터 구성** 페이지에서 다음 정보를 입력합니다.
	- **버전 릴리스 날짜**. 여러 이미지를 사용할 수 있는 경우 최신 이미지를 선택합니다.
	- 고유한 **가상 컴퓨터 이름**.
	- **새 사용자 이름** 상자에 컴퓨터 로컬 관리자 계정의 고유한 사용자 이름을 입력합니다.
	- **새 암호** 상자에 강력한 암호를 입력합니다. 
	- **암호 확인** 상자에 암호를 다시 입력합니다.
	- 드롭다운 목록에서 적절한 **크기**를 선택합니다. 

	![VM 구성](./media/virtual-machines-provision-sql-server/4VM-Config.png)

	>[AZURE.NOTE]가상 컴퓨터의 크기는 프로비전 중 지정합니다.
 	>
	> - A2는 프로덕션 작업용으로 권장되는 최소 크기입니다. 
    > - SQL Server Enterprise Edition을 사용할 경우 가상 컴퓨터의 최소 권장 크기는 A3입니다.
    > - SQL Server Enterprise Edition을 사용할 경우 A3 이상을 선택합니다.
   	> - 트랜잭션 작업 이미지에 최적화된 SQL Server 2012 또는 2014 Enterprise를 사용할 때는 A4 이상을 선택합니다.  
   	> - 데이터 웨어하우징 이미지에 최적화된 SQL Server 2012 또는 2014 Enterprise를 사용할 때는 A7 이상을 선택합니다. 
   	> - 최상의 성능을 위해 프리미엄 저장소와 DS2 또는 DS3을 사용합니다. 자세한 내용은 [Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례](virtual-machines-sql-server-performance-best-practices.md)를 참조하세요.
   	> - 선택한 크기는 구성할 수 있는 데이터 디스크 수를 제한합니다. 사용 가능한 가상 컴퓨터 크기 및 가상 컴퓨터에 연결할 수 있는 데이터 디스크 수에 대한 최신 정보는 [Azure의 가상 컴퓨터 크기](virtual-machines-size-specs.md)를 참조하십시오.

5. VM 구성 세부 정보를 입력한 후 오른쪽 아래에 있는 다음 화살표를 클릭하여 계속합니다.

5. 두 번째 **가상 컴퓨터 구성** 페이지에서 네트워킹, 저장소 및 가용성에 대한 리소스를 구성합니다.
	- **클라우드 서비스** 상자에서 **새 클라우드 서비스 만들기**를 선택합니다.
	- **클라우드 서비스 DNS 이름** 상자에 **TESTNAME.cloudapp.net** 형식으로 이름이 완성되도록 선택한 DNS 이름의 첫 번째 부분을 입력합니다. 
	- 선택할 수 있는 여러 구독이 있는 경우 **구독**을 선택합니다. 선택 항목에 따라 사용할 수 있는 **저장소 계정**이 결정됩니다.
	- **지역/선호도 그룹/가상 네트워크** 상자에서 이 가상 이미지를 호스트할 영역을 선택합니다.
	- **저장소 계정**에서 계정을 자동으로 생성하거나 목록에서 선택합니다. **구독**을 변경하여 추가 계정을 확인합니다. 
	- **가용성 집합** 상자에서 **(없음)**을 선택합니다.
	- 약관을 읽고 동의합니다.
	

6. 다음 화살표를 클릭하여 계속합니다.


7. 오른쪽 아래에서 확인 표시를 클릭하여 계속합니다.

8. Azure에서 가상 컴퓨터를 준비하는 동안 기다립니다. 다음을 통해 진행할 가상 컴퓨터 상태를 예상합니다.

	- **시작 중(프로비전 중)**
	- **중지**
	- **시작 중(프로비전 중)**
	- **실행 중(프로비전 중)**
	- **실행 중**
	

##<a id="RemoteDesktop">원격 데스크톱을 사용하여 VM을 열고 설치 완료</a>

1. 프로비전이 완료되면 가상 컴퓨터의 이름을 클릭하여 대시보드 페이지로 이동합니다. 페이지 맨 아래에 있는 **Connect**를 클릭합니다.

2. **열기** 단추를 클릭합니다.

	![열기 단추 클릭](./media/virtual-machines-provision-sql-server/click-open-to-connect.png)

3. **Windows 보안** 대화 상자에서 **다른 계정 사용**을 클릭합니다.

	![다른 계정 사용 클릭](./media/virtual-machines-provision-sql-server/credentials.png)

4. 도메인 이름, 관리자 이름 순서의 형식으로 컴퓨터 이름을 사용합니다. `machinename\username`. 암호를 입력하고 컴퓨터에 연결합니다.

4. 처음으로 로그온하면, 데스크톱 설정, Windows 업데이트 및 Windows 초기 구성 작업(sysprep) 완료를 포함한 여러 프로세스가 완료됩니다. Windows sysprep이 완료되면 SQL Server 설치 프로세스에서 구성 작업을 완료합니다. 이러한 작업으로 인해 완료되는 동안 잠시 지연이 발생할 수 있습니다. SQL Server 설치가 완료될 때까지 `SELECT @@SERVERNAME`에서 올바른 이름을 반환하지 못할 수 있으며, SQL Server Management Studio가 시작 페이지에 표시되지 않을 수 있습니다.

Windows 원격 데스크톱을 사용하여 가상 컴퓨터에 연결된 후 가상 컴퓨터는 다른 컴퓨터와 상당히 유사하게 작동합니다. SQL Server Management Studio(가상 컴퓨터에서 실행 중인)가 설치되어 있는 기본 SQL Server 인스턴스에 일반적인 방식으로 연결합니다.

##<a id="SSMS">다른 컴퓨터의 SSMS에서 SQL Server VM 인스턴스에 연결</a>

[AZURE.INCLUDE [VM에서 SQL Server에 연결](../../includes/virtual-machines-sql-server-connection-steps.md)]

## <a id="cdea">응용 프로그램에서 데이터베이스 엔진에 연결</a>

Management Studio를 사용하여 Azure 가상 컴퓨터에서 실행 중인 SQL Server 인스턴스에 연결하는 경우 다음과 유사한 연결 문자열을 사용하여 연결할 수 있어야 합니다.

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

자세한 내용은 [SQL Server 데이터베이스 엔진에 연결하는 문제를 해결하는 방법](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)(영문)을 참조하십시오.

##<a id="Optional">다음 단계</a>

플랫폼 이미지를 사용하여 Azure 가상 컴퓨터에서 SQL Server를 만들고 구성하는 방법을 살펴봤습니다. 대부분의 경우 다음 단계는 이 새로운 SQL Server VM에 데이터베이스를 마이그레이션하는 것입니다. 데이터베이스 마이그레이션 지침은 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-migrate-onpremises-database.md)을 참조하세요.

다음 목록에서는 Azure 가상 컴퓨터의 SQL Server에 대한 추가 리소스를 제공합니다.

### Azure VM에서 SQL Server에 대한 권장 리소스
- [Azure 가상 컴퓨터의 SQL Server 개요](virtual-machines-sql-server-infrastructure-services.md)

- [Azure에서 SQL Server 가상 컴퓨터 연결](virtual-machines-sql-server-connectivity.md)

- [Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례](virtual-machines-sql-server-performance-best-practices.md)

- [Azure 가상 컴퓨터의 SQL Server에 대한 보안 고려 사항](virtual-machines-sql-server-security-considerations.md)

### 고가용성 및 재해 복구:
- [Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)

- [Azure 가상 컴퓨터에서 SQL Server의 백업 및 복원](virtual-machines-sql-server-backup-and-restore.md)

### Azure에서 SQL Server 서버 작업:
- [Azure 가상 컴퓨터의 SQL Server Business Intelligence](virtual-machines-sql-server-business-intelligence.md)

- [Azure 가상 컴퓨터의 SQL Server 데이터 웨어하우징 및 트랜잭션 작업](virtual-machines-sql-server-dw-and-oltp-workloads.md)

### 백서:
- [Azure 가상 컴퓨터의 Azure SQL 데이터베이스 및 SQL Server 이해](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [Azure 가상 컴퓨터의 SQL Server에 대한 응용 프로그램 패턴 및 개발 전략](virtual-machines-sql-server-application-patterns-and-development-strategies.md)

<!------HONumber=August15_HO9-->