<properties
   pageTitle="로컬 네트워크 게이트웨이 IP 주소 접두사 및 게이트웨이 IP 수정 | Microsoft Azure"
   description="이 문서는 로컬 네트워크 게이트웨이에 대한 IP 주소 접두사를 변경하는 방법을 안내합니다."
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
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# PowerShell을 사용하여 로컬 네트워크 게이트웨이 설정 수정

때로는 로컬 네트워크 게이트웨이 AddressPrefix 또는 GatewayIPAddress에 대한 설정을 변경합니다. 아래 지침을 따라 로컬 네트워크 게이트웨이 설정을 수정할 수 있습니다. 또한 Azure 포털에서 이러한 설정을 수정할 수 있습니다.

## 시작하기 전에
	
최신 버전의 Azure 리소스 관리자 PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

## IP 주소 접두사를 수정하려면

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## 게이트웨이 IP 주소를 수정하려면

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## 다음 단계

게이트웨이 연결을 확인할 수 있습니다. [게이트웨이 연결 확인](vpn-gateway-verify-connection-resource-manager.md)을 참조하세요.

<!---HONumber=AcomDC_0810_2016-->