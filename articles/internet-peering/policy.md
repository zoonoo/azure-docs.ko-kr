---
title: Microsoft 피어 링 정책
titleSuffix: Azure
description: Microsoft 피어 링 정책
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bee41bb8e5beb4df3086ab50499cb185a83e4efe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592333"
---
# <a name="peering-policy"></a>피어링 정책
Microsoft는 업계 표준 및 모범 사례에 따라 지원 되는 최상의 사용자 환경을 보장 하 고, 향후 수요에 맞게 크기를 조정 하 고, 피어 링의 전략적 배치를 위해 설계 된 선택적 피어 링 정책을 유지 합니다 따라서 Microsoft는 필요에 따라 정책에 대 한 예외를 만들 수 있는 권리를 보유 합니다. 네트워크의 일반적인 Microsoft 요구 사항은 아래 섹션에 설명 되어 있습니다. 이는 직접 피어 링 및 교환 피어 링 요청 모두에 적용 됩니다. 

## <a name="technical-requirements"></a>기술 요구 사항

* 정체 없이 트래픽을 교환할 수 있는 충분 한 용량이 있는 완전히 중복 된 네트워크입니다.
* 피어에는 공개적으로 라우팅할 수 있는 ASN (자치 시스템 번호)이 있습니다.
* IPv4 및 i p v 6이 모두 지원 되며 Microsoft는 각 피어 링 위치에 두 유형의 세션을 설정 해야 합니다.
* MD5는 지원 되지 않습니다.
* **ASN 세부 정보:**

    * Microsoft는 다음과 같은 ASNs: AS8068, AS8069, AS12076와 함께 AS8075를 관리 합니다. AS8075 피어 링을 사용 하는 ASNs의 전체 목록은 SET MICROSOFT로 참조 하세요.
    * Microsoft와의 모든 파티 피어 링은 모든 상황에서 AS12076 (Express Route)의 경로를 허용 하는 것이 아니라 모든 피어에서 AS12076를 필터링 해야 합니다.

* **라우팅 정책:**
    * 피어에는 하나 이상의 공개적으로 라우팅할 수 있는/24가 있습니다.
    * Microsoft는 수신 되는 판별자 (다중 종료)를 덮어씁니다.
    * Microsoft는 피어 로부터 BGP 커뮤니티 태그를 수신 하 여 경로를 표시 하는 것을 선호 합니다.
    * 피어 링 세션에서 Microsoft와의 최대 접두사 2000 (IPv4) 및 500 (IPv6) 경로를 설정 하는 것이 좋습니다.
    * 사전에 특별히 동의한 경우가 아니면 피어는 Microsoft와 피어 링 하는 모든 위치에서 일관 된 경로를 발표 해야 합니다.
    * 일반적으로 AS8075를 사용 하 여 피어 링 세션은 모든 MICROSOFT 경로를 보급 합니다. Microsoft는 지역별 특정 사항을 알릴 수 있습니다.
    * 당사자는 모두 정적 경로, 마지막 수단의 경로 또는 BGP를 통해 알려지지 않은 경로에 대 한 트래픽을 다른 당사자에 게 보냅니다.
    * 피어는 필터링을 목적으로 하는 IRR (공용 인터넷 라우팅 레지스트리) 데이터베이스에 경로를 등록 하 고이 정보를 최신 상태로 유지 하는 데 필요 합니다.      
    * 피어는 경로 보안을 위한 수동 RS 산업 표준을 준수 합니다.  유일한 판단에 따라 Microsoft는 다음을 선택할 수 있습니다. 1.) 경로가 서명 및 등록 되지 않은 회사와의 피어 링을 설정 하지 않습니다. 2.) 잘못 된 RPKI 경로를 제거 하려면 3.) 등록 및 서명 되지 않은 설정 된 피어의 경로를 허용 하지 않습니다. 

## <a name="operational-requirements"></a>운영 요구 사항
* 모든 기술 및 성능 문제 해결, 보안 위반, 서비스 거부 공격 또는 피어 또는 고객 내에서 발생 하는 기타 남용을 지원할 수 있는, 연중 무휴의 연중 무휴 네트워크 운영 센터 (NOC)
* 피어는 회사 도메인 및 전화 번호의 연중 무휴 전자 메일을 포함 하 여 [Peeringdb](https://www.peeringdb.com) 에 완전 하 고 최신 프로필이 있어야 합니다. Microsoft는이 정보를 사용 하 여 NOC 정보, 기술 연락 정보 및 피어 링 기능에서의 현재 상태와 같은 피어의 세부 정보에 대 한 유효성을 검사 합니다. 개인 Yahoo, Gmail 및 hotmail 계정은 허용 되지 않습니다.

## <a name="physical-connection-requirements"></a>실제 연결 요구 사항
* 직접 피어 링 또는 Exchange 피어 링을 위해 Microsoft에 연결할 수 있는 위치는 [Peeringdb](https://www.peeringdb.com/net/694) 에 나열 됩니다.

* **교환 피어링:**
    * 피어는 exchange에 대 한 최소 10gb 연결 이어야 합니다.
    * 최대 사용률이 50%를 초과 하면 피어는 해당 포트를 업그레이드 해야 합니다.
    * Microsoft는 장애 조치 시나리오를 지원 하기 위해 피어가 exchange에 대 한 다양 한 연결을 유지 하도록 권장 합니다

* **직접 피어링:**
    * 상호는 100 Gbps의 광학을 사용 하 여 단일 모드 파이버 보다 커야 합니다.
    * Microsoft는 ISP 또는 네트워크 서비스 공급자와의 직접 피어 링만 설정 합니다.
    * 피어는 최고 사용률이 50%를 초과 하 고 각 metro에서 단일 위치 또는 metro의 여러 위치에서 다양 한 용량을 유지 하는 경우 해당 포트를 업그레이드 해야 합니다.
    * 각 직접 피어 링은 피어의 가장자리에 위치한 피어의 라우터에서 두 개의 Microsoft edge 라우터에 연결 되는 두 개의 연결로 구성 됩니다. Microsoft에서 이러한 연결에는 이중 BGP 세션이 필요 합니다. 피어에서 중복 장치를 배포 하지 않도록 선택할 수 있습니다.


## <a name="traffic-requirements"></a>트래픽 요구 사항

* Exchange 피어 링을 통한 피어는 최소 500의 트래픽을 보유 하 고 2gb 미만 이어야 합니다. 2gb의 직접 피어 링을 초과 하는 트래픽의 경우를 설정 해야 합니다.
* Microsoft는 직접 피어 링을 위해 최소 2gb가 필요 합니다. 피어 링 위치에 대 한 상호 합의 된 각 위치는 장애 조치 (failover) 시나리오에서 피어 링이 지역화 된 상태로 유지 되도록 장애 조치 

## <a name="next-steps"></a>다음 단계

* Microsoft와 직접 피어 링을 설정 하는 단계에 대해 알아보려면 [직접 피어 링 연습](walkthrough-direct-all.md)을 수행 합니다.
* Microsoft와의 Exchange 피어 링을 설정 하는 단계에 대해 알아보려면 [exchange 피어 링 연습](walkthrough-exchange-all.md)을 수행 하세요.
