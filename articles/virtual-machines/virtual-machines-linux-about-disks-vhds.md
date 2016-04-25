<properties
	pageTitle="Linux VM에 대한 디스크 및 VHD 정보 | Microsoft Azure"
	description="Azure의 Linux 가상 컴퓨터용 디스크 및 VHD의 기본 사항에 대해 알아봅니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="cynthn"/>

# Azure 가상 컴퓨터용 디스크 및 VHD 정보

다른 컴퓨터와 마찬가지로, Azure에서 가상 컴퓨터는 운영 체제, 응용 프로그램 및 데이터를 저장하는 장소로 디스크를 사용합니다. 모든 Azure 가상 컴퓨터는 Linux 운영 체제 디스크(Linux VM인 경우)와 임시 디스크라는 적어도 2개의 디스크를 갖습니다. 운영 체제 디스크는 이미지에서 만들어지며, 운영 체제 디스크 및 이미지 모두는 실제로 Azure 저장소 계정에 저장된 가상 하드 디스크(VHD)입니다. 가상 컴퓨터에도 데이터 디스크가 있을 수 있으며 이러한 디스크도 VHD로 저장됩니다. 이 문서는 [Windows 가상 컴퓨터](virtual-machines-windows-about-disks-vhds.md)에도 적용됩니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## 다음 단계

-  [디스크를 연결](virtual-machines-linux-attach-disk-portal.md)하여 VM에 다른 저장소를 추가합니다.
-  중복성을 위해 [소프트웨어 RAID를 구성](virtual-machines-linux-configure-raid.md)합니다.
-  추가 VM을 신속하게 배포할 수 있도록 [Linux 가상 컴퓨터를 캡처](virtual-machines-linux-classic-capture-image.md)합니다.

<!---HONumber=AcomDC_0413_2016-->