<properties 
	pageTitle="SQL Server 가상 컴퓨터에 연결(리소스 관리자) | Microsoft Azure"
	description="이 항목에서는 클래식 배포 모델을 사용하여 만든 리소스를 사용하며, Azure 가상 컴퓨터에서 실행 중인 SQL Server에 연결하는 방법을 설명합니다. 시나리오는 네트워킹 구성 및 클라이언트의 위치에 따라 다릅니다."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"    
	tags="azure-service-management"/>
<tags 
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="03/24/2016"
	ms.author="jroth" />

# Azure에서 SQL Server 가상 컴퓨터 연결(리소스 관리자)

> [AZURE.SELECTOR]
- [리소스 관리자](virtual-machines-windows-sql-connect.md)
- [클래식](virtual-machines-windows-classic-sql-connect.md)

## 개요

리소스 관리자의 Azure 가상 컴퓨터에서 실행되는 SQL Server에 대한 연결 구성은 온-프레미스 SQL Server 인스턴스에 필요한 절차와 크게는 다르지 않습니다. 여기서도 방화벽, 인증, 데이터베이스 로그인과 관련한 구성 단계를 수행해야 합니다.

하지만 Azure VM에 특정한 SQL Server 연결 요소가 몇 가지 존재합니다. 이 문서에서는 몇 가지 [일반 연결 시나리오](#connection-scenarios)를 다룬 다음 [Azure VM에서 SQL Server 연결을 구성하기 위한 상세 단계](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm)를 제공합니다.

이 문서는 리소스 관리자 모델을 사용하여 SQL Server VM에 연결하는 방법에 중점을 둡니다. 프로비저닝 및 연결의 전체 연습은 [Azure에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.

## 연결 시나리오

클라이언트가 가상 컴퓨터를 실행 중인 SQL Server에 연결하는 방법은 클라이언트의 위치 및 컴퓨터/네트워킹 구성에 따라 달라집니다. 이 시나리오에는 다음이 포함됩니다.

- [인터넷을 통해 SQL Server에 연결 ](#connect-to-sql-server-over-the-internet)
- [동일한 가상 네트워크의 SQL Server에 연결](#connect-to-sql-server-in-the-same-virtual-network)

### 인터넷을 통해 SQL Server에 연결

인터넷에서 SQL Server 데이터베이스 엔진에 연결하려는 경우 방화벽 구성, SQL 인증 사용, 네트워크 보안 그룹 구성 등의 여러 단계가 있습니다. 포트 1433에 TCP 트래픽을 허용하도록 네트워크 보안 그룹 규칙이 있어야 합니다.

포털을 사용하여 리소스 관리자를 통해 SQL Server 가상 컴퓨터 이미지를 프로비전하는 경우 SQL 연결 옵션에 **공용**을 선택하면 다음 단계가 수행됩니다.

![](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

프로비전하는 동안 하나가 아닌 경우 [수동으로 연결을 구성하는 이 문서의 단계](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm)에 따라 SQL Server 및 가상 컴퓨터를 수동으로 구성할 수 있습니다.

이 작업이 완료되면 인터넷에 연결된 모든 클라이언트는 가상 컴퓨터의 공용 IP 주소 또는 IP 주소에 할당된 DNS 이름을 지정하여 SQL Server 인스턴스에 연결할 수 있습니다. SQL Server 포트가 1433인 경우 연결 문자열에 지정할 필요가 없습니다.

	"Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

이를 통해 인터넷을 통한 클라이언트의 연결이 활성화되지만 누구나 SQL Server에 연결할 수 있다는 뜻은 아닙니다. 외부 클라이언트는 정확한 사용자 이름과 암호가 있어야 합니다. 추가 보안을 위해 잘 알려진 포트 1433을 피할 수 있습니다. 예를 들어 1,500 포트에서 수신하도록 SQL Server를 구성하고 적절한 방화벽 및 네트워크 보안 그룹 규칙을 설정하는 경우 다음 예제와 같이 서버 이름에 포트 번호를 추가하여 연결할 수 있습니다.

	"Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] SQL Server와의 통신에 이 방법을 사용할 때는 반환되는 모든 데이터가 데이터 센터에서 나가는 트래픽으로 고려된다는 점을 유의해야 합니다. 일반적인 [아웃바운드 데이터 전송 가격](https://azure.microsoft.com/pricing/details/data-transfers/)의 적용 대상입니다. 이 방법을 동일한 데이터 센터의 다른 컴퓨터나 클라우드 서비스에 사용하는 경우에도 마찬가지입니다. 트래픽이 Azure 공용 부하 분산 장치를 통해 이동하기 때문입니다.

### 동일한 가상 네트워크의 SQL Server에 연결

[가상 네트워크](../virtual-network/virtual-networks-overview.md)에서는 추가적인 시나리오가 가능합니다. VM이 다른 리소스 그룹에 있더라도 동일한 가상 네트워크의 VM에 연결할 수 있습니다. 또한 [사이트 간 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)을 통해 온-프레미스 네트워크와 컴퓨터에 VM을 연결하는 하이브리드 아키텍처를 만들 수 있습니다.

가상 네트워크를 사용하면 Azure VM을 도메인에 연결할 수 있습니다. 이것이 SQL Server에 Windows 인증을 사용하는 유일한 방법입니다. 다른 연결 시나리오의 경우 사용자 이름과 암호가 있는 SQL 인증이 필요합니다.

리소스 관리자를 통해 SQL Server 가상 컴퓨터 이미지를 프로비전하는 데 포털을 사용하는 경우 SQL 연결 옵션에 대해 **개인**을 선택할 때 가상 네트워크의 통신에 대한 적절한 방화벽 규칙이 설정됩니다. 프로비전하는 동안 하나가 아닌 경우 [수동으로 연결을 구성하는 이 문서의 단계](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm)에 따라 SQL Server 및 가상 컴퓨터를 수동으로 구성할 수 있습니다. 그러나 도메인 환경과 Windows 인증을 구성하려는 경우 이 글의 단계를 사용하여 SQL 인증과 로그인을 구성할 필요가 없습니다. 또한 인터넷을 통한 액세스를 위해 네트워크 보안 그룹 규칙을 구성할 필요가 없습니다.

가상 네트워크에 DNS를 구성했다고 가정하면 연결 문자열에 SQL Server VM 컴퓨터 이름을 지정하여 SQL Server에 연결할 수 있습니다. 또한 다음 예에서는 Windows 인증도 구성되었고 사용자에게 SQL Server 인스턴스에 대한 액세스 권한이 있다고 가정합니다.

	"Server=mysqlvm;Integrated Security=true" 

이 시나리오에서는 VM의 IP 주소도 지정할 수 있습니다.

## Azure VM에서 SQL Server 연결을 수동으로 구성하기 위한 단계

다음 단계는 SQL Server 인스턴스에 수동으로 연결을 설정하고 SSMS(SQL Server Management Studio)를 사용하여 인터넷을 통해 연결하는 방법을 보여줍니다. 이러한 단계는 대부분 포털에서 적절한 SQL Server 연결 옵션을 선택하는 경우 수행됩니다.

인터넷 또는 다른 VM에서 SQL Server의 인스턴스에 연결하기 전에 먼저 아래의 섹션에 설명된 대로 다음 작업을 완료해야 합니다.

- [Windows 방화벽에서 TCP 포트 열기](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [TCP 프로토콜에서 수신하도록 SQL Server 구성](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [혼합된 모드 인증에 대한 SQL Server 구성](#configure-sql-server-for-mixed-mode-authentication)
- [SQL Server 인증 로그인 만들기](#create-sql-server-authentication-logins)
- [공용 IP 주소에 대한 DNS 레이블 구성](#configure-a-dns-label-for-the-public-ip-address)
- [다른 컴퓨터에서 데이터베이스 엔진에 연결](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [VM에서 SQL Server에 연결](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [VM 리소스 관리자에서 SQL Server에 연결](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [VM 리소스 관리자에서 SQL Server에 연결](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## 다음 단계

이러한 연결 단계와 함께 프로비저닝 지침을 확인하려면 [Azure에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요.

Azure 가상 컴퓨터에서 실행되는 SQL Server에 대한 모든 보안 모범 사례를 반드시 검토해야 합니다. 자세한 내용은 [Azure 가상 컴퓨터의 SQL Server에 대한 보안 고려 사항](virtual-machines-windows-sql-security.md)을 참조하세요.

Azure VM에서의 SQL Server 실행에 관한 다른 항목은 [Azure 가상 컴퓨터의 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

<!---HONumber=AcomDC_0413_2016-->