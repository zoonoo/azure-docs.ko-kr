<properties
	pageTitle="Azure에서 Linux를 실행하는 사용자 지정 가상 컴퓨터 만들기"
	description="Azure에서 Linux를 실행하는 사용자 지정 가상 컴퓨터를 만드는 방법을 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="dkshir"/>

# Azure에서 Linux를 실행하는 사용자 지정 가상 컴퓨터를 만드는 방법

이 항목에서는 Azure CLI에서 클래식 배포 모델을 사용하여 *사용자 지정* 가상 컴퓨터를 만드는 방법을 설명합니다. Azure에서 제공되는 **이미지**의 Linux 이미지를 사용합니다. Azure CLI 명령은 특히 다음과 같은 구성 선택 항목을 제공합니다.

- 가상 네트워크에 VM 연결
- 기존 클라우드 서비스에 VM 추가
- 기존 저장소 계정에 VM 추가
- 가용성 집합 또는 위치에 VM 추가

> [AZURE.IMPORTANT]가상 컴퓨터에서 가상 네트워크를 사용하여 호스트 이름으로 가상 컴퓨터에 직접 연결하거나 프레미스 간 연결을 설정할 수 있게 하려는 경우 가상 컴퓨터를 만들 때 가상 네트워크를 지정해야 합니다. 가상 컴퓨터를 만드는 경우에만 가상 네트워크에 가입하도록 가상 컴퓨터를 구성할 수 있습니다. 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요](http://go.microsoft.com/fwlink/p/?LinkID=294063)를 참조하세요.

<p/>
[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Linux를 실행하는 가상 컴퓨터 만들기](virtual-machines-linux-tutorial.md)


## 클래식 배포 모델을 사용하여 Linux 가상 컴퓨터를 만드는 방법

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]

<!---HONumber=August15_HO8-->