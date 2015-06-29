<properties 
	pageTitle="Windows Server를 실행하는 가상 컴퓨터에 로그온" 
	description="Azure 관리 포털을 사용하여 Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/12/2015" 
	ms.author="kathydav"/>


# Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법#

Azure 포털의 **연결** 단추를 사용하여 원격 데스크톱 세션을 시작합니다. (Linux VM의 경우 [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-linux-how-to-log-on.md)을 참조하세요.)

## 로그온하는 방법

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## 문제 해결 팁

다음 몇 가지 사항을 신속하게 시도해 봅니다.

원격 데스크톱 연결에 문제가 있는 경우, 포털에서 구성 재설정을 시도해 보세요. 가상 컴퓨터 대시보드에서, **빠른 보기** 아래 **재설정 원격 구성**을 클릭합니다.

암호에 문제가 있는 경우, 포털에서 다시 설정해 보세요. 가상 컴퓨터 대시보드에서, **빠른 보기** 아래 **암호 재설정**을 클릭합니다.

작동하지 않으면 더 광범위 한 문제 해결을 수행해야 합니다. 지침은 [Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-troubleshoot-remote-desktop-connections.md)을 참조하세요.
 
 

<!---HONumber=58_postMigration-->