---
title: 게이트웨이에 대 한 VPN NAT 규칙 구성
titleSuffix: Azure Virtual WAN
description: VWAN VPN gateway에 대 한 NAT 규칙을 구성 하는 방법 알아보기
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6fbee31f015953bd7e65648ea273e3ca84686115
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431219"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>가상 WAN VPN gateway에 대 한 NAT 규칙 구성-미리 보기

> [!IMPORTANT]
> NAT 규칙은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

정적 일대일 NAT 규칙을 사용 하 여 가상 WAN VPN gateway를 구성할 수 있습니다. NAT 규칙은 IP 주소의 일 대 일 변환을 설정 하는 메커니즘을 제공 합니다. NAT는 호환 되지 않거나 겹치는 IP 주소를 가진 두 개의 IP 네트워크를 상호 연결 하는 데 사용할 수 있습니다. 일반적인 시나리오는 Azure VNet 리소스에 액세스 하려는 겹치는 Ip가 있는 분기입니다.

이 구성은 외부 (호스트) IP 주소에서 가상 네트워크 (가상 머신, 컴퓨터, 컨테이너 등) 내의 끝점과 연결 된 내부 IP 주소로 트래픽을 라우팅하는 흐름 테이블을 사용 합니다.

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="아키텍처를 보여 주는 다이어그램.":::

## <a name="configure-nat-rules"></a><a name="rules"></a>NAT 규칙 구성

언제 든 지 VPN gateway 설정에서 NAT 규칙을 구성 하 고 볼 수 있습니다.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="편집 규칙을 보여 주는 스크린샷":::

1. 가상 허브로 이동 합니다.
1. **VPN (사이트 간)을** 선택 합니다.
1. **NAT 규칙 (편집)** 을 선택 합니다.
1. **Nat 규칙 편집** 페이지에서 다음 값을 사용 하 여 nat 규칙을 **추가/편집/삭제할** 수 있습니다.

   * **이름:** NAT 규칙에 대 한 고유 이름입니다.
   * **유형:** 정적인. 정적 일대일 NAT는 내부 주소와 외부 주소 간에 일 대 일 관계를 설정 합니다.
   * **모드:** IngressSnat 또는는  
      * IngressSnat 모드 (수신 원본 NAT 라고도 함)는 Azure 허브의 사이트 간 VPN gateway를 시작 하는 트래픽에 적용 됩니다.
      * Azure 허브의 사이트 간 VPN gateway를 종료 하는 트래픽에는 (송신 원본 NAT 라고도 함)가 적용 됩니다.
   * **Internalmapping:** 외부 Ip 집합에 매핑될 내부 네트워크에 있는 원본 Ip의 주소 접두사 범위입니다. 즉, 사전 NAT 주소 접두사 범위입니다.
   * **Externalmapping:** 원본 Ip가 매핑될 외부 네트워크에 있는 대상 Ip의 주소 접두사 범위입니다. 즉, 사후 NAT 주소 접두사 범위가 있습니다.
   * **연결 연결:** VPN 사이트를 Azure 허브의 사이트 간 VPN gateway에 가상으로 연결 하는 연결 리소스입니다.

### <a name="configuration-considerations"></a><a name="considerations"></a>구성 고려 사항

* 정적 일 대 일 NAT의 경우 내부 매핑과 외부 매핑의 서브넷 크기가 동일 해야 합니다.
* ' 개인 주소 공간 ' 필드에 **Externalmapping** 접두사를 추가 하려면 AZURE PORTAL에서 VPN 사이트를 편집 해야 합니다. 현재 BGP를 사용 하는 사이트에서는 온-프레미스 BGP announcer (장치 BGP 설정)에 외부 매핑 접두사에 대 한 항목이 포함 되어 있는지 확인 해야 합니다.

## <a name="examples-and-verification"></a><a name="examples"></a>예 및 확인

### <a name="ingress-mode-nat"></a>수신 모드 NAT

수신 모드 NAT 규칙은 가상 WAN 사이트 간 VPN gateway를 통해 Azure를 입력 하는 패킷에 적용 됩니다. 이 시나리오에서는 두 사이트 간 VPN 분기를 Azure에 연결 하려고 합니다. VPN 사이트 1은 Link1을 통해 연결 되 고, VPN 사이트 2는 링크 2를 통해 연결 됩니다. 각 사이트에는 192.169.1.0/24 주소 공간이 있습니다.

다음 다이어그램에서는 예상 되는 최종 결과를 보여 줍니다.

:::image type="content" source="./media/nat-rules-vpn-gateway/ingress.png" alt-text="수신 모드 NAT를 보여 주는 다이어그램":::

1. NAT 규칙을 지정 하십시오.

   사이트 간 VPN 게이트웨이가 겹치는 주소 공간 (예: 192.168.1.0/24)을 사용 하는 두 분기를 구분할 수 있도록 NAT 규칙을 지정 합니다. 이 예제에서는 VPN 사이트 1에 대 한 Link1에 초점을 둡니다.

   다음 NAT 규칙을 설정 하 고 분기 중 하나의 링크 1에 연결할 수 있습니다. 이는 정적 NAT 규칙 이므로 InternalMapping 및 ExternalMapping의 주소 공간에 동일한 수의 IP 주소가 포함 되어 있습니다.

   * **이름:** IngressRule01
   * **유형:** 정적인
   * **모드:** IngressSnat
   * **Internalmapping:** 192.168.1.0/24
   * **Externalmapping:** 10.1.1.0/24
   * **연결 연결:** 링크 1

1. 올바른 ExternalMapping을 보급 합니다.

   이 단계에서는 사이트 간 VPN 게이트웨이가 올바른 ExternalMapping 주소 공간을 나머지 Azure 리소스에 보급 하는지 확인 합니다. BGP를 사용 하는지 여부에 따라 다른 지침이 있습니다.

   **예제 1: BGP 사용**

   * VPN 사이트 1에 있는 온-프레미스 BGP 스피커가 10.1.1.0/24 주소 공간을 알리도록 구성 되어 있는지 확인 합니다.
   * 이 미리 보기 중에 BGP를 사용 하는 사이트에서 온-프레미스 BGP announcer (장치 BGP 설정)에 외부 매핑 접두사에 대 한 항목이 포함 되어 있는지 확인 해야 합니다.

   **예 2: BGP를 사용할 수 없음**

   * 사이트 간 VPN gateway가 포함 된 가상 허브 리소스로 이동 합니다. 가상 허브 페이지의 **연결** 에서 **VPN (사이트 간)** 을 선택 합니다.
   * 링크 1을 통해 가상 WAN 허브에 연결 된 VPN 사이트를 선택 합니다. **사이트 편집** 을 선택 하 고 VPN 사이트의 개인 주소 공간으로 10.1.1.0/24를 입력 합니다.

     :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site.png" alt-text="VPN 사이트 편집 페이지를 보여 주는 스크린샷":::

### <a name="packet-flow"></a>패킷 흐름

이 예제에서 온-프레미스 장치는 스포크 가상 네트워크에 연결 하려고 합니다. 패킷 흐름은 다음과 같습니다. NAT 변환은 굵게 표시 됩니다.

1. 온-프레미스의 트래픽이 시작 됩니다.
   * 원본 IP 주소: **192.168.1.1**
   * 대상 IP 주소: 30.0.0.1
1. 트래픽은 사이트 간 게이트웨이로 들어오고 NAT 규칙을 사용 하 여 변환 된 다음 스포크로 전송 됩니다.
   * 원본 IP 주소: **10.1.1.1**
   * 대상 IP 주소: 30.0.0.1
1. 스포크에서 회신이 시작 됩니다.
   * 원본 IP 주소: 30.0.0.1
   * 대상 IP 주소: **10.1.1.1**
1. 트래픽이 사이트 간 VPN gateway에 들어가면 이동 하 여 온-프레미스로 전송 됩니다.
   * 원본 IP 주소: 30.0.0.1
   * 대상 IP 주소: **192.168.1.1**

### <a name="verification-checks"></a>확인 확인

이 섹션에서는 구성이 제대로 설정 되었는지 확인 하는 검사를 보여 줍니다.

#### <a name="validate-defaultroutetable-rules-and-routes"></a>DefaultRouteTable, 규칙 및 경로 유효성 검사

**DefaultRouteTable** 에 연결 되는 가상 WAN의 분기는 모든 분기 연결이 DefaultRouteTable 내에서 채워지는 경로를 학습 합니다. DefaultRouteTable의 유효 경로에 외부 매핑 접두사를 포함 하는 NAT 규칙이 표시 됩니다.

예제:

* **접두사:** 10.1.1.0/24  
* **다음 홉 유형:** VPN_S2S_Gateway
* **다음 홉:** VPN_S2S_Gateway 리소스

#### <a name="validate-address-prefixes"></a>주소 접두사 유효성 검사

이 예제는 DefaultRouteTable에 연결 된 가상 네트워크의 리소스에 적용 됩니다.

가상 WAN 허브에 연결 된 스포크 가상 네트워크에 있는 모든 가상 컴퓨터의 NIC (네트워크 인터페이스 카드)의 **유효 경로** 에는 NAT 규칙 **externalmapping** 의 주소 접두사도 포함 되어야 합니다.

#### <a name="validate-bgp-advertisements"></a>BGP 광고 유효성 검사

VPN 사이트 연결에 BGP가 구성 되어 있는 경우 온-프레미스 BGP 스피커를 확인 하 여 외부 매핑 접두사에 대 한 항목을 광고 하는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

사이트 간 구성에 대 한 자세한 내용은 [가상 WAN 사이트 간 연결 구성](virtual-wan-site-to-site-portal.md)을 참조 하세요.
