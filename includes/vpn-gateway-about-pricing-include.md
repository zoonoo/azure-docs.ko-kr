---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ae36adc78d8c87d85c64fd61cb3a50dfcae60b85
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170530"
---
가상 네트워크 게이트웨이에 대한 시간당 컴퓨팅 비용 및 가상 네트워크 게이트웨이에서 송신 데이터 전송 등 두 가지에 대한 비용을 지불합니다. 가격 책정 정보는 [가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway) 페이지에 있습니다.

**가상 네트워크 게이트웨이 계산 비용**<br>각 가상 네트워크 게이트웨이에는 시간당 계산 비용이 포함됩니다. 가격은 가상 네트워크 게이트웨이를 만들 때 지정한 게이트웨이 SKU에 따라 다릅니다. 비용은 게이트웨이 자체에 대한 것이며 게이트웨이를 통해 전달되는 데이터 전송에 추가됩니다. 활성-능동 설정의 비용은 활성-수동의 비용과 같습니다.

**데이터 전송 비용**<br>데이터 전송 비용은 원본 가상 네트워크 게이트웨이의 송신 트래픽에 따라 계산됩니다.

* 온-프레미스 VPN 디바이스에 트래픽을 보내는 경우 인터넷 송신 데이터 전송율을 사용하여 청구됩니다.
* 다른 지역에 있는 가상 네트워크 사이에 트래픽을 보내는 경우 가격 책정은 지역을 기반으로 합니다.
* 동일한 지역에 있는 가상 네트워크 간에 트래픽을 보내는 경우 데이터 비용이 없습니다. 동일한 지역에 있는 VNet 간의 트래픽은 무료입니다.