---
title: Azure Express 경로 직접 정보
description: 'Azure Express 경로 다이렉트의 주요 기능 및 Express 경로 직접에 등록 하는 데 필요한 정보 (예: 사용 가능한 Sku, 기술 요구 사항)에 대해 알아봅니다.'
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: duau
ms.openlocfilehash: 4b129a218f0fe90f632adef1325288b3f8d97d16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585964"
---
# <a name="about-expressroute-direct"></a>ExpressRoute Direct 정보

Express 경로 다이렉트는 전 세계에 분산 된 피어 링 위치에서 Microsoft의 글로벌 네트워크에 직접 연결 하는 기능을 제공 합니다. Express 경로 다이렉트는 규모에 맞게 활성/활성 연결을 지 원하는 이중 100 Gbps 또는 10gbps 연결을 제공 합니다.

ExpressRoute Direct가 제공하는 주요 기능은 다음을 포함하지만 제한되지 않습니다.

* 스토리지 및 Cosmos DB와 같은 서비스로 대규모 데이터 수집
* 은행, 정부, 소매업체 등 규제가 적용되며 전용 격리 연결을 사용해야 하는 업계의 물리적 격리
* 비즈니스 단위에 따라 세부적으로 회로 배포 제어

## <a name="onboard-to-expressroute-direct"></a>Express 경로 직접에 등록

Express 경로 다이렉트를 사용 하기 전에 먼저 구독을 등록 해야 합니다. 등록 하려면 Azure PowerShell을 사용 하 여 다음 명령을 실행 합니다.

1.  Azure에 로그인 하 고 등록 하려는 구독을 선택 합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. 다음 명령을 사용 하 여 공개 미리 보기에 대 한 구독을 등록 합니다.
1. 
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

등록 한 후에는 **Microsoft 네트워크** 리소스 공급자가 구독에 등록 되어 있는지 확인 합니다. 리소스 공급자를 등록하면 구독이 리소스 공급자에서 작동하도록 구성됩니다.

1. [Azure 리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md)에 설명 된 대로 구독 설정에 액세스 합니다.

1. 구독에서 **리소스 공급자** 에 대해 **Microsoft 네트워크** 공급자가 **등록 된** 상태를 표시 하는지 확인 합니다. Microsoft. 네트워크 리소스 공급자가 등록 된 공급자 목록에 없는 경우 추가 합니다.

Express 경로 직접 사용을 시작 하 고 선택한 피어 링 위치에 사용할 수 있는 포트가 없음을 확인 하는 경우 ExpressRouteDirect@microsoft.com 더 많은 인벤토리를 요청 하는 전자 메일을 보냅니다.

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>서비스 공급자 및 ExpressRoute Direct를 사용하는 ExpressRoute

| **서비스 공급자를 사용하는 ExpressRoute** | **ExpressRoute Direct** | 
| --- | --- |
| 서비스 공급자를 사용 하 여 빠른 온 보 딩 및 기존 인프라에 대 한 연결을 설정 합니다. | 모든 계층의 100 Gbps/10 Gbps 인프라 및 전체 관리 필요
| 이더넷 및 MPLS를 포함하는 수백 개의 공급자와 통합 | 규제 산업 및 대규모 데이터 수집에 대한 직접/전용 용량 |
| 50Mbps - 10Gbps의 회로 SKU | 고객은 100-Gbps Express 경로 다이렉트에서 다음 회로 Sku의 조합을 선택할 수 있습니다. <ul><li>5Gbps</li><li>10Gbps</li><li>40Gbps</li><li>100Gbps</li></ul> 고객은 10gbps Express 경로 다이렉트에서 다음 회로 Sku의 조합을 선택할 수 있습니다.<ul><li>1Gbps</li><li>2Gbps</li><li>5Gbps</li><li>10Gbps</li></ul>
| 단일 테넌트에 최적화됨 | 여러 사업부 및 여러 작업 환경에서 단일 테 넌 트에 대해 최적화 됨

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct 회로

Microsoft Azure ExpressRoute를 사용 하면 연결 공급자가 쉽게 만든 개인 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. Express 경로를 사용 하 여 Microsoft Azure 및 Microsoft 365와 같은 Microsoft 클라우드 서비스에 대 한 연결을 설정할 수 있습니다.

각 피어 링 위치는 Microsoft의 글로벌 네트워크에 액세스할 수 있으며 기본적으로 지정 학적 영역에 있는 모든 지역에 액세스할 수 있습니다. 프리미엄 회로를 사용 하 여 모든 글로벌 지역에 액세스할 수 있습니다.  

대부분의 시나리오에서 기능은 Express 경로 서비스 공급자를 사용 하 여 작동 하는 회로와 동일 합니다. 추가 세분성 및 ExpressRoute Direct를 사용하여 제공되는 새로운 기능을 지원하기 위해 ExpressRoute Direct 회로에 존재하는 특정 키 기능이 있습니다.

## <a name="circuit-skus"></a>회로 SKU

ExpressRoute Direct는 Azure Storage 및 기타 빅 데이터 서비스에 대규모 데이터 수집 시나리오를 지원합니다. 100-Gbps Express 경로 다이렉트의 Express 경로 회로는 이제 **40 Gbps** 및 * * 100-Gbps 회로 sku도 지원 합니다. 실제 포트 쌍은 **100 gbps 또는 10gbps** 전용 이며 여러 가상 회로를 가질 수 있습니다. 회로 크기:

| **100-Gbps Express 경로 직접** | **10gbps Express 경로 직접** | 
| --- | --- |
| **구독 된 대역폭**: 200 Gbps | **구독 된 대역폭**: 20gbps |
| <ul><li>5Gbps</li><li>10Gbps</li><li>40Gbps</li><li>100Gbps</li></ul> | <ul><li>1Gbps</li><li>2Gbps</li><li>5Gbps</li><li>10Gbps</li></ul>

## <a name="technical-requirements"></a>기술적인 요구 사항

* Microsoft Enterprise Edge Router (MSEE) 인터페이스:
    * 이중 10 기가 비트 또는 100-기가 비트 이더넷 포트만 라우터 쌍 간
    * 단일 모드 LR 파이버 연결
    * IPv4 및 IPv6
    * IP MTU 1500 바이트

* 스위치/라우터 계층 2/계층 3 개 연결:
    * 1 802.1 Q (Dot1Q) 태그 또는 2 개의 Tag 802.1 Q (QinQ) 태그 캡슐화를 지원 해야 합니다.
    * Ethertype = 0x8100
    * Microsoft에서 지정한 VLAN ID에 따라 외부 VLAN 태그 (STAG)를 추가 해야 합니다 ( *QinQ에만 해당* ).
    * 포트 및 장치당 여러 BGP 세션 (Vlan)을 지원 해야 합니다.
    * IPv4 및 IPv6 연결 I p v 6 *의 경우 추가 하위 인터페이스가 생성 되지 않습니다. IPv6 주소가 기존 하위 인터페이스에 추가* 됩니다. 
    * 선택 사항: [BFD (양방향 전달 검색)](./expressroute-bfd.md) 지원: express 경로 회로의 모든 개인 피어 링에 대해 기본적으로 구성 됩니다.

## <a name="vlan-tagging"></a>VLAN 태그 지정

ExpressRoute Direct는 QinQ 및 Dot1Q VLAN 태그 지정을 지원합니다.

* **QinQ VLAN 태그 지정** 은 ExpressRoute 회로 기준당 격리된 라우팅 도메인을 허용합니다. Azure는 회로를 만들 때 S 태그를 동적으로 제공 하며 변경할 수 없습니다. 회로의 각 피어 링 (개인 및 Microsoft)은 고유한 C 태그를 VLAN으로 사용 합니다. C # 태그는 Express 경로 직접 포트의 회로에서 고유할 필요가 없습니다.

* **Dot1Q VLAN 태그 지정** 은 각 ExpressRoute Direct 포트 쌍 기준당 단일 태그가 지정된 VLAN을 허용합니다. 피어링에 사용되는 C 태그는 ExpressRoute Direct 포트 쌍의 모든 회로 및 피어링에서 고유해야 합니다.

## <a name="workflow"></a>워크플로

[![워크플로](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct는 Microsoft 글로벌 네트워크에 대한 활성/활성 중복 연결과 동일한 엔터프라이즈급 SLA를 제공합니다. Express 경로 인프라는 중복 되며 Microsoft 글로벌 네트워크에 대 한 연결은 중복 되 고 다양 하며 고객 요구 사항에 따라 올바르게 확장 됩니다. 

## <a name="next-steps"></a>다음 단계

[ExpressRoute Direct 구성](expressroute-howto-erdirect.md)
