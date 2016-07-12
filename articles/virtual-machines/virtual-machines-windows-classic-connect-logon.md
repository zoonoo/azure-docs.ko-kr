<properties
	pageTitle="클래식 Azure VM에 로그온 | Microsoft Azure"
	description="Azure 클래식 포털을 사용하여 클래식 배포 모델로 만든 Windows 가상 컴퓨터에 로그온할 수 있습니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/05/2016"
	ms.author="cynthn"/>


# Azure 클래식 포털을 사용하여 Windows 가상 컴퓨터에 로그온

Azure 클래식 포털에서 **연결** 버튼을 사용하여 원격 데스크톱 세션을 시작 및 Windows VM에 로그온합니다.

Linux VM에 연결하고 싶으세요? [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-linux-classic-log-on.md)을 참조하세요.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager 모델을 사용하여 이러한 단계를 수행](virtual-machines-windows-connect-logon.md)하는 방법을 알아봅니다.


## 연습 동영상

이 자습서의 비디오 연습은 다음과 같습니다. 또한 Azure에서 Windows VM에 연결하는 데 사용되는 끝점과 공용 및 개인 포트에 대해서도 설명합니다.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## 가상 컴퓨터에 연결

1. Azure 클래식 포털에 로그인합니다.

2. **가상 컴퓨터**를 클릭한 다음 가상 컴퓨터를 선택합니다.

3. 페이지 아래쪽의 명령 모음에서 **연결**을 클릭합니다.

	![가상 컴퓨터에 로그온](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)
	
> [AZURE.TIP] 연결 단추를 사용할 수 없는 경우 이 문서의 끝에 문제 해결 팁을 참조하세요.

## 가상 컴퓨터에 로그온

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## 다음 단계

-	**연결** 단추가 비활성 상태이거나 원격 데스크톱 연결에 다른 문제가 있는 경우 구성을 다시 설정해 봅니다. 가상 컴퓨터 대시보드에서, **빠른 보기** 아래 **재설정 원격 구성**을 클릭합니다.
-	암호에 문제가 있는 경우 암호를 다시 설정합니다. 가상 컴퓨터 대시보드에서, **빠른 보기** 아래 **암호 재설정**을 클릭합니다.

이러한 팁이 작동하지 않거나 필요한 정보가 아닌 경우 [Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-windows-troubleshoot-rdp-connection.md)을 참조하세요. 이 문서에서는 일반적인 문제를 진단 및 해결하는 과정을 안내합니다.

<!---HONumber=AcomDC_0629_2016-->