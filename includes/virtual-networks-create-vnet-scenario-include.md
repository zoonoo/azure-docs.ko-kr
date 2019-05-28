---
title: 포함 파일
description: 포함 파일
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116831"
---
## <a name="scenario"></a>시나리오

VNet 및 서브넷을 만드는 방법을 설명하기 위해 이 문서에서는 다음 시나리오를 사용합니다.

![VNet 시나리오](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

이 시나리오에서는 예약된 CIDR 블록 **192.168.0.0./16**을 사용하여 **TestVNet**이라는 VNet을 만듭니다. VNet은 다음 서브넷을 포함합니다. 

* **FrontEnd**, CIDR 블록으로 **192.168.1.0/24** 사용
* **BackEnd**, CIDR 블록으로 **192.168.2.0/24** 사용

