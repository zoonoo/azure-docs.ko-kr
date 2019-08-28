---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52ab0413dffeee59cb9d34c6276a0c806a4d0322
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780262"
---
게이트웨이 서브넷은 가상 네트워크 게이트웨이 서비스가 사용하는 예약된 IP 주소를 포함합니다. 게이트웨이 서브넷을 만듭니다.

1. 포털에서 가상 네트워크 게이트웨이를 만들려는 가상 네트워크로 이동합니다.
2. 가상 네트워크 페이지에서 **서브넷**을 클릭하여 **VNet1 - 서브넷** 페이지를 확장합니다.
3. 맨 위에 있는 **+게이트웨이 서브넷**을 클릭하여 **서브넷 추가** 페이지를 엽니다.

   ![게이트웨이 서브넷 추가](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "게이트웨이 서브넷 추가")
4. 서브넷의 **이름**에 필수 값 ‘GatewaySubnet’이 자동으로 채워집니다. 자동으로 채워진 **주소 범위 (CIDR 블록)** 를 다음 값과 일치 하도록 조정 합니다.

   **주소 범위(CIDR 블록)** : 10.1.255.0/27

   ![게이트웨이 서브넷 추가](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet1.png "게이트웨이 서브넷 추가")
5. 기본값은 **없음** 또는 **0을 선택**하 여 나머지 설정을 그대로 둡니다. 그런 다음 **확인** 을 클릭 하 여 게이트웨이 서브넷을 만듭니다.