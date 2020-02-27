---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 872ba86c9e43b1f6642331908eb829605f6c19bd
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619652"
---
다음 단계에 따라 Resource Manager 배포 모델과 Azure Portal을 사용하여 VNet을 만들 수 있습니다. 가상 네트워크에 대한 자세한 내용은 [Virtual Network 개요](../articles/virtual-network/virtual-networks-overview.md)를 참조하세요.

>[!NOTE]
>VNet을 온-프레미스 위치에 연결하려면 온-프레미스 네트워크 관리자와 협력하여 이 가상 네트워크에 특별히 사용할 수 있는 IP 주소 범위를 구성합니다. VPN 연결의 양쪽 모두에 중복 주소 범위가 있는 경우 트래픽이 예기치 않은 방식으로 라우팅됩니다. 또한 이 VNet을 다른 VNet에 연결하려면 주소 공간이 다른 VNet과 겹치면 안됩니다. 이에 따라 네트워크 구성을 적절히 계획합니다.
>
>

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **리소스, 서비스 및 문서 검색 (G +/)** 에서 *가상 네트워크*를 입력 합니다.

   ![Virtual Network 리소스 페이지 찾기](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "가상 네트워크 리소스 찾기 페이지")
1. **Marketplace** 결과에서 **Virtual Network** 를 선택 합니다.

   ![가상 네트워크 선택](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "가상 네트워크 리소스 찾기 페이지")
1. **Virtual Network** 페이지에서 **만들기**를 클릭 합니다.

   ![가상 네트워크 페이지](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "만들기를 클릭")
1. 만들기를 클릭 하면 **가상 네트워크 만들기** 페이지가 열립니다.

   ![가상 네트워크 만들기 페이지](./media/vpn-gateway-basic-vnet-rm-portal-include/create-virtual-network-page.png "가상 네트워크 만들기 페이지")
1. **가상 네트워크 만들기** 페이지에서 VNet 설정을 구성합니다. 필드를 채울 때 필드에 입력한 문자의 유효성이 검사되면 빨간색 느낌표가 녹색 확인 표시가 됩니다. 일부 값은 자동으로 채워지며, 사용자 고유의 값으로 바꿀 수 있습니다.

   - **이름**: 가상 네트워크의 이름을 입력합니다.
   - **주소 공간**: 주소 공간을 입력합니다. 추가할 주소 공간이 여러 개 있으면 첫 번째 주소 공간을 입력합니다. 다른 주소 공간은 VNet을 만든 후 나중에 추가할 수 있습니다. 구성에 IPv6 주소 공간이 필요한 경우 해당 정보를 입력 하는 확인란을 선택 합니다.
   - **구독**: 나열된 구독이 올바른지 확인합니다. 드롭다운을 사용하여 구독을 변경할 수 있습니다.
   - **리소스 그룹**: 기존 리소스 그룹을 선택 하거나 새 리소스 그룹의 이름을 입력 하 여 새 리소스 그룹을 만듭니다. 새 그룹을 만드는 경우 계획된 구성 값에 따라 리소스 그룹의 이름을 지정합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../articles/azure-resource-manager/management/overview.md#resource-groups)를 참조하세요.
   - **위치**: VNet의 위치를 선택합니다. 이 위치는 이 VNet에 배포하는 리소스가 상주하는 위치를 결정합니다.
   - **서브넷**: 서브넷 **이름** 및 서브넷 **주소 범위**를 추가 합니다. 서브넷은 VNet을 만든 후 나중에 추가할 수 있습니다.
   - **DDos protection**: 표준 서비스를 사용 하지 않을 경우 **기본**을 선택 합니다.
   - **서비스 끝점**: 구성에서이 설정을 지정 하지 않은 경우이 설정을 **사용 안 함**으로 그대로 둘 수 있습니다.
   - **방화벽**: 구성에서이 설정을 지정 하지 않은 경우이 설정을 **사용 안 함**으로 그대로 둘 수 있습니다.
1. **만들기** 를 클릭 하 여 가상 네트워크 배포를 시작 합니다.
