---
title: Azure ExpressRoute Direct 정보 | Microsoft Docs
description: 이 페이지는 ExpressRoute Direct의 개요를 제공합니다(미리 보기).
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: mialdrid
ms.openlocfilehash: 3b46f24edf10fee7001b35ffdd9fa218aa438e19
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959682"
---
# <a name="about-expressroute-direct-preview"></a>ExpressRoute Direct 정보(미리 보기)

ExpressRoute Direct는 고객에게 전 세계에 전략적으로 분산된 피어링 위치에서 Microsoft의 글로벌 네트워크에 직접 연결하는 기능을 제공합니다. ExpressRoute Direct는 대규모로 활성/활성 연결을 지원하는 이중 100Gbps 연결을 제공합니다.

ExpressRoute Direct가 제공하는 주요 기능은 다음을 포함하지만 제한되지 않습니다.

* Storage 및 Cosmos DB와 같은 서비스로의 대규모 데이터 수집
* 은행, 정부, 소매업체 등 규제가 적용되며 전용 격리 연결을 사용해야 하는 업계의 물리적 격리
* 비즈니스 단위에 따라 세부적으로 회로 배포 제어

> [!IMPORTANT]
> ExpressRoute Direct는 현재 미리 보기로 제공되고 있습니다.
>
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="enroll-in-the-preview"></a>미리 보기에 등록

ExpressRoute Direct를 사용하려면 먼저 미리 보기에서 구독을 등록해야 합니다. 등록하려면 다음 세부 사항을 포함하여 구독 ID로 <ExpressRouteDirect@microsoft.com>에 이메일을 전송합니다.

* **ExpressRoute Direct**를 사용하여 수행하려는 시나리오
* 위치 기본 설정 - 모든 위치의 전체 목록은 [파트너 및 피어링 위치](expressroute-locations-providers.md) 참조
* 구현을 위한 타임라인
* 기타 궁금한 점이 있는 경우

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>서비스 공급자 및 ExpressRoute Direct를 사용하는 ExpressRoute

| **서비스 공급자를 사용하는 ExpressRoute** | **ExpressRoute Direct** | 
| --- | --- |
| 서비스 공급자를 활용하여 기존 인프라로 빠른 온보딩 및 연결 활성화 | 100Gbps 인프라 및 모든 계층의 전체 관리 필요
| 이더넷 및 MPLS를 포함하는 수백 개의 공급자와 통합 | 규제 산업 및 대규모 데이터 수집에 대한 직접/전용 용량 |
| 50Mbps - 10Gbps의 회로 SKU | 고객은 5Gbps, 10Gbps, 40Gbps 100Gbps(총 200Gbps로 제한)의 회로 SKU 조합을 선택할 수 있습니다.
| 단일 테넌트에 최적화됨 | 단일 테넌트/클라우드 서비스 공급자/여러 사업부에 최적화됨

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct 회로

Microsoft Azure ExpressRoute를 사용하면 연결 공급자에서 쉽게 처리된 개인 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. ExpressRoute를 사용하면 Microsoft Azure, Office 365 및 Dynamics 365와 같은 Microsoft 클라우드 서비스에 대한 연결을 설정할 수 있습니다.  

각 피어링 위치는 Microsoft의 글로벌 네트워크에 대한 액세스 권한을 가지며 기본적으로 지리적 영역에 있는 모든 지역에 액세스할 수 있으며 프리미엄 회로를 사용하여 전 세계 모든 지역에 액세스할 수 있습니다.  

대부분의 시나리오에서 기능은 작동하기 위해 ExpressRoute 서비스 공급자를 활용하는 회로와 동일합니다. 추가 세분성 및 ExpressRoute Direct를 사용하여 제공되는 새로운 기능을 지원하기 위해 ExpressRoute Direct 회로에 존재하는 특정 키 기능이 있습니다.

## <a name="circuit-skus"></a>회로 SKU

ExpressRoute Direct는 Azure 저장소 및 기타 빅 데이터 서비스에 대규모 데이터 수집 시나리오를 지원합니다. ExpressRoute Direct의 ExpressRoute 회로는 이제 **40Gbps** 및 **100Gbps** 회로 SKU도 지원합니다.

## <a name="vlan-tagging"></a>VLAN 태그 지정

ExpressRoute Direct는 QinQ 및 Dot1Q VLAN 태그 지정을 지원합니다.

* **QinQ VLAN 태그 지정**은 ExpressRoute 회로 기준당 격리된 라우팅 도메인을 허용합니다. Azure는 회로 생성 시 동적으로 S 태그를 할당하고 이는 변경할 수 없습니다. 회로의 각 피어링(개인 및 Microsoft)은 VLAN으로 고유한 C 태그를 활용합니다. C 태그는 ExpressRoute Direct 포트의 회로에서 고유할 필요가 없습니다.

* **Dot1Q VLAN 태그 지정**은 각 ExpressRoute Direct 포트 쌍 기준당 단일 태그가 지정된 VLAN을 허용합니다. 피어링에 사용되는 C 태그는 ExpressRoute Direct 포트 쌍의 모든 회로 및 피어링에서 고유해야 합니다.

## <a name="workflow"></a>워크플로

![워크플로](./media/expressroute-erdirect-about/workflow1.png)

## <a name="sla"></a>SLA

ExpressRoute Direct는 Microsoft 글로벌 네트워크에 대한 활성/활성 중복 연결과 동일한 엔터프라이즈급 SLA를 제공합니다. ExpressRoute 인프라는 중복되며, Microsoft 글로벌 네트워크로 연결은 중복되고 다양하며 고객 요구 사항에 적절하게 크기를 조정합니다. 미리 보기 중 SLA는 없으며 비-프로덕션 워크로드만 고려해야 합니다.

## <a name="next-steps"></a>다음 단계

[ExpressRoute Direct 구성](expressroute-howto-erdirect.md)
