<properties
   pageTitle="게이트웨이 연결 확인 | Microsoft Azure"
   description="이 문서에서는 Resource Manager 배포 모델에서 게이트웨이 연결을 확인하는 방법을 보여 줍니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/03/2016"
   ms.author="cherylmc"/>

# 게이트웨이 연결 확인

몇 가지 방법으로 게이트웨이 연결을 확인할 수 있습니다. 이 문서에서는 Azure 포털을 사용하고 PowerShell을 사용하여 Resource Manager 게이트웨이 연결의 상태를 확인하는 방법을 보여 줍니다.


## 시작하기 전에

PowerShell을 사용하려는 경우 최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요. Resource Manager cmdlet을 사용하는 방법에 대한 자세한 내용은 [Resource Manager에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

1. PowerShell 콘솔을 열고 계정에 연결합니다.

		Login-AzureRmAccount

2. 계정에 대한 구독을 확인합니다.

		Get-AzureRmSubscription 

3. 사용할 구독을 지정합니다.

		Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 연결 확인


[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## 다음 단계

- 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 단계는 [가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md)를 참조하세요.

<!---HONumber=AcomDC_0810_2016-->