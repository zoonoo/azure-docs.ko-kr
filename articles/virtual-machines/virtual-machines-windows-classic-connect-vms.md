<properties
	pageTitle="클라우드 서비스에서 Windows VM 연결 | Microsoft Azure"
	description="클래식 배포 모델을 사용하여 만든 Windows 가상 컴퓨터를 Azure 클라우드 서비스 또는 가상 네트워크에 연결합니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="cynthn"/>

# 클래식 배포 모델을 사용하여 만든 Windows 가상 컴퓨터를 가상 네트워크 또는 클라우드 서비스에 연결

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

클래식 배포 모델을 사용하여 만든 Windows 가상 컴퓨터는 항상 클라우드 서비스에 배치됩니다. 클라우드 컴퓨터는 컨테이너 역할을 하며 인터넷을 통해 가상 컴퓨터에 액세스할 수 있도록 고유한 공용 DNS 이름, 공용 IP 주소 및 끝점 집합을 제공합니다. 클라우드 서비스는 가상 네트워크에 있을 수 있지만 요구 사항은 아닙니다. 또한 [Linux 가상 컴퓨터를 가상 네트워크 또는 클라우드 서비스와 연결](virtual-machines-linux-classic-connect-vms.md)할 수도 있습니다.

가상 네트워크에 없는 클라우드 서비스를 *독립 실행형* 클라우드 서비스라고 합니다. 독립 실행형 클라우드 서비스의 가상 컴퓨터는 다른 가상 컴퓨터의 공용 DNS 이름을 사용하여 다른 가상 컴퓨터와 통신만 할 수 있고 인터넷을 통해 트래픽을 이동시킵니다. 클라우드 서비스가 가상 네트워크에 있는 경우 해당 클라우드 서비스의 가상 컴퓨터는 인터넷을 통해 트래픽을 보내지 않고도 가상 네트워크에 있는 다른 모든 가상 컴퓨터와 통신할 수 있습니다.

같은 독립 실행형 클라우드 서비스에 가상 컴퓨터를 배치하는 경우에는 계속 부하 분산 및 가용성 집합을 사용할 수 있습니다. 자세한 내용은 [가상 컴퓨터 부하 분산](virtual-machines-windows-load-balance.md) 및 [가상 컴퓨터의 가용성 관리](virtual-machines-windows-manage-availability.md)를 참조하세요. 그러나 서브넷에서 가상 컴퓨터를 구성하거나 독립 실행형 클라우드 서비스를 온-프레미스 네트워크에 연결할 수는 없습니다. 예를 들면 다음과 같습니다.

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## 다음 단계

가상 컴퓨터를 만들고 나서 서비스 및 워크로드에 데이터를 저장할 위치가 포함되도록 [데이터 디스크를 추가](virtual-machines-windows-classic-attach-disk.md)하는 것이 좋습니다.

<!---HONumber=AcomDC_0706_2016-->