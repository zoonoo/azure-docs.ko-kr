<properties
	pageTitle="Linux VM 할당 오류 문제 해결 | Microsoft Azure"
	description="Azure에서 Linux VM을 만들거나 재시작하거나 크기를 조정하는 경우 할당 오류 해결"
	services="virtual-machines-linux, azure-resource-manager"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue,azure-resourece-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="na"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="cjiang"/>

# Azure에서 Linux VM을 만들거나 재시작하거나 크기를 조정하는 경우 할당 오류 해결

VM을 만들거나 중지된(할당이 취소된) VM을 재시작하거나 VM의 크기를 조정하는 경우 Microsoft Azure에서 구독에 계산 리소스를 할당합니다. 이러한 작업을 수행하면서 오류를 수신하는 경우도 있습니다. Azure 구독 제한에 도달하기 전에도 그렇습니다. 이 문서는 일부 일반적인 할당 오류의 이유를 설명하고 가능한 수정을 제안합니다. 서비스 배포를 계획하는 사용자에게 이 정보가 유용할 수 있습니다.

"일반 문제 해결 단계" 섹션에는 일반적인 문제를 해결하는 단계가 나와 있습니다. "자세한 문제 해결 단계" 섹션은 특정 오류 메시지에 따른 해결 단계를 제공합니다. 시작하기 전에 할당이 어떻게 작동하며 할당 오류가 발생하는 이유에 대한 몇 가지 배경 정보를 설명합니다. [Azure에서 Windows VM을 만들거나 재시작하거나 크기를 조정하는 경우 할당 오류를 해결](virtual-machines-windows-allocation-failure.md)할 수도 있습니다.


[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]

<!---HONumber=AcomDC_0330_2016-->