---
title: 포함 파일
description: 포함 파일
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ad75b333fc7d5d37a4ac074727e4d54b67bd0321
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45636998"
---
#### <a name="expressroute-limits"></a>ExpressRoute 제한
구독당 ExpressRoute 리소스에는 다음과 같은 제한이 적용됩니다.

| 리소스 | 기본값/최대 한도 |
| --- | --- |
| 구독당 ExpressRoute 회로 |10 |
| 구독당 지역별 ExpressRoute 회로(Azure Resource Manager) |10 |
| ExpressRoute 표준을 사용할 경우 Azure 개인 피어링에 대한 최대 경로 수 |4,000 |
| ExpressRoute 프리미엄 추가 기능을 사용할 경우 Azure 개인 피어링에 대한 최대 경로 수 |10000 |
| ExpressRoute 표준을 사용할 경우 Azure Microsoft 피어링에 대한 최대 경로 수 |200 |
| ExpressRoute 프리미엄 추가 기능을 사용할 경우 Azure Microsoft 피어링에 대한 최대 경로 수 |200 |
| 서로 다른 피어링 위치의 동일한 가상 네트워크에 연결된 ExpressRoute 회로의 최대 수 |4 |
| ExpressRoute 회로당 허용되는 가상 네트워크 링크 수 |아래 표 참조 |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>ExpressRoute 회로당 Virtual Network 수
| **회로 크기** | **표준을 사용할 경우 VNet 링크 수** | **프리미엄 추가 기능을 사용할 경우 VNet 링크 수** |
| --- | --- | --- |
| 50Mbps |10 |20 |
| 100Mbps |10 |25 |
| 200Mbps |10 |25 |
| 500Mbps |10 |40 |
| 1Gbps |10 |50 |
| 2Gbps |10 |60 |
| 5Gbps |10 |75 |
| 10Gbps |10 |100 |

