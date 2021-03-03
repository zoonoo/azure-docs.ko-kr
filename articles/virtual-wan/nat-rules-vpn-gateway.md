---
title: 게이트웨이에 대 한 VPN NAT 규칙 구성
titleSuffix: Azure Virtual WAN
description: VWAN VPN gateway에 대 한 NAT 규칙을 구성 하는 방법 알아보기
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/17/2021
ms.author: cherylmc
ms.openlocfilehash: a31b3718eb1baa32aef39474383924efe8cf93b6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745868"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>가상 WAN VPN gateway에 대 한 NAT 규칙 구성-미리 보기

> [!IMPORTANT]
> NAT 규칙은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

정적 일대일 NAT 규칙을 사용 하 여 가상 WAN VPN gateway를 구성할 수 있습니다. NAT 규칙은 IP 주소의 일 대 일 변환을 설정 하는 메커니즘을 제공 합니다. NAT는 호환 되지 않거나 겹치는 IP 주소를 가진 두 개의 IP 네트워크를 상호 연결 하는 데 사용할 수 있습니다. 일반적인 시나리오는 Azure VNet 리소스에 액세스 하려는 겹치는 Ip가 있는 분기입니다.

이 구성은 외부 (호스트) IP 주소에서 가상 네트워크 (가상 머신, 컴퓨터, 컨테이너 등) 내의 끝점과 연결 된 내부 IP 주소로 트래픽을 라우팅하는 흐름 테이블을 사용 합니다.

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="아키텍처를 보여 주는 다이어그램.":::

## <a name="configure-and-view-rules"></a><a name="view"></a>규칙 구성 및 보기

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

### <a name="configuration-considerations"></a>구성 고려 사항

* 정적 일 대 일 NAT의 경우 내부 매핑과 외부 매핑의 서브넷 크기가 동일 해야 합니다.
* ' 개인 주소 공간 ' 필드에 **Externalmapping** 접두사를 추가 하려면 AZURE PORTAL에서 VPN 사이트를 편집 해야 합니다. 현재 BGP를 사용 하는 사이트에서는 온-프레미스 BGP announcer (장치 BGP 설정)에 외부 매핑 접두사에 대 한 항목이 포함 되어 있는지 확인 해야 합니다.

## <a name="next-steps"></a>다음 단계

사이트 간 구성에 대 한 자세한 내용은 [가상 WAN 사이트 간 연결 구성](virtual-wan-site-to-site-portal.md)을 참조 하세요.
