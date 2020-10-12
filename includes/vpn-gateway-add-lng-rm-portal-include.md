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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025219"
---
1. 포털에서 **+리소스 만들기**를 클릭합니다.
2. 검색 상자에 **로컬 네트워크 게이트웨이**를 입력한 후 **Enter**를 눌러서 검색합니다. 그러면 결과 목록이 반환됩니다. **로컬 네트워크 게이트웨이**를 클릭한 다음, **만들기** 단추를 클릭하여 **로컬 네트워크 게이트웨이 만들기** 페이지를 엽니다.

   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-ip.png" alt-text="로컬 네트워크 게이트웨이 만들기":::

3. **로컬 네트워크 게이트웨이 만들기 블레이드**에서 로컬 네트워크 게이트웨이의 값을 지정합니다.

   - **이름:** 로컬 네트워크 게이트웨이 개체의 이름을 지정합니다.
   - **엔드포인트:** 온-프레미스 VPN 디바이스의 엔드포인트 유형(**IP 주소** 또는 **FQDN(정규화된 도메인 이름)** )을 선택합니다.
      - **IP 주소**: VPN 디바이스에 대해 인터넷 서비스 공급자로부터 할당된 고정 공용 IP 주소가 있는 경우 IP 주소 옵션을 선택하고 예제에 표시된 대로 IP 주소를 입력합니다. 이는 Azure VPN 게이트웨이를 연결하려는 VPN 디바이스의 공용 IP 주소입니다. IP 주소가 지금 당장 없는 경우 예제에 표시된 값을 사용할 수 있지만 다시 이동해서 VPN 디바이스의 공용 IP 주소로 자리 표시자 IP 주소를 바꿔야 합니다. 그렇지 않으면 Azure를 연결할 수 없습니다.
      - **FQDN**: 일반적으로 인터넷 서비스 공급자에 의해 결정되는 특정 기간 후에 변경될 수 있는 동적 공용 IP 주소가 있는 경우 동적 DNS 서비스와 함께 상수 DNS 이름을 사용하여 VPN 디바이스의 현재 공용 IP 주소를 가리킬 수 있습니다. Azure VPN 게이트웨이는 FQDN을 확인하여 연결할 공용 IP 주소를 결정합니다. 아래 스크린샷은 IP 주소 대신 FQDN을 사용하는 예를 보여줍니다.
   - **주소 공간** 은 이 로컬 네트워크가 나타내는 네트워크에 대한 주소 범위를 가리킵니다. 주소 공간 범위를 여러 개 추가할 수 있습니다. 여기에서 지정한 범위가 연결하려는 다른 네트워크의 범위와 겹치지 않는지 확인합니다. Azure는 지정한 주소 범위를 온-프레미스 VPN 디바이스 IP 주소에 라우팅합니다. *온-프레미스 사이트에 연결하려는 경우 예제에 표시된 값이 아니라 자체 값을 여기에 사용합니다*.
   - **BGP 설정 구성:** BGP를 구성할 경우에만 사용합니다. 그렇지 않으면 이것을 선택하지 마십시오.
   - **구독:** 올바른 구독이 표시되는지 확인합니다.
   - **리소스 그룹:** 사용하려는 리소스 그룹을 선택합니다. 새 리소스 그룹을 만들거나 이미 만든 리소스 그룹을 선택할 수 있습니다.
   - **위치:** 이 개체가 생성 될 위치를 선택 합니다. VNet이 있는 동일한 위치를 선택하는 것이 좋지만 그렇게 하지 않아도 됩니다.

    이는 동일한 페이지이지만, FQDN이 강조 표시됩니다.
   
   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-fqdn.png" alt-text="로컬 네트워크 게이트웨이 만들기":::
   
   > [!NOTE]
   >
   > * Azure VPN은 각 FQDN에 대해 하나의 IPv4 주소만 지원합니다. 도메인 이름이 여러 IP 주소로 확인되는 경우 Azure VPN Gateway는 DNS 서버에서 반환한 첫 번째 IP 주소를 사용합니다. 불확실성을 제거 하려면 FQDN이 항상 단일 IPv4 주소로 확인 하는 것이 좋습니다. IPv6은 지원되지 않습니다.
   > * Azure VPN Gateway는 5분마다 새로 고쳐지는 DNS 캐시를 유지 관리합니다. 연결되지 않은 터널에 대해서만 게이트웨이가 FQDN을 확인하려고 합니다. 게이트웨이를 재설정하면 FQDN도 확인됩니다.
   >

4. 값 지정을 완료 한 후 **만들기** 단추를 선택 하 여 게이트웨이를 만듭니다.
