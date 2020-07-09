---
title: Azure Event Grid 이벤트 처리기
description: Azure Event Grid에 지원되는 이벤트 처리기를 설명합니다. Azure Automation, Functions, Event Hubs, 하이브리드 연결, Logic Apps, Service Bus, Queue Storage, 웹후크.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 76f7fff3e0a3e1c0c554e452987bf944503358b8
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86117018"
---
# <a name="event-handlers-in-azure-event-grid"></a>Azure Event Grid의 이벤트 처리기
이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리하기 위한 추가 작업을 수행합니다. 여러 Azure 서비스는 이벤트를 처리하도록 자동으로 구성됩니다. 이벤트를 처리하기 위해 웹후크를 사용할 수도 있습니다. 웹후크는 이벤트를 처리하기 위해 Azure에서 호스트될 필요가 없습니다. Event Grid는 HTTPS 웹후크 엔드포인트만 지원합니다.

## <a name="supported-event-handlers"></a>지원되는 이벤트 처리기
지원되는 이벤트 처리기는 다음과 같습니다. 

- [웹후크](handler-webhooks.md). Azure Automation Runbook 및 Logic Apps는 웹후크를 통해 지원됩니다. 
- [Azure 함수](handler-functions.md)
- [Event Hubs](handler-event-hubs.md)
- [릴레이 하이브리드 연결](handler-relay-hybrid-connections.md)
- [Service Bus 큐 및 항목](handler-service-bus.md)
- [스토리지 큐](handler-storage-queues.md)

## <a name="next-steps"></a>다음 단계
- Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
