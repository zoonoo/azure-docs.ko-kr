<properties
	pageTitle="Azure의 SQL Server에 대한 보안 고려 사항 | Microsoft Azure"
	description="이 항목에서는 클래식 배포 모델을 사용하여 만든 리소스를 참조하며, Azure 가상 컴퓨터에서 실행 중인 SQL Server의 보안을 유지하기 위한 일반적인 지침을 제공합니다."
	services="virtual-machines-windows"
	documentationCenter="na"
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
	ms.date="06/24/2016"
	ms.author="jroth" />

# Azure 가상 컴퓨터의 SQL Server에 대한 보안 고려 사항
 
이 항목에는 Azure VM에서 SQL Server 인스턴스로의 안전하게 액세스하는 데 도움이 되는 전반적인 보안 지침이 포함되어 있습니다. 그러나 Azure의 SQL Server 데이터베이스 인스턴스를 더 잘 보호하기 위해서는 Azure에 대한 보안 모범 사례에 더불어 기존의 온-프레미스 보안 사례 역시 구현하는 것이 좋습니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


SQL Server 보안 방법에 대한 자세한 내용은 [SQL Server 2008 R2 보안 모범 사례 - 운영 및 관리 작업](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)을 참조하세요.

Azure는 가상 컴퓨터에서 실행되는 SQL Server로 호환되는 솔루션을 제작할 수 있도록 하는 몇 가지 산업 규정 및 표준을 준수합니다. Azure 규정 준수에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/support/trust-center/)를 참조하세요.

다음은 Azure VM에서 SQL Server의 인스턴스를 구성 및 연결할 때 고려해야 하는 보안 권장 사항의 목록입니다.

## 계정 관리에 대한 고려 사항:

- 로컬 관리자 계정의 이름을 **Administrator**가 아닌 다른 이름으로 만듭니다.

- 모든 계정에 복잡하고 강력한 암호를 사용합니다. 강력한 암호를 만드는 방법에 대한 자세한 내용은 [강력한 암호를 만들기 위한 팁](http://windows.microsoft.com/en-us/windows-vista/Tips-for-creating-a-strong-password) 문서를 참조하세요.

- 기본적으로 Azure는 SQL Server 가상 컴퓨터를 설치하는 동안 Windows 인증을 선택합니다. 따라서 **SA** 로그인이 사용하지 않도록 설정되고 설치 프로그램에서 암호를 할당합니다. **SA** 로그인을 사용하거나 사용하도록 설정하지 않는 것이 좋습니다. SQL 로그인을 원할 경우 다음과 같은 다른 전략을 사용할 수 있습니다.
	- sysadmin 멤버 자격을 보유하는 SQL 계정을 만듭니다.
	- **SA** 로그인을 사용해야 한다면, 로그인을 사용하도록 설정하고 이름을 변경한 다음 새 암호를 할당합니다.
	- 위에서 설명한 두 옵션 모두 인증 모드를 **SQL Server 및 Windows 인증 모드**로 변경해야 합니다. 자세한 내용은 [서버 인증 모드 변경](https://msdn.microsoft.com/library/ms188670.aspx)을 참조하세요.

## Azure 가상 컴퓨터에 대한 연결을 보호하기 위한 고려 사항:

- 공용 RDP 포트 대신 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)를 사용하여 가상 컴퓨터를 관리하는 것이 좋습니다.

- [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md)(NSG)을 사용하여 가상 컴퓨터에 네트워크 트래픽을 허용하거나 거부합니다. NSG를 사용하려는데 끝점 ACL이 이미 있는 경우 먼저, 끝점 ACL을 제거합니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 끝점에 대한 ACL(액세스 제어 목록) 관리](../virtual-network/virtual-networks-acl-powershell.md)를 참조하세요.

- 끝점을 사용하는 경우 사용하지 않는 모든 끝점은 가상 컴퓨터에서 제거합니다. 끝점에서 ACL을 사용하는 방법에 대한 지침은 [끝점에 대한 ACL 관리](../virtual-network/virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint)를 참조하세요.

- Azure 가상 컴퓨터의 SQL Server 데이터베이스 엔진의 인스턴스에 대해 암호화된 연결 옵션을 사용하도록 설정합니다. 서명된 인증서로 SQL server 인스턴스를 구성합니다. 자세한 내용은 [데이터베이스 엔진에 암호화된 연결 사용](https://msdn.microsoft.com/library/ms191192.aspx) 및 [연결 문자열 구문](https://msdn.microsoft.com/library/ms254500.aspx)을 참조하세요.

- 가상 컴퓨터를 특정 네트워크에서만 액세스할 수 있도록 하려면 Windows 방화벽을 사용하여 특정 IP 주소나 네트워크 서브넷의 액세스를 제한합니다.

## 다음 단계

성능에 대한 모범 사례에도 관심이 있으면 [Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)를 참조하세요.

Azure VM에서 SQL Server 실행과 관련된 다른 항목은 [Azure 가상 컴퓨터의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

<!---HONumber=AcomDC_0629_2016-->