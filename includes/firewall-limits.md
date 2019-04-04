---
title: 포함 파일
description: 포함 파일
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/25/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0467359cd9d6a067e519a62532f00459bc5f68cb
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891020"
---
| 리소스 | 기본 제한 |
| --- | --- |
| 데이터 처리량 |30gbps<sup>1</sup> |
|규칙|10,000 종류 함께 모든 규칙입니다.|
|AzureFirewallSubnet 최소 크기 |/26|
|네트워크 및 애플리케이션 규칙의 포트 범위|0-64,000 이 제한을 완화하는 작업이 진행 중입니다.|
|경로 테이블|AzureFirewallSubnet로 NextHopType 값을 사용 하 여 0.0.0.0/0 경로 기본적으로 **인터넷**합니다.<br><br>ExpressRoute 또는 VPN 게이트웨이 통한 온-프레미스에 강제 터널링을 사용 하는 경우 명시적으로 인터넷으로 NextHopType 값 집합을 사용 하 여 0.0.0.0/0 사용자 정의 경로 (UDR)를 구성 하 고 프로그램 AzureFirewallSubnet를 사용 하 여 연결 해야 합니다. 이 잠재적 기본 게이트웨이 다시 온-프레미스 네트워크에 대 한 BGP 광고를 재정의합니다. 조직 온-프레미스 네트워크를 통해 다시 기본 게이트웨이 트래픽을 Azure 방화벽에 대 한 강제 터널링에 필요한 경우 지원에 문의 합니다. 허용 목록을 구독 필수 방화벽이 인터넷에 연결을 유지 관리 됩니다 수 있습니다.|

<sup>1</sup>이러한 제한을 늘려야 하는 경우 Azure 지원에 문의 합니다.
