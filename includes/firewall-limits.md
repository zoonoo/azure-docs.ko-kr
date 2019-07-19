---
title: 포함 파일
description: 포함 파일
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/16/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a42284765a46f4a000dc5b7fcf2867ef17d69570
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229278"
---
| 리소스 | 기본 제한 |
| --- | --- |
| 데이터 처리량 |30gbps<sup>1</sup> |
|규칙|1만, 모든 규칙 형식이 조합 되어 있습니다.|
|AzureFirewallSubnet 최소 크기 |/26|
|네트워크 및 애플리케이션 규칙의 포트 범위|0-64,000 이 제한을 완화하는 작업이 진행 중입니다.|
|공용 IP 주소|최대 100|
|경로 테이블|기본적으로 AzureFirewallSubnet에는 NextHopType 값이 **인터넷**으로 설정 된 0.0.0.0/0 경로가 있습니다.<br><br>Azure Firewall에는 직접 인터넷 연결이 있어야 합니다. AzureFirewallSubnet가 BGP를 통해 온-프레미스 네트워크에 대 한 기본 경로를 학습 하는 경우 직접 인터넷 연결을 유지 하려면 **internet** 로 설정 된 **NextHopType** 값을 사용 하 여 0.0.0.0/0 udr으로 재정의 해야 합니다. 기본적으로 Azure Firewall은 온-프레미스 네트워크에 대한 강제 터널링을 지원하지 않습니다.<br><br>그러나 구성에 온-프레미스 네트워크에 대한 강제 터널링이 필요한 경우 Microsoft는 사례별로 지원할 예정입니다. 사용자의 사례를 검토할 수 있도록 지원 부서에 연락해주시기 바랍니다. 수락 되 면 구독을 허용 하 고 필요한 방화벽 인터넷 연결이 유지 되는지 확인 합니다.|

<sup>1</sup> 이러한 제한을 늘려야 하는 경우 Azure 지원에 문의 하세요.
