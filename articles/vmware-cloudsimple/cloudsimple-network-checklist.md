---
title: CloudSimple-네트워크 검사 목록의 Azure VMware 솔루션
description: CloudSimple로 Azure VMware 솔루션에서 네트워크 CIDR을 할당 하기 위한 검사 목록
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c31942b198697ddc913f3732fd41409334ff0ef6
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817476"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>CloudSimple의 Azure VMware 솔루션에 대 한 네트워킹 필수 조건

CloudSimple의 azure VMware 솔루션은 Azure 리소스 뿐만 아니라 엔터프라이즈 관리 장치에서 온-프레미스 환경의 사용자와 응용 프로그램에 액세스할 수 있는 VMware 사설 클라우드 환경을 제공 합니다. 연결은 Vpn 및 Express 경로 연결과 같은 네트워킹 서비스를 사용 하 여 전달 됩니다.  일부 Network Services에서는 서비스를 사용 하도록 설정 하는 데 필요한 네트워크 주소 범위를 지정 해야 합니다.  이 문서의 표에서는 지정 된 주소를 사용 하는 주소 범위 및 해당 서비스 집합을 설명 합니다.  일부 주소는 필수 이며 일부는 배포 하려는 서비스에 따라 달라 집니다.  이러한 주소 공간은 온-프레미스 서브넷, Azure Virtual Network 서브넷 또는 계획 된 CloudSimple 워크 로드 서브넷과 겹칠 수 없습니다.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>사설 클라우드를 만드는 데 필요한 네트워크 주소 범위

CloudSimple 서비스와 사설 클라우드를 만드는 동안 다음 네트워크 CIDR 범위가 필요 합니다.

| 이름/사용 되는     | 설명                                                                                                                            | 주소 범위            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| 게이트웨이 CIDR      | Edge 서비스 (VPN gateway)에 필요 합니다.  이 CIDR은 CloudSimple 서비스를 만드는 동안 필요 하며 RFC 1918 공간에 있어야 합니다. | /28                      |
| vSphere/Vsphere CIDR | VMware 관리 네트워크에 필요 합니다. 사설 클라우드를 만드는 동안이 CIDR을 지정 해야 합니다.                                    | /24 또는/23 또는/22 또는/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-on-premises-network"></a>온-프레미스 네트워크에 대 한 Azure 네트워크 연결에 필요한 네트워크 주소 범위

Express 경로를 [사용 하 여 온-프레미스 네트워크에서 사설 클라우드 네트워크로](on-premises-connection.md) 연결 하면 Global Reach 연결이 설정 됩니다.  연결은 온-프레미스 네트워크, 사설 클라우드 네트워크 및 Azure 네트워크 간에 BGP를 통해 경로를 교환 합니다.

| 이름/사용 되는             | 설명                                                                                                                                                                             | 주소 범위 |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| Express 경로 피어 링 CIDR | Express 경로 Global Reach를 사용 하는 경우 필수-온-프레미스 연결에 사용 됩니다. 지원 티켓을 통해 Global Reach 연결 요청을 수행 하는 경우이 CIDR을 제공 해야 합니다. | /29           |

## <a name="network-address-range-required-for-using-site-to-site-vpn-connection-to-on-premises-network"></a>온-프레미스 네트워크에 대 한 사이트 간 VPN 연결을 사용 하는 데 필요한 네트워크 주소 범위

사이트 간 [VPN을 사용 하 여 온-프레미스 네트워크에서 사설 클라우드 네트워크로](vpn-gateway.md) 연결 하려면 다음 IP 주소, 온-프레미스 네트워크 및 식별자가 필요 합니다. 

| 주소/주소 범위 | 설명                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 피어 IP               | 온-프레미스 VPN gateway 공용 IP 주소입니다. 온-프레미스 데이터 센터와 CloudSimple 서비스 지역 간에 사이트 간 VPN 연결을 설정 하는 데 필요 합니다. 이 IP는 사이트 간 VPN gateway를 만드는 동안 필요 합니다.                                         |
| 피어 식별자       | 온-프레미스 VPN 게이트웨이의 피어 식별자입니다. 이는 일반적으로 **피어 IP**와 동일 합니다.  온-프레미스 VPN gateway에 고유 식별자가 지정 된 경우에는 식별자를 지정 해야 합니다.  사이트 간 VPN Gateway를 만드는 동안에는 피어 ID가 필요 합니다.   |
| 온-프레미스 네트워크   | 지역에서 CloudSimple 네트워크에 액세스 해야 하는 온-프레미스 접두사입니다.  사용자가 액세스할 클라이언트 네트워크를 포함 하 여 CloudSimple 네트워크에 액세스 하는 온-프레미스 네트워크의 모든 접두사를 포함 합니다.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>지점 및 사이트 간 VPN 연결을 사용 하는 데 필요한 네트워크 주소 범위

지점 및 사이트 간 VPN 연결을 통해 클라이언트 컴퓨터에서 CloudSimple 네트워크에 액세스할 수 있습니다.  [지점 및 사이트 간 VPN을 설정](vpn-gateway.md) 하려면 다음 네트워크 주소 범위를 지정 해야 합니다.

| 주소/주소 범위 | 설명                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 클라이언트 서브넷         | 지점 및 사이트 간 VPN을 사용 하 여 연결 하면 클라이언트 서브넷에서 DHCP 주소가 제공 됩니다. 이 서브넷은 CloudSimple 포털에서 지점 및 사이트 간 VPN gateway를 만드는 데 필요 합니다.  네트워크는 두 개의 서브넷으로 나뉩니다. 하나는 UDP 연결 및 TCP 연결에 사용 됩니다. |

## <a name="next-steps"></a>다음 단계

* [빠른 시작-CloudSimple service 만들기](quickstart-create-cloudsimple-service.md)
* [빠른 시작-사설-클라우드 만들기](quickstart-create-private-cloud.md)
* [Azure 네트워크 연결](cloudsimple-azure-network-connection.md) 에 대 한 자세한 정보
* [VPN](cloudsimple-vpn-gateways.md) gateway에 대 한 자세한 정보
