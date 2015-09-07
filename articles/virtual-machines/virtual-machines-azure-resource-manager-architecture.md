<properties
   pageTitle="Azure 리소스 관리자 아키텍처"
	description="리소스 관리자의 아키텍처 및 계산, 네트워크 및 저장소 리소스 공급자 간의 관계에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="davidmu"/>

# Azure 리소스 관리자 아키텍처

이 문서에서는 인프라 기반 응용 프로그램 및 워크로드를 만들기 위한 서비스 관리 및 리소스 관리자 아키텍처의 개요를 제공합니다.

## 서비스 관리를 위한 아키텍처

Azure 리소스 관리자 및 다양한 리소스 공급자의 아키텍처를 이야기하기 전에 Azure 서비스 관리에 위한 현재 존재하는 아키텍처를 검토해 보겠습니다. Azure 서비스 관리에서 다음을 통해 가상 컴퓨터 호스팅에 대한 계산, 저장소 또는 계산 리소스가 제공됩니다.

- 가상 컴퓨터 호스팅(계산)을 위한 컨테이너 역할을 하는 데 필요한 클라우드 서비스. 가상 컴퓨터는 NIC(네트워크 인터페이스 카드) 및 Azure에서 할당된 IP 주소와 함께 자동으로 제공됩니다. 또한 클라우드 서비스는 외부 부하 분산 장치 인스턴스, 공용 IP 주소, 기본 끝점을 포함하여 Windows 기반의 가상 컴퓨터를 위한 원격 데스크톱 및 원격 PowerShell 트래픽 및 Linux 기반의 가상 컴퓨터를 위한 SSH(Secure Shell) 트래픽을 허용합니다.
- 운영 체제, 임시 및 추가 데이터 디스크(저장소)를 비롯하여 가상 컴퓨터를 위한 VHD를 저장하는 데 필요한 저장소 계정.
- 서브넷된 구조를 만들고 가상 컴퓨터가 위치한(네트워크) 서브넷을 지정할 수 있는 추가 컨테이너 역할을 하는 옵션 가상 네트워크입니다.

Azure 서비스 관리를 위한 구성 요소 및 해당 관계는 다음과 같습니다.

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## 리소스 관리자를 위한 아키텍처

Azure 리소스 관리자의 경우, 리소스 공급자는 사용자가 필요한 구성에서 작동하는 가상 컴퓨터를 만들기 위한 개별 리소스를 지원합니다. 가상 컴퓨터의 경우, 다음과 같은 세 가지 주요 리소스 공급자가 있습니다.

- CRP(계산 리소스 공급자): 가상 컴퓨터 및 옵션 가용성 집합의 인스턴스를 지원합니다.
- SRP(저장소 리소스 공급자): 운영 체제 및 추가 데이터 디스크를 비롯하여 가상 컴퓨터를 위한 VHD를 저장하는 데 필요한 저장소 계정을 지원합니다.
- NRP(네트워크 리소스 공급자): 필수 NIC, 가상 컴퓨터 IP 주소, 가상 네트워크 및 옵션 부하 분산 장치, 부하 분산 장치 IP 주소 내의 서브넷, 네트워크 보안 그룹을 지원합니다.

또한 다음과 같이 리소스 공급자 내에서 리소스 간에 관계가 있습니다.

- Blob 저장소(필수)에 해당 디스크를 저장하기 위해 가상 컴퓨터는 SRP에서 정의된 특정 저장소 계정에 따라 달라집니다.
- 가상 컴퓨터는 NRP(필수)에서 정의된 특정 NIC 및 CRP(옵션)에서 정의된 가용성 집합을 참조합니다.
- NIC는 가상 컴퓨터의 할당된 IP 주소(필수), 가상 컴퓨터에 대한 가상 네트워크의 서브넷(필수) 및 네트워크 보안 그룹(옵션)을 참조합니다.
- 가상 네트워크 내의 서브넷은 네트워크 보안 그룹(옵션)을 참조합니다.
- 부하 분산 장치 인스턴스는 가상 컴퓨터의 NIC(옵션)를 포함하는 IP 주소의 백 엔드 풀을 참조하며 로드 분산 장치 공용 또는 개인 IP 주소(옵션)를 참조합니다.

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch2.png)

리소스의 구성 요소화는 Azure에 호스팅된 IT 워크로드에 대한 인프라를 구성할 때 더 많은 유용성을 허용합니다. Azure 리소스 관리자 템플릿을 사용하면 특정 구성에 필요한 종속 리소스 집합을 만드는 데 이러한 유연성을 활용할 수 있습니다. 템플릿을 실행할 때 리소스 관리자는 리소스가 종속성 및 참조를 유지하기 위해 올바른 순서로 구성되도록 합니다. 예를 들어, 리소스 관리자는 서브넷 및 IP 주소(네트워크 보안 그룹은 옵션임)와 함께 가상 네트워크를 만들 때까지 가상 컴퓨터에 대한 NIC를 만들지 않습니다.

리소스 그룹은 여러 가상 컴퓨터, NIC, IP 주소, 부하 분산 장치, 서브넷 및 네트워크 보안 그룹으로 구성될 수 있는 응용 프로그램에 대해 관련 리소스를 유지하는 논리적 컨테이너입니다. 예를 들어, 단일 관리 단위로 응용 프로그램의 모든 리소스를 관리할 수 있습니다. 이들 모두를 함께 만들고, 업데이트하고, 삭제할 수 있습니다. 다음은 단일 리소스 그룹으로 배포된 예제 응용 프로그램입니다.

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

응용 프로그램은 다음과 같이 구성되어 있습니다.

- 동일한 저장소 계정을 사용하는 두 개의 가상 컴퓨터는 동일한 가용성 집합에 있으며 가상 네트워크의 동일한 서브넷에 있습니다.
- 각 가상 컴퓨터에 대한 단일 NIC 및 VM IP 주소입니다.
- 인터넷 트래픽을 두 가상 컴퓨터의 NIC에 분산하는 외부 부하 분산 장치입니다.

이 응용 프로그램의 모든 리소스는 이들을 포함하는 단일 리소스 그룹을 통해 관리됩니다.

또한 Azure PowerShell 또는 Azure CLI를 사용하여 리소스 관리자 기반의 가상 컴퓨터를 만들 때 리소스 사이의 종속 관계 및 구성 요소화를 볼 수 있습니다. 가상 컴퓨터를 만드는 명령을 실행하려면 리소스 그룹, 저장소 계정, 서브넷 포함 가상 네트워크, IP 주소 포함 NIC를 만들어야 합니다. 자세한 내용은 [Azure 리소스 관리자 및 PowerShell을 사용하여 Windows 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)을 참조하세요.

## 다음 단계

[Azure 리소스 관리자 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리](virtual-machines-deploy-rmtemplates-azure-cli.md)

[리소스 관리자 템플릿 및 PowerShell을 사용하여 Azure 가상 컴퓨터 배포 및 관리](virtual-machines-deploy-rmtemplates-powershell.md)

## 추가 리소스

[Azure 리소스 관리자의 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md)

[Azure 리소스 관리자 개요](resource-group-overview.md)

<!---HONumber=August15_HO9-->