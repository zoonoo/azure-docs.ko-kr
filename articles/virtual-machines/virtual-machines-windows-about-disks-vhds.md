<properties
	pageTitle="Windows VM에 대한 디스크 및 VHD 정보 | Microsoft Azure"
	description="Azure의 Windows 가상 컴퓨터용 디스크 및 VHD의 기본 사항에 대해 알아봅니다. "
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="cynthn"/>

# Azure 가상 컴퓨터용 디스크 및 VHD 정보

다른 컴퓨터와 마찬가지로, Azure에서 가상 컴퓨터는 운영 체제, 응용 프로그램 및 데이터를 저장하는 장소로 디스크를 사용합니다. 모든 Azure 가상 컴퓨터는 Windows 운영 체제 디스크(Windows VM인 경우)와 임시 디스크라는 적어도 2개의 디스크를 갖습니다. 운영 체제 디스크는 이미지에서 만들어지며, 운영 체제 디스크 및 이미지 모두는 실제로 Azure 저장소 계정에 저장된 가상 하드 디스크(VHD)입니다. 가상 컴퓨터에도 데이터 디스크가 있을 수 있으며 이러한 디스크도 VHD로 저장됩니다. 이 문서는 [Linux 가상 컴퓨터](virtual-machines-linux-about-disks-vhds.md)에도 적용됩니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## 다음 단계
-  VM 배포 규모를 확장할 수 있도록 [Windows 가상 컴퓨터를 캡처](virtual-machines-windows-capture-image.md)합니다.
-  새 VM을 만들 때 사용하기 위해 [Windows VM 이미지를 Azure에 업로드](virtual-machines-windows-upload-image.md)합니다.
-  응용 프로그램이 D: 드라이브를 사용할 수 있도록 [Windows 임시 디스크의 드라이브 문자를 변경](virtual-machines-windows-classic-change-drive-letter.md)합니다.

<!---HONumber=AcomDC_0622_2016-->