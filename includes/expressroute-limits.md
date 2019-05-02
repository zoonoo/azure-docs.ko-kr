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
ms.openlocfilehash: d067f1af3d5479aef28ddf2290cebe3fe45726b0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733740"
---
| 리소스 | 기본/최대 제한 |
| --- | --- |
| 구독당 ExpressRoute 회로 |10 |
| Azure Resource Manager를 사용 하 여 구독 당 지역별 ExpressRoute 회로 |10 |
| Azure 개인 피어 링 ExpressRoute 표준에 대 한 경로의 최대 수 |4,000 |
| Azure 개인 피어 링에 대해 ExpressRoute 프리미엄 추가 기능을 사용 하 여 경로의 최대 수 |10000 |
| Azure 개인 피어 링 용 ExpressRoute 연결에 대 한 VNet 주소 공간에서 경로의 최대 수 |200 | 
| Microsoft Azure ExpressRoute 표준을 사용할 피어 링에 대 한 경로의 최대 수 |200 |
| Microsoft Azure ExpressRoute Premium 추가 기능을 사용 하 여 피어 링에 대 한 경로의 최대 수 |200 |
| ExpressRoute 회로가 동일한 피어 링 위치에 동일한 가상 네트워크에 연결의 최대 수 |4 |
| 서로 다른 피어링 위치의 동일한 가상 네트워크에 연결된 ExpressRoute 회로의 최대 수 |> 4 GatewaySubnet 크기에 따라 달라 집니다.|
| ExpressRoute 회로당 허용되는 가상 네트워크 링크 수 |다음 표를 참조 하십시오. |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>ExpressRoute 회로 당 가상 네트워크의 수
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

