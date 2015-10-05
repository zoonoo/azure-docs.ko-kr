<properties
	pageTitle="VM에 로그온 | Microsoft Azure"
	description="포털을 사용하여 클래식 배포 모델로 만든 Windows 가상 컴퓨터에 로그온합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>


# 클래식 배포 모델을 사용하여 만든 Windows 가상 컴퓨터에 로그온

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 클래식 배포 모델을 사용하여 리소스를 관리하는 방법을 설명합니다.

Azure Preview 포털의 **연결** 단추를 사용하여 원격 데스크톱 세션을 시작합니다. 먼저 가상 컴퓨터에 연결한 다음 로그온합니다.

Linux VM에 연결하고 싶으세요? [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-linux-how-to-log-on.md)을 참조하세요.

## 가상 컴퓨터에 연결

이 자습서의 단계 연습은 다음과 같습니다.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]

1. 아직 로그인하지 않은 경우 [Azure 포털](http://manage.windowsazure.com)에 로그인합니다.

2. **가상 컴퓨터**를 클릭하고 해당 가상 컴퓨터를 선택합니다.

3. 명령 모음에서 **연결**을 클릭합니다.

	![가상 컴퓨터에 로그온](./media/virtual-machines-log-on-windows-server/connectwindows.png)

## 가상 컴퓨터에 로그온

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## 문제 해결 팁

포털에서 다음 몇 가지 작업을 신속하게 시도해 보세요.

-	원격 데스크톱 연결에 문제가 있는 경우 구성을 다시 설정합니다. 가상 컴퓨터 대시보드에서, **빠른 보기** 아래 **재설정 원격 구성**을 클릭합니다.
-	암호에 문제가 있는 경우 암호를 다시 설정합니다. 가상 컴퓨터 대시보드에서, **빠른 보기** 아래 **암호 재설정**을 클릭합니다.

이러한 팁이 작동하지 않거나 필요한 정보가 아닌 경우[Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-troubleshoot-remote-desktop-connections.md)을 참조하세요. 이 문서에서는 일반적인 문제를 진단 및 해결하는 과정을 안내합니다.

<!---HONumber=Sept15_HO4-->