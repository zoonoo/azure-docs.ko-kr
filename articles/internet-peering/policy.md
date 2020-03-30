---
title: 마이크로소프트 피어링 정책
titleSuffix: Azure
description: 마이크로소프트 피어링 정책
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775226"
---
# <a name="peering-policy"></a>피어링 정책
네트워크에서 Microsoft의 일반적인 요구 사항은 아래 섹션에 설명되어 있습니다. 이러한 요청은 직접 피어링 및 Exchange 피어링 요청 모두에 적용됩니다.

## <a name="technical-requirements"></a>기술적인 요구 사항

* 혼잡 없이 트래픽을 교환할 수 있는 충분한 용량을 갖춘 완전히 중복된 네트워크입니다.
* 피어는 공개적으로 라우팅 가능한 자율 시스템 번호(ASN)를 갖습니다.
* IPv4와 IPv6모두 지원되며 Microsoft는 각 피어링 위치에서 두 유형의 세션을 설정할 것으로 예상합니다.
* MD5는 지원되지 않습니다.
* **ASN 세부 정보:**
    * Microsoft는 AS8075와 함께 AS8068, AS8069, AS12076을 관리합니다. AS8075 피어링이 있는 ASN의 전체 목록은 AS-SET MICROSOFT를 참조하십시오.
    * Microsoft를 피어링하는 모든 당사자는 어떠한 경우에도 AS12076(익스프레스 루트)의 경로를 수락하지 않을 것에 동의하며 모든 피어에서 AS12076을 필터링해야 합니다.
* **라우팅 정책:**
    * 피어는 적어도 하나의 공개적으로 라우팅 /24를 갖습니다.
    * Microsoft는 수신된 다중 종료 판별자(MED)를 덮어씁니다.
    * Microsoft는 경로 출처를 나타내기 위해 동료로부터 BGP 커뮤니티 태그를 받는 것을 선호합니다.
    * 피어는 필터링을 위해 공용 인터넷 라우팅 레지스트리(IRR) 데이터베이스에 경로를 등록해야 하며 이 정보를 최신 상태로 유지하기 위해 선의의 노력을 기울일 것입니다.
    * Microsoft의 피어링 세션에서 피어링 세션에서 피어링 접두사를 1000(IPv4) 및 100(IPv6) 경로의 최대 접두사를 설정하는 것이 좋습니다.
    * 사전에 구체적으로 합의하지 않는 한 피어는 Microsoft와 피어를 연결하는 모든 위치에서 일관된 경로를 발표할 것으로 예상됩니다.
    * 일반적으로 AS8075를 사용하여 피어링 세션은 모든 AS-MICROSOFT 경로를 광고합니다. 아프리카와 아시아의 AS8075 상호 연결은 해당 지역과 관련된 경로로 제한될 수 있습니다.
    * 어느 당사자도 정적 경로, 마지막 수단의 경로를 설정하거나 BGP를 통해 발표되지 않은 경로에 대해 상대방에게 트래픽을 전송하지 않습니다.
    * 피어는 경로 보안을 위해 [MANRS](https://www.manrs.org/) 업계 표준을 준수해야 합니다.

## <a name="operational-requirements"></a>운영 요구 사항
* 모든 기술 및 성능 문제, 보안 위반, 서비스 거부 공격 또는 피어 또는 고객 내에서 발생하는 기타 악용 문제를 해결하는 데 도움을 줄 수 있는 완전 직원으로 구성된 NOC(네트워크 운영 센터)입니다.
* 피어링DB에는 회사 도메인및 전화 번호의 24x7 NOC 이메일을 포함하여 [피어링DB에](https://www.peeringdb.com) 대한 완전하고 최신 프로필이 있을 것으로 예상됩니다. 당사는 이 정보를 사용하여 NOC 정보, 기술 연락처 정보 및 피어링 시설 등에서피어의 존재와 같은 피어의 세부 정보를 검증합니다. 개인 야후, Gmail 및 핫메일 계정은 허용되지 않습니다.

## <a name="physical-connection-requirements"></a>물리적 연결 요구 사항
* 직접 피어링 또는 Exchange 피어링을 위해 Microsoft와 연결할 수 있는 위치는 [PeeringDB에](https://www.peeringdb.com/net/694) 나열됩니다.
* **교환 피어링:**
    * 상호 연결은 적절한 10Gbps 광학을 사용하여 단일 모드 파이버 이상이어야 합니다.
    * 피어는 최대 사용률이 50%를 초과하면 포트를 업그레이드해야 합니다.
* **직접 피어링:**
    * 상호 연결은 적절한 10Gbps 또는 100Gbps 광학을 사용하여 단일 모드 파이버 이상이어야 합니다.
    * Microsoft는 ISP 또는 네트워크 서비스 공급자와의 직접 피어링만 설정합니다.
    * 피어는 최대 사용률이 50%를 초과할 때 포트를 업그레이드하고 각 지하철에서 단일 위치 또는 지하철의 여러 위치에서 다양한 용량을 유지해야 합니다.
    * 각 직접 피어링은 피어의 가장자리에 있는 피어의 라우터에서 두 개의 Microsoft 에지 라우터에 대한 두 개의 연결로 구성됩니다. Microsoft는 이러한 연결에서 이중 BGP 세션이 필요합니다. 피어는 마지막에 중복 장치를 배포하지 않도록 선택할 수 있습니다.

## <a name="traffic-requirements"></a>트래픽 요구 사항
* Exchange 피어링을 초과하는 피어는 최소 200Mb의 트래픽과 2Gb 미만이어야 합니다.  2Gb Direct 피어링을 초과하는 트래픽의 경우 검토해야 합니다.
* 직접 피어링의 경우 네트워크에서 Microsoft로의 트래픽이 최소 요구 사항 미만을 충족해야 합니다.

    | 지역                      | 마이크로 소프트에 최소 트래픽   |
    | :----------------------- |:-------------------------------|
    | 아프리카                   | 500Mbps                       |
    | APAC(인도 제외)      |   2Gbps                       |
    | APAC(인도만)        | 500Mbps                       |
    | 유럽                   |   2Gbps                       |
    | 라탐 (것)들                    |   2Gbps                       |
    | 중동              | 500Mbps                       |
    | 해당 없음                       |   2Gbps                       |

* **다양성:**
    * NA, 유럽, APAC 및 LATAM에서는 가능한 경우 최소 3개의 지리적으로 다양한 위치에서 상호 연결하고 각 지하철 내에서 트래픽 장애 조치(failover)를 허용할 수 있도록 다양한 용량을 유지합니다.
    * 아프리카, 중동 및 인도에서는 가능한 한 다양한 위치에서 상호 연결됩니다. 지역에 트래픽이 유지되도록 충분한 다양한 용량을 유지해야 합니다.

## <a name="next-steps"></a>다음 단계

* Microsoft를 사용하여 직접 피어링을 설정하는 단계에 대해 알아보려면 [직접 피어링 연습을](walkthrough-direct-all.md)따르십시오.
* Microsoft를 사용하여 Exchange 피어링을 설정하는 단계에 대해 알아보려면 [Exchange 피어링 단계를](walkthrough-exchange-all.md)따릅니다.