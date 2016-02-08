<properties
pageTitle="랩 정책 정의 | Microsoft Azure"
description="VM 크기, 사용자당 최대 VM 수 및 자동 종료와 같은 랩 정책을 정의하는 방법에 대해 알아봅니다."
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
ms.date="11/01/2015"
ms.author="tarcher"/>

# 랩 정책 정의

## 개요

DevTest Lab을 사용하면 랩 및 해당 VM을 사용하는 방법을 관리하는 핵심 정책을 지정할 수 있습니다. 이러한 정책에는 비용 임계값, 허용 VM 크기, 사용자당 최대 VM 수, 랩에 있는 VM 자동 종료가 포함됩니다.

## 랩 정책 액세스

랩에 대한 정책을 보거나 변경하려면 다음 단계를 수행합니다.

1. [Azure Preview 포털](https://portal.azure.com)에 로그인합니다.

1. **찾아보기**를 탭한 다음 목록에서 **DevTest Lab**을 탭합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. **설정**을 탭합니다.

	![설정](./media/devtest-lab-set-lab-policy/lab-blade-settings.png)

1. **설정** 블레이드에 **정책**이라는 설정 그룹화가 있습니다.

	![설정](./media/devtest-lab-set-lab-policy/policies.png)

	설정하는 방법에 대해 자세히 알아보려면 아래 목록에서 원하는 정책을 탭합니다.

	- 비용 임계값 - 이 정책은 현재 지원되지 않습니다.

	- [Allowed VM Size](#set-allowed-vm-sizes)(허용 VM 크기) - 랩에서 허용되는 VM 크기 목록을 선택합니다. 사용자는 이 목록에서만 VM을 만들 수 있습니다.

	- [최대 VM 수](#set-maximum-vms) - 랩에 대해 만들 수 있는 최대 VM 수와 사용자가 만들 수 있는 최대 VM 수를 지정합니다.

	- [Auto shutdown](#set-auto-shutdown)(자동 종료) - 현재 랩의 VM이 종료되어야 하는 시간을 지정합니다.

## 허용 VM 크기 설정

허용 VM 크기를 설정하는 정책은 랩에서 허용되는 VM 크기를 지정함으로써 랩의 낭비가 최소화되도록 돕습니다. 이 정책이 활성화되면 이 목록의 VM 크기만 사용하여 VM을 만들 수 있습니다.

1. 랩의 **설정** 블레이드의 **정책** 아래에서 **Allowed VM Sizes**(허용 VM 크기)를 탭합니다.

	![설정](./media/devtest-lab-set-lab-policy/allowed-vm-sizes-policy.png)
 
1. 이 정책을 사용하도록 설정하려면 **설정**을 탭하고 사용하지 않도록 설정하려면 **해제**를 탭합니다.

1. 이 정책을 사용하도록 설정한 경우 랩에서 만들 수 있는 하나 이상의 VM 크기를 탭합니다.

1. **저장**을 탭합니다.

## 최대 VM 설정

최대 VM 정책을 설정하면 사용자가 만들 수 있는 최대 VM 수뿐만 아니라 현재의 랩에 대해 만들 수 있는 최대 VM 수를 지정할 수 있습니다. 사용자 제한이나 랩 제한에 도달하면 사용자가 새 VM을 만들려고 하는 경우 VM을 만들 수 없다는 오류 메시지가 표시됩니다.

1. 랩의 **설정** 블레이드의 **정책** 아래에서 **최대 VM 수**를 탭합니다.

	![설정](./media/devtest-lab-set-lab-policy/max-vms-policies.png)

1. **Policy per User**(사용자당 정책) 섹션에서
 
	1. 이 정책을 사용하도록 설정하려면 **설정**을 탭하고 사용하지 않도록 설정하려면 **해제**를 탭합니다.
	
	1. 이 정책을 사용하도록 설정했다면 **Maximum VMs allowed per User**(사용자당 허용된 최대 VM 수) 텍스트 상자에 사용자가 만들 수 있는 최대 VM 수를 나타내는 숫자 값을 입력합니다. 유효하지 않은 수를 입력하면 필드에 허용되는 최대 수가 UI에 표시됩니다.

1. **Policy per Lab**(랩당 정책) 섹션에서
 
	1. 이 정책을 사용하도록 설정하려면 **설정**을 탭하고 사용하지 않도록 설정하려면 **해제**를 탭합니다.
	
	1. 이 정책을 사용하도록 설정한 경우 **Maximum VMs allowed in this lab**(이 랩에 허용된 최대 VM 수) 텍스트 상자에 현재 랩에 대해 만들 수 있는 최대 VM 수를 나타내는 숫자 값을 입력합니다. 유효하지 않은 수를 입력하면 필드에 허용되는 최대 수가 UI에 표시됩니다.

1. **저장**을 탭합니다.

## 자동 종료 설정

자동 종료 정책에서는 이 랩의 VM이 종료되는 시간을 지정하여 랩 낭비를 최소화할 수 있도록 돕습니다.

1. 랩의 **설정** 블레이드의 **정책** 아래에서 **Auto Shutdown**(자동 종료)을 탭합니다.

	![설정](./media/devtest-lab-set-lab-policy/auto-shutdown-policy.png)

1. 이 정책을 사용하도록 설정하려면 **설정**을 탭하고 사용하지 않도록 설정하려면 **해제**를 탭합니다.

1. 이 정책을 사용하도록 설정한 경우 현재 랩의 모든 VM을 종료하는 시간을 지정합니다.

1. **저장**을 탭합니다.

<!---HONumber=AcomDC_0128_2016-->