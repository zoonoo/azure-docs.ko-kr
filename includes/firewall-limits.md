---
title: 포함 파일
description: 포함 파일
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/07/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: fb95301d4faa958a677c42bc8092ac52a7c95c8b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80813723"
---
| 리소스 | 제한 |
| --- | --- |
| 데이터 처리량 |30Gbps<sup>1</sup> |
|규칙|10,000. 모든 규칙 형식이 결합되었습니다.|
|최대 DNAT 규칙|298<br>규칙의 프로토콜이 TCP와 UDP 모두에 대해 구성된 경우 두 개의 규칙으로 계산됩니다.|
|AzureFirewallSubnet 최소 크기 |/26|
|네트워크 및 애플리케이션 규칙의 포트 범위|1 - 65535|
|공용 IP 주소|최대 100개(현재 SNAT 포트는 처음 5개의 공용 IP 주소에 대해서만 추가됩니다.)|
|IP 그룹 IP 주소|IP 그룹 50개 이하: 방화벽 인스턴스당 최대 5,000개의 개별 IP 주소.<br>51 - 100개의 IP 그룹: 방화벽 인스턴스당 각각 500개의 개별 IP 주소.<br><br>자세한 내용은 [Azure 방화벽의 IP 그룹(미리 보기)을 참조하십시오.](../articles/firewall/ip-groups.md#ip-address-limits)
|경로 테이블|기본적으로 AzureFirewallSubnet에는 0.0.0.0/0 경로가 있으며 NextHopType 값은 **인터넷으로**설정됩니다.<br><br>Azure Firewall에는 직접 인터넷 연결이 있어야 합니다. AzureFirewallSubnetB를 통해 온-프레미스 네트워크에 대 한 기본 경로를 학습 하는 경우 직접 인터넷 연결을 유지 **하려면** NextHopType 값으로 설정 된 **NextHopType** 값으로 0.0.0.0/0 UDR을 재정의 해야 합니다. 기본적으로 Azure Firewall은 온-프레미스 네트워크에 대한 강제 터널링을 지원하지 않습니다.<br><br>그러나 구성에 온-프레미스 네트워크에 대한 강제 터널링이 필요한 경우 Microsoft는 사례별로 지원할 예정입니다. 사용자의 사례를 검토할 수 있도록 지원 부서에 연락해주시기 바랍니다. 수락되면 사용자의 구독을 허용하고 필요한 방화벽 인터넷 연결이 유지되도록 합니다.|

<sup>1개</sup> 이러한 제한을 늘려야 하는 경우 Azure 지원에 문의하십시오.
