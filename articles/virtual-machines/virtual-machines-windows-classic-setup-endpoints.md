<properties
	pageTitle="클래식 Windows VM에서 끝점 설정 | Microsoft Azure"
	description="Azure에서 Windows 가상 컴퓨터와 통신을 허용하도록 Azure 클래식 포털에서 Windows VM에 대한 끝점을 설정하는 방법에 대해 알아봅니다."
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
	ms.date="07/13/2016"
	ms.author="cynthn"/>

# Azure에서 클래식 Windows 가상 컴퓨터에 끝점을 설정하는 방법


클래식 배포 모델을 사용하여 Azure에서 만든 모든 Windows 가상 컴퓨터는 개인 네트워크 채널을 통해 동일한 클라우드 서비스 또는 가상 네트워크에 있는 다른 가상 컴퓨터와 자동으로 통신할 수 있습니다. 그러나 인터넷이나 다른 가상 네트워크의 컴퓨터가 가상 컴퓨터로 인바운드 네트워크 트래픽을 전달하려면 끝점이 필요합니다. 이 문서는 [Linux 가상 컴퓨터](virtual-machines-linux-classic-setup-endpoints.md)에도 적용됩니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] **리소스 관리자** 배포 모델에서는 **NSG(네트워크 보안 그룹)**를 사용하여 끝점을 구성합니다. 자세한 내용은 [Azure 포털을 사용하여 VM에 대한 외부 액세스 허용](virtual-machines-windows-nsg-quickstart-portal.md)을 참조하세요.

Azure 클래식 포털에서 Windows 가상 컴퓨터를 만들 때 원격 데스크톱, Windows PowerShell 원격 등에 대한 일반적인 끝점은 일반적으로 자동으로 만들어집니다. 가상 컴퓨터를 만드는 동안 또는 가상 컴퓨터를 만든 후 필요에 따라 추가 끝점을 만들 수 있습니다.



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## 다음 단계

* Azure PowerShell cmdlet을 사용하여 VM 끝점을 설정하려면 [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx)를 참조하세요.

* Azure PowerShell cmdlet을 사용하여 끝점에서 ACL을 관리하려면 [PowerShell을 사용하여 끝점에 대한 ACL(액세스 제어 목록) 관리](../virtual-network/virtual-networks-acl-powershell.md)를 참조하세요.

* 리소스 관리자 배포 모델에서 가상 컴퓨터를 만들면, Azure PowerShell을 사용하여 VM에 대한 트래픽을 제어하는 [네트워크 보안 그룹을 만들 수 있습니다](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

<!---HONumber=AcomDC_0713_2016-->