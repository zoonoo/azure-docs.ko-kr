<properties
	pageTitle="DevTest Lab에 대한 가상 네트워크 구성 | Microsoft Azure"
	description="기존 가상 네트워크 및 서브넷을 구성하고 VM에서 사용하는 방법에 대해 알아봅니다."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="tarcher"/>

# DevTest Lab에 대한 가상 네트워크 구성

## 개요

[Azure DevTest Lab에 아티팩트를 사용하여 VM 추가](devtest-lab-add-vm-with-artifacts.md) 문서에 설명된 대로 랩에서 VM을 만들 때 해당 VM에 대해 구성된 가상 네트워크(및 서브넷)을 지정할 수 있습니다. 이 작업을 수행하는 시나리오 중 하나는 Express 경로 또는 사이트 간 VPN으로 설정된 가상 네트워크를 사용하여 VM에서 회사 네트워크 리소스에 액세스할 수 있게 되기를 원하는 경우입니다. 다음 섹션에서는 VM을 만들 때 선택할 수 있도록 랩의 가상 네트워크 설정으로 기존 가상 네트워크를 추가하는 방법을 보여 줍니다.

## Azure 포털을 사용하여 DevTest Lab에 대한 가상 네트워크 구성
다음 단계에서는 동일한 랩에서 VM을 만들 때 사용할 수 있도록 DevTest Lab에 기존 가상 네트워크(및 서브넷)을 추가하는 방법을 안내합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

1. **찾아보기**를 탭한 다음 목록에서 **DevTest Lab**을 탭합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. 선택된 랩의 **설정** 블레이드가 표시됩니다.

1. **가상 네트워크**를 탭합니다.

	![랩의 설정 블레이드에서 가상 네트워크를 구성할 수 있습니다.](./media/devtest-lab-configure-vnet/lab-settings-vnet.png)
	
1. **가상 네트워크** 블레이드에서 현재 랩에 대해 구성한 가상 네트워크 및 랩에 대해 만들어진 기본 가상 네트워크의 목록이 표시됩니다.

1. **+ 추가**를 탭합니다.

	![랩에 기존 가상 네트워크 추가](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
	
1. **가상 네트워크** 블레이드에서 **[가상 네트워크 선택]**을 탭합니다.

	![기존 가상 네트워크 선택](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
	
1. **가상 네트워크 선택** 블레이드에서 원하는 가상 네트워크를 선택합니다. 블레이드는 랩으로 구독에 동일한 지역에서 사용 중인 모든 가상 네트워크를 보여 줍니다.

1. 가상 네트워크를 선택하고 **가상 네트워크** 블레이드로 돌아오면 여러 필드가 활성화된 것을 볼 수 있습니다.

	![기존 가상 네트워크 선택](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)

1. 가상 네트워크 / 랩 조합에 대한 설명을 지정합니다.

1. 랩 VM 생성에서 사용할 서브넷을 허용하려면 **USE IN VM CREATION** 옵션을 체크합니다.

1. 서브넷에서 공용 IP 주소를 허용하려면 **공용 IP 허용** 옵션을 체크합니다.

1. **사용자당 최대 VM** 필드에서 각 서브넷에 대한 사용자당 최대 VM 지정합니다. VM 수에 제한을 두지 않으려면 이 필드는 공백으로 둡니다.

1. **저장**을 탭합니다.

1. 이제 가상 네트워크를 구성했으므로 새 VM을 만들 때 선택할 수 있습니다. 이는 [Azure DevTest Lab에 아티팩트를 사용하여 VM 추가](devtest-lab-add-vm-with-artifacts.md) 문서에 설명되어 있습니다.

## 다음 단계

랩에 원하는 가상 네트워크를 추가한 후 다음 단계는 [DevTest Lab에 VM을 추가](devtest-lab-add-vm-with-artifacts.md)하는 것입니다.

<!---HONumber=AcomDC_0323_2016-->