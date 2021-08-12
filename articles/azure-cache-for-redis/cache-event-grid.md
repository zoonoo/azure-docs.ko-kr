---
title: Azure Cache for Redis Event Grid 개요
description: Azure Event Grid를 사용하여 Azure Cache for Redis 이벤트를 게시합니다.
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99056073"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Azure Cache for Redis Event Grid 개요 

패치, 스케일링, RDB(가져오기/내보내기) 이벤트와 같은 Azure Cache for Redis 이벤트는 Azure Functions, Azure Logic Apps 또는 사용자 고유의 http 수신기와 같은 구독자에게 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)를 사용하여 푸시됩니다. Event Grid은 풍부한 재시도 정책 및 배달 못 한 편지를 통해 애플리케이션에 안정적으로 이벤트를 배달합니다.

Azure Cache for Redis에서 지원하는 이벤트의 전체 목록을 보려면 [Azure Cache for Redis 이벤트 스키마](../event-grid/event-schema-azure-cache.md) 문서를 참조하세요.

Azure Cache for Redis 이벤트를 시도하려면 다음 빠른 시작을 참조하세요.

|사용하려는 도구:    |참조할 빠른 시작: |
|--|-|
|Azure portal    |[빠른 시작: Azure Portal을 사용하여 Azure Cache for Redis 이벤트를 웹 엔드포인트로 라우팅](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[빠른 시작: PowerShell을 사용하여 Azure Cache for Redis 이벤트를 웹 엔드포인트로 라우팅](cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[빠른 시작: Azure CLI를 사용하여 Azure Cache for Redis 이벤트를 웹 엔드포인트로 라우팅](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>이벤트 모델

Event Grid는 [이벤트 구독](../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. 이 이미지는 이벤트 게시자, 이벤트 구독 및 이벤트 처리기 간 관계를 보여 줍니다.

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Event Grid 모델":::

먼저 이벤트에 엔드포인트를 구독합니다. 그러면 이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 엔드포인트로 보냅니다.

[Azure Cache for Redis 이벤트 스키마](../event-grid/event-schema-azure-cache.md) 문서를 참조하여 다음 내용을 확인하세요.

> [!div class="checklist"]
> * Azure Cache for Redis 이벤트의 전체 목록과 각 이벤트가 트리거되는 방법
> * 이러한 각 이벤트에 대해 Event Grid에서 전송하는 데이터 예제
> * 데이터에 표시되는 각 키 값 쌍의 용도


## <a name="best-practices-for-consuming-events"></a>이벤트 사용 모범 사례

Azure Cache for Redis 이벤트를 처리하는 애플리케이션은 다음과 같은 몇 가지 권장 지침을 따라야 합니다.
> [!div class="checklist"]
> * 동일한 이벤트 처리기로 이벤트를 라우팅하도록 여러 구독을 구성할 수 있으므로, 이벤트가 특정 원본에서 온 것이라고 가정하지 말고 메시지의 토픽을 확인하여 예상하는 Azure Cache for Redis 인스턴스에서 온 것인지 확인해야 합니다.
> * 마찬가지로, eventType이 본인이 처리하려는 형식인지 확인하고, 수신된 모든 이벤트가 예상하는 형식일 것이라고 간주하지 않도록 합니다.
> * Azure Cache for Redis 이벤트는 구독자에게 한 번 이상 배달되도록 보장하여 모든 메시지가 출력되도록 합니다. 그러나 다시 시도 또는 구독 가용성으로 인해 중복 메시지가 가끔 발생할 수 있습니다. 메시지 배달 및 다시 시도에 대한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](../event-grid/delivery-and-retry.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

Event Grid에 대해 자세히 알아보고 Azure Cache for Redis 이벤트를 사용해 보세요.

- [Event Grid 정보](../event-grid/overview.md)
- [Azure Cache for Redis 이벤트 스키마](../event-grid/event-schema-azure-cache.md)
- [Azure CLI를 사용하여 Azure Cache for Redis 이벤트를 웹 엔드포인트로 라우팅](cache-event-grid-quickstart-cli.md)
- [Azure Portal을 사용하여 Azure Cache for Redis 이벤트를 웹 엔드포인트로 라우팅](cache-event-grid-quickstart-portal.md)
- [PowerShell을 사용하여 Azure Cache for Redis 이벤트를 웹 엔드포인트로 라우팅](cache-event-grid-quickstart-powershell.md)
