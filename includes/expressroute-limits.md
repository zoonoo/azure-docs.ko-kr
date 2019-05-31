---
title: 포함 파일
description: 포함 파일
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 05/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9f7f9b1504533e0ec503e97c086c380da71671a0
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299709"
---
| Resource | 기본/최대 제한 |
| --- | --- |
| 구독당 ExpressRoute 회로 |10 |
| Azure Resource Manager를 사용 하 여 구독 당 지역별 ExpressRoute 회로 |10 |
| Azure 개인 피어 링 ExpressRoute 표준에 보급 된 최대 경로 수 |4,000 |
| Azure 개인 피어 링으로 ExpressRoute 프리미엄 추가 기능에 보급 된 최대 경로 수 |10000 |
| Azure 개인 피어 링 ExpressRoute 연결에 대 한 VNet 주소 공간에서에서 보급 된 최대 경로 수 |200 |
| Microsoft 피어 링으로 ExpressRoute 표준을 보급 된 최대 경로 수 |200 |
| Microsoft 피어 링으로 ExpressRoute 프리미엄 추가 기능 보급 된 최대 경로 수 |200 |
| ExpressRoute 회로가 동일한 피어 링 위치에 동일한 가상 네트워크에 연결의 최대 수 |4 |
| 서로 다른 피어링 위치의 동일한 가상 네트워크에 연결된 ExpressRoute 회로의 최대 수 |4 |
| ExpressRoute 회로당 허용되는 가상 네트워크 링크 수 |참조 된 [ExpressRoute 회로 당 가상 네트워크 수가](#vnetpercircuit) 테이블입니다.  |

#### <a name="vnetpercircuit"></a> ExpressRoute 회로 당 가상 네트워크의 수
| **회로 크기** | **표준에 대 한 가상 네트워크 링크 수** | **프리미엄 추가 기능을 사용 하 여 가상 네트워크 링크 수** |
| --- | --- | --- |
| 50Mbps |10 |20 |
| 100Mbps |10 |25 |
| 200Mbps |10 |25 |
| 500Mbps |10 |40 |
| 1Gbps |10 |50 |
| 2Gbps |10 |60 |
| 5Gbps |10 |75 |
| 10Gbps |10 |100 |
| 40 g b p s * |10 |100 |
| 100 g b p s * |10 |100 |

* ExpressRoute 직접만
