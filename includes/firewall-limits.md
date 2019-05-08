---
title: 포함 파일
description: 포함 파일
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 5/3/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8709d4d903bd31ff94d04ec61e226857e4190407
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150328"
---
| 리소스 | 기본 제한 |
| --- | --- |
| 데이터 처리량 |30gbps<sup>1</sup> |
|규칙|10,000 종류 함께 모든 규칙입니다.|
|AzureFirewallSubnet 최소 크기 |/26|
|네트워크 및 애플리케이션 규칙의 포트 범위|0-64,000 이 제한을 완화하는 작업이 진행 중입니다.|
|경로 테이블|AzureFirewallSubnet로 NextHopType 값을 사용 하 여 0.0.0.0/0 경로 기본적으로 **인터넷**합니다.<br><br>Azure 방화벽에는 직접 인터넷 연결이 있어야 합니다. 프로그램 AzureFirewallSubnet 학습 기본 경로가 BGP 통해 온-프레미스 네트워크에이 사용 하 여 0.0.0.0/0 UDR을 사용 하 여 재정의 해야 합니다 **NextHopType** 값으로 설정 됩니다 **인터넷** 직접 유지 관리 인터넷에 연결 합니다. 기본적으로 Azure 방화벽 온-프레미스 네트워크에 강제 터널링을 지원 하지 않습니다.<br><br>그러나 구성에 필요한 온-프레미스 네트워크에 강제 터널링을 경우 Microsoft는 지원에서 상황별으로. 에서는 사례를 검토할 수 있도록 지원에 문의 합니다. 수락 된 경우에서는 허용 목록 구독 하 고 필요한 방화벽 인터넷 연결이 유지 되도록 합니다.|

<sup>1</sup>이러한 제한을 늘려야 하는 경우 Azure 지원에 문의 합니다.
