<properties 
   pageTitle="VPN 게이트웨이 정보 | Microsoft Azure"
   description="Azure 가상 네트워크용 VPN 게이트웨이 연결에 대해 알아봅니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# VPN 게이트웨이 정보


VPN 게이트웨이는 가상 네트워크와 온-프레미스 위치 간에 네트워크 트래픽을 보내는 데 사용되는 리소스의 컬렉션입니다. 게이트웨이는 사이트간, 지점-사이트 및 Express 경로 연결에 사용됩니다. 또한 VPN 게이트웨이는 Azure 내의 여러 가상 네트워크 간(VNet 간)에 트래픽을 보내는 데에도 사용됩니다.

연결을 만들려면 VNet에 가상 네트워크 게이트웨이를 추가하고 추가 VPN 게이트웨이 리소스 및 설정을 구성합니다. 각각의 가상 네트워크에는 게이트웨이 유형당 하나의 가상 네트워크 게이트웨이가 있을 수 있습니다. 예를 들어 -GatewayType Vpn을 사용하는 하나의 가상 네트워크 게이트웨이와 -GatewayType Express 경로를 사용하는 하나의 가상 네트워크 게이트웨이가 있을 수 있습니다.

게이트웨이 요구 사항에 관한 정보는 [게이트웨이 요구 사항](vpn-gateway-about-vpn-gateway-settings.md#requirements)을 참조하세요. 예상된 총 처리량은 [VPN 게이트웨이 설정 정보](vpn-gateway-about-vpn-gateway-settings.md#aggthroughput)를 참조하세요. 가격 책정은 [VPN 게이트웨이 가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway)을 참조하세요. 구독 및 서비스 제한은 [네트워킹 제한](../articles/azure-subscription-service-limits.md#networking-limits)을 참조하세요.

VPN 게이트웨이를 구성할 때 사용할 지침은 가상 네트워크를 만드는 데 사용되는 배포 모델에 따라 달라집니다. 예를 들어 클래식 배포 모델을 사용하여 VNet을 만든 경우 클래식 배포 모델에 대한 가이드 및 지침을 사용하여 VPN 게이트웨이 설정을 만들고 구성합니다. 자세한 내용은 [Resource Manager 배포 및 클래식 배포 모델 이해](../resource-manager-deployment-model.md)를 참조하세요.

아래 섹션에서는 구성에 대해 다음 정보를 나열하는 테이블을 포함합니다.

- 사용 가능한 배포 모델
- 사용 가능한 구성 도구
- 사용할 수 있는 경우 문서로 직접 이동하는 링크


다이어그램 및 설명을 사용하여 요구 사항에 맞게 구성 토폴로지를 선택하도록 도울 수 있습니다. 다이어그램은 기본 초기 토폴로지를 보여 주지만 다이어그램을 지침으로 사용하여 더 복잡한 구성을 작성할 수 있습니다. 각 구성은 선택할 VPN 게이트웨이 설정에 따라 달라집니다.

### VPN 게이트웨이 구성 설정

VPN 게이트웨이가 리소스의 컬렉션이기 때문에 하나의 도구를 사용하여 일부 리소스를 구성한 다음 서로 전환하여 여러 리소스 설정을 구성할 수 있습니다. 현재, Azure 포털에서 모든 VPN 게이트웨이 리소스 설정을 구성할 수 없습니다. 각 구성에 대한 문서의 지침은 특정 도구가 필요한지 여부를 지정합니다. 클래식 배포 모델을 사용하는 경우 클래식 포털에서 작업하거나 PowerShell을 사용할 수 있습니다. 사용할 수 있는 개별 설정에 대한 정보는 [VPN 게이트웨이 설정 정보](vpn-gateway-about-vpn-gateway-settings.md)를 참조하세요.



## 사이트 간 및 다중 사이트

### 사이트 간

S2S(사이트 간) 연결은 IPsec/IKE(IKEv1 또는 IKEv2) VPN 터널을 통한 연결입니다. 이 연결 유형은 할당된 공용 IP 주소를 가지고 NAT 다음에 위치하지 않는 온-프레미스에 있는 VPN 장치를 필요로 합니다. S2S 연결은 프레미스 간 및 하이브리드 구성에 사용될 수 있습니다.

![S2S 연결](./media/vpn-gateway-about-vpngateways/demos2s.png "사이트 간")


### 다중 사이트

VNet과 여러 온-프레미스 네트워크 간의 VPN 연결을 만들고 구성할 수 있습니다. 여러 연결을 사용하는 경우 경로 기반 VPN 유형(클래식 VNet에 대한 동적 게이트웨이)을 사용해야 합니다. VNet이 하나의 가상 네트워크 게이트웨이만 사용할 수 있으므로 게이트웨이를 통한 모든 연결은 사용 가능한 대역폭을 공유합니다. 이러한 유형의 구성은 "다중 사이트" 연결이라고 합니다.
 

![다중 사이트 연결](./media/vpn-gateway-about-vpngateways/demomulti.png "다중 사이트")

### 배포 모델 및 메서드

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## VNet 간

가상 네트워크를 다른 가상 네트워크에 연결(VNet-VNet)하는 것은 VNet을 온-프레미스 사이트 위치에 연결하는 것과 유사합니다. 두 연결 유형 모두 Azure VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공합니다. VNet 간 통신을 다중 사이트 구성과 통합할 수도 있습니다. 이렇게 하면 프레미스 간 연결을 가상 네트워크 간 연결과 결합하는 네트워크 토폴로지를 설정할 수 있습니다.

연결할 VNet의 상태는 다음과 같습니다.

- 동일하거나 다른 지역에 위치
- 동일하거나 다른 구독에 위치
- 동일하거나 다른 배포 모델



![VNet 간 연결](./media/vpn-gateway-about-vpngateways/demov2v.png "VNet 간")



### 배포 모델 간의 연결

Azure에는 현재 클래식 및 Resource Manager 등 두 개의 배포 모델이 있습니다. 일정 기간 동안 Azure를 사용한 경우 Azure VM 및 인스턴스 역할이 클래식 VNet에서 실행되고 있을 것입니다. 이후의 VM 및 역할 인스턴스는 Resource Manager에서 만들 VNet에서 실행되고 있을 것입니다. VNet 간의 연결을 만들어 어떤 VNet의 리소스가 다른 리소스와 서로 직접 통신하도록 할 수 있습니다.


### 배포 모델 및 메서드

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

### VNet 피어링

가상 네트워크 구성이 특정 요구 사항을 충족하면 VNet 피어링을 사용하여 연결을 만들 수 있습니다. VNet 피어링은 가상 네트워크 게이트웨이를 사용하지 않습니다. [VNet 피어링](../virtual-network/virtual-network-peering-overview.md)은 현재 미리 보기로 제공됩니다.



## 지점 및 사이트 간

지점 및 사이트 간(P2S) 구성을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 안전한 연결을 만들 수 있습니다. P2S는 SSTP를 통한 VPN 연결입니다(보안 소켓 터널링 프로토콜). P2S 연결을 작동하는 데는 VPN 장치 또는 공용 IP 주소가 필요하지 않습니다. 클라이언트 컴퓨터부터 시작하여 VPN 연결을 설정합니다. 집 또는 회의와 같은 원격 위치에서 VNet에 연결하려는 경우 또는 몇 명의 클라이언트만 VNet에 연결해야 하는 경우에 사용할 수 있는 유용한 솔루션입니다.


![지점 및 사이트 간 연결](./media/vpn-gateway-about-vpngateways/demop2s.png "지점 및 사이트 간")

### 배포 모델 및 메서드

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]


## Express 경로

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Express 경로에 대한 자세한 내용은 [Express 경로 기술 개요](../expressroute/expressroute-introduction.md)를 참조하세요.


## 사이트 간 및 Express 경로 공존 연결

Express 경로는 공용 인터넷을 사용하지 않고 WAN에서 Azure를 비롯한 Microsoft 서비스에 대한 직접 전용 연결입니다. 사이트 간 VPN 트래픽은 공용 인터넷을 통해 암호화되어 이동합니다. 동일한 가상 네트워크에 대한 사이트 간 VPN 및 Express 경로 연결을 구성할 수 있으면 여러 장점이 있습니다.

사이트 간 VPN을 Express 경로에 대한 보안 장애 조치(failover) 경로로 구성하거나 사이트 간 VPN을 사용하여 사용자 네트워크의 일부가 아니지만 Express 경로를 통해 연결된 사이트에 연결할 수 있습니다. 이 구성에는 동일한 가상 네트워크에 대한 두 개의 가상 네트워크 게이트웨이가 필요합니다. 하나는 -GatewayType Vpn을 사용하고 다른 하나는 -GatewayType Express 경로를 사용합니다.


![연결 공존](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")


### 배포 모델 및 메서드

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## 다음 단계

VPN 게이트웨이에 대한 자세한 내용은 [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md)를 참조하세요.

VNet에 온-프레미스 위치를 연결합니다. [사이트간 연결 만들기](vpn-gateway-howto-site-to-site-resource-manager-portal.md)를 참조하세요.





 

<!---HONumber=AcomDC_0831_2016-->