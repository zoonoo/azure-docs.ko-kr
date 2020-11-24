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
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95554191"
---
GatewaySubnet의 0.0.0.0/0 대상과 NSG를 사용하는 사용자 정의 경로는 **지원되지 않습니다**. 이 구성을 사용하여 만든 게이트웨이는 생성이 차단됩니다. 게이트웨이가 제대로 작동하려면 관리 컨트롤러에 대한 액세스 권한이 필요합니다. 게이트웨이의 가용성을 보장하려면 GatewaySubnet에서 [BGP 경로 전파](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol)를 "사용"으로 설정해야 합니다. 사용 안 함으로 설정된 경우에는 게이트웨이가 작동하지 않습니다.