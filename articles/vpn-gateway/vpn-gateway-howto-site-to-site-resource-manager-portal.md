---
title: "크로스-프레미스 사이트 간 Azure VPN 연결 구성: 포털 | Microsoft Docs"
description: "Resource Manager 배포 모델을 사용하여 VNet을 만들고 S2S VPN 게이트웨이 연결을 사용하여 로컬 온-프레미스 네트워크에 연결하는 방법입니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0258e1093926e6239650a8b1ecbb7c7a778616c6
ms.openlocfilehash: d9af99dc3b24315d3876228dc926d433c4b692e9


---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Azure 포털을 사용하여 사이트 간 연결로 VNet 만들기
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [클래식 - 클래식 포털](vpn-gateway-site-to-site-create.md)
> 
> 

이 문서에서는 Azure Resource Manager 배포 모델 및 Azure 포털을 사용하여 온-프레미스 네트워크에 대한 가상 네트워크와 사이트 간 VPN 게이트웨이 연결을 만드는 과정을 안내합니다. 사이트간 연결은 프레미스 간 및 하이브리드 구성에 사용될 수 있습니다.

![다이어그램](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>사이트 간 연결에 대한 배포 모델 및 메서드
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

아래 표에서는 현재 사용할 수 있는 배포 모델 및 사이트 간 구성을 위한 메서드를 보여 줍니다. 구성 단계를 포함한 문서를 사용할 수 있는 경우 아래 표에서 관련 링크를 직접 제공합니다.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>추가 구성
VNet을 서로 연결하되 온-프레미스 위치에는 연결하지 않으려는 경우 [VNet간 연결 구성](vpn-gateway-vnet-vnet-rm-ps.md)을 참조하세요. 이미 연결되어 있는 VNet에 사이트 간 연결을 추가하려는 경우 [기존 VPN 게이트웨이와 연결된 VNet에 S2S 연결 추가](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에
구성을 시작하기 전에 다음 항목이 있는지 확인합니다.

* 호환되는 VPN 장치 및 구성할 수 있는 사람. [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요. VPN 장치를 구성하는 방법과 온-프레미스 네트워크 구성에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다.
* VPN 장치에 대한 외부 연결 공용 IP 주소. 이 IP 주소는 NAT 뒤에 배치할 수 없습니다.
* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](http://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

### <a name="a-namevaluesasample-configuration-values-for-this-exercise"></a><a name="values"></a>이 연습에 대한 샘플 구성 값
연습으로 이러한 단계를 사용하는 경우 샘플 구성 값을 사용할 수 있습니다.

* **VNet 이름:** TestVNet1
* **주소 공간:** 10.11.0.0/16 및 10.12.0.0/16
* **서브넷:**
  * 프런트 엔드: 10.11.0.0/24
  * 백 엔드: 10.12.0.0/24
  * 게이트웨이 서브넷 = 10.12.255.0/27
* **리소스 그룹:** TestRG1
* **위치:** 미국 동부
* **DNS 서버:** 8.8.8.8
* **게이트웨이 이름:** VNet1GW
* **공용 IP:** VNet1GWIP
* **VPN 유형:** 경로 기반
* **연결 형식:** 사이트 간(IPsec)
* **게이트웨이 유형:** VPN
* **로컬 네트워크 게이트웨이 이름:** Site2
* **연결 이름:** VNet1toSite2

## <a name="a-namecreatvneta1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. 가상 네트워크 만들기
VNet이 이미 있는 경우 설정이 VPN 게이트웨이 설계와 호환되는지 확인합니다. 다른 네트워크와 겹칠 수 있는 서브넷에 특히 주의합니다. 겹치는 서브넷에 있으면 연결이 제대로 작동하지 않습니다. VNet이 올바른 설정으로 구성되었다면 [DNS 서버 지정](#dns) 섹션의 단계를 시작할 수 있습니다.

### <a name="to-create-a-virtual-network"></a>가상 네트워크를 만들려면
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="a-namesubnetsa2-add-additional-address-space-and-subnets"></a><a name="subnets"></a>2. 다른 주소 공간 및 서브넷 추가
VNet이 만들어지면 여기에 다른 주소 공간 및 서브넷을 추가할 수 있습니다.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="a-namednsa3-specify-a-dns-server"></a><a name="dns"></a>3. DNS 서버 지정
### <a name="to-specify-a-dns-server"></a>DNS 서버를 지정하려면
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namegatewaysubneta4-create-a-gateway-subnet"></a><a name="gatewaysubnet"></a>4. 게이트웨이 서브넷 만들기
가상 네트워크를 게이트웨이에 연결하기 전에 먼저 연결하려는 가상 네트워크에 대한 게이트웨이 서브넷을 만들어야 합니다. 향후 추가적인 구성 요구 사항을 수용하기에 충분한 IP 주소를 제공하기 위하여 가능하면 /28 또는 /27 CIDR 블록을 사용하여 게이트웨이 서브넷을 만드는 것이 가장 좋습니다.

연습으로 이 구성을 만드는 경우 게이트웨이 서브넷을 만들 때 이러한 [값](#values) 을 참조합니다.

### <a name="to-create-a-gateway-subnet"></a>게이트웨이 서브넷을 만들려면
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="a-namevnetgatewaya5-create-a-virtual-network-gateway"></a><a name="VNetGateway"></a>5. 가상 네트워크 게이트웨이 만들기
연습으로 이 구성을 만드는 경우 [샘플 구성 값](#values)을 참조하세요.

### <a name="to-create-a-virtual-network-gateway"></a>가상 네트워크 게이트웨이를 만들려면
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namelocalnetworkgatewaya6-create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>6. 로컬 네트워크 게이트웨이 만들기
'로컬 네트워크 게이트웨이'는 온-프레미스 위치를 말합니다. Azure에서 참조할 수 있는 이름을 로컬 네트워크 게이트웨이에 지정합니다. 

연습으로 이 구성을 만드는 경우 [샘플 구성 값](#values)을 참조하세요.

### <a name="to-create-a-local-network-gateway"></a>로컬 네트워크 게이트웨이를 만들려면
[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="a-namevpndevicea7-configure-your-vpn-device"></a><a name="VPNDevice"></a>7. VPN 장치 구성
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="a-namecreateconnectiona8-create-a-site-to-site-vpn-connection"></a><a name="CreateConnection"></a>8. 사이트 간 VPN 연결 만들기
가상 네트워크 게이트웨이와 VPN 장치 사이에 사이트 간 VPN 연결을 만듭니다. 사용자 고유의 값으로 대체해야 합니다. 공유 키는 VPN 장치 구성에 사용한 값과 일치해야 합니다. 

이 섹션을 시작하기 전에 가상 네트워크 게이트웨이 및 로컬 네트워크 게이트웨이를 다 만들었는지 확인합니다. 연습으로 이 구성을 만드는 경우 연결을 만들 때 이러한 [값](#values) 을 참조합니다.

### <a name="to-create-the-vpn-connection"></a>VPN 연결을 만들려면
[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="a-nameverifyconnectiona9-verify-the-vpn-connection"></a><a name="VerifyConnection"></a>9. VPN 연결 확인
포털에서 또는 PowerShell을 사용하여 VPN 연결을 확인할 수 있습니다.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>다음 단계
*  연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)를 참조하세요.
*  BGP에 대한 내용은 [BGP 개요](vpn-gateway-bgp-overview.md) 및 [BGP를 구성하는 방법](vpn-gateway-bgp-resource-manager-ps.md)을 참조하세요.




<!--HONumber=Jan17_HO4-->


