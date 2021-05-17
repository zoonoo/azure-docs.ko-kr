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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95563444"
---
| 리소스                                | 제한        |
|-----------------------------------------|------------------------------|
| VNet 주소 접두사                   | VPN 게이트웨이당 600개          |
| BGP 경로 집계                    | VPN 게이트웨이당 4,000개        |
| 로컬 네트워크 게이트웨이 주소 접두사  | 로컬 네트워크 게이트웨이당 1000개               |
| S2S 연결                         | [게이트웨이 SKU에 따라 다름](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)|
| P2S 연결                         | [게이트웨이 SKU에 따라 다름](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |
| P2S 경로 제한 - IKEv2                 | Windows가 아닌 경우 256 **/** Windows인 경우 25           |
| P2S 경로 제한-OpenVPN               | 1000                         |
| 최대 flows                              | VpnGw1/AZ의 경우 100K **/** VpnGw2-4/AZ의 경우 512K|