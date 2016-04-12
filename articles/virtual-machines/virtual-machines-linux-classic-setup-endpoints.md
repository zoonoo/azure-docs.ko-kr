<properties
	pageTitle="클래식 Linux VM에서 끝점 설정 | Microsoft Azure"
	description="Azure에서 Linux 가상 컴퓨터와 통신을 허용하도록 Azure 클래식 포털에서 끝점을 설정하는 방법에 대해 알아봅니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="cynthn"/>

# Azure에서 클래식 Linux 가상 컴퓨터에 끝점을 설정하는 방법

클래식 배포 모델을 사용하여 Azure에서 만든 모든 Linux 가상 컴퓨터는 개인 네트워크 채널을 통해 동일한 클라우드 서비스 또는 가상 네트워크에 있는 다른 가상 컴퓨터와 자동으로 통신할 수 있습니다. 그러나 인터넷이나 다른 가상 네트워크의 컴퓨터가 가상 컴퓨터로 인바운드 네트워크 트래픽을 전달하려면 끝점이 필요합니다. Windows VM에서 끝점에 대한 정보를 원하는 경우 [Azure에서 클래식 Windows 가상 컴퓨터에 끝점을 설정하는 방법](virtual-machines-windows-classic-setup-endpoints.md)을 참조하세요.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.

Azure 클래식 포털에서 Linux 가상 컴퓨터를 만들 때 SSH(Secure Shell)에 대한 공통 끝점은 일반적으로 자동으로 만들어집니다. 가상 컴퓨터를 만드는 동안 또는 가상 컴퓨터를 만든 후 필요에 따라 추가 끝점을 만들 수 있습니다.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## 다음 단계

* [서비스 관리 모드](../virtual-machines-command-line-tools.md)에서 Azure 명령줄 인터페이스를 사용하여 VM 끝점을 만들 수도 있습니다. **azure vm endpoint create** 명령을 실행합니다.

<!---HONumber=AcomDC_0330_2016-->