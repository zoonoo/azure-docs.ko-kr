---
title: 포함 파일
description: 포함 파일
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 06/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: b317c2a43352a750d4700fad56d5f7d741b2cc7d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805430"
---
| 리소스 | 제한 |
| --- | --- |
| 데이터 처리량 |30Gbps<sup>1</sup> |
|규칙|10,000. 모든 규칙 유형이 결합되었습니다.|
|최대 DNAT 규칙|단일 공용 IP 주소에 대 한 298.<br>모든 추가 공용 IP 주소는 사용 가능한 SNAT 포트에 영향을 주지만 사용 가능한 DNAT 규칙의 수를 줄입니다. 예를 들어 두 개의 공용 IP 주소는 297 DNAT 규칙을 허용 합니다. 규칙의 프로토콜이 TCP와 UDP 둘 다에 대해 구성된 경우 두 개의 규칙으로 계산됩니다.|
|AzureFirewallSubnet 최소 크기 |/26|
|네트워크 및 애플리케이션 규칙의 포트 범위|1 - 65535|
|공용 IP 주소|최대 250. 모든 공용 IP 주소는 DNAT 규칙에서 사용할 수 있으며 모두 사용 가능한 SNAT 포트에 적용됩니다.|
|IP 그룹의 IP 주소|방화벽 당 최대 100 IP 그룹<br>각 IP 그룹당 최대 5000 개별 IP 주소 또는 IP 접두사<br><br>자세한 내용은 [Azure 방화벽의 IP 그룹](../articles/firewall/ip-groups.md#ip-address-limits)을 참조 하세요.
|경로 테이블|기본적으로 AzureFirewallSubnet에는 NextHopType 값이 **Internet**으로 설정된 0.0.0.0/0 경로가 있습니다.<br><br>Azure Firewall에는 직접 인터넷 연결이 있어야 합니다. AzureFirewallSubnet이 BGP를 통해 온-프레미스 네트워크에 대한 기본 경로를 학습하는 경우 직접 인터넷 연결을 유지하기 위해 **Internet**으로 설정된 **NextHopType** 값을 통해 0.0.0.0/0 UDR로 재정의해야 합니다. 기본적으로 Azure Firewall은 온-프레미스 네트워크에 대한 강제 터널링을 지원하지 않습니다.<br><br>그러나 구성에 온-프레미스 네트워크에 대한 강제 터널링이 필요한 경우 Microsoft는 사례별로 지원할 예정입니다. 사용자의 사례를 검토할 수 있도록 지원 부서에 연락해주시기 바랍니다. 수락되면 사용자의 구독을 허용하고 필요한 방화벽 인터넷 연결이 유지되도록 합니다.|

<sup>1</sup> 이러한 제한을 늘려야 하는 경우 Azure 지원에 문의하세요.
