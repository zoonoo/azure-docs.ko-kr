---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e12ad66c635632b29b70000b6e227ddcbb5357b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80502104"
---
0.0.0.0/0 대상및 GatewaySubnet의 NSG가 있는 사용자 정의 **경로는 지원되지 않습니다.** 이 구성으로 생성된 게이트웨이는 생성이 차단됩니다. 게이트웨이가 제대로 작동하려면 관리 컨트롤러에 액세스해야 합니다. 게이트웨이의 가용성을 보장하기 위해 [BGP 경로 전파를](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol]) 게이트웨이서브넷에서 "사용"으로 설정해야 합니다. 이 것을 비활성화하도록 설정하면 게이트웨이가 작동하지 않습니다.
