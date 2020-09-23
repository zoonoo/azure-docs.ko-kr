---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f708052e6c1b474d1447a352d10eb1defe311fc4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025219"
---
1. 포털에서 **+리소스 만들기**를 클릭합니다.
2. 검색 상자에 **로컬 네트워크 게이트웨이**를 입력한 후 **Enter**를 눌러서 검색합니다. 그러면 결과 목록이 반환됩니다. **로컬 네트워크 게이트웨이**를 클릭한 다음, **만들기** 단추를 클릭하여 **로컬 네트워크 게이트웨이 만들기** 페이지를 엽니다.

   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-ip.png" alt-text="로컬 네트워크 게이트웨이 만들기":::

3. **로컬 네트워크 게이트웨이 만들기 블레이드**에서 로컬 네트워크 게이트웨이의 값을 지정합니다.

   - **이름:** 로컬 네트워크 게이트웨이 개체의 이름을 지정합니다.
   - **끝점:** 온-프레미스 VPN 장치 **IP 주소** 또는 **FQDN (정규화 된 도메인 이름)** 에 대 한 끝점 유형을 선택 합니다.
      - **Ip 주소**: VPN 장치에 대 한 인터넷 서비스 공급자에서 고정 공용 ip 주소를 할당 한 경우 ip 주소 옵션을 선택 하 고 예제에 표시 된 대로 ip 주소를 입력 합니다. Azure VPN gateway를 연결 하려는 VPN 장치의 공용 IP 주소입니다. IP 주소가 지금 당장 없는 경우 예제에 표시된 값을 사용할 수 있지만 다시 이동해서 VPN 디바이스의 공용 IP 주소로 자리 표시자 IP 주소를 바꿔야 합니다. 그렇지 않으면 Azure를 연결할 수 없습니다.
      - **FQDN**: 특정 기간이 지난 후 변경 될 수 있는 동적 공용 ip 주소 (일반적으로 인터넷 서비스 공급자에 의해 결정 됨)가 있는 경우 동적 dns 서비스에서 상수 dns 이름을 사용 하 여 VPN 장치의 현재 공용 ip 주소를 가리킬 수 있습니다. Azure VPN gateway에서 FQDN을 확인 하 여 연결할 공용 IP 주소를 확인 합니다. 아래 스크린샷에서는 IP 주소 대신 FQDN을 사용 하는 예를 보여 줍니다.
   - **주소 공간** 은 이 로컬 네트워크가 나타내는 네트워크에 대한 주소 범위를 가리킵니다. 주소 공간 범위를 여러 개 추가할 수 있습니다. 여기에서 지정한 범위가 연결하려는 다른 네트워크의 범위와 겹치지 않는지 확인합니다. Azure는 지정한 주소 범위를 온-프레미스 VPN 디바이스 IP 주소에 라우팅합니다. *온-프레미스 사이트에 연결하려는 경우 예제에 표시된 값이 아니라 자체 값을 여기에 사용합니다*.
   - **BGP 설정 구성:** BGP를 구성할 경우에만 사용합니다. 그렇지 않으면 이것을 선택하지 마십시오.
   - **구독:** 올바른 구독이 표시되는지 확인합니다.
   - **리소스 그룹:** 사용하려는 리소스 그룹을 선택합니다. 새 리소스 그룹을 만들거나 이미 만든 리소스 그룹을 선택할 수 있습니다.
   - **위치:** 이 개체가 생성 될 위치를 선택 합니다. VNet이 있는 동일한 위치를 선택하는 것이 좋지만 그렇게 하지 않아도 됩니다.

    이 페이지는 동일 하지만 FQDN이 강조 표시 되어 있습니다.
   
   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-fqdn.png" alt-text="로컬 네트워크 게이트웨이 만들기-FQDN":::
   
   > [!NOTE]
   >
   > * Azure VPN은 각 FQDN에 대해 IPv4 주소를 하나만 지원 합니다. 도메인 이름이 여러 IP 주소로 확인 되는 경우 Azure VPN Gateway는 DNS 서버에서 반환 된 첫 번째 IP 주소를 사용 합니다. 불확실성을 제거 하려면 FQDN이 항상 단일 IPv4 주소로 확인 하는 것이 좋습니다. IPv6은 지원되지 않습니다.
   > * Azure VPN Gateway는 5 분 마다 새로 고쳐진 DNS 캐시를 유지 관리 합니다. 게이트웨이가 연결 되지 않은 터널에 대해서만 Fqdn을 확인 하려고 합니다. 게이트웨이를 다시 설정 하면 FQDN 확인도 트리거됩니다.
   >

4. 값 지정을 완료 한 후 **만들기** 단추를 선택 하 여 게이트웨이를 만듭니다.
