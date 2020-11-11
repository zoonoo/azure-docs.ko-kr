---
title: 포함 파일
description: 포함 파일
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/06/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: d479c3087b971aa17cf145e0111890da07381eab
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386603"
---
| 리소스 | 제한 |
| --- | --- |
| 데이터 처리량 |30Gbps<sup>1</sup> |
|규칙|10,000. 모든 규칙 유형이 결합되었습니다.|
|최대 DNAT 규칙|단일 공용 IP 주소의 경우 298입니다.<br>모든 추가 공용 IP 주소는 사용 가능한 SNAT 포트에 영향을 주지만, 사용 가능한 DNAT 규칙 수는 줄어듭니다. 예를 들어 두 개의 공용 IP 주소는 297개의 DNAT 규칙을 허용합니다. 규칙의 프로토콜이 TCP와 UDP 둘 다에 대해 구성된 경우 두 개의 규칙으로 계산됩니다.|
|AzureFirewallSubnet 최소 크기 |/26|
|네트워크 및 애플리케이션 규칙의 포트 범위|1 - 65535|
|공용 IP 주소|최대 250. 모든 공용 IP 주소는 DNAT 규칙에서 사용할 수 있으며 모두 사용 가능한 SNAT 포트에 적용됩니다.|
|IP 그룹의 IP 주소|방화벽당 최대 100개 IP 그룹<br>각 IP 그룹당 최대 5,000개 개별 IP 주소 또는 IP 접두사
|경로 테이블|기본적으로 AzureFirewallSubnet에는 NextHopType 값이 **Internet** 으로 설정된 0.0.0.0/0 경로가 있습니다.<br><br>Azure Firewall에는 직접 인터넷 연결이 있어야 합니다. AzureFirewallSubnet이 BGP를 통해 온-프레미스 네트워크에 대한 기본 경로를 학습하는 경우 직접 인터넷 연결을 유지하기 위해 **Internet** 으로 설정된 **NextHopType** 값을 통해 0.0.0.0/0 UDR로 재정의해야 합니다. 기본적으로 Azure Firewall은 온-프레미스 네트워크에 대한 강제 터널링을 지원하지 않습니다.<br><br>그러나 구성에 온-프레미스 네트워크에 대한 강제 터널링이 필요한 경우 Microsoft는 사례별로 지원할 예정입니다. 사용자의 사례를 검토할 수 있도록 지원 부서에 연락해주시기 바랍니다. 수락되면 사용자의 구독을 허용하고 필요한 방화벽 인터넷 연결이 유지되도록 합니다.|
|네트워크 규칙의 FQDN|성능을 향상시키려면 방화벽당 모든 네트워크 규칙에서 FQDN이 1000개를 초과하지 않도록 합니다.|

<sup>1</sup> 이러한 제한을 늘려야 하는 경우 Azure 지원에 문의하세요.
