<properties 
   pageTitle="VPN 게이트웨이 연결 토폴로지 | Microsoft Azure"
   description="VPN 게이트웨이 연결 토폴로지 및 사용 가능한 구성 도구와 배포 모델을 봅니다."
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
   ms.date="07/19/2016"
   ms.author="cherylmc" />

# Azure VPN 게이트웨이 연결

이 문서는 초기 VPN 게이트웨이 연결 토폴로지를 보여줍니다. 그래픽 및 설명을 사용하여 요구 사항에 맞게 구성 토폴로지를 선택하도록 도울 수 있습니다. 이 문서에서 기본 초기 토폴로지를 살펴보는 동안 다이어그램을 지침으로 사용하여 더 복잡한 토폴로지를 작성할 수 있습니다.

각 토폴로지는 토폴로지를 사용할 수 있는 배포 모델을 나열한 테이블, 각 토폴로지를 구성하는 데 사용할 수 있는 배포 도구를 포함하며 문서를 사용할 수 있는 경우 문서에 직접 연결합니다. 테이블을 새 문서로 자주 업데이트하면 배포 도구를 사용할 수 있게 됩니다.

VPN 게이트웨이를 만드는 데 사용할 지침은 가상 네트워크를 만드는 데 사용되는 배포 모델에 따라 달라집니다. 예를 들어 클래식 배포 모델을 사용하여 VNet을 만든 경우 클래식 배포 모델에 대한 가이드 및 지침을 사용하여 VPN 게이트웨이를 만들고 구성합니다. 클래식 배포 모델 가상 네트워크에 리소스 관리자 VPN 게이트웨이를 만들 수 없습니다. 배포 모델에 대한 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 모델 이해](../resource-manager-deployment-model.md)를 참조하세요.

## 사이트 간 및 다중 사이트

사이트 간 연결은 IPsec/IKE(IKEv1 또는 IKEv2) VPN 터널을 통한 연결입니다. 이 연결 유형은 VPN 장치 또는 Windows Server RRAS 온-프레미스가 필요합니다. 사이트간 연결은 프레미스 간 및 하이브리드 구성에 사용될 수 있습니다.


**S2S 다이어그램**

![S2S 연결](./media/vpn-gateway-topology/site2site.png "사이트 간")

Azure 경로 기반 VPN을 사용하는 경우 온-프레미스 네트워크에 둘 이상의 S2S VPN 연결을 만들고 구성할 수 있습니다. 이러한 유형의 구성은 "다중 사이트" 연결이라고 합니다.
 

**다중 사이트 다이어그램**

![다중 사이트 연결](./media/vpn-gateway-topology/multisite.png "다중 사이트")


**사용 가능한 배포 모델 및 메서드**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## VNet 간

가상 네트워크를 다른 가상 네트워크에 연결(VNet-VNet)하는 것은 VNet을 온-프레미스 사이트 위치에 연결하는 것과 매우 유사합니다. 두 연결 유형 모두 Azure VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공합니다. 연결한 VNet은 서로 다른 지역이나 서로 다른 구독에 있을 수 있습니다. VNet 간 통신을 다중 사이트 구성과 통합할 수도 있습니다. 이렇게 하면 프레미스 간 연결을 가상 네트워크 간 연결과 결합하는 네트워크 토폴로지를 설정할 수 있습니다.

Azure에는 현재 Azure 서비스 관리자(클래식이라고 함) 및 Azure 리소스 관리자의 두 가지 배포 모델이 있습니다. 한동안 Azure를 사용했다면 새로운 VM 및 역할 인스턴스가 ARM에서 만든 VNet에서 실행될 수 있는 반면 Azure VM 및 클래식 VNet에서 실행 중인 인스턴스 역할이 있을 가능성이 큽니다. 다른 배포 모델에 있더라도 가상 네트워크 간의 연결을 만들 수 있습니다.


**VNet2VNet 다이어그램**

![VNet 간 연결](./media/vpn-gateway-topology/vnet2vnet.png "VNet 간")


**사용 가능한 배포 모델 및 메서드**

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]



## 사이트 간 및 Express 경로 공존 연결

Express 경로는 공용 인터넷을 사용하지 않고 WAN에서 Azure를 비롯한 Microsoft 서비스에 대한 직접 전용 연결입니다. 사이트 간 VPN 트래픽은 공용 인터넷을 통해 암호화되어 이동합니다. 동일한 가상 네트워크에 대한 사이트 간 VPN 및 Express 경로 연결을 구성하는 기능이 있으면 여러 장점이 있습니다. 사이트 간 VPN을 Exress 경로에 대한 보안 장애 조치(failover) 경로로 구성하거나 사이트 간 VPN을 사용하여 사용자 네트워크의 일부가 아니지만 Express 경로를 통해 연결된 사이트에 연결할 수 있습니다.


**공존 연결 다이어그램**

![연결 공존](./media/vpn-gateway-topology/expressroutes2s.png "expressroute-site2site")


**사용 가능한 배포 모델 및 메서드**

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## 지점 및 사이트 간

지점 및 사이트 간 구성을 사용하면 개별적으로 클라이언트 컴퓨터에서 가상 네트워크에 안전한 연결을 만들 수 있습니다. 클라이언트 컴퓨터에서 연결을 시작하여 VPN 연결을 설정합니다. 집 또는 회의와 같은 원격 위치에서 VNet에 연결하려는 경우 또는 몇 명의 클라이언트만 가상 네트워크에 연결해야 하는 경우에 사용할 수 있는 유용한 솔루션입니다.

지점 및 사이트 간 연결은 SSTP를 통한 VPN 연결(보안 소켓 터널링 프로토콜)입니다. 지점 및 사이트 간 연결을 작동하는 데는 VPN 장치 또는 공용 IP 주소가 필요하지 않습니다.

**P2S 다이어그램**

![지점 및 사이트 간 연결](./media/vpn-gateway-topology/point2site.png "지점 및 사이트 간")

**사용 가능한 배포 모델 및 메서드**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## 다음 단계

연결을 계획하고 설계하는 과정을 진행하기 전에 VPN 게이트웨이를 이해하기 위해 [VPN 게이트웨이 정보](vpn-gateway-about-vpngateways.md) 및 [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md) 문서에 있는 항목에 익숙해지려고 합니다.





 

<!---HONumber=AcomDC_0720_2016-->