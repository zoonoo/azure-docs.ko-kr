---
title: Azure Virtual WAN을 기본에서 표준으로 업그레이드 - Azure Portal | Microsoft Docs
description: 더 강력한 기능을 위해 가상 WAN 유형을 업그레이드할 수 있습니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 6290d89ed0ee539b4df4c2c8bc9070097da98c81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91447360"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>가상 WAN을 기본에서 표준으로 업그레이드

이 문서는 기본 WAN을 표준 WAN으로 업그레이드하는 데 도움이 됩니다. '기본' WAN 유형은 내부에 있는 모든 허브를 기본 SKU 허브로 만듭니다. 기본 허브에서 사용자는 사이트 간 VPN 기능만으로 제한됩니다. '표준' WAN 유형은 내부에 있는 모든 허브를 표준 SKU 허브로 만듭니다. 표준 허브를 사용하는 경우, Azure 허브를 통해 ExpressRoute, 사용자(지점 및 사이트 간) VPN, 전체 메시 허브 및 VNet 간 전송을 사용하도록 설정할 수 있습니다.

다음 표에서는 각 WAN 유형에 사용 가능한 구성을 보여 줍니다.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>가상 WAN 유형을 변경하려면,

1. 가상 WAN에 대한 페이지에서 **구성** 을 선택하여 구성 페이지를 엽니다.

   ![아래쪽에 강조 표시된 표준 유형의 가상 WAN으로 업그레이드하는 방법에 대한 정보 텍스트 상자가 있는 "구성" 페이지를 보여 주는 스크린샷입니다.](./media/upgrade-virtual-wan/1.png)
2. 가상 WAN 유형의 경우, 드롭다운에서 **표준** 을 선택합니다.

   !["가상 WAN 유형" 드롭다운 메뉴를 보여 주는 스크린샷](./media/upgrade-virtual-wan/2.png)
3. 표준 가상 WAN으로 업그레이드하는 경우 기본 가상 WAN으로 돌아갈 수 없습니다. 업그레이드하려는 경우 **확인** 을 선택합니다.

   !["업그레이드 확인" 대화를 보여 주는 스크린샷](./media/upgrade-virtual-wan/4.png)
4. 변경 내용이 저장되면 가상 WAN 페이지가 다음 예제와 유사하게 표시됩니다.

   ![Virtual WAN 다이어그램](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.