---
title: 여러 ISP 링크에서 Azure 경로 선택
titleSuffix: Azure Virtual WAN
description: Azure 경로 선택 및 Virtual WAN에 대해 알아보기
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: f24696c0db3155a59106e1361b01454b9ac16a20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91267757"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>여러 ISP 링크에서 Azure 경로 선택

Azure Virtual WAN은 VPN 사이트에 링크 정보를 포함하는 기능을 사용자에게 제공하여, VPN/SD-WAN 디바이스 솔루션이 다양한 링크를 통해 Azure로 트래픽을 유도하는 분기 관련 정책을 프로그래밍할 수 있는 시나리오를 가능하게 합니다. 이를 **Azure 경로 선택** 이라고 합니다.

## <a name="architecture"></a>Architecture

Azure 경로 선택이 작동하는 방식을 이해하기 위해, Virtual WAN VPN 사이트의 사례와 사이트 간 연결을 사용해 보겠습니다.

VPN 사이트는 공용 IP, 디바이스 모델 및 이름 등과 같은 정보를 사용하여 온-프레미스 SD-WAN/VPN 디바이스를 나타냅니다. 실제 온-프레미스 VPN 사이트에는 Virtual WAN VPN 사이트 정보에 포함될 수도 있는 여러 ISP 링크가 포함될 수 있습니다. 이렇게 하면 Azure에서 링크 정보를 볼 수 있습니다.

Virtual WAN의 VPN에 들어오는 사이트 간 IPsec 연결은 가상 허브 내부의 VPN 게이트웨이 인스턴스에서 종료됩니다. 사이트 간 연결은 VPN 사이트와 Azure VPN 게이트웨이 간의 연결을 나타냅니다. 이것은 하나 이상의 링크 연결로 구성됩니다. 각 링크 연결은 두 개의 터널로 구성되는데, 여기서 각 터널은 Azure Virtual WAN VPN 게이트웨이의 고유 인스턴스에서 종료합니다. 사이트 간 연결에서 최대 4개의 링크 연결을 설정할 수 있습니다. 그러면 사이트 간 연결 내에서 최대 8개의 터널을 사용할 수 있게 됩니다. Azure는 단일 Virtual WAN VPN 게이트웨이 내에서 종료되는 최대 2000개의 터널을 지원합니다.

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="다중 링크 다이어그램":::

이 그림에서는 Azure Virtual WAN에 연결되는 사이트의 다중 링크를 보여줍니다. 이 다이어그램에서

* 온-프레미스 분기(VPN/SD-WAN 디바이스)에는 두 개의 ISP 링크가 있습니다. 각 ISP 링크는 링크 연결에 해당합니다.

* 온-프레미스 고객관리 VPN/SD-WAN 디바이스에서 IKEv1 또는 IKEv2 IPsec을 지원한다고 가정합니다.

* 각 Azure 사이트 간 Virtual WAN 연결은 자체 내의 링크 연결로 구성됩니다. 연결은 최대 4개의 링크 연결을 지원합니다. Azure는 Virtual WAN 연결에 대해 연결 단위 요금을 청구합니다. 링크 연결에 대한 요금은 부과되지 않습니다.

* 결국, 각 링크 연결은 두 개의 IPsec 터널로 구성되는데, 이는 Virtual WAN VPN 게이트웨이의 서로 다른 두 개의 인스턴스에서 종료할 수 있습니다. 게이트웨이는 복원력을 위해 활성-활성 게이트웨이로 설정됩니다. 각 링크 연결에는 고유한 IP 주소와 BGP 피어링 IP가 있어야 합니다. 다이어그램에서 터널 0은 인스턴스 0에서 종료되고, 터널 1은 인스턴스 1에서 종료될 수 있습니다.

* 경로 선택을 제공하는 분기 디바이스를 통해 분기 관리 솔루션에서 적절한 정책을 사용할 수 있어서, Azure에 대한 여러 링크를 통해 트래픽을 분산할 수 있습니다. 예를 들어, ISP 1 링크를 우선 순위가 높은 트래픽에 사용하고, ISP 2 링크를 백업으로 사용할 수 있습니다.

* 가상 허브 VPN은 모든 종료 터널에서 ECMP(동일한 비용 다중 경로 라우팅)를 사용한다는 점에 유의해야 합니다.

## <a name="next-steps"></a>다음 단계

[Azure FAQ](virtual-wan-faq.md)를 참조하십시오.