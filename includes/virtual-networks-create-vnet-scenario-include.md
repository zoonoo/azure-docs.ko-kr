---
title: 포함 파일
description: 포함 파일
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 29b2ac1a5dc128028dbd40e683c1b45e6208d124
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>시나리오

VNet 및 서브넷을 만드는 방법을 설명하기 위해 이 문서에서는 다음 시나리오를 사용합니다.

![VNet 시나리오](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

이 시나리오에서는 예약된 CIDR 블록 **192.168.0.0./16**을 사용하여 **TestVNet**이라는 VNet을 만듭니다. VNet은 다음 서브넷을 포함합니다. 

* **FrontEnd**, CIDR 블록으로 **192.168.1.0/24** 사용
* **BackEnd**, CIDR 블록으로 **192.168.2.0/24** 사용

