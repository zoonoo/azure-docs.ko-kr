---
title: 프리미엄 Azure Event Hubs 네임스페이스에 대한 처리 단위 구성
description: 프리미엄 계층 Event Hubs 네임스페이스에 대한 처리 단위를 구성하는 지침을 제공합니다.
ms.topic: article
ms.date: 05/26/2021
ms.openlocfilehash: 443832c9fcd4ee8ce8e314a80251f2575aed003d
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110631691"
---
# <a name="configure-processing-units-for-a-premium-tier-azure-event-hubs-namespace"></a>프리미엄 계층 Azure Event Hubs 네임스페이스에 대한 처리 단위 구성
이 문서에서는 프리미엄 계층 Azure Event Hubs 네임스페이스에 대한 PU(처리 단위)를 구성하는 지침을 제공합니다. **프리미엄** 계층에 대한 자세한 내용은 [Event Hubs 프리미엄](event-hubs-premium-overview.md)을 참조하세요.

## <a name="configure-processing-units-when-creating-a-namespace"></a>네임스페이스를 만들 때 처리 단위 구성
Azure Portal에서 **프리미엄** 계층 네임스페이스를 만들 때 자동 확장 기능이 자동으로 사용되도록 설정됩니다. 네임스페이스를 만든 후 업데이트할 수 있는 네임스페이스(1, 2, 4, 8 또는 16)의 처리 단위(PU) 수를 구성할 수 있습니다. 

:::image type="content" source="./media/configure-processing-units-premium-namespace/event-hubs-auto-inflate-prem.png" alt-text="프리미엄 네임스페이스를 만들 때 사용하도록 설정하는 스크린샷":::

## <a name="configure-processing-units-for-an-existing-namespace"></a>기존 네임스페이스에 대한 처리 단위 구성
기존 프리미엄 네임스페이스에 대한 PU 수를 업데이트하려면 다음 단계를 수행합니다. 

1. 네임스페이스의 **Event Hubs 네임스페이스** 페이지에서 왼쪽 메뉴의 **설정** 아래에서 **확장** 을 선택합니다. 
1. **처리 단위** 의 값을 업데이트하고 **저장** 을 선택합니다. 

    :::image type="content" source="./media/configure-processing-units-premium-namespace/scale-settings-prem.png" alt-text="기존의 프리미엄 네임스페이스에 대해 자동 확장을 사용하도록 설정하는 스크린샷":::

## <a name="next-steps"></a>다음 단계
처리 단위 및 Event Hubs 프리미엄 계층에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Event Hubs 프리미엄](event-hubs-premium-overview.md)
- [Event Hubs 확장성](event-hubs-scalability.md)
