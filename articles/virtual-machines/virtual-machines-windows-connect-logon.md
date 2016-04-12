<properties
	pageTitle="Windows Server VM에 로그온 | Microsoft Azure"
	description="Azure 포털 및 리소스 관리자 배포 모델을 사용하여 Windows Server VM에 로그온하는 방법을 알아봅니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-connect-logon.md).

Azure 포털의 **연결** 단추를 사용하여 원격 데스크톱 세션을 시작합니다. 먼저 가상 컴퓨터에 연결한 다음 로그온합니다.

## 가상 컴퓨터에 연결

1. 아직 로그인하지 않은 경우 [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2.	허브 메뉴에서 **가상 컴퓨터**를 클릭합니다.

3.	목록에서 가상 컴퓨터를 선택합니다.

4. 가상 컴퓨터 블레이드에서 **연결**을 클릭합니다.

	![가상 컴퓨터에 연결](./media/virtual-machines-windows-connect-logon/preview-portal-connect.png)

## 가상 컴퓨터에 로그온

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## 문제 해결

로그온에 대한 팁이 도움이 되지 않거나 필요한 정보가 아닌 경우[Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-windows-troubleshoot-rdp-connection.md)을 참조하세요. 이 문서에서는 일반적인 문제를 진단 및 해결하는 과정을 안내합니다.

<!---HONumber=AcomDC_0323_2016-->