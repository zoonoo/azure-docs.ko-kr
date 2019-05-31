---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159242"
---
Resource Manager 배포 모델을 사용 중인 경우 새로운 게이트웨이 SKU로 변경할 수 있습니다. 레거시 게이트웨이 SKU에서 새 SKU로 변경할 때는 기존 VPN 게이트웨이를 삭제하고 새 VPN 게이트웨이를 만듭니다.

워크플로:

1. 가상 네트워크 게이트웨이에 대한 모든 연결을 제거합니다.
2. 이전 VPN Gateway를 삭제합니다.
3. 새 VPN Gateway를 만듭니다.
4. 새 VPN Gateway IP 주소로 온-프레미스 VPN 디바이스를 업데이트합니다(사이트 간 연결의 경우).
5. 이 게이트웨이에 연결할 모든 VNet 간 로컬 네트워크 게이트웨이에 대해 게이트웨이 IP 주소 값을 업데이트합니다.
6. 이 VPN Gateway를 통해 가상 네트워크에 연결하는 P2S 클라이언트용 새 클라이언트 VPN 구성 패키지를 다운로드합니다.
7. 가상 네트워크 게이트웨이에 대한 모든 연결을 다시 만듭니다.

고려 사항:

* 새 SKU로 이동하려면 VPN 게이트웨이가 Resource Manager 배포 모델에 있어야 합니다.
* 클래식 VPN 게이트웨이가 있을 경우 해당 게이트웨이에 대한 이전 버전의 레거시 SKU를 계속 사용해야 하지만 레거시 SKU 간에 크기를 조정할 수 있습니다. 새 SKU로 변경할 수는 없습니다.
* 레거시 SKU에서 새 SKU로 변경하면 연결 가동 중지 시간이 발생합니다.
* 새 게이트웨이 SKU로 변경하면 VPN 게이트웨이의 공용 IP 주소가 변경됩니다. 이전에 사용했던 것과 동일한 공용 IP 주소 개체를 지정하는 경우에도 마찬가지입니다.