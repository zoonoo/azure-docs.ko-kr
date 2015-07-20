<properties
	pageTitle="서비스 관리|Microsoft Azure에 대한 문서"
	description="Azure 서비스 관리에서 가상 컴퓨터 생성과 관리를 돕는 문서의 목록"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-multiple"
	ms.workload="infrastructure-services"
	ms.date="06/30/2015"
	ms.author="danlep"/>

# 서비스 관리에서의 가상 컴퓨터에 대한 문서
이는 Azure 서비스 관리에서 가상 컴퓨터 작업에 대한 문서의 목록입니다. 새 배포에 대해, Microsoft는 Azure 리소스 관리자 기반 가상 컴퓨터 및 다른 리소스 사용을 권장합니다. 리소스 관리자의 장점에 대한 설명은 [Azure 리소스 관리자에 통합된 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md)를 참조하세요.

## 시작

[Azure 관리에서 Windows를 실행하는 가상 컴퓨터를 만들기](virtual-machines-windows-tutorial-classic-portal.md)

[Windows를 실행하는 사용자 지정 가상 컴퓨터를 만드는 방법](virtual-machines-windows-create-custom.md)

[Linux를 실행하는 사용자 지정 가상 컴퓨터를 만드는 방법](virtual-machines-linux-create-custom.md)

[자습서: Azure에서 클라우드 전용 가상 네트워크 만들기](create-virtual-network.md)

## 자동화

[Azure PowerShell을 사용하여 Linux 기반 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-linux-vms.md)

[Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-windows-vms.md)

[PowerShell 및 Azure 서비스 관리를 사용하여 Windows 가상 컴퓨터 만들기 및 관리](virtual-machines-create-windows-powershell-service-manager.md)

[Azure CLI(Azure 명령줄 인터페이스)를 사용하여 VM 만들기](virtual-machines-xplat-getting-started.md)

[Azure CLI를 사용하여 여러 VM 배포 만들기](virtual-machines-create-multi-vm-deployment-xplat-cli.md)

## 계획

[Azure 인프라 서비스 구현 지침](virtual-machines-infrastructure-services-implementation-guidelines.md)

[VM 구성 설정 정보](https://msdn.microsoft.com/library/azure/dn763935.aspx)

[가상 네트워크가 필요합니까?](https://msdn.microsoft.com/library/azure/jj156007.aspx)

## 생성

[Windows Server VHD를 만들어서 Azure에 업로드](virtual-machines-create-upload-vhd-windows-server.md)

[Linux 운영 체제를 포함하는 가상 하드 디스크 만들기 및 업로드](virtual-machines-linux-create-upload-vhd.md)

[Windows 가상 컴퓨터를 캡처하여 템플릿으로 사용하는 방법](virtual-machines-capture-image-windows-server.md)


[Linux 가상 컴퓨터를 캡처하여 템플릿으로 사용하는 방법](virtual-machines-linux-capture-image.md)

[Linux 가상 컴퓨터를 캡처하여 CLI를 통해 템플릿으로 사용하는 방법](virtual-machines-vm-capture-image-cli.md)


## 관리

[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-log-on-windows-server.md)

[Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-linux-how-to-log-on.md)

[Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법](storage-windows-attach-disk.md)

[Windows 가상 컴퓨터에서 데이터 디스크를 분리하는 방법](storage-windows-detach-disk.md)

[Linux 가상 컴퓨터에 데이터 디스크를 연결하는 방법](virtual-machines-linux-how-to-attach-disk.md)

[Linux 가상 컴퓨터에서 데이터 디스크를 분리하는 방법](virtual-machines-linux-how-to-detach-disk.md)

[클라우드 서비스 또는 가상 네트워크로 가상 컴퓨터에 연결하는 방법](cloud-services-connect-virtual-machine.md)

[가상 컴퓨터의 가용성 집합을 구성하는 방법](virtual-machines-how-to-configure-availability.md)

[내부 부하 분산 장치 구성 시작](../load-balancer/load-balancer-internal-getstarted.md)

[인터넷 연결 부하 분산 장치 구성 시작](../load-balancer/load-balancer-internet-getstarted.md)

[가상 컴퓨터에 끝점을 설정하는 방법](virtual-machines-set-up-endpoints.md)

## 워크로드 만들기

[Active Directory](https://msdn.microsoft.com/library/azure/jj156090.aspx)

[Cassandra](virtual-machines-linux-nodejs-running-cassandra.md)

[Docker](virtual-machines-docker-with-xplat-cli.md)

[동적 NAV](https://msdn.microsoft.com/library/azure/dn168977.aspx)

[HPC 팩](https://msdn.microsoft.com/library/azure/dn518135.aspx)

[LAMP 스택](virtual-machines-linux-install-lamp-stack.md)

[Windows Server의 MongoDB](virtual-machines-install-mongodb-windows-server.md)

[Windows Server의 MySQL](virtual-machines-mysql-windows-server-2008r2.md)

[Oracle](virtual-machines-oracle-azure-virtual-machines.md)

[SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

[SQL Server](virtual-machines-sql-server-infrastructure-services.md)

## 모니터

[Azure에서 모니터링 경고 및 알림 이해](https://msdn.microsoft.com/library/azure/dn306639.aspx)

[진단 로그를 사용하도록 설정](../cloud-services/cloud-services-dotnet-diagnostics.md)

[Visual Studio에서 클라우드 서비스 또는 가상 컴퓨터 디버깅](https://msdn.microsoft.com/library/azure/ff683670.aspx)

[개발 및 테스트 환경에 게시하기 위해 Windows PowerShell Scripts 사용하기](https://msdn.microsoft.com/library/azure/dn642480.aspx)

## 문제 해결

[Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Linux 기반 Azure 가상 컴퓨터에 SSH(보안 셸) 연결 문제 해결](virtual-machines-troubleshoot-ssh-connections.md)

## 참조

[서비스 관리에 대한 PowerShell cmdlets](https://msdn.microsoft.com/library/azure/dn708504.aspx)

[서비스 관리 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)

[서비스 관리에 대한 Azure CLI 명령](virtual-machines-command-line-tools.md)

<!---HONumber=July15_HO2-->