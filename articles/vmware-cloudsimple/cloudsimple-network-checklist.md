---
title: CloudSimple-네트워크 검사 목록의 Azure VMware 솔루션
description: CloudSimple로 Azure VMware 솔루션에서 네트워크 CIDR을 할당하기 위한 검사 목록
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "77025013"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple에 대한 네트워킹 사전 요구 사항

Azure VMware Solution by CloudSimple은 온-프레미스 환경, 기업 관리 디바이스 또는 Azure 리소스의 사용자와 애플리케이션에 액세스할 수 있는 VMware 프라이빗 클라우드 환경을 제공합니다. Azure ExpressRoute 및 VPN 연결과 같은 네트워킹 서비스를 통해 연결됩니다. 이러한 네트워킹 서비스 중 일부를 사용하려면 서비스를 사용하도록 설정하는데 필요한 네트워크 주소 범위를 지정해야 합니다. 

이 문서의 표에서는 지정된 주소를 사용하는 주소 범위 및 해당 서비스 집합을 설명합니다. 일부 주소는 필수이며 일부는 배포하려는 서비스에 따라 달라집니다. 이러한 주소 공간은 온-프레미스 서브넷, Azure 가상 네트워크 서브넷 또는 계획된 CloudSimple 워크로드 서브넷과 겹칠 수 없습니다.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>프라이빗 클라우드를 만드는 데 필요한 네트워크 주소 범위

CloudSimple 서비스와 프라이빗 클라우드를 만드는 동안 다음과 같이 지정된 네트워크 CIDR(도메인 간 라우팅) 범위를 준수해야 합니다.

| 이름/사용 목적     | 설명                                                                                                                            | 주소 범위            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| 게이트웨이 CIDR      | Edge 서비스(VPN 게이트웨이)에 필요합니다.  이 CIDR은 CloudSimple 서비스를 만드는 동안 필요하며 RFC 1918 공간에 있어야 합니다. | /28                      |
| vSphere/Vsphere CIDR | VMware 관리 네트워크에 필요합니다. 프라이빗 클라우드를 만드는 동안 이 CIDR을 지정해야 합니다.                                    | /24 또는/23 또는/22 또는/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>온-프레미스 네트워크에 대한 Azure 네트워크 연결에 필요한 네트워크 주소 범위

Express 경로를 [통해 온-프레미스 네트워크에서 프라이빗 클라우드 네트워크로](on-premises-connection.md) 연결하면 Global Reach 연결이 설정됩니다.  연결은 BGP (Border Gateway Protocol)를 사용하여 온-프레미스 네트워크, 프라이빗 클라우드 네트워크 및 Azure 네트워크 간에 경로를 교환합니다.

| 이름/사용 목적             | 설명                                                                                                                                                                             | 주소 범위 |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute 피어링 CIDR | 온-프레미스 연결에 대해 ExpressRoute Global Reach를 사용하는 경우 필요합니다. 지원 티켓을 통해 Global Reach 연결 요청을 수행하는 경우 이 CIDR을 제공해야 합니다. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>온-프레미스 네트워크에 대한 사이트 간 VPN 연결을 사용하는 데 필요한 네트워크 주소 범위

[사이트 간 VPN을 사용하여 온-프레미스 네트워크에서 프라이빗 클라우드 네트워크](vpn-gateway.md)로 연결하려면 다음 IP 주소, 온-프레미스 네트워크 및 식별자가 필요합니다. 

| 주소/주소 범위 | 설명                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 피어 IP               | 온 프레미스 VPN 게이트웨이 공용 IP 주소. 온-프레미스 데이터 센터와 CloudSimple 서비스 지역 간에 사이트 간 VPN 연결을 설정하는데 필요합니다. 이 IP 주소는 사이트 간 VPN 게이트웨이를 만드는 동안 필요합니다.                                         |
| 피어 식별자       | 온-프레미스 VPN 게이트웨이의 피어 식별자입니다. 이는 일반적으로 **피어 IP** 와 동일합니다.  온-프레미스 VPN 게이트웨이에서 고유 식별자가 지정된 경우에는 식별자를 지정해야 합니다.  피어 ID는 사이트 간 VPN 게이트웨이를 만드는 동안 필요합니다.   |
| 온-프레미스 네트워크   | 지역에서 CloudSimple 네트워크에 액세스해야 하는 온-프레미스 접두사입니다.  사용자가 네트워크에 액세스하는 클라이언트 네트워크를 포함하여 CloudSimple 네트워크에 액세스하는 온-프레미스 네트워크의 모든 접두사를 포함합니다.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>지점 및 사이트 간 VPN 연결을 사용하는데 필요한 네트워크 주소 범위

지점 및 사이트 간 VPN 연결을 사용하면 클라이언트 컴퓨터에서 CloudSimple 네트워크에 액세스할 수 있습니다.  [지점 및 사이트 간 VPN을 설정하려면](vpn-gateway.md)다음 네트워크 주소 범위를 지정해야 합니다.

| 주소/주소 범위 | 설명                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 클라이언트 서브넷         | DHCP 주소는 지점 및 사이트 간 VPN을 사용하여 연결할 때 클라이언트 서브넷에서 제공됩니다. 이 서브넷은 CloudSimple 포털에서 지점 및 사이트 간 VPN 게이트웨이를 만드는 동안 필요합니다.  네트워크는 두 개의 서브넷으로 나뉩니다. 하나는 UDP 연결을 위한 것이 고 다른 하나는 TCP 연결용입니다. |

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드에 액세스하기 위한 온-프레미스 방화벽 설정](on-premises-firewall-configuration.md)
* [빠른 시작 - CloudSimple 서비스 만들기](quickstart-create-cloudsimple-service.md)
* [빠른 시작 - 프라이빗 클라우드 구성](quickstart-create-private-cloud.md)
* [Azure 네트워크 연결](cloudsimple-azure-network-connection.md)에 대해 자세히 알아봅시다
* [VPN 게이트웨이](cloudsimple-vpn-gateways.md)에 대해 자세히 알아봅시다
