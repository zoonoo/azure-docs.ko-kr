---
title: '가상 허브의 효과적인 경로 보기: Azure 가상 WAN | 마이크로 소프트 문서'
description: Azure 가상 WAN의 가상 허브에 대한 효과적인 경로
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515851"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>가상 허브의 효과적인 경로 보기

Azure 포털에서 가상 WAN 허브의 모든 경로를 볼 수 있습니다. 경로를 보려면 가상 허브로 이동한 다음 **유효 경로 보기 -> 라우팅을**선택합니다.

## <a name="understanding-routes"></a><a name="understand"></a>경로 이해

다음 예제에서는 가상 WAN 라우팅이 표시되는 방식을 더 잘 이해하는 데 도움이 될 수 있습니다.

이 예제에서는 세 개의 허브가 있는 가상 WAN이 있습니다. 첫 번째 허브는 미국 동부 지역에, 두 번째 허브는 서유럽 지역에, 세 번째 허브는 미국 서부 지역에 있습니다. 가상 WAN에서 모든 허브가 상호 연결됩니다. 이 예제에서는 동미 및 서유럽 허브에 온-프레미스 지점(스포크) 및 Azure 가상 네트워크(스포크)의 연결이 있다고 가정합니다.

네트워크 가상 어플라이언스(10.4.0.6)가 있는 Azure VNet 스포크(10.4.0.0/16)는 VNet(10.5.0.0/16)을 더 피어로 만듭니다. 허브 경로 테이블에 대한 자세한 내용은 이 문서의 [추가 정보를](#abouthubroute) 참조하십시오.

이 예제에서는 서유럽 분기 1이 미국 동부 허브와 서유럽 허브에 연결되어 있다고 가정합니다. 미국 동부의 익스프레스루트 회로는 브랜치 2를 미국 동부 허브와 연결합니다.

![다이어그램](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>효과적인 경로 보기

포털에서 '유효 경로 보기'를 선택하면 미국 동부 허브의 [허브 경로 테이블에](#routetable) 표시된 출력이 생성됩니다.

이러한 관점에서 볼 때, 첫 번째 줄은 VPN *Next 홉 유형* 연결('Next hop' VPN 게이트웨이 인스턴스0 IP 10.1.0.6, Instance1 IP 10.1.0.7)으로 인해 미국 동부 허브가 10.20.1.0/24(분기 1)의 경로를 학습했다는 것을 의미합니다. *경로 원점은* 리소스 ID를 가리킵니다. *AS 경로는* 분기 1에 대한 AS 경로를 나타냅니다.

### <a name="hub-route-table"></a><a name="routetable"></a>허브 경로 테이블

테이블 하단의 스크롤 막대를 사용하여 "AS 경로"를 봅니다.

| **접두사** |  **다음 홉 유형** | **다음 홉** |  **루트 원산지** |**경로로** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /구독//리소스그룹//공급자/Microsoft.Network/vpnGateways/343a19a6ac74e4d81f05ccccf1536cf-eastus-gw`<sub>``<rg>`| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/구독//리소스그룹/공급자/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-gw`<sub>``<rg>`|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/구독//리소스그룹//공급자/Microsoft.Network/vpnGateways/343a19a6ac74e4d81f05ccccf1536cf-eastus-gw`<sub>``<rg>`|23000|
|10.4.0.0/16|가상 네트워크 연결| 온링크 |  |  |
|10.5.0.0/16| IP 주소| 10.4.0.6|/구독//리소스그룹/공급자/Microsoft.Network/가상허브/easthub_1/경로테이블/table_1`<sub>``<rg>`| |
|0.0.0.0/0| IP 주소| `<Azure Firewall IP>` |/구독//리소스그룹/공급자/Microsoft.Network/가상허브/easthub_1/경로테이블/table_1`<sub>``<rg>`| |
|10.22.1.0/16| 원격 허브|10.8.0.6, 10.8.0.7|/구독/`<sub>`/resourceGroups//공급자/Microsoft.Network/virtualHubs/westhub_`<rg>`| 4848-22000 |
|10.9.0.0/16| 원격 허브|  온링크 |/구독/`<sub>`/resourceGroups//공급자/Microsoft.Network/virtualHubs/westhub_1`<rg>`| |

>[!NOTE]
> 미국 동부와 서유럽 허브가 예제 토폴로지에서 서로 통신하지 않는 경우 학습된 경로(10.9.0.0/16)는 존재하지 않습니다. 허브는 네트워크에 직접 연결된 네트워크만 보급합니다.
>

## <a name="additional-information"></a><a name="additional"></a>추가 정보

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>허브 경로 테이블 에 대해

가상 허브 경로를 만들어 가상 허브 경로 테이블에 적용할 수 있습니다. 가상 허브 경로 테이블에 여러 경로를 적용할 수 있습니다. 이렇게 하면 IP 주소(일반적으로 스포크 VNet의 NVA 네트워크 가상 어플라이언스)를 통해 대상 VNet에 대한 경로를 설정할 수 있습니다. NVA에 대한 자세한 내용은 [가상 허브에서 NVA로의 트래픽 경로를](virtual-wan-route-table-portal.md)참조하십시오.

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>기본 경로 에 대해(0.0.0.0/0)

가상 허브에는 학습된 기본 경로를 가상 네트워크, 사이트 간 VPN 및 플래그가 연결에서 '활성화'된 경우 ExpressRoute 연결을 전파하는 기능이 있습니다. 이 플래그는 가상 네트워크 연결, VPN 연결 또는 ExpressRoute 연결을 편집할 때 표시됩니다. '인에이블인터넷보안'은 허브 VNet, 익스프레스루트 및 VPN 연결에서 기본적으로 false입니다.

기본 경로는 가상 WAN 허브에서 시작되지 않습니다. 기본 경로는 허브에 방화벽을 배포한 결과로 가상 WAN 허브에서 이미 학습되었거나 연결된 다른 사이트에서 강제 터널링이 사용하도록 설정된 경우 전파됩니다.

## <a name="next-steps"></a>다음 단계

가상 WAN에 대한 자세한 내용은 [가상 WAN 개요를](virtual-wan-about.md)참조하십시오.