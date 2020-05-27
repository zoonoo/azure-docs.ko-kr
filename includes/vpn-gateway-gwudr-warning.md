---
title: 파일 포함
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275487"
---
0.0.0.0/0 대상 및 서브넷에 대 한 NSGs를 사용 하는 사용자 정의 경로 **는 지원 되지 않습니다**. 이 구성을 사용 하 여 만든 게이트웨이는 생성을 차단 합니다. 게이트웨이가 제대로 작동 하려면 관리 컨트롤러에 대 한 액세스가 필요 합니다. [BGP 경로 전파](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) 는 게이트웨이의 가용성을 보장 하기 위해 게이트웨이 서브넷에서 "사용"으로 설정 해야 합니다. 사용 안 함으로 설정 하면 게이트웨이가 작동 하지 않습니다.
