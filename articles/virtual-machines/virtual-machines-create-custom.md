<properties
	pageTitle="사용자 지정 Windows 가상 컴퓨터 만들기 | Microsoft Azure"
	description="클래식 배포 모델을 사용하여 Azure 클래식 포털에서 사용자 지정 Windows 가상 컴퓨터를 만드는 방법을 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/15/2016"
	ms.author="cynthn"/>

	
# Windows를 실행하는 사용자 지정 가상 컴퓨터 만들기


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.
 


사용자 지정 가상 컴퓨터란 단순히 **갤러리에서** 옵션을 사용하여 만든 가상 컴퓨터를 의미하며, **빠른 생성** 옵션보다 사용할 수 있는 구성 옵션이 더 많기 때문입니다. 이러한 옵션에는 다음이 포함됩니다.

- 가상 네트워크에 가상 컴퓨터 연결
- 맬웨어 방지 등, Azure Virtual Machine Agent 및 Azure Virtual Machine Extensions 설치
- 기존 클라우드 서비스에 가상 컴퓨터 추가
- 기존 저장소 계정에 가상 컴퓨터 추가
- 가상 컴퓨터를 가용성 집합에 추가

> [AZURE.IMPORTANT]가상 컴퓨터에서 가상 네트워크를 사용하여 호스트 이름으로 가상 컴퓨터에 직접 연결하거나 프레미스 간 연결을 설정할 수 있게 하려는 경우 가상 컴퓨터를 만들 때 가상 네트워크를 지정해야 합니다. 가상 컴퓨터를 만드는 경우에만 가상 네트워크에 가입하도록 가상 컴퓨터를 구성할 수 있습니다. 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요](virtual-networks-overview.md)를 참조하세요.


## 가상 컴퓨터를 만들려면


[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

<!---HONumber=AcomDC_0121_2016-->