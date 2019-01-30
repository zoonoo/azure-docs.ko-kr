---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eb2555cdc163ee0f88149248b9e7d83a51d34a1b
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444251"
---
1. [Azure Portal](http://portal.azure.com)에서 가상 네트워크 게이트웨이를 만들려는 Resource Manager 가상 네트워크를 선택합니다.

2. 가상 네트워크 페이지의 **설정** 섹션에서 **서브넷**을 선택하여 **서브넷** 페이지를 확장합니다.

3. **서브넷** 페이지에서 **게이트웨이 서브넷**을 선택하여 **서브넷 추가** 페이지를 엽니다.

   ![게이트웨이 서브넷 추가](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "게이트웨이 서브넷 추가")

4. 서브넷의 **이름**에 *GatewaySubnet* 값이 자동으로 채워집니다. Azure가 서브넷을 게이트웨이 서브넷으로 인식하기 위해 이 값이 필요합니다. 구성 요구 사항에 맞게 자동으로 채워진 **주소 범위** 값을 조정한 후 **확인**을 선택하여 서브넷을 만듭니다.

   ![서브넷 추가](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "서브넷 추가")