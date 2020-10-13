---
title: Azure 가상 WAN을 기본에서 표준으로 업그레이드-Azure Portal | Microsoft Docs
description: 더 강력한 기능을 위해 가상 WAN 유형을 업그레이드할 수 있습니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 6290d89ed0ee539b4df4c2c8bc9070097da98c81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447360"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>기본에서 표준으로 가상 WAN 업그레이드

이 문서는 기본 WAN을 표준 WAN으로 업그레이드 하는 데 도움이 됩니다. ' 기본 ' WAN 종류는 내부에 있는 모든 허브를 기본 SKU 허브로 만듭니다. 기본 허브에서는 사이트 간 VPN 기능만 사용할 수 있습니다. ' 표준 ' WAN 유형은 표준 SKU 허브로이 내부에 모든 허브를 만듭니다. Standard hubs를 사용 하는 경우 Azure 허브를 통해 Express 경로, 사용자 (지점 및 사이트 간) VPN, 전체 메시 허브 및 VNet 간 전송을 사용 하도록 설정할 수 있습니다.

다음 표에서는 각 WAN 유형에 사용할 수 있는 구성을 보여 줍니다.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>가상 WAN 유형을 변경 하려면

1. 가상 WAN에 대 한 페이지에서 **구성** 을 선택 하 여 구성 페이지를 엽니다.

   ![아래쪽에 강조 표시 된 표준 유형의 가상 WAN으로 업그레이드 하는 방법에 대 한 정보 텍스트 상자가 있는 "구성" 페이지를 보여 주는 스크린샷](./media/upgrade-virtual-wan/1.png)
2. 가상 WAN 유형의 경우 드롭다운에서 **표준** 을 선택 합니다.

   !["가상 WAN 유형" 드롭다운 메뉴를 보여 주는 스크린샷](./media/upgrade-virtual-wan/2.png)
3. 표준 가상 WAN으로 업그레이드 하는 경우 기본 가상 WAN으로 되돌릴 수 없다는 것을 이해 합니다. 업그레이드 하려는 경우 **확인** 을 선택 합니다.

   !["업그레이드 확인" 대화 상자를 보여 주는 스크린샷](./media/upgrade-virtual-wan/4.png)
4. 변경 내용이 저장 되 면 가상 WAN 페이지가 다음 예와 유사 하 게 표시 됩니다.

   ![Virtual WAN 다이어그램](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.