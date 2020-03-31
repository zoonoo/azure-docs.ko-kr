---
title: 포함 파일
description: 포함 파일
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334623"
---
| 리소스 | 제한 |
| --- | --- |
| 구독당 ExpressRoute 회로 |10 |
| Azure 리소스 관리자를 사용 하 고 구독 당 지역 당 ExpressRoute 회로 |10 |
| ExpressRoute 표준을 사용하여 Azure 개인 피어링에 보급되는 최대 경로 수 |4,000 |
| ExpressRoute 프리미엄 추가 기능을 사용하여 Azure 개인 피어링에 보급되는 최대 경로 수 |10000 |
| ExpressRoute 연결에 대 한 VNet 주소 공간에서 Azure 개인 피어링에서 보급 된 경로의 최대 수 |200 |
| 익스프레스루트 표준으로 Microsoft 피어링에 보급된 최대 경로 수 |200 |
| ExpressRoute 프리미엄 추가 기능을 사용하여 Microsoft 피어링에 보급된 최대 경로 수 |200 |
| 동일한 피어링 위치에서 동일한 가상 네트워크에 연결된 최대 ExpressRoute 회로 수 |4 |
| 서로 다른 피어링 위치의 동일한 가상 네트워크에 연결된 ExpressRoute 회로의 최대 수 |4 |
| ExpressRoute 회로당 허용되는 가상 네트워크 링크 수 |[ExpressRoute 회로 테이블당 가상 네트워크 수를](#vnetpercircuit) 참조하십시오.  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>익스프레스루트 회로당 가상 네트워크 수
| **회로 크기** | **표준용 가상 네트워크 링크 수** | **프리미엄 추가 기능을 갖춘 가상 네트워크 링크 수** |
| --- | --- | --- |
| 50Mbps |10 |20 |
| 100Mbps |10 |25 |
| 200Mbps |10 |25 |
| 500Mbps |10 |40 |
| 1Gbps |10 |50 |
| 2Gbps |10 |60 |
| 5Gbps |10 |75 |
| 10Gbps |10 |100 |
| 40Gbps* |10 |100 |
| 100Gbps* |10 |100 |

**100Gbps 익스프레스루트 다이렉트 전용*

> [!NOTE]
> 전역 리치 연결은 ExpressRoute 회로당 가상 네트워크 연결 제한에 대해 계산됩니다. 예를 들어, 10Gbps 프리미엄 회로를 사용하면 5개의 글로벌 리치 연결과 ExpressRoute 게이트웨이에 대한 95개의 연결 또는 95개의 글로벌 리치 연결, ExpressRoute 게이트웨이 또는 최대 100개의 연결 제한까지 다른 조합에 대한 5개의 연결을 허용합니다. 회로에 대한.
