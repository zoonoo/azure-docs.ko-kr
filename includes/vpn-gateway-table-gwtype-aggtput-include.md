---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fad9b990b6ff1021efdaf8aadeb1e19d8a55871d
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39137990"
---
|**SKU**   | **S2S/VNet 간<br>터널** | **P2S<br>연결** | **집계<br>처리량 벤치마크** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| 최대 30*                         | 최대 128\*\*             | 650Mbps                    |
|**VpnGw2**| 최대 30*                         | 최대 128\*\*             | 1Gbps                      |
|**VpnGw3**| 최대 30*                         | 최대 128\*\*             | 1.25Gbps                   |
|**Basic** | 최대 10                         | 최대 128               | 100Mbps                    | 

* (*) S2S VPN 터널이 30개 넘게 필요한 경우 [가상 WAN](../articles/virtual-wan/virtual-wan-about.md)을 사용해야 합니다.

* (\*\*) 추가 연결이 필요한 경우 지원에 문의하세요.

* 집계 처리량 벤치마크는 단일 게이트웨이를 통해 집계된 여러 터널의 측정값을 기반으로 합니다. 인터넷 트래픽 조건 및 응용 프로그램 동작으로 인해 처리량이 보장되지 않습니다.

* 가격 책정 정보는 [가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway) 페이지에 있습니다.

* [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 페이지에서 Service Level Agreement(서비스 수준 계약) 정보를 확인할 수 있습니다.

* VpnGw1, VpnGw2 및 VpnGw3은 Resource Manager 배포 모델을 사용하는 VPN 게이트웨이에만 지원됩니다.