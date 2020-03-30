---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2cf1a2e2ab9cf2d6e35aa12b5b0f8ddc04ad0e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301953"
---
다음 단계에 따라 Resource Manager 배포 모델과 Azure Portal을 사용하여 VNet을 만들 수 있습니다. 가상 네트워크에 대한 자세한 내용은 [Virtual Network 개요](../articles/virtual-network/virtual-networks-overview.md)를 참조하세요.

>[!NOTE]
>가상 네트워크를 크로스 프레미스 아키텍처의 일부로 사용하는 경우 온-프레미스 네트워크 관리자와 협력하여 이 가상 네트워크에 특별히 사용할 수 있는 IP 주소 범위를 개척해야 합니다. VPN 연결의 양쪽 모두에 중복 주소 범위가 있는 경우 트래픽이 예기치 않은 방식으로 라우팅됩니다. 또한 이 가상 네트워크를 다른 가상 네트워크에 연결하려는 경우 주소 공간이 다른 가상 네트워크와 겹칠 수 없습니다. 이에 따라 네트워크 구성을 적절히 계획합니다.
>
>

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. **검색 리소스, 서비스 및 문서(G+/)에서** *가상 네트워크를*입력합니다.

   ![가상 네트워크 리소스 페이지 찾기](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "가상 네트워크 리소스 페이지 찾기")
1. **마켓플레이스** 결과에서 **가상 네트워크를** 선택합니다.

   ![가상 네트워크 선택](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "가상 네트워크 리소스 페이지 찾기")
1. 가상 **네트워크** 페이지에서 **을 선택합니다.**

   ![가상 네트워크 페이지](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "만들기 선택")
1. **만들기를**선택하면 **가상 네트워크 만들기** 페이지가 열립니다.
1. **기본** 탭에서 프로젝트 **세부 정보를** 구성하고 VNet 세부 **정보를 세부합니다.**

   ![기본 탭](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "기본 사항 탭") 필드를 채우면 필드에 입력한 문자의 유효성이 검사될 때 녹색 확인 표시가 표시됩니다. 일부 값은 자동으로 채워지며, 사용자 고유의 값으로 바꿀 수 있습니다.

   - **구독**: 나열된 구독이 올바른지 확인합니다. 드롭다운을 사용하여 구독을 변경할 수 있습니다.
   - **리소스 그룹**: 기존 리소스 그룹을 선택하거나 **새 리소스 그룹을** 새로 만들기를 클릭합니다. 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요를](../articles/azure-resource-manager/management/overview.md#resource-groups)참조하십시오.
   - **이름**: 가상 네트워크의 이름을 입력합니다.
   - **지역**: VNet의 위치를 선택합니다. 이 위치는 이 VNet에 배포하는 리소스가 상주하는 위치를 결정합니다.

1. IP 주소 탭에서 값을 **구성합니다.** 아래 예제에 표시된 값은 데모를 위한 것입니다. 필요한 설정에 따라 이러한 값을 조정합니다.

   ![IP 주소 탭](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "IP 주소 탭")  
   - **IPv4 주소 공간**: 기본적으로 주소 공간이 자동으로 만들어집니다. 주소 공간을 클릭하여 사용자 고유의 값을 반영하도록 조정할 수 있습니다. 주소 공간을 추가할 수도 있습니다.
   - **IPv6**: 구성에 IPv6 주소 공간이 필요한 경우 **IPv6 주소 공간 추가** 상자를 선택하여 해당 정보를 입력합니다.
   - **서브넷**: 기본 주소 공간을 사용하는 경우 기본 서브넷이 자동으로 만들어집니다. 주소 공간을 변경하는 경우 서브넷을 추가해야 합니다. **+ 서브넷 추가를** 선택하여 서브넷 추가 창을 **엽니다.** 다음 설정을 구성한 다음 **추가를** 선택하여 값을 추가합니다.
      - **서브넷 이름**: 이 예제에서는 서브넷을 "FrontEnd"라고 명명했습니다.
      - **서브넷 주소 범위**: 이 서브넷의 주소 범위입니다.

1. **보안** 탭에서 현재 기본값을 그대로 둡니다.

   - **DDoS 보호**: 기본
   - **방화벽**: 사용 안 함
1. **검토 + 만들기를** 선택하여 가상 네트워크 설정의 유효성을 검사합니다.
1. 설정의 유효성이 검사된 후 **에서 만들기를**선택합니다.
