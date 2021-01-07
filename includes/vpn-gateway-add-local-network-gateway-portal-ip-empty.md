---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c538c510eeafacd1596fdeb1b4593919e6236cf0
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487088"
---
1. [Azure Portal](https://portal.azure.com)의 **리소스, 서비스 및 문서 검색 (G +/)** 에서 **로컬 네트워크 게이트웨이**를 입력 합니다. 검색 결과의 **Marketplace** 에서 **로컬 네트워크 게이트웨이** 를 찾아 선택 합니다. **로컬 네트워크 게이트웨이 만들기** 페이지가 열립니다.
1. **로컬 네트워크 게이트웨이 만들기 블레이드**에서 로컬 네트워크 게이트웨이의 값을 지정합니다.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-ip-empty/create-ip.png" alt-text="IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 만들기":::

   * **이름:** 로컬 네트워크 게이트웨이 개체의 이름을 지정합니다.
   * **엔드포인트:** 온-프레미스 VPN 디바이스의 엔드포인트 유형(**IP 주소** 또는 **FQDN(정규화된 도메인 이름)** )을 선택합니다.
      * **IP 주소**: VPN 디바이스에 대해 인터넷 서비스 공급자로부터 할당된 고정 공용 IP 주소가 있는 경우 IP 주소 옵션을 선택하고 예제에 표시된 대로 IP 주소를 입력합니다. 이는 Azure VPN 게이트웨이를 연결하려는 VPN 디바이스의 공용 IP 주소입니다. IP 주소가 지금 당장 없는 경우 예제에 표시된 값을 사용할 수 있지만 다시 이동해서 VPN 디바이스의 공용 IP 주소로 자리 표시자 IP 주소를 바꿔야 합니다. 그렇지 않으면 Azure를 연결할 수 없습니다.
   * **주소 공간** 은 이 로컬 네트워크가 나타내는 네트워크에 대한 주소 범위를 가리킵니다. 주소 공간 범위를 여러 개 추가할 수 있습니다. 여기에서 지정한 범위가 연결하려는 다른 네트워크의 범위와 겹치지 않는지 확인합니다. Azure는 지정한 주소 범위를 온-프레미스 VPN 디바이스 IP 주소에 라우팅합니다. *온-프레미스 사이트에 연결하려는 경우 예제에 표시된 값이 아니라 자체 값을 여기에 사용합니다*.
   * **BGP 설정 구성:** BGP를 구성할 경우에만 사용합니다. 그렇지 않으면 이것을 선택하지 마십시오.
   * **구독:** 올바른 구독이 표시되는지 확인합니다.
   * **리소스 그룹:** 사용하려는 리소스 그룹을 선택합니다. 새 리소스 그룹을 만들거나 이미 만든 리소스 그룹을 선택할 수 있습니다.
   * **위치:** 위치는 다른 설정의 **지역**과 동일합니다. 이 개체를 만들 위치를 선택합니다. VNet이 있는 동일한 위치를 선택하는 것이 좋지만 그렇게 하지 않아도 됩니다.

1. 값을 지정하는 작업을 완료하면 페이지 맨 아래에서 **만들기** 단추를 선택하여 로컬 네트워크 게이트웨이를 만듭니다.
