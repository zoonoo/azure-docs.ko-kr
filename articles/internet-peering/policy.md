---
title: Microsoft 피어링 정책
titleSuffix: Azure
description: Microsoft 피어링 정책
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bee41bb8e5beb4df3086ab50499cb185a83e4efe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592333"
---
# <a name="peering-policy"></a>피어링 정책
Microsoft는 업계 표준 및 모범 사례를 기반으로 하는 최상의 고객 경험을 보장하고 향후 수요와 전략적 피어링 배치에 맞춰 확장할 수 있도록 설계된 선택적 피어링 정책을 유지합니다. 따라서 Microsoft는 필요에 따라 정책에 예외를 적용할 수 있는 권리를 보유합니다. 네트워크에 대한 Microsoft의 일반적인 요구 사항은 아래 섹션에 설명되어 있습니다. 이것은 직접 피어링 및 교환 피어링 요청 모두에 적용됩니다. 

## <a name="technical-requirements"></a>기술 요구 사항

* 정체없이 트래픽을 교환할 수 있는 충분한 용량이 있는 완전히 중복된 네트워크입니다.
* 피어는 공개적으로 라우팅할 수 있는 ASN(자치 시스템 번호)을 갖게 됩니다.
* IPv4와 IPv6 모두 지원되며 Microsoft는 각 피어링 위치에서 두 유형의 세션을 설정해야 합니다.
* MD5는 지원되지 않습니다.
* **ASN 세부 정보:**

    * Microsoft는 다음 ASN과 함께 AS8075를 관리합니다: AS8068, AS8069, AS12076. AS8075 피어링을 사용하는 ASN 전체 목록은 AS-SET MICROSOFT를 참조하십시오.
    * Microsoft와 피어링하는 모든 당사자는 어떠한 상황에서도 AS12076(Express Route) 경로를 허용하지 않기로 동의하며 모든 피어에서 AS12076을 필터링해야 합니다.

* **라우팅 정책:**
    * 피어에는 공개적으로 라우팅할 수 있는 하나 이상의 /24가 있습니다.
    * Microsoft는 수신된 MED(다중 종료 판별자)를 덮어씁니다.
    * Microsoft는 피어로부터 BGP 커뮤니티 태그를 수신하여 경로 시작을 나타내는 것을 선호합니다.
    * 피어는 Microsoft와의 피어링 세션에서 최대 접두사 2000(IPv4) 및 500(IPv6) 경로를 설정하는 것을 권장합니다.
    * 사전에 특별히 동의한 경우가 아니면 피어는 Microsoft와 피어링하는 모든 위치에서 일관된 경로를 발표해야 합니다.
    * 일반적으로 AS8075를 사용한 피어링 세션은 모든 AS-MICROSOFT 경로를 보급합니다. Microsoft는 일부 지역별 특정 사항을 알릴 수 있습니다.
    * 어느 당사자도 고정 경로나 마지막 수단 경로를 설정하거나, BGP를 통해 알려지지 않은 경로에 대해 상대방에게 트래픽을 보내지 않습니다.
    * 피어는 필터링을 위해 공용 IRR(인터넷 라우팅 레지스트리) 데이터베이스에 경로를 등록해야 하며 이 정보를 최신 상태로 유지합니다.      
    * 피어는 경로 보안에 대한 MANRS 업계 표준을 준수합니다.  Microsoft는 단독 판단에 따라 다음을 선택할 수 있습니다. 1.) 경로가 서명 및 등록되지 않은 회사와 피어링을 설정하지 않습니다, 2.) 잘못된 RPKI 경로를 제거합니다, 3.) 등록 및 서명되지 않은 설정된 피어의 경로를 허용하지 않습니다. 

## <a name="operational-requirements"></a>운영 요구 사항
* 모든 기술 및 성능 문제, 보안 위반, 서비스 거부 공격 또는 피어 또는 고객 내에서 발생하는 기타 남용의 해결을 지원할 수 있는 완전히 배치된 24x7 NOC(네트워크 운영 센터)입니다.
* 피어는 회사 도메인 및 전화번호의 24x7 NOC 이메일을 포함하여 [PeeringDB](https://www.peeringdb.com)에 완전한 최신 프로필이 있어야합니다. 해당 정보를 사용하여 NOC 정보, 기술 담당자 연락처 정보 및 피어링 시설에서의 현재 상태와 같은 피어의 유효성을 검사합니다. 개인 Yahoo, Gmail 및 hotmail 계정은 허용되지 않습니다.

## <a name="physical-connection-requirements"></a>실제 연결 요구 사항
* 직접 피어링 또는 교환 피어링을 위해 Microsoft와 연결할 수 있는 위치는 [PeeringDB](https://www.peeringdb.com/net/694)에 나열되어 있습니다.

* **교환 피어링:**
    * 피어 교환에 최소 10GB 이상의 연결이 있어야 합니다.
    * 피어는 최대 사용률이 50%를 초과하면 포트를 업그레이드해야 합니다.
    * Microsoft는 피어가 장애 조치(failover) 시나리오를 지원하기 위해 교환에 대한 다양한 연결을 유지하도록 권장합니다.

* **직접 피어링:**
    * 상호 연결은 100Gbps 광학을 사용하는 단일 모드 파이버를 통해 이루어져야 합니다.
    * Microsoft는 ISP 또는 네트워크 서비스 공급자와 직접 피어링만 설정합니다.
    * 피어는 최고 사용률이 50%를 초과할 때 포트를 업그레이드하고, 단일 위치 내에서 또는 도시 내의 여러 위치에서 도시마다 다양한 용량을 유지 관리해야 합니다.
    * 각 직접 피어링은 피어 에지에 위치한 피어의 라우터에서 두 개의 Microsoft 에지 라우터에 연결되는 두 개의 연결로 구성됩니다. Microsoft는 이러한 연결에서 이중 BGP 세션이 필요합니다. 피어에서 중복 디바이스를 배포하지 않도록 선택할 수 있습니다.


## <a name="traffic-requirements"></a>트래픽 요구 사항

* 교환 피어링을 통한 피어에는 최소 500MB 이상 2GB 미만의 트래픽이 있어야 합니다. 2GB를 초과하는 트래픽의 경우 직접 피어링을 설정해야 합니다.
* Microsoft는 직접 피어링을 위해 최소 2GB가 필요합니다. 상호 합의된 각 피어링 위치는 장애 조치(failover) 시나리오 동안 피어링이 현지화된 상태로 유지되도록 하는 장애 조치(failover)를 지원해야 합니다. 

## <a name="next-steps"></a>다음 단계

* Microsoft와의 직접 피어링을 설정하는 단계에 대한 자세한 내용은 [직접 피어링 연습](walkthrough-direct-all.md)을 참조하세요.
* Microsoft와의 교환 피어링을 설정하는 단계에 대한 자세한 내용은 [교환 피어링 연습](walkthrough-exchange-all.md)을 참조하세요.
