<properties 
   pageTitle="VPN 게이트웨이 계획 및 설계 | Microsoft Azure"
   description="크로스-프레미스, 하이브리드, VNet 간 연결에 대한 VPN 게이트웨이 계획 및 설계에 대해 알아보세요."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2016"
   ms.author="cherylmc"/>

# VPN 게이트웨이 계획 및 설계

크로스 프레미스 및 VNet-VNet 연결을 계획 및 설계할 경우 네트워킹 요구 사항에 따라 연결이 매우 단순하거나 복잡해질 수 있습니다. 이 문서에서는 계획 및 설계 시 기본적으로 고려해야 할 사항을 안내합니다.

## 계획


### <a name="compare"></a>프레미스 간 연결 옵션

온-프레미스 사이트를 가상 네트워크에 안전하게 연결하려면 사이트 간, 지점 및 사이트 간, Express 경로의 세 가지 방법을 사용할 수 있습니다. 사용 가능한 여러 크로스-프레미스 연결을 비교하십시오. 선택하는 옵션은 다음과 같은 다양한 고려 사항에 따라 달라질 수 있습니다.


- 솔루션에 어떤 종류의 처리량이 필요한가요?
- 보안 VPN을 통한 공용 인터넷을 통해 통신하시겠어요? 아니면 개인 연결을 통해 통신하시겠어요?
- 사용할 수 있는 공용 IP 주소가 있나요?
- VPN 장치를 사용할 계획인가요? 그렇다면 해당 장치가 호환되나요?
- 컴퓨터 몇 대만 연결하시겠어요? 아니면 사이트에 대한 영구 연결을 원하세요?
- 만들려는 솔루션에 어떤 형식의 VPN 게이트웨이가 필요한가요?
- 어떤 게이트웨이 SKU를 사용해야 합니까?


다음 테이블은 솔루션에 대한 최상의 연결 옵션을 결정하는 데 도움이 될 수 있습니다.


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>VPN 유형 및 SKU별 게이트웨이 요구 사항


VPN 게이트웨이 만들 때 사용하려는 게이트웨이 SKU를 지정해야 합니다. 세 가지 VPN 게이트웨이 SKU가 있습니다.

- Basic
- Standard
- 고성능

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]



### <a name="aggthroughput"></a>게이트웨이 유형 및 예상 총 처리량

다음 테이블에서는 게이트웨이 형식과 예상 총 처리량을 보여 줍니다. 예상되는 총 처리량은 설계의 결정적 요인이 될 수 있습니다. 가격은 게이트웨이 SKU마다 다릅니다. 가격에 대한 자세한 내용은 [VPN 게이트웨이 가격](https://azure.microsoft.com/pricing/details/vpn-gateway/)을 참조하세요. 이 표는 리소스 관리자 배포 모델과 클래식 배포 모델 모두에 적용됩니다.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]



### <a name="wf"></a>워크플로

다음은 클라우드 연결의 공통 워크플로를 요약한 목록입니다.

1.	연결 토폴로지를 설계 및 계획하고 연결하려는 모든 네트워크의 주소 공간을 나열합니다.
2.	Azure 가상 네트워크를 만듭니다.
3.	가상 네트워크의 VPN 게이트웨이를 만듭니다.
4.	필요에 따라 온-프레미스 네트워크 또는 기타 가상 네트워크에 대한 연결을 만들고 구성합니다.
5.	필요에 따라 Azure VPN 게이트웨이에 대한 지점 및 사이트 간 연결을 만들고 구성합니다.
 

## 디자인

### <a name="topologies"></a>연결 토폴로지

먼저 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md) 문서의 다이어그램을 확인하세요. 이 문서는 기본 다이어그램, 각 토폴로지(리소스 관리자 또는 클래식)의 배포 모델, 구성을 배포하는 데 사용할 수 있는 배포 도구에 대해 설명합니다.

### <a name="designbasics"></a>디자인 기본 사항

다음 섹션은 VPN 게이트웨이 기본 사항에 대해 설명합니다. 또한 [네트워킹 서비스 제한 사항](../articles/azure-subscription-service-limits.md#networking-limits)을 고려하세요.


#### <a name="subnets"></a>서브넷 정보

연결을 만들 때 서브넷 범위를 고려해야 합니다. 서브넷 주소 범위가 겹치면 안 됩니다. 서브넷 중복이란 하나의 가상 네트워크 또는 온-프레미스 위치에 다른 위치에 포함된 것과 동일한 주소 공간이 포함된 것을 의미합니다. 이 경우 로컬 온-프레미스 네트워크의 네트워크 엔지니어가 Azure IP 주소 지정 공간/서브넷에 사용할 범위를 만들어야 합니다. 로컬 온-프레미스 네트워크에서 사용되고 있지 않은 주소 공간이 필요합니다.

또한 VNet 간 연결을 사용할 때는 서브넷이 중복되지 않도록 하는 것이 중요합니다. 서브넷이 중복되고 보내는 VNet과 대상 VNet에 같은 IP 주소가 있을 경우 VNet 간 연결을 만들 수 없습니다. 대상 주소가 보내는 VNet의 일부이므로 Azure에서 다른 VNet으로 데이터를 라우팅할 수 없습니다.

VPN 게이트웨이에는 게이트웨이 서브넷이라는 특정 서브넷이 필요합니다. 모든 게이트웨이 서브넷이 제대로 작동하려면 이름을 GatewaySubnet으로 지정해야 합니다. 게이트웨이 서브넷에 다른 이름을 지정하지 않도록 해야 하며 VM 등을 게이트웨이 서브넷에 배포하지 마십시오. [게이트웨이 서브넷](vpn-gateway-about-vpn-gateway-settings.md#gwsub)을 참조하세요.

#### <a name="local"></a>로컬 네트워크 게이트웨이 정보

로컬 네트워크 게이트웨이는 일반적으로 온-프레미스 위치를 가리킵니다. 클래식 배포 모델에서 로컬 네트워크 게이트웨이는 로컬 네트워크 사이트라고 했습니다. 로컬 네트워크 게이트웨이를 구성할 때 이름을 지정하고, 온-프레미스 VPN 장치의 공용 IP 주소를 지정하고, 온-프레미스 위치에 있는 주소 접두사를 지정합니다. Azure는 네트워크 트래픽에 대상 주소 접두사를 보고 로컬 네트워크 게이트웨이에 대해 지정한 구성을 참조하며 그에 따라 패킷을 라우팅합니다. 필요에 따라 이러한 주소 접두사를 수정할 수 있습니다. 자세한 내용은 [로컬 네트워크 게이트웨이](vpn-gateway-about-vpn-gateway-settings.md#lng)를 참조하세요.


#### <a name="gwtype"></a>게이트웨이 형식 정보

토폴로지에 정확한 게이트웨이 유형을 선택하는 것이 중요합니다. 잘못된 형식을 선택할 경우 게이트웨이가 정상적으로 작동하지 않습니다. 게이트웨이 유형은 게이트웨이 자체를 연결하는 방법을 지정하며 리소스 관리자 배포 모델에 대한 필수 구성 설정입니다.

게이트웨이 유형은 다음과 같습니다.

- Vpn
- Express 경로

#### <a name="connectiontype"></a>연결 형식 정보

각 구성이 작동하려면 특정 연결 형식이 필요합니다. 연결 형식은 다음과 같습니다.

- IPsec
- Vnet2Vnet
- Express 경로
- VPNClient


#### <a name="vpntype"></a>VPN 형식 정보

각 구성에는 특정 VPN 형식이 필요합니다. 동일한 VNet에 사이트 간 연결 및 지점 및 사이트 간 연결을 만드는 것과 같은 두 가지 구성을 결합하는 경우 두 연결 요구 사항을 충족하는 VPN 유형을 사용해야 합니다.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

다음 테이블에는 각 연결 구성에 해당하는 VPN 형식이 정리되어 있습니다. 게이트웨이의 VPN 유형이 만들려는 구성과 일치하는지 확인하십시오.


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>사이트 간 연결에 대한 VPN 장치

사이트 간 연결을 구성하려면 배포 모델과 상관없이 다음 항목이 필요합니다.

- Azure VPN 게이트웨이와 호환되는 VPN 장치
- NAT 뒤에 있지 않은 공용 IPv4 IP 주소

VPN 장치를 구성해본 경험이 필요하거나 누군가가 장치를 대신 구성해줄 수 있어야 합니다. VPN 장치에 대한 자세한 내용은 [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요. VPN 장치 문서에는 검증된 장치, 검증되지 않은 장치의 요구 사항, 장치 구성 문서에 대한 링크(사용 가능한 경우)가 포함되어 있습니다.

### <a name="forcedtunnel"></a>강제 터널 라우팅 고려

대부분의 구성에서 강제 터널링을 구성할 수 있습니다. 강제 터널링을 사용하면 검사 및 감사에 대한 사이트 간 VPN 터널을 통해 모든 인터넷 바인딩된 트래픽을 온-프레미스 위치에 다시 리디렉션하거나 "force"할 수 있습니다. 대부분의 엔터프라이즈 IT 정책에 있어서 중요한 보안 요구 사항입니다.

강제 터널링 없이 Azure의 VM에서 인터넷 바인딩된 트래픽은 항상 트래픽을 검사 또는 감사하도록 허용하는 옵션 없이 Azure 네트워크 인프라에서 직접 인터넷으로 트래버스합니다. 인증되지 않은 인터넷 액세스는 잠재적으로 정보 공개 또는 다른 유형의 보안 위반을 발생시킬 수 있습니다.

**강제 터널링 다이어그램**

![강제 터널링 연결](./media/vpn-gateway-plan-design/forced-tunnel.png "강제 터널링")

두 배포 모델에서 다양한 도구를 사용하여 강제 터널링 연결을 구성할 수 있습니다. 자세한 내용은 다음 테이블을 참조하세요. 이 구성에 사용할 수 있게 된 새 문서, 새로운 배포 모델 및 추가 도구로 이 표를 업데이트합니다. 문서를 사용할 수 있는 경우 표에서 직접 링크를 제공합니다.

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]



## 다음 단계

설계에 도움이 되는 정보를 보려면 [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md) 및 [VPN 게이트웨이 정보](vpn-gateway-about-vpngateways.md) 문서를 참조하세요.

특정 게이트웨이 설정에 대한 자세한 내용은 [VPN 게이트웨이 설정 정보](vpn-gateway-about-vpn-gateway-settings.md)를 참조하세요.

<!---HONumber=AcomDC_0831_2016-->