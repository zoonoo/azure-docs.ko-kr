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
ms.openlocfilehash: 45d34297bf37a6e46bc57e95ff49def49051e32e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67622111"
---
| 리소스 | 기본/최대 제한 |
| --- | --- |
| 구독당 ExpressRoute 회로 |10 |
| 구독 당 지역별 Express 경로 회로, Azure Resource Manager |10 |
| Express 경로 표준을 사용 하 여 Azure 개인 피어 링에 알린 최대 경로 수 |4,000 |
| Express 경로 프리미엄 추가 기능을 사용 하 여 Azure 개인 피어 링에 알린 최대 경로 수 |10000 |
| Express 경로 연결을 위한 VNet 주소 공간에서 Azure 개인 피어 링에서 알린 최대 경로 수 |200 |
| Express 경로 표준을 사용 하 여 Microsoft 피어 링에 알린 최대 경로 수 |200 |
| Express 경로 프리미엄 추가 기능을 사용 하 여 Microsoft 피어 링에 알린 최대 경로 수 |200 |
| 동일한 피어 링 위치의 동일한 가상 네트워크에 연결 된 최대 Express 경로 회로 수 |4 |
| 서로 다른 피어링 위치의 동일한 가상 네트워크에 연결된 ExpressRoute 회로의 최대 수 |4 |
| ExpressRoute 회로당 허용되는 가상 네트워크 링크 수 |Express 경로 [회로 테이블당 가상 네트워크 수](#vnetpercircuit) 를 참조 하세요.  |

#### <a name="vnetpercircuit"></a>Express 경로 회로 당 가상 네트워크 수
| **회로 크기** | **표준에 대 한 가상 네트워크 링크 수** | **프리미엄 추가 기능을 사용 하는 가상 네트워크 링크 수** |
| --- | --- | --- |
| 50Mbps |10 |20 |
| 100Mbps |10 |25 |
| 200Mbps |10 |25 |
| 500Mbps |10 |40 |
| 1Gbps |10 |50 |
| 2Gbps |10 |60 |
| 5Gbps |10 |75 |
| 10Gbps |10 |100 |
| 40 Gbps * |10 |100 |
| 100 Gbps * |10 |100 |

**100 Gbps Express 경로 직접만*
