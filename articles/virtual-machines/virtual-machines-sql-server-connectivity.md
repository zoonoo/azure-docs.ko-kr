<properties 
   pageTitle="SQL Server 가상 컴퓨터에 연결 | Microsoft Azure"
   description="이 항목에서는 클래식 배포 모델을 사용하여 만든 리소스를 사용하며, Azure 가상 컴퓨터에서 실행 중인 SQL Server에 연결하는 방법을 설명합니다. 시나리오는 네트워킹 구성 및 클라이언트의 위치에 따라 다릅니다."
   services="virtual-machines"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar"    
   tags="azure-service-management"/>
<tags 
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="08/18/2015"
   ms.author="jroth" />

# Azure에서 SQL Server 가상 컴퓨터 연결

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 클래식 배포 모델을 사용하여 리소스를 사용하는 방법을 설명합니다.
 
## 개요

Azure 가상 컴퓨터에서 실행되는 SQL Server에 대한 연결 구성은 온-프레미스 SQL Server 인스턴스에 필요한 절차와 크게는 다르지 않습니다. 여기서도 방화벽, 인증, 데이터베이스 로그인과 관련한 구성 단계를 수행해야 합니다.

하지만 Azure VM에 특정한 SQL Server 연결 요소가 몇 가지 존재합니다. 이 문서에서는 몇 가지 [일반 연결 시나리오](#connection-scenarios)를 다룬 다음 [Azure VM에서 SQL Server 연결을 구성하기 위한 상세 단계](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)를 제공합니다.

>[AZURE.NOTE]이 문서에서는 연결에 초점을 맞춥니다. 프로비저닝 및 연결의 전체 연습은 [Azure에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-provision-sql-server.md)을 참조하세요.

## 연결 시나리오

클라이언트가 가상 컴퓨터를 실행 중인 SQL Server에 연결하는 방법은 클라이언트의 위치 및 컴퓨터/네트워킹 구성에 따라 달라집니다. 이 시나리오에는 다음이 포함됩니다.

- [동일한 클라우드 서비스의 SQL Server에 연결](#connect-to-sql-server-in-the-same-cloud-service)
- [인터넷을 통해 SQL Server에 연결 ](#connect-to-sql-server-over-the-internet)
- [동일한 가상 네트워크의 SQL Server에 연결](#connect-to-sql-server-in-the-same-virtual-network)

### 동일한 클라우드 서비스의 SQL Server에 연결

한 클라우드 서비스에 여러 가상 컴퓨터를 만들 수 있습니다. 가상 컴퓨터 시나리오를 이해하려면 [가상 컴퓨터를 가상 네트워크 또는 클라우드 서비스와 연결하는 방법](cloud-services-connect-virtual-machine.md)을 참조하세요.

먼저 [이 문서의 단계에 따라 연결을 구성합니다](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). 동일한 클라우드 서비스에 있는 시스템을 연결할 경우 공용 끝점을 설정할 필요가 없습니다.

클라이언트 연결 문자열에 VM **호스트 이름**을 사용할 수 있습니다. 호스트 이름은 VM을 만드는 동안 지정한 이름입니다. 예를 들어 SQL VM 이름을 **mysqlvm**으로 지정했고 클라우드 서비스 DNS 이름이 **mycloudservice.cloudapp.net**인 경우 동일한 클라우드 서비스의 클라이언트 VM은 연결을 위해 다음 연결 문자열을 사용합니다.

	"Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### 인터넷을 통해 SQL Server에 연결

인터넷을 통해 SQL Server 데이터베이스 엔진에 연결할 경우 들어오는 TCP 통신에 대해 가상 컴퓨터 끝점을 만들어야 합니다. 이 Azure 구성 단계에서는 들어오는 TCP 포트 트래픽을 가상 컴퓨터에 액세스 가능한 TCP 포트로 보냅니다.

먼저 [이 문서의 단계에 따라 연결을 구성합니다](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). 그러면 인터넷에 액세스할 수 있는 모든 클라이언트가 클라우드 서비스 DNS 이름(예: **mycloudservice.cloudapp.net**)과 VM 끝점(예: **57500**)을 지정하여 SQL Server 인스턴스에 연결할 수 있습니다.

	"Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

이를 통해 인터넷을 통한 클라이언트의 연결이 활성화되지만 누구나 SQL Server에 연결할 수 있다는 뜻은 아닙니다. 외부 클라이언트는 정확한 사용자 이름과 암호가 있어야 합니다. 보안을 높이기 위해 잘 알려진 포트 1433은 공용 가상 컴퓨터 끝점으로 사용하지 않는 것이 좋습니다. 또한 가능하다면 끝점에 ACL을 추가하여 트래픽을 허용한 클라이언트로 한정하는 것이 좋습니다. 끝점에서 ACL을 사용하는 방법에 대한 지침은 [끝점에 대한 ACL 관리](virtual-machines-set-up-endpoints.md#manage-the-acl-on-an-endpoint)를 참조하세요.

>[AZURE.NOTE]SQL Server와의 통신에 이 방법을 사용할 때는 반환되는 모든 데이터가 데이터 센터에서 나가는 트래픽으로 고려된다는 점을 유의해야 합니다. 일반적인 [아웃바운드 데이터 전송 가격](http://azure.microsoft.com/pricing/details/data-transfers)의 적용 대상입니다. 이 방법을 동일한 데이터 센터의 다른 컴퓨터나 클라우드 서비스에 사용하는 경우에도 마찬가지입니다. 트래픽이 Azure 공용 부하 분산 장치를 통해 이동하기 때문입니다.

### 동일한 가상 네트워크의 SQL Server에 연결

[가상 네트워크](..\virtual-network\virtual-networks-overview.md)에서는 추가적인 시나리오가 가능합니다. VM이 다른 클라우드 서비스에 있더라도 동일한 가상 네트워크의 VM에 연결할 수 있습니다. 또한 [사이트 간 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)을 통해 온-프레미스 네트워크와 컴퓨터에 VM을 연결하는 하이브리드 아키텍처를 만들 수 있습니다.

가상 네트워크를 사용하면 Azure VM을 도메인에 연결할 수 있습니다. 이것이 SQL Server에 Windows 인증을 사용하는 유일한 방법입니다. 다른 연결 시나리오의 경우 사용자 이름과 암호가 있는 SQL 인증이 필요합니다.

먼저 [이 문서의 단계에 따라 연결을 구성합니다](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). 도메인 환경과 Windows 인증을 구성하려는 경우 이 글의 단계를 사용하여 SQL 인증과 로그인을 구성할 필요가 없습니다. 또한 이 시나리오에서는 공용 끝점이 필요하지 않습니다.

DNS를 구성했다고 가정하면 연결 문자열에 SQL Server VM 호스트 이름을 지정하여 SQL Server에 연결할 수 있습니다. 다음 예에서는 Windows 인증도 구성되었고 사용자에게 SQL Server 인스턴스에 대한 액세스 권한이 있다고 가정합니다.

	"Server=mysqlvm;Integrated Security=true" 

이 시나리오에서는 VM의 IP 주소도 지정할 수 있습니다.

## Azure VM에서 SQL Server 연결을 구성하기 위한 단계

[AZURE.INCLUDE [VM에서 SQL Server에 연결](../../includes/virtual-machines-sql-server-connection-steps.md)]

## 다음 단계

이러한 연결 단계와 함께 프로비저닝 지침을 확인하려면 [Azure에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-provision-sql-server.md)을 참조하세요.

고가용성 및 재해 복구를 위해 AlwaysOn 가용성 그룹도 사용하려는 경우 수신기의 구현을 고려해야 합니다. 데이터베이스 클라이언트는 SQL Server 인스턴스 중 하나에 직접 연결하기 보다는 수신기에 연결합니다. 수신기는 가용성 그룹의 주 복제본에 클라이언트를 라우팅합니다. 자세한 내용은 [Azure에서 AlwaysOn 가용성 그룹에 대한 ILB 수신기 구성](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)을 참조하세요.

Azure 가상 컴퓨터에서 실행되는 SQL Server에 대한 모든 보안 모범 사례를 반드시 검토해야 합니다. 자세한 내용은 [Azure 가상 컴퓨터의 SQL Server에 대한 보안 고려 사항](virtual-machines-sql-server-security-considerations.md)을 참조하세요.

Azure VM에서 SQL Server 실행과 관련된 다른 항목은 [Azure 가상 컴퓨터의 SQL Server](virtual-machines-sql-server-infrastructure-services.md)를 참조하세요.

<!---HONumber=Sept15_HO4-->