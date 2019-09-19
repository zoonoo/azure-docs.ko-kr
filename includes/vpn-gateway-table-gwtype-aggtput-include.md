---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 65c1011e6e005c190d1ae5d51fdd009f66a20956
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919711"
---
|**SKU**   | **S2S/VNet 간<br>터널** | **P2S<br> SSTP 연결** | **P2S<br> IKEv2/OpenVPN 연결** | **집계<br>처리량 벤치마크** | **BGP** | **Zone-redundant** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**Basic** | 최대 10    | 최대 128  | 지원되지 않음  | 100Mbps  | 지원되지 않음| 아니요 |
|**VpnGw1**| 최대 30*   | 최대 128  | 최대 250       | 650Mbps  | 지원됨 | 아니요 |
|**VpnGw2**| 최대 30*   | 최대 128  | 최대 500       | 1Gbps    | 지원됨 | 아니요 |
|**VpnGw3**| 최대 30*   | 최대 128  | 최대 1000      | 1.25Gbps | 지원됨 | 아니요 |
|**VpnGw1AZ**| 최대 30*   | 최대 128  | 최대 250       | 650Mbps  | 지원됨 | 예 |
|**VpnGw2AZ**| 최대 30*   | 최대 128  | 최대 500       | 1Gbps    | 지원됨 | 예 |
|**VpnGw3AZ**| 최대 30*   | 최대 128  | 최대 1000      | 1.25Gbps | 지원됨 | 예 |

(*) S2S VPN 터널이 30개 넘게 필요한 경우 [가상 WAN](../articles/virtual-wan/virtual-wan-about.md)을 사용해야 합니다.

* 이러한 연결 제한은 별도로 적용됩니다. 예를 들어 VpnGw1 SKU 하나에 SSTP 연결 128개와 IKEv2 연결 250개가 있을 수 있습니다.

* 가격 책정 정보는 [가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway) 페이지에 있습니다.

* [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 페이지에서 Service Level Agreement(서비스 수준 계약) 정보를 확인할 수 있습니다.

* VpnGw1, VpnGw2, VpnGw3, VpnGw1AZ, VpnGw2AZ 및 VpnGw3AZ는 Resource Manager 배포 모델을 사용하는 VPN 게이트웨이에만 지원됩니다.

* 기본 SKU는 레거시 SKU로 간주됩니다. Basic SKU에는 몇 가지 기능이 제한됩니다. 새 게이트웨이 SKU 중 하나에 대한 기본 SKU를 사용하는 게이트웨이의 크기를 조정할 수 없습니다. 대신 VPN Gateway를 삭제하고 다시 만드는 작업이 포함된 새 SKU로 변경해야 합니다. 기본 SKU를 사용하기 전에 필요한 기능을 지원하는지 확인합니다.

* 집계 처리량 벤치마크는 단일 게이트웨이를 통해 집계된 여러 터널의 측정값을 기반으로 합니다. VPN Gateway의 집계 처리량 벤치마크는 S2S + P2S 결합형입니다. **P2S 연결이 많을 경우 처리량 제한으로 인해 S2S 연결에 부정적인 영향을 줄 수 있습니다.** 집계 처리량 벤치마크는 인터넷 트래픽 조건 및 애플리케이션 동작으로 인해 처리량이 보장되지 않습니다.

* 고객이 VpnGw SKU의 상대 성능을 파악할 수 있도록, 공개적으로 사용 가능한 iPerf 및 CTSTraffic 도구를 사용하여 성능을 측정했습니다. 아래 표에는 다양한 알고리즘을 사용한 성능 테스트 결과가 나와 있습니다. 여기서 확인할 수 있듯이, IPsec 암호화와 무결성에 모두 GCMAES256 알고리즘을 사용했을 때 최고 성능을 얻었습니다. IPsec 암호화에 AES256을 사용하고 무결성에 SHA256을 사용했을 때는 평균 성능을 얻었습니다. IPsec 암호화에 DES3을 사용하고 무결성에 SHA256을 사용했을 때 성능이 가장 낮았습니다.

|**SKU**   | **사용된<br>알고리즘** | **관찰된<br>처리량** | **관찰된<br>초당 패킷 수** |
|---       | ---                 | ---            | ---                    |
|**VpnGw1**| GCMAES256<br>AES256, SHA256<br>DES3, SHA256| 650Mbps<br>500Mbps<br>120Mbps   | 58,000<br>50,000<br>50,000|
|**VpnGw2**| GCMAES256<br>AES256, SHA256<br>DES3, SHA256| 1Gbps<br>500Mbps<br>120Mbps | 90,000<br>80,000<br>55,000|
|**VpnGw3**| GCMAES256<br>AES256, SHA256<br>DES3, SHA256| 1.25Gbps<br>550Mbps<br>120Mbps | 105,000<br>90,000<br>60,000|
|**VpnGw1AZ**| GCMAES256<br>AES256, SHA256<br>DES3, SHA256| 650Mbps<br>500Mbps<br>120Mbps   | 58,000<br>50,000<br>50,000|
|**VpnGw2AZ**| GCMAES256<br>AES256, SHA256<br>DES3, SHA256| 1Gbps<br>500Mbps<br>120Mbps | 90,000<br>80,000<br>55,000|
|**VpnGw3AZ**| GCMAES256<br>AES256, SHA256<br>DES3, SHA256| 1.25Gbps<br>550Mbps<br>120Mbps | 105,000<br>90,000<br>60,000|
