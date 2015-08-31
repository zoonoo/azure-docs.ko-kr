<properties
	pageTitle="Azure에서 Windows를 실행하는 사용자 지정 가상 컴퓨터 만들기"
	description="Azure에서 Windows를 실행하는 사용자 지정 가상 컴퓨터를 만드는 방법에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>


<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="kathydav"/>

#Azure에서 Windows를 실행하는 사용자 지정 가상 컴퓨터 만들기

*사용자 지정* 가상 컴퓨터란 **갤러리에서** 옵션을 사용하여 만드는 가상 컴퓨터를 말하는데, 이는 **빠른 생성** 옵션보다 사용할 수 있는 구성 옵션이 더 많기 때문입니다. 이러한 옵션에는 다음이 포함됩니다.

- 가상 네트워크에 가상 컴퓨터 연결
- VM 에이전트 및 확장 설치(예: 맬웨어 방지용)
- 기존 클라우드 서비스에 가상 컴퓨터 추가
- 기존 저장소 계정에 가상 컴퓨터 추가
- 가상 컴퓨터를 가용성 집합에 추가

> [AZURE.IMPORTANT]가상 컴퓨터에서 가상 네트워크를 사용하여 호스트 이름으로 가상 컴퓨터에 직접 연결하거나 프레미스 간 연결을 설정할 수 있게 하려는 경우 가상 컴퓨터를 만들 때 가상 네트워크를 지정해야 합니다. 가상 컴퓨터를 만드는 경우에만 가상 네트워크에 가입하도록 가상 컴퓨터를 구성할 수 있습니다. 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요](virtual-networks-overview.md)를 참조하세요.

##가상 컴퓨터를 만들려면

[AZURE.INCLUDE [virtual-machines-create-windowsvm](../../includes/virtual-machines-create-windowsvm.md)]

<!-----HONumber=August15_HO7-->