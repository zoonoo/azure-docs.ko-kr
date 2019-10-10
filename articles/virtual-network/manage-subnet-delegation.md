---
title: Azure 가상 네트워크에서 서브넷 위임 추가 또는 제거
titlesuffix: Azure Virtual Network
description: Azure의 Azure에서 서비스에 대 한 위임 된 서브넷을 추가 하거나 제거 하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2019
ms.author: kumud
ms.openlocfilehash: 5fa340fc3c839d74f292f551b73184ea4df1c0f1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72175957"
---
# <a name="add-or-remove-a-subnet-delegation"></a>서브넷 위임 추가 또는 제거

서브넷 위임은 서비스를 배포할 때 고유 식별자를 사용하여 서브넷에서 서비스 특정 리소스를 만들 수 있는 서비스에 대한 명시적 권한을 제공합니다. 이 문서에서는 Azure 서비스에 대 한 위임 된 서브넷을 추가 하거나 제거 하는 방법을 설명 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="create-the-virtual-network"></a>가상 네트워크 만들기

이 섹션에서는 나중에 Azure 서비스에 위임할 가상 네트워크 및 서브넷을 만듭니다.

1. 화면의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **가상 네트워크**를 차례로 선택합니다.
1. **가상 네트워크 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | *MyVirtualNetwork*를 입력합니다. |
    | 주소 공간 | *10.0.0.0/16*을 입력합니다. |
    | 구독 | 구독을 선택합니다.|
    | 리소스 그룹 | **새로 만들기**를 선택하고 *myResourceGroup*을 입력한 다음, **확인**을 선택합니다. |
    | 위치 | **Eastus**를 선택 합니다.|
    | 서브넷 - 이름 | *mySubnet*을 입력합니다. |
    | 서브넷 - 주소 범위 | *10.0.0.0/24*를 입력합니다. |
    |||
1. 나머지를 기본값으로 유지 하 고 **만들기**를 선택 합니다.

## <a name="permissons"></a>권한이

Azure 서비스에 위임 하려는 서브넷을 만들지 않은 경우 다음 권한이 필요 합니다. `Microsoft.Network/virtualNetworks/subnets/write`.

기본 제공 [네트워크 참가자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에도 필요한 권한이 포함 되어 있습니다.

## <a name="delegate-a-subnet-to-an-azure-service"></a>Azure 서비스에 서브넷 위임

이 섹션에서는 이전 섹션에서 만든 서브넷을 Azure 서비스에 위임 합니다.

1. 포털의 검색 창에서 *myVirtualNetwork*를 입력합니다. 검색 결과에 **myVirtualNetwork**가 표시되면 선택합니다.
2. 검색 결과에서 *myVirtualNetwork*를 선택 합니다.
3. **설정**에서 **서브넷**을 선택 하 고 **mysubnet**을 선택 합니다.
4. *Mysubnet* 페이지의 **서브넷 위임** 목록에서 **서비스에 대 한 서브넷 위임** 에 나열 된 서비스 (예: **DBforPostgreSQL/serversv2**)를 선택 합니다.  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>Azure 서비스에서 서브넷 위임 제거

1. 포털의 검색 창에서 *myVirtualNetwork*를 입력합니다. 검색 결과에 **myVirtualNetwork**가 표시되면 선택합니다.
2. 검색 결과에서 *myVirtualNetwork*를 선택 합니다.
3. **설정**에서 **서브넷**을 선택 하 고 **mysubnet**을 선택 합니다.
4. *Mysubnet* 페이지의 **서브넷 위임** 목록에서 **서비스에 서브넷 위임**아래에 나열 된 서비스에서 **없음** 을 선택 합니다. 

## <a name="next-steps"></a>다음 단계
- [Azure에서 서브넷을 관리](virtual-network-manage-subnet.md)하는 방법을 알아봅니다.
