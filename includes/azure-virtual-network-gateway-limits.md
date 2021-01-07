---
title: 포함 파일
description: 포함 파일
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 9fe9ef5549ced3b73d18d553fa0b62ec019684fe
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563444"
---
| 리소스                                | 제한        |
|-----------------------------------------|------------------------------|
| VNet 주소 접두사                   | 600 VPN gateway 당          |
| BGP 경로 집계                    | 4000 VPN gateway 당        |
| 로컬 네트워크 게이트웨이 주소 접두사  | 1000-로컬 네트워크 게이트웨이 당               |
| S2S 연결                         | [게이트웨이 SKU에 따라 다릅니다.](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)|
| P2S 연결                         | [게이트웨이 SKU에 따라 다릅니다.](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |
| P2S 경로 제한-IKEv2                 | windows 용 Windows 25 이외의 경우 256 **/**           |
| P2S 경로 제한-OpenVPN               | 1000                         |
| 최대 flows                              | VpnGw2에 대 한 VpnGw1/AZ 512kb의 10만  **/**  -4/az|