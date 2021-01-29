---
title: Redis 용 Azure Cache Event Grid 개요
description: Azure Event Grid를 사용 하 여 Redis 용 Azure Cache 이벤트를 게시 합니다.
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056073"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Redis 용 Azure Cache Event Grid 개요 

Redis 이벤트 (예: 패치, 크기 조정, 가져오기/내보내기) 이벤트 (예: RDB) 이벤트에 대 한 Azure Cache는 Azure Functions, Azure Logic Apps 또는 사용자 고유의 http 수신기에 대 한 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) 를 사용 하 여 푸시됩니다. Event Grid은 풍부한 재시도 정책 및 배달 못 한 편지를 통해 응용 프로그램에 안정적인 이벤트 배달을 제공 합니다.

Redis 용 azure Cache에서 지 원하는 이벤트의 전체 목록을 보려면 [Azure cache For Redis events 스키마](../event-grid/event-schema-azure-cache.md) 문서를 참조 하세요.

Redis 이벤트에 대 한 Azure Cache를 시도 하려면 다음 빠른 시작 중 하나를 참조 하세요.

|이 도구를 사용 하려면 다음을 수행 합니다.    |이 빠른 시작을 참조 하세요. |
|--|-|
|Azure portal    |[빠른 시작: Azure Portal을 사용 하 여 Redis 이벤트에 대 한 Azure Cache를 웹 끝점으로 라우팅합니다.](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[빠른 시작: PowerShell을 사용 하 여 Redis 이벤트에 대 한 Azure Cache를 웹 엔드포인트로 라우팅](cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[빠른 시작: Azure CLI 사용 하 여 Redis 이벤트에 대 한 Azure Cache를 웹 엔드포인트로 라우팅](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>이벤트 모델

Event Grid는 [이벤트 구독](../event-grid/concepts.md#event-subscriptions) 을 사용 하 여 이벤트 메시지를 구독자로 라우팅합니다. 이 이미지는 이벤트 게시자, 이벤트 구독 및 이벤트 처리기 간의 관계를 보여 줍니다.

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Event grid 모델입니다.":::

먼저 이벤트에 끝점을 구독 합니다. 그런 다음 이벤트가 트리거되면 Event Grid 서비스에서 해당 이벤트에 대 한 데이터를 끝점으로 보냅니다.

보려면 [Azure Cache For Redis events 스키마](../event-grid/event-schema-azure-cache.md) 문서를 참조 하세요.

> [!div class="checklist"]
> * Redis 이벤트에 대 한 Azure 캐시의 전체 목록과 각 이벤트가 트리거되는 방법에 대해 설명 합니다.
> * 이러한 각 이벤트에 대해 Event Grid에서 전송 하는 데이터의 예입니다.
> * 데이터에 표시 되는 각 키 값 쌍의 용도입니다.


## <a name="best-practices-for-consuming-events"></a>이벤트 사용에 대 한 모범 사례

Redis 이벤트에 대 한 Azure Cache를 처리 하는 응용 프로그램은 다음과 같은 몇 가지 권장 방법을 따라야 합니다.
> [!div class="checklist"]
> * 이벤트를 동일한 이벤트 처리기로 라우팅하는 여러 구독을 구성할 수 있으므로 이벤트를 특정 원본에서 가져온 것으로 가정 하지 않는 것이 중요 하며, 메시지의 항목을 확인 하 여 예상 되는 Redis 인스턴스에 대 한 Azure Cache가 제공 되는지 확인 하는 것이 중요 합니다.
> * 마찬가지로, eventType이 본인이 처리하려는 형식인지 확인하고, 수신된 모든 이벤트가 예상하는 형식일 것이라고 간주하지 않도록 합니다.
> * Redis 이벤트에 대 한 Azure Cache는 구독자에 게 한 번 이상 배달 되도록 보장 하 여 모든 메시지가 출력 되도록 합니다. 그러나 구독을 다시 시도 하거나 사용할 수 있기 때문에 중복 된 메시지가 때때로 발생할 수 있습니다. 메시지 배달 및 다시 시도에 대 한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](../event-grid/delivery-and-retry.md)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

Event Grid에 대해 자세히 알아보고 Azure Cache에 Redis 이벤트를 제공 해 보세요.

- [Event Grid 정보](../event-grid/overview.md)
- [Azure Cache for Redis events 스키마](../event-grid/event-schema-azure-cache.md)
- [Azure CLI를 사용 하 여 Redis 용 Azure Cache 이벤트를 웹 엔드포인트로 라우팅](cache-event-grid-quickstart-cli.md)
- [Azure Portal를 사용 하 여 Redis 용 Azure Cache 이벤트를 웹 끝점으로 라우팅합니다.](cache-event-grid-quickstart-portal.md)
- [PowerShell을 사용 하 여 Redis 용 Azure Cache 이벤트를 웹 엔드포인트로 라우팅](cache-event-grid-quickstart-powershell.md)
