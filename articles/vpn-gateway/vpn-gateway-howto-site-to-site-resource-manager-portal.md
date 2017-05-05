---
title: "온-프레미스 네트워크를 Azure Virtual Network에 연결: 사이트 간 VPN: Portal | Microsoft Docs"
description: "공용 인터넷을 통해 온-프레미스 네트워크에서 Azure Virtual Network에 IPsec을 만드는 단계입니다. 이 단계는 포털을 사용하여 크로스-프레미스 사이트 간 VPN Gateway 연결을 만드는 데 도움이 됩니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: e839f99d599d682c31bd4c6d03a6a58268f05b7c
ms.lasthandoff: 04/25/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Azure Portal에서 사이트 간 연결 만들기

이 문서에서는 Azure Portal을 사용하여 온-프레미스 네트워크에서 VNet으로 사이트 간 VPN Gateway 연결을 만드는 방법을 보여줍니다. 이 문서의 단계는 Resource Manager 배포 모델에 적용됩니다. 다른 배포 도구 또는 배포 모델을 사용하는 경우 다음 목록에서 별도의 옵션을 선택하여 이 구성을 만들 수도 있습니다.

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [클래식 - Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [클래식 - 클래식 포털](vpn-gateway-site-to-site-create.md)
> 
>

![사이트 간 VPN Gateway 크로스-프레미스 연결 다이어그램](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

사이트 간 VPN Gateway 연결은 IPsec/IKE(IKEv1 또는 IKEv2) VPN 터널을 통해 온-프레미스 네트워크를 Azure Virtual Network에 연결하는 데 사용됩니다. 이 연결 유형은 할당된 외부 연결 공용 IP 주소를 갖고 있는 온-프레미스에 있는 VPN 장치를 필요로 합니다. VPN Gateway에 대한 자세한 내용은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* Resource Manager 배포 모델을 사용할 것인지 확인합니다. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* 호환되는 VPN 장치 및 구성할 수 있는 사람. 호환되는 VPN 장치 및 장치 구성에 대한 자세한 내용은 [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요.
* VPN 장치에 대한 외부 연결 공용 IPv4 IP 주소. 이 IP 주소는 NAT 뒤에 배치할 수 없습니다.
* 온-프레미스 네트워크에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다. 이 구성을 만들 때 Azure가 온-프레미스 위치에 라우팅할 IP 주소 범위 접두사를 지정해야 합니다. 온-프레미스 네트워크의 어떤 서브넷도 사용자가 연결하려는 가상 네트워크 서브넷과 중첩될 수 없습니다. 

### <a name="values"></a>예제 값
연습으로 이러한 단계를 사용하는 경우 다음 예제 값을 사용할 수 있습니다.

* **VNet 이름:** TestVNet1
* **주소 공간:** 
    * 10.11.0.0/16
    * 10.12.0.0/16(이 연습의 선택 사항)
* **서브넷:**
  * 프런트 엔드: 10.11.0.0/24
  * 백 엔드: 10.12.0.0/24(이 연습의 선택 사항)
  * GatewaySubnet: 10.11.255.0/27
* **리소스 그룹:** TestRG1
* **위치:** 미국 동부
* **DNS 서버:** DNS 서버의 IP 주소
* **가상 네트워크 게이트웨이 이름:** VNet1GW
* **공용 IP:** VNet1GWIP
* **VPN 유형:** 경로 기반
* **연결 형식:** 사이트 간(IPsec)
* **게이트웨이 유형:** VPN
* **로컬 네트워크 게이트웨이 이름:** Site2
* **연결 이름:** VNet1toSite2

## <a name="CreatVNet"></a>1. 가상 네트워크 만들기

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-s2s-rm-portal-include.md)]

## <a name="dns"></a>2. DNS 서버 지정
DNS는 사이트 간 연결에 필요하지 않습니다. 하지만 가상 네트워크에 배포된 리소스에 대한 이름을 확인하려는 경우 DNS 서버를 지정해야 합니다. 이 설정을 통해 이 가상 네트워크에 대한 이름을 확인하는 데 사용하려는 DNS 서버를 지정할 수 있습니다. DNS 서버를 만들지 않습니다.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>3. 게이트웨이 서브넷 만들기

가상 네트워크 게이트웨이는 VPN Gateway 서비스에서 사용하는 IP 주소가 포함된 게이트웨이 서브넷을 사용합니다. 게이트웨이 서브넷을 만들 때 이름을 'GatewaySubnet'으로 지정해야 합니다. 다른 이름을 지정하면 연결 구성이 실패합니다.

지정하는 게이트웨이 서브넷의 크기는 만들려는 VPN Gateway 구성에 따라 달라집니다. 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 /27 또는 /28을 선택하여 더 많은 주소를 포함하는 큰 서브넷을 만드는 것이 좋습니다. 지정하는 게이트웨이 서브넷의 크기는 만들려는 VPN Gateway 구성에 따라 달라집니다. 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 /27 또는 /28을 선택하여 더 많은 주소를 포함하는 큰 서브넷을 만드는 것이 좋습니다. 더 큰 게이트웨이 서브넷을 사용하면 향후 구성을 수용할 수 있을 만큼 충분한 IP 주소를 확보할 수 있습니다.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]


## <a name="VNetGateway"></a>4. VPN Gateway 만들기

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. 로컬 네트워크 게이트웨이 만들기

로컬 네트워크 게이트웨이는 일반적으로 온-프레미스 위치를 가리킵니다. Azure가 참조할 수 있는 사이트 이름을 지정한 다음 연결을 만들 온-프레미스 VPN 장치의 IP 주소를 지정합니다. 또한 VPN Gateway를 통해 VPN 장치로 라우팅될 IP 주소 접두사를 지정합니다. 사용자가 지정하는 주소 접두사는 온-프레미스 네트워크에 있는 접두사입니다. 온-프레미스 네트워크가 변경되면 이러한 접두사를 쉽게 업데이트할 수 있습니다.

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6. VPN 장치 구성
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

Azure Portal을 사용하여 VPN Gateway의 공용 IP 주소를 찾으려면 **가상 네트워크 게이트웨이**로 이동한 다음 게이트웨이의 이름을 클릭합니다.

## <a name="CreateConnection"></a>7. VPN 연결 만들기

가상 네트워크 게이트웨이와 온-프레미스 VPN 장치 사이의 사이트 간 VPN 연결을 만듭니다.

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8. VPN 연결 확인

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>다음 단계
*  연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)를 참조하세요.
*  BGP에 대한 내용은 [BGP 개요](vpn-gateway-bgp-overview.md) 및 [BGP를 구성하는 방법](vpn-gateway-bgp-resource-manager-ps.md)을 참조하세요.


