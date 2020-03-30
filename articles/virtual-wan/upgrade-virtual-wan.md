---
title: Azure 가상 WAN을 기본에서 표준 - Azure 포털로 업그레이드 | 마이크로 소프트 문서
description: 더 큰 기능을 위해 가상 WAN 유형을 업그레이드할 수 있습니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515812"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>기본에서 표준으로 가상 WAN 업그레이드

이 문서에서는 기본 WAN을 표준 WAN으로 업그레이드하는 데 도움이 됩니다. '기본' WAN 유형은 기본 SKU 허브로 내부의 모든 허브를 만듭니다. 기본 허브에서는 사이트 간 VPN 기능으로만 제한됩니다. '표준' WAN 유형은 내부의 모든 허브를 표준 SKU 허브로 만듭니다. 표준 허브를 사용하는 경우 ExpressRoute, 사용자(Point-to-Site) VPN, 전체 메시 허브 및 Azure 허브를 통한 VNet-VNet 전송을 활성화할 수 있습니다.

다음 표에서는 각 WAN 유형에 사용할 수 있는 구성을 보여 주며 있습니다.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>가상 WAN 유형을 변경하려면

1. 가상 WAN의 페이지에서 구성페이지를 열려면 **구성을** 선택합니다.

   ![Virtual WAN 다이어그램](./media/upgrade-virtual-wan/1.png)
2. 가상 WAN 유형의 경우 드롭다운에서 **표준을** 선택합니다.

   ![Virtual WAN 다이어그램](./media/upgrade-virtual-wan/2.png)
3. 표준 가상 WAN으로 업그레이드하는 경우 기본 가상 WAN으로 되돌릴 수 없습니다. 업그레이드할 지 **확인을** 선택합니다.

   ![Virtual WAN 다이어그램](./media/upgrade-virtual-wan/4.png)
4. 변경 이 저장되면 가상 WAN 페이지가 이 예제와 유사합니다.

   ![Virtual WAN 다이어그램](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.